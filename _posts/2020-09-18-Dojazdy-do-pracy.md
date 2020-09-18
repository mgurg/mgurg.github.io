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
Monitorowanie ruchu na drodze - pierwszym pomysłem była próba wyciągniecia danych z map TomTom lub Google, na szczeście zanim zdązylem otworzyć okno przegladarki przypomniałem sobie o górnośląskim ZTM (dawniej KZK GOP) które około rok temu zaczeło stawiać tablicę z dynamiczną informacją o kursowaniu autobusów. 

Okazało się że w ramach tego systemu istnieje też strona www z której z drobnymi trudościami można wyciagnąć informacje o opóznieniu.

### Plan działania
* Znalezienie wszystkich linii autobusowych do monitorowania
* Wyznaczenie przystanków do monitorowania
* Pobranie rozkładów jazdy i zapisanie w BD
* Pobrannie danych o opóżnieniu
  * Parsowanie danych, zapis do BD
  * Analiza i Wizualizacja
 * Wnioski

## TBD
Monitorowanie opóznień autobusów na linii powrotu z pracy.

[http://sdip.metropoliaztm.pl/web/ml/map/](http://sdip.metropoliaztm.pl/web/ml/map/)

Autobusy do monitorowania:

77,
149,
66,

- Pobranie rozkladu jzady na dany dzień
- Sprawdzenie odstępów czasowych w ciagu dnia
- Sprawdzenie opóżnien z zadanym interwałem czasowym (30s)

```html
<input type='hidden' name='csrfmiddlewaretoken' value='' /></div>
```
Porównanie z rzeczywistym rozkładem jazdy

Zapytanie o wszystkie autobusy dla przystanku o 
* post_id = 103782 
* intervale: 00%3A05%3A00 - %3A 00:05:00

https://stackoverflow.com/questions/29898348/how-do-you-interpret-this-url-time-format //

http://sdip.metropoliaztm.pl/web/map/vehicles/gj/A?interval=00%3A05%3A00&post_id=103782