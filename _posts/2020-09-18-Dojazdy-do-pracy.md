---
layout: post
title: "Kiedy dojadę?"
categories: Python
author: "Michał"
math: false
---

Dalekie dojazdy do pracy (która odbywa się w godzinach 9-17) spowodowały że zacząłem zastanawiać się jak zoptymalizwać powroty do domu.
Problem: wrócić z Gliwic do Mysłowic w jak najkrótszym czasie.
Rozwiązanie: Monitorowanie opóźnień miejskich autobusów żeby wyznaczyć optymalne okno czasowe na powrót do domu.

## Początkowe pomysły

Monitorowanie ruchu na drodze - pierwszym pomysłem była próba wyciągnięcia danych z map TomTom lub Google, na szczęście zanim zdążyłem otworzyć okno przeglądarki przypomniałem sobie o górnośląskim ZTM (dawniej KZK GOP) które około rok temu zaczęło stawiać tablicę z dynamiczną informacją o kursowaniu autobusów.

Okazało się że w ramach tego systemu istnieje też strona [System Dynamicznej Informacji Pasażerskiej - Portal Pasażera](http://sdip.metropoliaztm.pl/web/ml/map/) z której można wyciągnąć informacje o opóźnieniu autobusów.

### Plan działania

* ~~Znalezienie wszystkich linii autobusowych do monitorowania~~
* ~~Wyznaczenie przystanków do monitorowania~~
* ~~Pobranie rozkładów jazdy~~ i zapisanie w BD
* Pobranie danych o opóżnieniu
  * Parsowanie danych, zapis do BD
  * Analiza i Wizualizacja
* Wnioski

## Monitorowane przystanki i linie autobusowe

Najbardziej interesuje mnie odcinek od Katowic do Mysłowic, ze szczególnym uwzględnieniem węzła Bagienna (rejon Wilhelminy) Żeby zmniejszyć liczbę generowanych zapytań będę pobierał dane o wszystkich autobusach zatrzymujących się na przystanku Mysłowice Katowicka

Z tego przystanku odjezdzają autobusy linii: 35, 44, 66, 77, 77N, 106, 149, 219, 292, 536, 788, 931, 995. Wstępnie do monitorowania wybrałem trzy linie, jadące od strony Katowic:
* 66 (line_id:[68](http://sdip.metropoliaztm.pl/web/ml/line/68)),
* 77 (line_id:[73](http://sdip.metropoliaztm.pl/web/ml/line/73)),
* 149 (line_id:[79](http://sdip.metropoliaztm.pl/web/ml/line/79))
* 931 (line_id:[97](http://sdip.metropoliaztm.pl/web/ml/line/97))

Pobranie informacji o wszystkich autobusach zatrzymujących się na przystanku Mysłowice Katowicka (`post_id: 103750` )

```python
import requests

URL = 'http://sdip.metropoliaztm.pl/web/map/vehicles/gj/A?interval=00%3A05%3A00&post_id=103750'
headers = {
    'accept': 'text/html,application/xhtml+xml,application/xml',
    'user-agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36'
}

r = requests.get(URL, headers=headers, verify=False)
print(r.json())
```

Analiza ([pobranego pliku JSON](http://sdip.metropoliaztm.pl/web/map/vehicles/gj/A?interval=00%3A05%3A00&post_id=103750)) pozwoliła wyciągnąć kilka interesujących informacji:

* można ustalić koordynaty każdego autobusu ZTM:

```json
"geometry":{
  "type":"Point",
  "coordinates":[
    2130007.73094,
    6479933.6062
  ]
},
```

Sa one zapisane w układzie EPSG:3857 (csr - coordinate projection systems). Konwersja do innych formatów będzie odbywać się z pomocą *pyproj*

```
# conda install -c conda-forge pyproj
```

Różnica w stosunku do planowanego rozkładu jest opisana jako `difference`, oznaczenie linii jest zapisane w polu `get_line_id`

`stop_id`
`post_id`

Ponieważ przystanki w pliku JSON są zapisane jako ID dla własnej wygody potrzebowałem znać ich nazwy.Zrobiłem to [parsując podstrony z listą przystanków](http://sdip.metropoliaztm.pl/web/ml/stop/page/1)

```python
def get_stops():
    bus_stops = {}
    page = 1
    headers = {
        'accept': 'text/html,application/xhtml+xml,application/xml',
        'user-agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36'
    }

    while True:
        URL = f"http://sdip.metropoliaztm.pl/web/ml/stop/page/{page}"
        r = requests.get(URL, headers=headers, verify=False)
    
        if r.status_code == 200:
            soup = BeautifulSoup(r.content, 'html.parser')
            page += 1
            time.sleep(.400)
        else:
            break

        li = soup.find_all('li')
        for i in li:
            # <li><a data-ajax="false" data-mini="true" href="/web/ml/route/100939">Grodziec Boleradz n/ż</a></li>
            #  4 groups: [0] href=" ; [1] /web/ml/route/100939 ; [2] "> ; [3] Grodziec Boleradz n/ż
            results = re.search(r'(href=")(.*)(">)([\w\s.\/]*)', str(i)).groups() 

            if results[1].lstrip('/').split('/')[2] == 'route':
                key = results[1].lstrip('/').split('/')[3]
                bus_stops[key] = results[3]

    return bus_stops
```

Przejrzałem na spokojnie informacje w pliku JSON z autobusami przejeżdżającymi przez dany przystanek i okazało się że nie ma tam informacji o przystanku na którym ma sie zatrzymać autobus :/ Potrzebne będzie parsowanie HTML,[przykładowa podstrona](http://sdip.metropoliaztm.pl/web/ml/map/vehicles/1174). Na szczęście ten adres jest w pliku JSON.

### Pobranie rozkładu jazdy na dany dzień

Sprawdzenie rozkładu jazdy dla linii A66 pokazało że jego trasa może przebiegać w różnych wariantach. Na szczęście na stronie ZTM pokazywany jest rozkład wszystkich kursów na dany dzień dla danej linii. Postanowiłem ze będę pobierał codziennie o północy [rozkład na dany dzień ze strony ZTM](https://rj.metropoliaztm.pl/rozklady/przystanek/160056/)

```python
daily_timetable = {}

soup = BeautifulSoup(html, 'html.parser')
section = soup.find('section') 
containers = section.find_all('div', {'class': 'container'})

for entry in containers:
    bus_line = entry.find('a', {'class':'btn btn-danger btn-lg'}) # <a style="min-width:60px;" href="/rozklady/1-106/" class="btn btn-danger btn-lg" title="Zobacz szczegółowy rozkład jazdy dla linii 106">
    bus_no = str(bus_line).split()[-2]
    print(bus_no)

    arrivals =  entry.find_all('div', {'class':'panel-body'}) #  <div class="panel-body rundaycalendar" >
    today_arrivals = arrivals[2].find_all('div', {'class':'arrival-time'})

    bus_arrivals = {}
    for item in today_arrivals:

        txt = re.sub(r'[\ \n]{2,}', '', str(item)) # remowe double spaces and newlines
        no_tags = re.sub("<.*?>", "", txt) # remove HTML tags
        arrival_list = no_tags.split() 
        hour = int(arrival_list[0])
        minutes = arrival_list[1:]
        bus_arrivals[hour] = minutes


    daily_timetable[bus_no] = bus_arrivals
```

Dane o rozkładzie jazdy przetwarzam do słownika o strukturze:

```python
daily_timetable =  {
 '149' : { 8: [15, 30],
           9: [20, 40] }
 '931' : { 18: [25, 45],
           19: [10, 50] }
 }
```

* Sprawdzenie odstępów czasowych w ciągu dnia
* Sprawdzenie opóźnień z zadanym interwałem czasowym (30s)

Porównanie z rzeczywistym rozkładem jazdy

Zapytanie o wszystkie autobusy dla przystanku o

* post_id = 103750 (Przystanek: Mysłowice Katowicka)
* interwale: 00%3A05%3A00 - %3A 00:05:00
