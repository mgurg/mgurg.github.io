---
layout: post
title: "Statystyka: Podstawy"
categories: ML
author: "Michał"
---

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

W sytuacji, w której ilość liczb w ciągu jest parzysta, medianę oblicza  się poprzez wyliczenie średniej arytmetycznej z dwóch wartości  środkowych

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



## Odchylenie standardowe 

 Inaczej rozrzut - im niższa wartość sigma (σ) rym bardziej stromy (świecowy) jest rozkład normalny. Intuicyjnie rzecz ujmując, odchylenie standardowe mówi, jak szeroko wartości jakiejś wielkości (na przykład wieku, inflacji, kursu walutowego) są rozrzucone wokół jej średniej[a]. Im mniejsza wartość odchylenia tym obserwacje są bardziej skupione wokół średniej. ([wikipedia](https://pl.wikipedia.org/wiki/Odchylenie_standardowe))

Duże odchylenie standardowe to wada modelu.



Przekształcenie rozkładu skośnego do normalnego (normalizacja)

Korelacja

korelacja pearsona

Korelacja dla rozkładu normalnego?





---

Faktoryzacja

Interpretowalność modelu