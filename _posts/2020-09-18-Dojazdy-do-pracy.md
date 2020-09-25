---
layout: post
title: "Kiedy dojadę?"
categories: Python
author: "Michał"
math: false
---

Dalekie dojazdy do pracy (która odbywa się w godzinach 9-17) spowodowały że zacząłem zastanawiać się jak zoptymalizwać powroty do domu. 
Problem: wrócić z Gliwc do Mysłowic w jak najkrótszym czasie.
Rozwiązanie: Monitorowanie opóżnien miejskich autobusów żeby wyznaczyć optymalne okno czasowe na powrót do domu. 

## Początkowe pomysły
Monitorowanie ruchu na drodze - pierwszym pomysłem była próba wyciągniecia danych z map TomTom lub Google, na szczeście zanim zdążyłem otworzyć okno przegladarki przypomniałem sobie o górnośląskim ZTM (dawniej KZK GOP) które około rok temu zaczeło stawiać tablicę z dynamiczną informacją o kursowaniu autobusów. 

Okazało się że w ramach tego systemu istnieje też strona [System Dynamicznej Informacji Pasażerskiej - Portal Pasażera](http://sdip.metropoliaztm.pl/web/ml/map/) z której można wyciagnąć informacje o opóznieniu autobusów.

### Plan działania
* ~~Znalezienie wszystkich linii autobusowych do monitorowania~~
* Wyznaczenie przystanków do monitorowania
* Pobranie rozkładów jazdy i zapisanie w BD
* Pobrannie danych o opóżnieniu
  * Parsowanie danych, zapis do BD
  * Analiza i Wizualizacja
 * Wnioski

## Monitorowane przystanki i linie autobusowe
Najbardziej interesuje mnie odcinek od Katowic do Mysłowic, ze szczególnym uwzględnieniem węzła Bagienna (rejon Wilhelminy) 

Autobusy do monitorowania: 66,76,77,149

Żeby zmniejszyć liczbę generowanych zapytań będę pobierał dane o wszystkich liniach zatrzymujących się na przystanku Mysłowice Katowicka ([URL](http://sdip.metropoliaztm.pl/web/map/vehicles/gj/A?interval=00%3A05%3A00&post_id=103750))

Z tego przystanku odjezdzają autobusy linii: 35, 44, 66, 77, 77N, 106, 149, 219, 292, 536, 788, 931, 995. Wstępnie do monitorowania wybrałem trzy linie, jadące od strony Katowic:
- 66 (line_id:[68](http://sdip.metropoliaztm.pl/web/ml/line/68)),
- 77 (line_id:[79](http://sdip.metropoliaztm.pl/web/ml/line/73)),
- 149 (line_id:[79](http://sdip.metropoliaztm.pl/web/ml/line/79))
- 931 (line_id:[97](http://sdip.metropoliaztm.pl/web/ml/line/79))

### Pobranie rozkladu jazdy na dany dzień

Sprawdzenie rozkładu jazdy dla lini A66 pokazało że jego trasa moze przebiegać w różnych wariantach. Naa szczęście na srtronie ZTM pokazywany jesst rozkład wszystkich kursów na dany dzień dla danej linii. Poostanowiłem ze bede pobierał codziennie o północy [rozkład na dany dzień ze strony ZTM](https://rj.metropoliaztm.pl/rozklady/przystanek/160056/)

- Sprawdzenie odstępów czasowych w ciagu dnia
- Sprawdzenie opóżnien z zadanym interwałem czasowym (30s)


Porównanie z rzeczywistym rozkładem jazdy

Zapytanie o wszystkie autobusy dla przystanku o 
* post_id = 103750 (Przystanek: Mysłowice Katowicka)
* interwale: 00%3A05%3A00 - %3A 00:05:00


Informacje o wszystkich autobusach 

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