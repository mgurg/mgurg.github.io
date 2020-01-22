---
layout: post
title: "Web scrapping otomoto"
categories: python
author: "Michał"
---


## Jak dobrze sprzedać auto?

Jestem na etapie sprzedaży używanego samochodu, uznałem że będzie to dobry pretekst do tego żeby lepiej poznać Pythona w zastosowaniach pobierania danych ze stron WWW, analizy danych i wizualizacji.

## Dlaczego otomoto?
Ponieważ jest to chyba największy serwis z ogłoszeniami motoryzacyjnymi. Alternatywą jest OLX i Allegro, ale szybki (i niezbyt dokładny) research pokazał mi że na OLX są w większości również ogłoszenia z otomoto. Samych ogłoszeń publikowanych na OLX jest niezbyt wiele. Przynajmniej jeżeli chodzi o samochód Toyota Yaris II.

Uznałem że na początku prościej będzie parsować stronę gdzie wszystkie ogłoszenia mają taką samą formę. Jeżeli nie napotkam większych problemów to w przyszłości może również pokuszę się o dodanie ogłoszeń z OLX.

Trzecim miejscem wartym uwagi jest Allegro, ale tam ogłoszeń jest niewiele, co zniechęciło mnie do tego serwisu. Zależy mi na jak największej ilości danych żeby móc wyciągnąć lepsze wnioski.

## Pobieranie treści ogłoszeń
Z racji braku doświadczenia w temacie web scrappingu i podstawowej znajomości Pythona rozpocząłem od poszukiwania gotowych recept. Udało mi się znaleźć gotowy [skrypt do pobierania danych z otomoto](https://bananovitch.github.io/blog/2018/09/19/python-car-scraper.html),  który po drobnych modyfikacjach mogłem wykorzystać do swoich celów.

Pierwsze próby przeprowadzałem jesienią 2019 i byłem zadowolony z efektów. Niestety gdy wróciłem do tematu w grudniu 2019 to okazało się że otomoto mimo iż nie zmieniło się wizualnie to zrobiło rewolucję jeżeli chodzi o generowanie swoich stron WWW. Zamiast statycznego HTML strona zaczęła być generowana dynamicznie z wykorzystaniem JS.

Próba pobrania zawartości strony przy pomocy dotychczasowego kodu przestałą działać.

```python
import requests, bs4, io, csv, datetime

path = 'https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/'
res = requests.get(path)
res.raise_for_status()

#check how many pages are there
carSoup = bs4.BeautifulSoup(res.text, features="lxml")
lastPage = int(carSoup.select('.page')[-1].text)
```
W wyniku działania powyższego skryptu otrzymywałem część strony z której mogłem wyczytać jedynie liczę stron z ogłoszeniami. W danych brakowało listingu ofert. 

W nowej wersji serwisu ogłoszenia są zapisane jako JSON, który był obcinany przez bibliotekę *Requests*.
Z 32 ogłoszeń które były na stronie dostępnych było 8.
```javascript
if (c.indexOf(name) == 0) {
    return c.substring(name.length, c...eFromOdometer":{"@type":"QuantitativeValue","unitCode":"KMT","value":"133120"}}},{"@type":"Offer","priceSpecification":{"@type":"UnitPriceSpecification","price":"11600","priceCurrency":"PLN"},"itemOffered":{"@type":"Car","name":"Toyota Yaris","vehicleConfiguration":"Toyota Yaris 2009 rok, benzyna, klimatyzacja, 2x opony, \u015bliczna Toyota","brand":"Toyota","fuelType":"Benzyna","modelDate":"2009","mileageFromOdometer":{"@type":"QuantitativeValue","unitCode":"KMT","value":"150000"}}}]}}    </script>
```
Źródło tego samego fragmentu strony wyświetlane w przeglądarce wyglądało jak poniżej:

```javascript
<script type="application/ld+json"> {"@context":"http:\/\/schema.org","@type":"Webpage","url":"https:\/\/www.otomoto.pl\/osobowe\/toyota\/yaris\/ii-2005-2011\/","mainEntity":{"@type":"OfferCatalog","name":"Otomoto","itemListElement":[{"@type":"Offer","priceSpecification":{"@type":"UnitPriceSpecification","price":"14900","priceCurrency":"PLN"},"itemOffered"
```

## Jak pobrać ogłoszenia
Skoro dotychczasowy sposób przestał działać to musiałem mocno zastanowić się nad tym w jaki sposób pozyskać dane z ogłoszeń. Rozwiązanie z biblioteką *Requests* uważałem za najbardziej eleganckie i proste i to przy nim chciałem pozostać. Lista pomysłów jakie przyszły mi do głowy:

 Pozostać przy *requests* ale sprawdzić inne ustawienia. Jeżeli przepuściłem odpowiedź serwera przez [BeautifulSoup](https://lxml.de/elementsoup.html) to byłem w stanie odzyskać nieco danych (przykład z kodem powyżej). Zacząłem więc eksperymentować z różnymi [parserami](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#installing-a-parser). Niestety okazało się że nic więc co dawało mi *lxml* nie jestem w stanie uzyskać.

### To może OLX?
Skoro na olx i tak są ogłoszenia z otomoto, to może prościej będzie się dobrać do danych od tej strony? Dodatkowo przykładając się do pracy od początku będę miał nie tylko ogłoszenia z otomoto, ale również z OLX co otwiera drogę do tworzenia ciekawszych analiz. Szybki przegląd ogłoszeń wskazywał na to że ogłoszenia z tekstem **CID5** w linku do oferty pochodzą z OLX, pozostałe są z otomoto.

Niestety, próba pobrania treści strony dawała taki sam wynik jak w przypadku otomoto: brak listingu ogłoszeń w pliku HTML

### A co z RSS?

Poszukiwania danych dostępnych w strawnej formie zaprowadziły mnie do treści RSS. Początkowa dane te wyglądały nawet lepiej niż te które zamierzałem pobierać w wyniku przetwarzania całej strony. Oprócz podstawowych danych miałem dostęp do całej bazy dodatkowych informacji + fragment treści ogłoszenia.


```xml
<item>
   <title>Toyota Yaris Model po Liftingu, bardzo zadbany, silnik 1,33 benzyna 101KM</title>
   <link>https://www.otomoto.pl/oferta/toyota-yaris-model-po-liftingu-bardzo-zadbany-silnik-1-33-benzyna-101km-ID6CBUEt.html</link>
   <guid>https://www.otomoto.pl/oferta/toyota-yaris-model-po-liftingu-bardzo-zadbany-silnik-1-33-benzyna-101km-ID6CBUEt.html</guid>
   <pubDate>Wed, 01 Jan 2020 19:20:18 CET</pubDate>
   <description><![CDATA[Marka pojazdu: Toyota, Model pojazdu: Yaris, Wersja: II (2005-2011), Rok produkcji: 2009 , Przebieg: 124 000 km, Pojemność skokowa: 1 300 cm3, Rodzaj paliwa: Benzyna, Moc: 101 KM, Skrzynia biegów: Manualna, Napęd: Na przednie koła, Filtr cząstek stałych: Nie, Uszkodzony: Nie, Typ: Auta miejskie, Liczba drzwi: 5 , Liczba miejsc: 5 , Kolor: Srebrny, Metalik: Tak, Perłowy: Nie, Matowy: Nie, Akryl (niemetalizowany): Nie, Dodatkowe wyposażenie: ABS, Centralny zamek, Elektryczne szyby przednie, Elektrycznie ustawiane lusterka, Immobilizer, Poduszka powietrzna kierowcy, Poduszka powietrzna pasażera, Radio fabryczne, Wspomaganie kierownicy, ESP (stabilizacja toru jazdy), Gniazdo AUX, Gniazdo USB, Isofix, Klimatyzacja manualna, Komputer pokładowy, MP3, Poduszka powietrzna chroniąca kolana, Poduszki boczne przednie, Poduszki boczne tylne, Tapicerka welurowa, Kierownica po prawej (Anglik): Nie, Cena: 19 700 PLN, VAT marża: Nie, Możliwość finansowania: Nie, Faktura VAT: Nie, Leasing: Nie, Kraj pochodzenia: Niemcy, Zarejestrowany w Polsce: Nie, Pierwszy właściciel: Nie, Bezwypadkowy: Nie, Serwisowany w ASO: Nie, Zarejestrowany jako zabytek: Nie, Tuning: Nie, Homologacja ciężarowa: Nie, <br/>Witam, Mam do sprzedania  Toyote  Yaris z roku 2009 po Liftingu  z niezawodnym silnikiem benzynowym na łańcuszku rozrządu o pojemności  1,33cm3,  101KM z  sześciobiegową skrzynia biegów. Auto  jest w bardzo dobrym stanie technicznym i wizualnym , czyste i zadbane. Samochód  na bieżąco serwisowany, p... <a href="https://www.otomoto.pl/oferta/toyota-yaris-model-po-liftingu-bardzo-zadbany-silnik-1-33-benzyna-101km-ID6CBUEt.html">https://www.otomoto.pl/oferta/toyota-yaris-model-po-liftingu-bardzo-zadbany-silnik-1-33-benzyna-101km-ID6CBUEt.html</a><img src="https://apollo-ireland.akamaized.net/v1/files/eyJmbiI6ImRleHIwYTZhZzl4bjItT1RPTU9UT1BMIiwidyI6W3siZm4iOiJ3ZzRnbnFwNnkxZi1PVE9NT1RPUEwiLCJzIjoiMTYiLCJwIjoiMTAsLTEwIiwiYSI6IjAifV19.EB9TsnzfR5SZBj0qRS3RLReQkYuTmTTKaGa02ySA-3Y/image;s=732x488;cars_;/936362001_;slot=1;filename=eyJmbiI6ImRleHIwYTZhZzl4bjItT1RPTU9UT1BMIiwidyI6W3siZm4iOiJ3ZzRnbnFwNnkxZi1PVE9NT1RPUEwiLCJzIjoiMTYiLCJwIjoiMTAsLTEwIiwiYSI6IjAifV19.EB9TsnzfR5SZBj0qRS3RLReQkYuTmTTKaGa02ySA-3Y_rev001.jpg">]]></description>
   <category>Osobowe</category>
</item>
```

Niestety szybko okazało się że mając tylko te dane ciężko będzie pozyskiwać mi kluczowe dla mnie informacje: jak długo ogłoszenie jest aktywne, oraz jak zmienia się cena ustawiona przez sprzedającego. RSS informuje wyłącznie o nowych wpisach n stronie. Chcąc śledzić każde ogłoszenie musiałbym codziennie parsować strony sprzedaży każdego samochodu z osobna.

### Jeżeli nie *Requests*, to co?

W ten sposób wróciłem do początku, musiałem wymyślić jakikolwiek sposób pobierania stron z otomoto, który dawałby mi pełny HTML razem z ogłoszeniami. Przegląd internetu pokazał że są dostępne jeszcze dwa rozwiązania mojego problemu:
- Selenium
- Scrapy

Zacząłem od Selenium które na podstawi opisów wydawało się ciekawszym i pewniejszym rozwiązaniem. Jest to narzędzie do automatyzacji testów web aplikacji, ale skoro można wywołać go z poziomu Pythona to uznałem że powinno się nadać. Jego zaletą jest to że korzysta z silników rzeczywistych przeglądarek, które renderują stronę WWW. 

```python
from selenium import webdriver

driver = webdriver.Firefox()
driver.set_window_size(1120, 550)
driver.get("https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/")

a = driver.page_source

print(driver.page_source)
driver.quit()
```

Niestety wyniki które uzyskałem nie różniły się od tego co dawało *Requests* Być może to kwestia konfiguracji lub słabej znajomości tego narzędzia. Oprócz tego że nie potrafiłem uzyskać interesujących mnie danych to cały proces był powolny, a w jego trakcie otwierało się nowe okno przeglądarki. Tę ostatnią niedogodność próbowałem obejść( jak się okazało niewspieranym już) PhantomJS.

Ostatnim sposobem jaki mi pozostał było wykorzystanie Scrapy. Mimo iż tutaj również na początku miałem wiele problemów z konfiguracją to ostatecznie udało mi się pobrać treść którą potrzebowałem. O samym Scrapy i jego konfiguracji powstanie osobny wpis.
