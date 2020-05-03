---
layout: post
title: "Python: list comprehension"
categories: python
author: "Michał"
---


Służą do transformacji jednej listy na inną listę (tworzenie nowych list według określonych zasad):
Klasyczna pętla:

```python
liczby = [2,3,4]
liczby3 = []
for i in liczby:
    if i > 0:
        liczby3.append(i**3)
liczby3
```

List comprehension:
```python
liczby3 = [i**3 for i in liczby if i >0 ]
liczby3
```

Jeszcze jedne przykład:
```python
# prosty sposób:
kwadraty = []
for x in range(20, 39):
	kwadraty.append(x)
	
# List comprehension
kwadraty = [x for x in range(20, 39)]
```

### Składnia 

[**output expression** _for item in iterable_]

```python
# Exponentiation - all items from list 
numbers = [1, 2, 3, 4, 5]
squares = [number**2 for number in numbers]
print(squares)
```
można dodać również warunek if:

[**output expression** *for item in iterable* **if condition**]

```python
# Exponentiation - even items only 
numbers = [1, 2, 3, 4, 5]
squares = [number**2 for number in numbers if number > 2]
print(squares)
```
[**output expression** if-else clause  *for item in iterable* **condition/s on iterable**]

```python
numbers = [1, 2, 3, 4, 5, 6, 18, 20]
squares = [
    "small" if number < 10 else "big" 
    for number in numbers 
    if number % 2 == 0 
    if number % 3 == 0]
print(squares)
```

*List comprehension* są wydajniejsze od pętli for, zapis jest bardziej zwarty









