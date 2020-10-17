---
layout: post
title: "Automatyczne zliczanie pasażarów"
categories: Python
author: "Michał"
math: false
---

Przed weekendem wymyśliłem że będę monitorował korki na trasie autobusów miejskich. Zabrałem się za poszukiwania informacji i po godzinie przepadłem na nowym pomyśle: System zliczania pasażerów. 

Aktualizacja 15.10.2020: Dzisiaj weszły nowe wytyczne związane z COVID które mówią że w autobusie nie może być więcej osób niż 50% miejsc siedzących lub 30% miejsc stojących i siedzących. Dodatkowa mobilizacja do wdrożenia pomysłu w życie :)

## System zliczania pasażerów
Źródłem nowego pomysłu było to zamówienie: [Dostawa i montaż systemu zliczania pasażerów](http://bip.metropoliagzm.pl/przetarg/125941/za-270-5-1-2020)

> Podstawowe cechy przedmiotowego zadania to:
>
>a) Rozwiązania bazujące na dotychczasowym wyposażeniu wybranych pojazdów w bramki liczące;
>
>b) Wysoka dokładność danych – dopuszczalny błąd systemu do 3%;
>
>c) Automatyczny dostęp do danych bez konieczności przesyłania plików przez Operatorów;
>
>...
>
>i) Otwartość systemu – możliwość rozszerzania systemu na kolejne pojazdy zgodnie z przyjętym jednolitym standardem w zakresie wyposażenia pojazdów w bramki liczące i używanego oprogramowania do gromadzenia i analizy danych - opracowanie i opisanie interfejsów wymiany danych, jak i sposobu podłączania;

Obecnie chyba każdy autobus ma monitoring, pomyślałem że można by liczyć pasażerów z pomocą *Computer Vision*, a już od jakiegoś czasu chciałem zrealizować projekt w tym obszarze. Byłem ciekaw o ile gorsze będzie rozwiązanie oparte o analizę monitoringu.

 Dla porównania, [przykład klasycznego rozwiązania](http://www.infotron.com.pl/pliki/Infotron%20-%20SZP%20-%20Opis%20v5.pdf). Doszukałem się też artykułu  na temat skuteczności rozwiązań zliczających: [Przydatność automatycznych systemów zliczania pasażerów w celach predykcji popytu na usługi transportowe](http://yadda.icm.edu.pl/yadda/element/bwmeta1.element.baztech-23687bf1-a7cb-49c6-98ab-8689e6aba932/c/TMiR_4_2018_aleksandrowicz.pdf). Co pozwoli mi lepiej oszacować jakość mojego pomysłu.

Jeszcze kilka biznesowych informacji: [Podobny przetarg](https://www.gait.pl/wp-content/uploads/2018/01/zal_9_siwz.pdf) i przykłady komercyjnie dostępnych rozwiązań:

- [Dilax ACP](https://www.dilax.com/en/products/automatic-passenger-counting)
- [IRMA](https://www.iris-sensing.com/products/automatic-passenger-counting/)

Interesowała mnie rynkowa wartość rozwiązania, udało mi się znaleźć [przetarg UM w Poznaniu](https://bip.umww.pl/292---648---k_74---k_231---k_216---przetarg-nieograniczony-pn-doposazenie-autobusow-szynowych), który odpowiadał mniej więcej temu co chcę zrobić. Jego [rozstrzygnięcie](https://www.przetargi.egospodarka.pl/kto-wygral/13907877,emtal-sp-z-o-o.html) opiewało na kwotę 597 800,00 PLN dla 5 pojazdów.



### Plan działania

Wstępny plan: 
- ~~Zdobycie odpowiedniego wideo~~: ✅ (18.09.2020)
- ~~OpenCV - przetwarzanie wideo - instalacja Dlib~~, research teoretyczny: ✅ (22.09.2020)
- Motion Tracking (określenie czy autobus jest w ruchu)
- ~~Rozpoznawanie obiektów na obrazach przy pomocy MobileNet SSD~~: ✅
- Przegląd innych architektur sieci (YOLO?), rozpoznawanie obiektów przy pomocy ich pomocy. Pomiary wydajności/dokładności 
- Tracking obiektów na filmie
- Próba zdobycia większej ilości nagrań
- Porównanie uzyskanego rozwiązania do komercyjnych rozwiązań opisanych w literaturze

### Postęp prac

Pamiętnik projektu :)

### Nagranie z wnętrza autobusu

Zdobycie odpowiedniego wideo było jednym z bardziej pracochłonnych etapów na początku. Po przebrnięciu przez morze filmów z wypadków udało mi się zdobyć dwa nagrania: [YT: CCTV camera in bus super techonogy](https://www.youtube.com/watch?v=MOuPL-dhszQ) oraz [YT: CCTV Manchester HD Vehicle camera](https://www.youtube.com/watch?v=eWZtH96EKZk) Oba są w umiarkowanej jakości, ale przedstawiają dokładnie takie ujęcia o jakie mi chodziło. Do tego są stosunkowo długie i pokazują etap jazdy autobusu. Wstępna selekcja pokazała że łatwiejszym przypadkiem do analizy jest  drugi film.

<img src="{{site.url}}/images/2020_10/cap_CCTV_2.jpg" style="display: block; margin: auto;" />

### Przygotowanie środowiska
Konieczne było doinstalowanie Dlib. Przy okazji dowiedziałem się o istnieniu [opencv_contrib](https://github.com/opencv/opencv_contrib/tree/master/modules). 

### Motion Tracking

Pierwszą rzeczą jaką zrobiłem był prosty *object tracking*, od razu jednak stało się jasne, że będę potrzebował informacji o tym czy autobus się porusza. Bez tego będę zliczał omyłkowo ludzi którzy czekają na przystanku gdy  autobus podjeżdża. Dodatkowo niepotrzebnie tracę czas na próbach wyszukiwania ludzi w kadrze.

Zacząłem od najprostszego rozwiązania które przyszło mi do głowy: monitorowanie czy drzwi są otwarte. Drzwi składają się z dwóch skrzydeł każde z nich ma grubą ramkę, co sprawia że gdy są zamknięte  to wystarczy sprawdzić kolor w określanym miejscu obrazu. 

<img src="{{site.url}}/images/2020_10/door_detection.png" style="display: block; margin: auto;" />

Jeżeli jest czarny, drzwi są zamknięte, każdy inny - otwarte, co oznacza że autobus właśnie zatrzymał się na przystanku. Metoda ta ze względu na swoją prostotę nie jest pozbawiona wad: w monitorowanym miejscu może w tle stać np. czarna latarnia. Można próbować monitorować większa ilość punktów, ale na tym etapie nie miałem takiej potrzeby

Ważniejszym problemem okazały się artefakty kompresji wideo, które powodowały że monitorowanie jednego piksela na obrazie było pozbawione sensu - nie dało określić się jednoznacznego progu przy którym miałbym pewność że drzwi są otwarte. Rozwiązaniem było uśrednienie koloru z większego obszaru:

```python
 img_mask = np.zeros((height, width), np.uint8) # mask
 x_center = 180
 y_center = 15
 radius = 5
 cv2.circle(img_mask,(x_center,y_center),radius,(255,255,255),-1) # measuring area
 a,b,g,r = cv2.mean(frame, mask=img_mask)[::-1]

 if np.mean([b,g,r]) > 10 :
    print([b, g, r])
 cv2.circle(frame, (180,15), 5, (255,0,0), -1)
```





### Object Tracking - teoria

Pierwsze próby wykonałem posiłkując się [Object detection with deep learning and OpenCV](https://www.pyimagesearch.com/2017/09/11/object-detection-with-deep-learning-and-opencv/)  wyniki jednak trochę mnie rozczarowały (analiza statycznych klatek z filmu). 



Kolejnym krokiem będzie  zaprzęgniecie do pracy [TensorFlow Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection)

https://deepdrive.pl/warsztat-wykrywanie-obiektow-w-tensorflow/

https://www.mygreatlearning.com/blog/object-detection-using-tensorflow/

https://deepdrive.pl/10-simple-steps-to-tensorflow-object-detection-api/

https://colab.research.google.com/github/tensorflow/hub/blob/master/examples/colab/tf2_object_detection.ipynb#scrollTo=oi28cqGGFWnY

[Jaki model?](https://github.com/hoya012/deep_learning_object_detection)

---


[OpenCV Object Tracking](https://www.pyimagesearch.com/2018/07/30/opencv-object-tracking/)

[Blob from Image](https://www.pyimagesearch.com/2017/11/06/deep-learning-opencvs-blobfromimage-works/)



[Train an AI to swipe tinder for you](https://medium.com/@joel.barmettler/train-an-ai-to-swipe-tinder-for-you-bc226df8709d)


[Jak uruchomić DETR do wykrywania obiektów?](https://deepdrive.pl/jak-uruchomic-detr-do-wykrywania-obiektow/)

## TBD
