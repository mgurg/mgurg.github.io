---
layout: post
title: "OpenCV - podstawy"
categories: OpenCV
author: "Michał"
math: true
---

Krótka teoria wprowadzająca do OpenCV oraz rozwiązania prostych projektów (opis + Jupyter notebooki).

# OpenCV - szybkie wprowadzenie

Szybkie wprowadzenie: [Learn X in Y minutes: OpenCV](https://learnxinyminutes.com/docs/opencv/)

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

Wczytanie istniejącego obrazu:
```python
img = cv2.imread('Lenna.png')
Image.fromarray(img)
```

Numpy przechowuje kanały kolorów jako RGB, OpenCV zaś jako BGR

* Matplotlib pyplot.imshow(): M x N x 3 image, where last dimension is RGB.
* OpenCV cv2.imshow(): M x N x 3 image, where last dimension is BGR

OpenCV zawiera funkcję która pozwala przekonwertować kanały. Można też skorzystać z pomocy Numpy, choć [nie jest to optymalne](https://answers.opencv.org/question/219040/fastest-way-to-convert-bgr-rgb-aka-do-not-use-numpy-magic-tricks/):

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
```
cv2.putText(img, "TXT", (10,190), # lewy dolny pkt
		cv2.FONT_HERSHEY_SIMPLEX, 1.2, (0,255,200))
```

### Kolory

* grayscale
* RGB
* HSV (hue, saturation, value) : `cv2.COLOR_BGR2HSV`
* HLS (hue, saturation, lightness): `COLOR_BGR2HLS`

[Finding Lane Lines — Simple Pipeline For Lane Detection](https://towardsdatascience.com/finding-lane-lines-simple-pipeline-for-lane-detection-d02b62e7572b)


### Operacje morfologiczne
Bardzo dobry opis operacji morfologicznych znajduje się na polskiej wersji Wikipedi, np:
[Dylatacja](https://pl.wikipedia.org/wiki/Cyfrowe_przetwarzanie_obrazów_binarnych#Dylatacja) i 
[Erozja](https://pl.wikipedia.org/wiki/Cyfrowe_przetwarzanie_obrazów_binarnych#Erozja)

```
opening(A) = dilate(erode(A))
closing(A) = erode(dilate(A))
```

Operatory tophat (ekstrakcja obszarów jasnych) i blackhat (ekstrakcja obszarów ciemnych) służą odpowiednio ekstrakcji jasnych i ciemnych obszarów w obrazie (poziom jasności określany w odniesieniu do najbliższego otoczenia). 

tophat(A) = A − open(A)
blackhat(A) = close(A) − A


## Projekty

### Rotate images
[Rotate Images](https://www.pyimagesearch.com/2017/01/02/rotate-images-correctly-with-opencv-and-python/)

Tematy które pozznałem przy okzaji tego ćwiczenia: 
* findContours ([RetrievalModes](https://docs.opencv.org/master/d3/dc0/group__imgproc__shape.html#ga819779b9857cc2f8601e6526a3a5bc71))
* drawCountours
* Konwersja `cv2.COLOR_BGR2RGB` i `cv2.COLOR_BGR2GRAY` to nie to samo co wyłącznie `cv2.COLOR_BGR2GRAY`

[Building a Pokedex in Python: Finding the Game Boy Screen](https://www.pyimagesearch.com/2014/04/21/building-pokedex-python-finding-game-boy-screen-step-4-6/)

[Contours Hierarchy](https://docs.opencv.org/master/d9/d8b/tutorial_py_contours_hierarchy.html)

### Watermarking Images
[Watermarking images](https://www.pyimagesearch.com/2016/04/25/watermarking-images-with-opencv-and-python/)

### Determining object color
[Determining object color](https://www.pyimagesearch.com/2016/02/15/determining-object-color-with-opencv/)

### Shape detection
[Shape detection](https://www.pyimagesearch.com/2016/02/08/opencv-shape-detection/)

### Center of contour
[Center of contour](https://www.pyimagesearch.com/2016/02/01/opencv-center-of-contour/)

### Text skew correction
[Text skew correction](https://www.pyimagesearch.com/2017/02/20/text-skew-correction-opencv-python/)

### Recognizing LCD digits
[Recognizing LCD Digits](https://www.pyimagesearch.com/2017/02/13/recognizing-digits-with-opencv-and-python/)

### Determining object color
[Determining object color](https://www.pyimagesearch.com/2016/02/15/determining-object-color-with-opencv/)

### Shape detection
[Shape detection](https://www.pyimagesearch.com/2016/02/08/opencv-shape-detection/)

### Center of contour
[Center of contour](https://www.pyimagesearch.com/2016/02/01/opencv-center-of-contour/)

### Panorama stitching
[panorama stitching](https://www.pyimagesearch.com/2016/01/11/opencv-panorama-stitching/)







