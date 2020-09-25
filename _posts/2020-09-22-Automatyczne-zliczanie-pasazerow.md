---
layout: post
title: "Automatyczne zliczanie pasażarów"
categories: Python
author: "Michał"
math: false
---

Przed weekendem wymyśliłem że bedę monitorował korki na trasie autobusów miejskich. Zabrałem się za research i po godzinie przepadłem na nowym pomyśle. 

## System zliczania pasażerów
Powodem było to zamówienie: [Dostawa i montaż systemu zliczania pasażerów](http://bip.metropoliagzm.pl/przetarg/125941/za-270-5-1-2020)

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


[Przykład rozwiązania](http://www.infotron.com.pl/pliki/Infotron%20-%20SZP%20-%20Opis%20v5.pdf)

[Podobny przetarg](https://www.gait.pl/wp-content/uploads/2018/01/zal_9_siwz.pdf)

Artykuł na temat skuteczności rozwiązan zliczajacych: [Przydatność automatycznych systemów zliczania pasażerów w celach predykcji popytu na usługi transportowe](http://yadda.icm.edu.pl/yadda/element/bwmeta1.element.baztech-23687bf1-a7cb-49c6-98ab-8689e6aba932/c/TMiR_4_2018_aleksandrowicz.pdf)

Przykłady komercyjnie dostępnych rozwiązań:
- [Dilax ACP](https://www.dilax.com/en/products/automatic-passenger-counting)
- [IRMA](https://www.iris-sensing.com/products/automatic-passenger-counting/)

Rynkowa wartość rozwiązania:
- [Przetarg UM w poznaniu](https://bip.umww.pl/292---648---k_74---k_231---k_216---przetarg-nieograniczony-pn-doposazenie-autobusow-szynowych)
- [Rozstrzygnięcie](https://www.przetargi.egospodarka.pl/kto-wygral/13907877,emtal-sp-z-o-o.html) na kwotę 597 800,00 PLN dla 5 pojazdów.

Obecnie chyba każdy autobus ma monitoring, pomyślałem że można by liczyć pasażerów z pomoca Computer Vision.

### Plan działania

Wstępny plan: 
- ~~Zdobycie odpowiedniego wideo~~ (18.09.2020)
- OpenCV - przetwarzanie wideo - instalacja Dlib, research teoretyczny (22.09.2020)
- Rozpoznawanie obiektów na obrazach przy pomocy MobileNet SSD
- Przegląd innych architur sieci (YOLO?), rozpoznawanie obiektów przy pomocy ich pomocy. Pomiary wydajności/dokładności 
- Tracking obiektów na filmie
- Próba zdobycia wiekszej ilości nagrań
- Porównanie uzyskanego rozwiązania do komercyjnych rozwiązań opisanych w literaturze

### Postęp prac

Pamiętnik projektu :)

### Nagranie z wnetrza autobusu

Zdobycie odpowiedniego wideo było jednym z bardziej pracochłonnych etapów na początku: 
- [YT: CCTV camera in bus super techonogy](https://www.youtube.com/watch?v=MOuPL-dhszQ)
- [YT: CCTV Manchester HD Vehicle camera](https://www.youtube.com/watch?v=eWZtH96EKZk)

### Przygotowanie środowiska
Konieczne było doinstalowanie Dlib. Przy okazji dowiedziałem się o istnieniu [opencv_contrib](https://github.com/opencv/opencv_contrib/tree/master/modules).

### Object Tracking - teoria

Pierwsze próby wykonałem posiłkująć się [Object detection with deep learning and OpenCV](https://www.pyimagesearch.com/2017/09/11/object-detection-with-deep-learning-and-opencv/)  wyniki jednak trochę mnie rozczarowały. kolejnym krokime było zaprzegnięcie do pracy [TensorFlow Object Detection API](https://github.com/tensorflow/models/tree/master/research/object_detection)

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
