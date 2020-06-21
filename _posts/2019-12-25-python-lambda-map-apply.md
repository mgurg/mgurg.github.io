---
layout: post
title: "Python: lambda / map / apply"
categories: python
author: "Michał"
---


## Funkcja Lambda
Funkcja nie posiadająca nazwy, o zwartym zapisie stosowana do prostych zadań. Przykład:

```python
f = lambda x: x * x 
```

Może być zapisana wyłącznie w jednej linijce i może zawierać tylko jedno wyrażenie

## Map()

Przekazuje do funkcji element listy jako argument. Wynik działania funkcji jest zwracany jako element nowej listy

```python
# map(function, element_iterowalny)
map(function, iterable, ...)
```
Krok po kroku:
- pobranie elementu z listy

- przekazanie go do funkcji jako argument

- funkcja coś z nim robi (np. mnoży razy dwa) i zwraca wynik

- wynik, który zwraca przekazuje do nowej listy

  

Przykład:


```python
numbers = [7, 4, 9, 6, 8, 1]

def double(items):
    output = []
    for val in items:
      output.append(val * 2 )
    return output

result = double(numbers)
print(result)
```
Odpowiednik powyższego kodu z wykorzystaniem `map()`:
```python
def double(value):
    return value * 2

numbers = [7, 4, 9, 6, 8, 1]
result = list(map(double, numbers))
print(result)
```

## Apply

Umożliwia razem z funkcją lambda szybkie iterowanie po kolumnach dataframe:

```python
df['GPS y'] = df['GPS y'].apply(lambda x : dms2dd(x))
```

Jeżeli chcemy przekazać do funkcji kilka argumentów (z jednego wiersza Pandas DataFrame):

```python
df['newcolumn'] = df.apply(lambda x: fxy(x['A'], x['B']), axis=1)
```



[Pythonizmy](https://python101.readthedocs.io/pl/latest/podstawy/pythonizmy.html)


