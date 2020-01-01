---
layout: post
title: "Web scrapping otomot"
categories: python
author: "Michał"
---


### Jak dobrze sprzedać auto?

Jestem na etapie sprzedaży używanego samochodu, uznałem że będzie to dobry pretekst do tego żeby lepiej poznać Pythona w zastosowaniach pobierania danych ze stron WWW, analizy danych i wizualizacji.

### Dlaczego otomoto?
Ponieważ jest to chyba największy serwis z ogłoszeniami motoryzacyjnymi. Alternatywą jest OLX i Allegro, ale szybki (i niezbyt dokładny) research pokazał mi że na OLX są w większości również ogłoszenia z otomoto. Samych ogłoszeń publikowanych na OLX jest niezbyt wiele. Przynajmniej jeżeli chodzi o samochód Toyota Yaris II.

Uznałem że na początku prościej będzie parsować stronę gdzie wszystkie ogłoszenia mają taką samą formę. Jeżeli nie napotkam większych problemów to w przyszłości może również pokuszę się o dodanie ogłoszeń z OLX.

Trzecim miejscem wartym uwagi jest Allegro, ale tam ogłoszeń jest niewiele, co zniechęciło mnie do tego serwisu. Zależy mi na jak największej ilości danych żeby móc wyciągnąć lepsze wnioski.

### Pobieranie treści ogłoszeń
Z racji braku doświadczenia w temacie web scrappingu i podstawowej znajomości Pythona rozpocząłem od poszukiwania gotowych recept. Udało mi się znaleźć gotowy [skrypt do pobierania danych z otomoto](https://bananovitch.github.io/blog/2018/09/19/python-car-scraper.html),  który po drobnych modyfikacjach mogłem wykorzystać do swoich celów.

Pierwsze próby przeprowadzałem jesienią 2019 i byłem zadowolony z efektów. Niestety gdy wróciłem do tematu w grudniu 2019 to okazało się że otomoto mimo iż nie zmieniło się wizualnie to zrobiło rewolucję jeżeli chodzi o generowanie swoich stron www. Zamiast statycznego HTML storna zaczęła być generowana dynamicznie z wykorzystaniem JS.

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

W nowej wersji serwisu ogłoszenia są zapisane jako JSON, który był obcinany przez bibliotekę *Requests*
Z 32 ogłoszeń które były na stronie dostępnych było 8.
```javascript
                if (c.indexOf(name) == 0) {
                    return c.substring(name.length, c...eFromOdometer":{"@type":"QuantitativeValue","unitCode":"KMT","value":"133120"}}},{"@type":"Offer","priceSpecification":{"@type":"UnitPriceSpecification","price":"11600","priceCurrency":"PLN"},"itemOffered":{"@type":"Car","name":"Toyota Yaris","vehicleConfiguration":"Toyota Yaris 2009 rok, benzyna, klimatyzacja, 2x opony, \u015bliczna Toyota","brand":"Toyota","fuelType":"Benzyna","modelDate":"2009","mileageFromOdometer":{"@type":"QuantitativeValue","unitCode":"KMT","value":"150000"}}}]}}    </script>
```
Źródło tego samego fragmentu strony wyświetlane w przeglądarce wyglądało jak poniżej:

```javascript
  <script type="application/ld+json"> {"@context":"http:\/\/schema.org","@type":"Webpage","url":"https:\/\/www.otomoto.pl\/osobowe\/toyota\/yaris\/ii-2005-2011\/","mainEntity":{"@type":"OfferCatalog","name":"Otomoto","itemListElement":[{"@type":"Offer","priceSpecification":{"@type":"UnitPriceSpecification","price":"14900","priceCurrency":"PLN"},"itemOffered"
  ```








