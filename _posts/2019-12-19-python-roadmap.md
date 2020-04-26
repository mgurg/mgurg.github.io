---
layout: post
title: "Roadmap 🛣️"
categories: python
author: "Michał"
---



Poniżej to co faktycznie się nauczyłem do tej pory :) :

**[26-04-2020]**

`Flask` - udało mi się szybko zbudować działający serwis który jest całkiem przyjazny dla oka (zasługa Boostrap). Dużo jest jeszcze do dopracowania, ale jako szybki POC sprawdza się znakomicie. Wszystko działa już na serwerze, dane się pobierają, a wykresy generują bez mojego udziału. 

**TODO:** ataki *CSRF*, walidacja danych w formularzach, nadrobienie braków w konfiguracji NGNIX 

`YAML` - potrzebowałem przerzucić cześć danych konfiguracyjnych do osobnego pliku. Myślałem żeby zrobić to jako JSON (i skreślić jeden punkt TODO), ale okazało się że możliwości są znacznie szersze. Wybrałem YAML, bardziej z ciekawości i chęci zabawy niż z jakiegoś racjonalnego względu.

`Wizualizacja` - udało się wygrać z serwerem VPS, mogę już rysować wykresy przy pomocy `matplotlib` Na razie tworzę najprostsze grafiki, mam za sobą testy z Bokeh. Będę chciał spróbować Plotly.





**[10-04-2020]**

`Scrapy` - Przy okazji wgryzania się mocniej w scrapy wyszło że powinienem przyłożyć się do programowanie obiektowego. Stąd poniższa lista rzeczy do nadrobienia.

**TODO:** Objects, Classes, `__Init__`, Class variables, Methods, inheritance

`Regex`  - poznałem podstawową składnię przy okazji wyciągania danych w formacie JSON





**[30-03-2020]**

Robienie własnego projektu który nie jest kalkulatorem ani  TODO listą związanego z przepisywaniem kodu który ktoś wymyślił sprawie że pojawia się masa rzeczy których trzeba się nauczyć choćby w minimalnym stopniu żeby zrobić to co się wymyśliło. Lista tematów które doszły od ostatniego zestawienia:

`SQL` - Zapis ogłoszeń do plików do `csv` i obróbka ich przy pomocy `pandas` była dobrym pomysłem na start. Skoro jednak mam działający kod dla najprostszego przypadku to  postanowiłem go rozwinąć w coś bardziej użytecznego. Parsowane treści wszystkich ogłoszeń sprawiło że mam znacznie więcej danych, uznałem że wygodniej będzie trzymać je wszystkie w bazie `SQLite`. To wymagało ode mnie nauki podstaw SQL. Na razie mam opanowane elementarne zapytania dla tworzenia tabel, dodawania, odpytywania i łącznia danych

**TODO:** ~~Bardziej zawansowane zapytania~~, przyjrzenie się problemowi SQL Injection (rozwiązanie: ORM SqlAlchemy?)



`JSON` - Jednym formatem wymiany danych który kojarzyłem był XML. Z racji tego że otomoto trzyma w kodzie strony część informacji w formacie JSON to poznałem jego podstawy z wykorzystaniem biblioteki `json` . Potrafię "spłaszczyć" odczytane dane, tak żeby móc dalej nimi manipulować

**TODO**: Zapisywanie danych w tym formacie



`Scrapy` - Tworzenie kodu pająka nadal jest dla mnie nieintuicyjne, ale potrafię przekazać do parsowania podstrony serwisu pozyskane w wyniku parsowania innej strony. Dodatkowo w  trakcie pozbywam się od razu tej części pliku HTML która nie jest mi potrzebna tak żeby oszczędzać czas i miejsce na dysku.

 **TODO**: ~~Limitowanie ilości zapytań przy pobieraniu większej ilości podstron~~



**[22-01-2020]**

`Markdown` - wybór bloga opartego o Jekyll prowadzonego na  Github pages sprawił że musiałem nauczyć się formatować tekst w podstawowym stopniu z wykorzystaniem *markdown*. Do tej pory opanowałem podstawowe rzeczy: nagłówki, wypunktowania, linki blok kodu. Stworzenie podstawowego pliku *Readme.md* nie jest już wyzwaniem. Co do edytorów przeszedłem drogę od Notepad++ (brak podglądu formatowania) przez webowy [StackEdit](https://stackedit.io/) (wady: na każdym komputerze inne pliki zapisane w cache, psuje nagłówek postów) po aplikację [Typora](https://www.typora.io/)  (wady: na razie brak)

**TODO**: ~~tabele~~, ~~osadzanie grafik~~



`Git` - na razie w bardzo podstawowym stopniu. Bardziej obsługa Githuba niż git. Rozumiem [zasadę działania repozytorium](https://github.com/tomcl/HowToUseGitTJWC), ale moja interakcja ogranicza się na razie do push/pull w Github desktop.

**TODO**: ~~przerobić cały workflow z kursu Gita~~



`Linux` - do tej pory: elementarne podstawy. Logowanie po SSH, poruszanie się po systemie z wykorzystaniem wyłącznie tekstowej konsoli

**TODO**: Prawa dostępu do plików, ~~dowiązania symboliczne~~



---



Ogólna lista (zrobiona przed rozpoczęciem nauki - na podstawie harmonogramu CodersLab):


 - Python podstawy

    - Podstawowe struktury sterujące: pętle
    - Instrukcje warunkowe if - elseif - else
    - Blok kodu i instrukcja pass
    - Funkcje, zwracane wartości, parametry funkcji
    - "Magiczne" parametry funkcji: *args i **kwargs
    - Listy i krotki: nawigowanie po kolekcjach
    - Modyfikowanie list
    - Krotki i różnice miedzy krotką a listą
    - [Lista składana (list comprehension)](https://mgurg.github.io/python/2019/12/23/python-list-comprehension.html)
    - Słowniki
    - Instrukcja "for in" oraz operator "in"
    - [Napisy (string), napis jako kolekcja](https://mgurg.github.io/python/2020/03/29/Python-string.html)
    - Moduły, importowanie modułów, nazw
    - Tworzenie własnych modułów, pakiety, biblioteki
    - [Virtualenv](https://mgurg.github.io/python/2020/01/16/SSH-VPS-konfiguracja-serwera.html#virtual-environment)
    - Obsługa wyjątków
    - Dekoratory
      Protokół HTTP, metody GET i POST
    - [Flask - prosty framework HTTP](https://mgurg.github.io/python/2020/04/21/Flask.html)
    - Metody HTTP we Flasku, odbieranie parametrów
 - Python zaawansownay

    - Pojęcie metody, klasy i właściwości
   2. Hermetyzacja
   3. Dziedziczenie
   4. Konstruktor, inicjalizator
   5. Nadpisywanie metod i właściwości
   6. Metody statyczne i metody klasowe
   7. Dynamiczne własności
   8. Iteratory
   9. Generatory
   10. Wyrażenie generowane (generator expression)
   11. [Wyrażenie regularne](https://mgurg.github.io/python/2020/04/12/Python-Regex.html)
 - Numpy 
 - Pandas 
 - MatplotLib