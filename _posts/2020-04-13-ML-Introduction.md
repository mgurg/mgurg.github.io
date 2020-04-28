---
layout: post
title: "ML: Wprowadzenie"
categories: ML
author: "Michał"
---

https://renanmf.com/machine-learning-and-deep-learning-software-engineers/


## Machine learning - podział

Supervised Learning:

- Classification
- Regression

Unsupervised Learning:

- Clustering
- Dimensionality reduction

Reinforcement learning





# Regresja liniowa

y = ax+b

- a - pochylenie
- b - odsunięcie na osi y

Rodzaje:

- Korelacja
  - Represents the relationship between two variables
  - Shows that two variables move together (no matter in which direction)
  - A single point (a number)
- Regresja
  - Represents the relationship between two or more variables
  - Shows cause and effect (one variable is affected by the other)
  - One way – – there is always only one variable that is causally dependent
  - A line (in 2D space)



Rozwiązanie nieliniowych problemów:

- non linear regression
- exponential transformation
- Log transformation (semi-log [x lub y]: gdy X wzrasta o 1 to Y wzrasta o b %, log-log [x i y]:gdy X wzrasta o 1% to Y wzrasta o b % )



Modele

- liniowe
- nieliniowe
  - quadratic
  - expotential
  - logistic

Supervised Learning

zawiera input (feature) [x] i output (target) [y]



### Underfitting - Overfiting

*Overfiting* - przeuczenie na danych treningowych. Zbyt małe uogólnienie modelu, za duże skoncentrowanie się na danych treningowych. High training accuracy. Lapie również wszystkie szumy

*Underfiting* - zbyt ogólny model, nie łapie o co chodzi nawet na treningowym. mało dokładny. Low train accuracy.  



## Clustering

- Flat (K-Means)
- Hierarchical
  - Agglomerative (Bottom-up)
  - Divisive (Top-Down)

📺 [How does k-nearest neighbors work?](https://www.youtube.com/watch?v=0p0o5cmgLdE)



# Sieci neuronowe (ANN)

Architektura najprostszej sieci neuronowej (MLP):

![https://www.ritchievink.com/img/post-9-mlp/nn_diagram_1.png](https://www.ritchievink.com/img/post-9-mlp/nn_diagram_1.png)

Źródło: [Programming a neural network from scratch](https://www.ritchievink.com/blog/2017/07/10/programming-a-neural-network-from-scratch/)

**Input layer** - Dostarczają do sieci informacje ze świata zewnętrznego W żadnym z węzłów wejściowych nie są wykonywane żadne obliczenia - po prostu przekazują one informacje do ukrytych węzłów.

**Hidden layer** - Nie mają bezpośredniego połączenia ze światem zewnętrznym (stąd nazwa "ukryte"). Wykonują one obliczenia i przekazują informacje z węzłów wejściowych do węzłów wyjściowych. Kolekcja ukrytych węzłów tworzy "Ukrytą warstwę". Sieć neuronowa ma tylko jedną warstwę wejściową i jedną warstwę wyjściową, może ona mieć zerową (*Single Layer Perceptron*) lub wielowarstwową (*Multi Layer Perceptron*) część ukrytą.

**Output layer** - Są odpowiedzialne za obliczenia i przesyłanie informacji z sieci do świata zewnętrznego.

Najczęstsze typy aktywacji

- *Sigmoid* (funkcja logistyczna). Zakres : (0,1) 
- *TanH* (tangens hiperboliczny). Zakres (-1, 1)
- *ReLu* (rectified linear unit). Zakres (0, inf)
- *SoftMax*. Zakres:  (0,1) Suma jest równa 1 (prawdopodobieństwo)

Przejście od warstwy wejściowej do wyjściowej to *Feed Forward*, Droga powrotna (OUT -> IN)  to *Back Propagation*. Sieć uczy się i optymalizuje swój stan wewnętrzny poprzez minimalizowanie funkcji straty.  Backdrop jest potrzebny żeby ustalić właściwe wagi i bias. Wykorzystuje gradienty, a żeby uniknąć pozostania w lokalnym minimum stosuje się [Momentum](https://distill.pub/2017/momentum/).

**Epoch** is when an ENTIRE dataset is passed forward and backward through the neural network only ONCE

**Batch** - is a number of samples fed to NN during single iteration. Total number of training examples present in a single batch. 

**Iterations** is the number of batches needed to complete one epoch.

[Epoch = Batch * Iterations](https://towardsdatascience.com/epoch-vs-iterations-vs-batch-size-4dfb9c7ce9c9)

### Normalizacja

Więcej: [Batch Normalization — Speed up Neural Network Training](https://medium.com/@ilango100/batch-normalization-speed-up-neural-network-training-245e39a62f85)

- Batch Gradient Descent  (Batch Size == Number of samples) 

- Mini-Batch Gradient Descent  ( 1 < Batch Size < Number of samples)

- Stochasting Gradient Descent ( Batch Size ==1)



## CNN

Warstwa konwolucyjna + pooling + ANN = CNN

```python
# simple_cnn
Sequential([
	Conv2D(32, kernel_size=(3, 3), activation='relu', input_shape=input_shape),
	MaxPool2D(pool_size=(2, 2)),
	Dropout(0.25), 
       
	Flatten(), # spłaszczanie, aby połączyć warstwy konwolucyjne z fully connected layers
        
	Dense(1024, activation='relu'),
	Dropout(0.5),
	Dense(num_classes, activation='softmax')
    ])
```



Konwolucja - nakładanie filtru

Pooling - sposób na  zmniejszanie zdjęć przy jednoczesnym zachowaniu najważniejszych informacji w nich zawartych.



