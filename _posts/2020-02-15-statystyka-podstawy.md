---
layout: post
title: "Statystyka: Podstawy"
categories: ML
author: "Michał"
---



## Rodzaje danych

- Categorical (BMW, Audi, Mercedes; Yes/No)
- Numerical
  - Discrete (no. of childrens)
  - Continuous (weight, height)

## Skala pomiarowa

- Qualitative
  - Nominal (cannot be put in any order)
  - Ordinal (can be ordered)
- Quantitative
  - Interval (degrees Celsius and Fahrenheit)
  - Ratio (have a a true zero - degrees Kelvin, length)


Podstawowe pojęcia:

## Średnia arytmetyczna

Suma liczb podzielona przez ich liczbę:

```python
np.mean([10,10,10,10,90])
```

Wynik: `26`


## Mediana

Wartość środkowa, czyli taka, która dzieli liczbę (posortowanych rosnąco) obserwacji na pół. Zatem powyżej i poniżej niej znajduje się jednakowa liczba obserwacji. Medianę można też inaczej nazwać drugim kwartylem.

```
np.median([10,10,10,10,90])
```

Wynik: `10`

W sytuacji, w której ilość liczb w ciągu jest parzysta, medianę oblicza  się poprzez wyliczenie średniej arytmetycznej z dwóch wartości  środkowych. MEdiana w przeciwieństwie do średniej nie jest czuła na `outliers`

## Dominanta

Wartość występującą w danym zbiorze najczęściej. Dominantą może być więcej niż jedna wartość (np. w zestawie 2, 3, 3, 4, 5, 5, 7 dominantami są 3 i 5), może też być 0 (np: 1,2,3,4). 

## Percentyl

Dla uszeregowanego rosnąco zbioru danych: [10,10,30,60,90]

- 0 percentyl: `10` [**10**,10,10,10,90]

- 50 percentyl (mediana): `30` [10,10,**30**,60,90]

- 100 percentyl (wartość maksymalna): `90` [10,10,30,60,**90**]

```
np.percentile([10,10,30,60,90],50)
```

Wynik: `30`

## Rozkład normalny

Krzywa Gaussa / Rozkład Laplace'a - Gaussa / Krzywa dzwonowa

Jeżeli zmienna losowa ma rozkład normalny N(μ,σ) to:

- 68,3 % populacji mieści się w przedziale (-σ; + σ)
- 95,5 % populacji mieści się w przedziale (-2σ; + 2σ)
- 99,7 % populacji mieści się w przedziale (- 3σ; + 3σ)

## Skośność (Skewness)

Skewness is a measure of asymmetry that indicates whether the observations in a dataset are concentrated on one side. Right (positive) skewness looks like the one in the graph. It means that the s outliers are to the right (long tail to the right). Left (negative) skewness means that the outliers are to the left.

![Skewness - Diva Jain / CC BY-SA (https://creativecommons.org/licenses/by-sa/4.0)](https://upload.wikimedia.org/wikipedia/commons/c/cc/Relationship_between_mean_and_median_under_different_skewness.png)



## Odchylenie standardowe 

Inaczej rozrzut - im niższa wartość sigma (σ) tym bardziej stromy (świecowy) jest rozkład normalny. Intuicyjnie rzecz ujmując, odchylenie standardowe mówi, jak szeroko wartości jakiejś wielkości (na przykład wieku, inflacji, kursu walutowego) są rozrzucone wokół jej średniej[a]. Im mniejsza wartość odchylenia tym obserwacje są bardziej skupione wokół średniej. ([wikipedia](https://pl.wikipedia.org/wiki/Odchylenie_standardowe))

Duże odchylenie standardowe to wada modelu.



Variance and standard deviation measure the dispersion of a set of data points around its mean value.

**TODO**: Wariancja, Kowariancja, Korelacja



Przekształcenie rozkładu skośnego do normalnego (normalizacja)

Korelacja

Korelacja Pearsona

Korelacja dla rozkładu normalnego?





---

Faktoryzacja

Interpretowalność modelu