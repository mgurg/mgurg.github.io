---
layout: post
title: "Python: lambda / map / apply"
categories: python
author: "Michał"
---


### Funcja Lambda
Funkcja nie posiadająca nazwy, o zwartym zapisie stosowana do prostych zadań. Przykład:

```python
f = lambda x: x * x 
```

Może być zapisana wyłącznie w jednej linijce i może zawierać tylko jedno wyrażenie

### Map()

Przekazuje do funkcji element listy jako argument. Wynik działania funkcji jest zwracany jako element nowej listy

```python
map(function, iterable, ...)
```

Przykłady:


```python
list(map(is_even, [1, 2, 3, 4, 5]))
```

```python
numbers = (1, 2, 3, 4)
result = map(calculateSquare, numbers)
print(result)
```

### Apply






