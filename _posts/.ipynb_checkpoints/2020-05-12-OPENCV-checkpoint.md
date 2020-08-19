---
layout: post
title: "OpenCV Roadmap"
categories: OpenCV
author: "Michał"
math: true
---

Ogolne informacje o CV
https://www.reddit.com/r/computervision/

Wykłady AGH: http://home.agh.edu.pl/~jsw/aipofm/

# OpenCV 

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

## Rysowanie
```python
cv2.line(img, (0,0), (200,200), (255,0,0), 5)
cv2.circle(img, (100,100), 50, (255,0,0), 5)
cv2.rectangle(img, (10,10), (100,100), (255,0,0), 5 )
```

## Operacje morfologiczne
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


### Ciekawe projekty

Poczatkujące:
- [text skew correction](https://www.pyimagesearch.com/2017/02/20/text-skew-correction-opencv-python/)
- [Recognizing LCD Digits](https://www.pyimagesearch.com/2017/02/13/recognizing-digits-with-opencv-and-python/)
- [Rotate Images](https://www.pyimagesearch.com/2017/01/02/rotate-images-correctly-with-opencv-and-python/)
- [Watermarking images](https://www.pyimagesearch.com/2016/04/25/watermarking-images-with-opencv-and-python/)
- [Determining object color](https://www.pyimagesearch.com/2016/02/15/determining-object-color-with-opencv/)
- [Shape detection](https://www.pyimagesearch.com/2016/02/08/opencv-shape-detection/)
- [center of contour](https://www.pyimagesearch.com/2016/02/01/opencv-center-of-contour/)
- [panorama stitching](https://www.pyimagesearch.com/2016/01/11/opencv-panorama-stitching/)


Zaawansowane:
- [Sudoku Solver](https://www.pyimagesearch.com/2020/08/10/opencv-sudoku-solver-and-ocr/)
- [Segmentacja obrazu GrabCut](https://www.pyimagesearch.com/2020/07/27/opencv-grabcut-foreground-segmentation-and-extraction/)
- [OpenCV Selective Search](https://www.pyimagesearch.com/2020/06/29/opencv-selective-search-for-object-detection/)
- [Open CV inpainting](https://www.pyimagesearch.com/2020/05/18/image-inpainting-with-opencv-and-python/)
- [Face Detection & Blur](https://www.pyimagesearch.com/2020/04/06/blur-and-anonymize-faces-with-opencv-and-python/)
- [Video: Scene Boundaries detection](https://www.pyimagesearch.com/2019/08/19/simple-scene-boundary-shot-transition-detection-with-opencv/)
- [Holistically-Nested Edge Detection](https://www.pyimagesearch.com/2019/03/04/holistically-nested-edge-detection-with-opencv-and-deep-learning/)
- [b&W images Colorization](https://www.pyimagesearch.com/2019/02/25/black-and-white-image-colorization-with-opencv-and-deep-learning/)

COMPUTER VISION I
Module 1 :Getting Started with OpenCV
   1. Introduction to computer vision
   2. Basic image operations
   3. Mathematical operations on images
   4. Sunglass filter : A simple application
   5. Bitwise operations
   6. Image Annotation
   7. QR code detection

Module 2 : Video IO and GUI
   1. Video IO using HighGUI
   2. Keyboard as input device
   3. Using the mouse for image annotation
   4. Adding trackbar controller to your OpenCV application

Module 3 : Binary Image Processing
   1. Thresholding
   2. Erosion / Dilation
   3. Implementing morphological operations from scratch.
   4. Opening and Closing
   5. Connected Component Analysis
   6. Contour Analysis
   7. Blob Detection
   8. Coin Detection

Module 4 : Image Enhancement and Filtering
   1. Color Spaces
   2. Color Transforms
   3. Image Filtering
   4. Image Smoothing
   5. Image Gradients
   6. Autofocus Application
   
Module 5 : Advanced Image Processing and Computational Photography
   1. Hough Transforms
   2. High Dynamic Range Imaging
   3. Seamless Cloning
   4. Image Inpainting
   
Module 6 : Geometric Transforms and Image Features
   1. Affine Transform
   2. Homography
   3. Image Features
   4. Feature Matching
   5. Image Alignment
   6. Creating Panoramas
   7. Finding known objects in an image
   8. Selfie Application 1:
      * Instagram Filters
      * Blemish Removal
      * Green Screening
      
Module 7 : Image Segmentation and Recognition
   1. Image segmentation using GrabCut
   2. Introduction to AI
   3. Object Detection
   4. Image Classification
   
Module 8 : Video Analysis
   1. Motion Estimation using Optical Flow
   2. Object Tracking
   3. Kalman Filtering
   4. Meanshift and Camshift
   
Module 9 : Deep Learning with OpenCV
   1. Image Classification
   2. Object Detection
   3. Face Detection
   4. Human Pose Estimation
   5. Selfie Application 
      * Skin smoothing
      * Sunglass filter
   6. Document Scanner
   7. Detection and Tracking


[OpenCV Python Tutorials](https://opencv-python-tutroals.readthedocs.io)

# Morphological Transformations  

**Erosion & Dilation**

Erosion: Wartośći pikseli sa uśredniane w dół.

Dilation: wartośći pikseli są uśredniane w górę.

Szeroki opis na [StackOverflow](https://stackoverflow.com/a/30380543)
[Morphological Transformations](http://www.codebind.com/python/opencv-python-tutorial-beginners-morphological-transformations/)
[Morphological transformations with OpenCV in Python](http://datahacker.rs/006-morphological-transformations-with-opencv-in-python/)