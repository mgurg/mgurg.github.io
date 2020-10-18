---
layout: post
title: "OpenCV - wprowadzenie"
categories: OpenCV
author: "Michał"
math: true
---

Krótka teoria wprowadzająca do OpenCV oraz rozwiązania prostych projektów (opis + Jupyter notebooki).

# OpenCV - szybkie wprowadzenie

Szybkie wprowadzenie: [Learn X in Y minutes: OpenCV](https://learnxinyminutes.com/docs/opencv/)

### Jupyter Notebook
**[Jupyter Notebook](https://nbviewer.jupyter.org/github/mgurg/mgurg.github.io/blob/master/images/ipnyb/opencv.ipynb)** - uznałem że w przypadku OpenCV wygodniej będzie umieszczać treść w notatnikach Jupytera niż pisać ją na blogu. Dlatego każdy wpis z OpenCV będzie posiadał odnośnik do niego.



### Instalacja
Prosto:
```bash
pip install opencv-contrib-python
```

#### Dlib
Do niektórych zagadnień oprócz OpenCV potrzebna jest biblioteka [Dlib](http://dlib.net/). Instrukcja instalacji dla Ubuntu:

* Utworzenie środowiska z python 3.7: `conda create -n cenv python=3.7.0`
* Instalacja cmake: `conda install cmake`
* Instalacja Dlib : `pip install dlib`
* Sprawdzenie poprawności w IPython:

```python
python
Python 3.7.0 (default, Oct  9 2018, 10:31:47) 
[GCC 7.3.0] :: Anaconda, Inc. on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import dlib
>>> dlib.__version__
'19.21.0'
```

Wczytanie OpenCV, Numpy i Pillow:
```python
# sudo apt install libgl1-mesa-glx
import cv2

import numpy as np
from PIL import Image
```

## Podstawowe operacje

Stworzenie pustego obrazu i wyświetlenie z pomoca biblioteki Pillow: 

```python
img = np.zeros((100,120,3), dtype='uint8')
Image.fromarray(img)
```

[Koordynaty w OpenCV](https://stackoverflow.com/questions/25642532/opencv-pointx-y-represent-column-row-or-row-column) są liczone wg ponizszego układu współrzędnych:

```
0/0---X--->
 |
 |
 Y
 |
 |
 v
 
OpenCV Point(x,y)
```

Wczytanie istniejącego obrazu:
```python
img = cv2.imread('Lenna.png')
Image.fromarray(img)
```

Numpy przechowuje kanały kolorów jako RGB, OpenCV zaś jako BGR

* Matplotlib `pyplot.imshow()`: M x N x 3 image, where last dimension is RGB.
* OpenCV `cv2.imshow()`: M x N x 3 image, where last dimension is BGR

OpenCV zawiera funkcję która pozwala zamienić kolejność kanałów. Można też skorzystać z pomocy Numpy, choć [nie jest to optymalne](https://answers.opencv.org/question/219040/fastest-way-to-convert-bgr-rgb-aka-do-not-use-numpy-magic-tricks/):

```python
img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)  # or numpy:
img = img[...,::-1]  # or:
img = img[:,:,::-1]
```

Wycięcie fragmentu zdjęcia
```python
# img_sliced = img[y1:y2,x1:x2]
img = img[0:200, 0:200, :]
```

Zapisywanie obrazu:
```python
cv2.imwrite('save.jpg', img)
```

### Rysowanie
```python
cv2.line(img, (0,0), (200,200), (255,0,0), 5)
cv2.circle(img, (100,100), 50, (255,0,0), 5)
cv2.rectangle(img, (10,10), (100,100), (255,0,0), 5 )
```

### Tekst
```python
cv2.putText(img, "TXT", (10,190), # lewy dolny pkt
		cv2.FONT_HERSHEY_SIMPLEX, 1.2, (0,255,200))
```

Listę dostępnych fontów można znaleźć w [pliku imgproc.hpp](https://github.com/opencv/opencv/blob/8bf451a3e0c6af796691ee336abd157b762fd6af/modules/imgproc/include/opencv2/imgproc.hpp#L817):

```
    FONT_HERSHEY_SIMPLEX        = 0, //!< normal size sans-serif font
    FONT_HERSHEY_PLAIN          = 1, //!< small size sans-serif font
    FONT_HERSHEY_DUPLEX         = 2, //!< normal size sans-serif font (more complex than FONT_HERSHEY_SIMPLEX)
    FONT_HERSHEY_COMPLEX        = 3, //!< normal size serif font
    FONT_HERSHEY_TRIPLEX        = 4, //!< normal size serif font (more complex than FONT_HERSHEY_COMPLEX)
    FONT_HERSHEY_COMPLEX_SMALL  = 5, //!< smaller version of FONT_HERSHEY_COMPLEX
    FONT_HERSHEY_SCRIPT_SIMPLEX = 6, //!< hand-writing style font
    FONT_HERSHEY_SCRIPT_COMPLEX = 7, //!< more complex variant of FONT_HERSHEY_SCRIPT_SIMPLEX
    FONT_ITALIC                 = 16 //!< flag for italic font
```



### Kolory

* grayscale
* RGB
* HSV (hue, saturation, value) : `cv2.COLOR_BGR2HSV`
* HLS (hue, saturation, lightness): `COLOR_BGR2HLS`

[Why do we use the HSV colour space so often in vision and image processing?](https://dsp.stackexchange.com/questions/2687/why-do-we-use-the-hsv-colour-space-so-often-in-vision-and-image-processing)

[RGB v/s HSV for Computer Vision](https://labs.imaginea.com/rbg-vs-hsv-for-computer-vision/)

[Finding Lane Lines — Simple Pipeline For Lane Detection](https://towardsdatascience.com/finding-lane-lines-simple-pipeline-for-lane-detection-d02b62e7572b)


### Operacje morfologiczne
Bardzo dobry opis operacji morfologicznych znajduje się na polskiej wersji Wikipedii, np:
[Dylatacja](https://pl.wikipedia.org/wiki/Cyfrowe_przetwarzanie_obrazów_binarnych#Dylatacja) i 
[Erozja](https://pl.wikipedia.org/wiki/Cyfrowe_przetwarzanie_obrazów_binarnych#Erozja)

```
opening(A) = dilate(erode(A))
closing(A) = erode(dilate(A))
```

Operatory *tophat* (ekstrakcja obszarów jasnych) i *blackhat* (ekstrakcja obszarów ciemnych) służą odpowiednio ekstrakcji jasnych i ciemnych obszarów w obrazie (poziom jasności określany w odniesieniu do najbliższego otoczenia). 

* tophat(A) = A − open(A)
* blackhat(A) = close(A) − A

### Wykrywanie konturów

```python
contours, hierarchy = cv2.findContours(image, mode, method)
```

Obraz należy sprowadzić do postaci czarno-białej. Kontury są tworzone tylko wokół białych obszarów.
```python
img = cv2.imread('img/rotate/cnt.png')
img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
img = cv2.bitwise_not(img)
```
Tryby wykrywania konturów:

* `CV_RETR_EXTERNAL` - wykrywa jedynie zewnętrzen kontury, wszystkie kontury zagnieżdzone są ignorowane. 

W hierarchii konturów dwie ostatnie koumny mają wrtość -1
```python
[[[ 1 -1 -1 -1]   # kontur 0
  [ 2  0 -1 -1]   # kontur 1
  [-1  1 -1 -1]]]  # kontur 2
```

* `CV_RETR_LIST` - zwraca wszystkie kontury, ale nie określa hierarchii między nimi . Należy stosować jeżeli nie potrzebujemy informacjii o tym czy któryś kontur jest zagnieżdzony wewnątrz kolejnego.

Ponieważ tym razem również nie liczymy hierarchii konturów, to dwie ostatnie koumny mają wrtość -1 Kolumny "Next" i "Previous" mają wymagane wartości

```python
[[[ 1 -1 -1 -1]  # kontur 0
  [ 2  0 -1 -1]  # kontur 1
  [ 3  1 -1 -1]  # kontur 2
  [ 4  2 -1 -1]  # ...
  [ 5  3 -1 -1]
  [ 6  4 -1 -1]
  [ 7  5 -1 -1]
  [-1  6 -1 -1]]]

```

* `CV_RETR_CCOMP`  - znajduje kontury i organizuje je w kontury wewnetrzne i zewnetrzne. gives contours and organises them into outer and inner contours. Every contour is either the outline of an object, or the outline of an object inside another object (i.e. hole). The hierarchy is adjusted accordingly. This can be useful if (say) you want to find all holes.

This flag retrieves all the contours and arranges them to a 2-level hierarchy. ie external contours of the object (ie its boundary) are placed in hierarchy-1. And the contours of holes inside object (if any) is placed in hierarchy-2. If any object inside it, its contour is placed again in hierarchy-1 only. And its hole in hierarchy-2 and so on.

```
[[[ 1 -1 -1 -1]
  [ 2  0 -1 -1]
  [ 4  1  3 -1]
  [-1 -1 -1  2]
  [ 6  2  5 -1]
  [-1 -1 -1  4]
  [ 7  4 -1 -1]
  [-1  6 -1 -1]]]

```

[OpenCV Countours: Dice](https://nbviewer.jupyter.org/github/arnavdutta/OpenCV-Contours-Hierarchy/blob/master/OpenCV_Contours.ipynb)

* `CV_RETR_TREE` - wylicza pełną hierarchę konturów. Widać np. ze kontur 4 jest zagnieżdzony o 4 poziomy względem konturu zerowego.

```python
[[[ 6 -1  1 -1]    # kontur 0 - równy 6, rodzic 1
  [-1 -1  2  0]    # kontur 1 - rodzic 2, dziecko 0
  [-1 -1  3  1]    # kontur 2 - rodzic 3, dziecko 1
  [-1 -1  4  2]    # kontur 3 - rodzic konturu 4, dziecko 2 
  [ 5 -1 -1  3]    # kontur 4 - dziecko konturu 3 (4-ty poziom zagnieżdzenia)
  [-1  4 -1  3]    # kontur 5 - dziecko konturu 3
  [ 7  0 -1 -1]    # kontur 6 - równy 7, bezdzietny
  [-1  6 -1 -1]]]  # kontur 7 - bezdzietny

```


Hierarchia obiektów:

```
[Next (same level), Previous (same level), First_Child, Parent]
[Next (same level), Previous (same level), First_Child, Parent]
[Next (same level), Previous (same level), First_Child, Parent]
...
```
* "Next" oznacza następny kontur na tym samym poziomie hierarchii.
* "Previous" oznacza poprzedni kontur na tym samym poziomie hierarchii.
* "First_Child" oznacza jego pierwszy kontur dziecka.
* "Parent" oznacza indeks konturu rodzica.


## Projekty

### Rotate images
[Rotate Images](https://www.pyimagesearch.com/2017/01/02/rotate-images-correctly-with-opencv-and-python/)

Tematy które poznałem przy okzaji tego ćwiczenia: 
* findContours  - ([RetrievalModes](https://docs.opencv.org/master/d3/dc0/group__imgproc__shape.html#ga819779b9857cc2f8601e6526a3a5bc71))
* drawCountours - podanie *1* jako grubość linii powoduje wypełnienie obiektu
* Konwersja `cv2.COLOR_BGR2RGB` i `cv2.COLOR_BGR2GRAY` to nie to samo co wyłącznie `cv2.COLOR_BGR2GRAY`

[Building a Pokedex in Python: Finding the Game Boy Screen](https://www.pyimagesearch.com/2014/04/21/building-pokedex-python-finding-game-boy-screen-step-4-6/)

[Contours Hierarchy](https://docs.opencv.org/master/d9/d8b/tutorial_py_contours_hierarchy.html)

### Text skew correction
[Text skew correction](https://www.pyimagesearch.com/2017/02/20/text-skew-correction-opencv-python/)

Lista tematów które poznałem przy okazji tego ćwiczenia:
* Układy współrzędnych w OpenCV
* Treshold
* BoundingBox


### Watermarking Images
[Watermarking images](https://www.pyimagesearch.com/2016/04/25/watermarking-images-with-opencv-and-python/)

* Tryby wczytywania obrazów [ImreadModes](https://docs.opencv.org/3.4/d4/da8/group__imgcodecs.html#ga61d9b0126a3e57d9277ac48327799c80)

### Determining object color
[Determining object color](https://www.pyimagesearch.com/2016/02/15/determining-object-color-with-opencv/)

### Shape detection
[Shape detection](https://www.pyimagesearch.com/2016/02/08/opencv-shape-detection/)

### Center of contour
[Center of contour](https://www.pyimagesearch.com/2016/02/01/opencv-center-of-contour/)

### Recognizing LCD digits
[Recognizing LCD Digits](https://www.pyimagesearch.com/2017/02/13/recognizing-digits-with-opencv-and-python/)

### Determining object color
[Determining object color](https://www.pyimagesearch.com/2016/02/15/determining-object-color-with-opencv/)

### Panorama stitching
[panorama stitching](https://www.pyimagesearch.com/2016/01/11/opencv-panorama-stitching/)

### Foreground Segmentation and Extraction
[OpenCV GrabCut: Foreground Segmentation and Extraction](https://www.pyimagesearch.com/2020/07/27/opencv-grabcut-foreground-segmentation-and-extraction/)






