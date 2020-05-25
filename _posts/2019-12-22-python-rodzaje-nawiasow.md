---
layout: post
title: "Python: Rodzaje nawiasów"
categories: python
author: "Michał"
---



## Nawiasy okrągłe `(...)` 

Tuple `(1, 2, 3)` -  jest typem niezmiennym, raz zdefiniowanego nie można zmienić.

Definiowanie tuple: 
```python
my_tuple = (0, 1, 2)
```

```python
tuple1 = ("raz", "dwa", "trzy")
# tuple1[0] = "jeden" – spowoduje błąd
```
Rozpakowanie tuple:
```python
tuple1 = ("raz", "dwa", "trzy")
x, y, z = tuple1

print(x) # >>> "raz"
print(y) # >>> "dwa"
print(z) # >>> "trzy"
```
To co z tuple robi tuple to przecinek:

```python
not_a_tuple = ('cat')   # <class 'str'>
now_a_tuple = ('cat',)  # <class 'tuple'>

weekend = 'Saturday', 'Sunday'
print(type(weekend))  # <class 'tuple'>

empty_tuple = tuple()
print(type(empty_tuple))  # <class 'tuple'>
```

Tuples are **faster** and **more memory-efficient** than lists.


## Nawiasy kwadratowe `[...]` 

Listy `[1, 2, 3]`, dostęp do poszczególnych elementów w `list` czy `tuple` odbywa się za pomocą indeksów: `my_list[0]` lub `my_tuple[0]`

Definiowanie list:
```python
my_list= [4, 5, 5, 3, 4]
```
```python
list = [1, 2, 3]
list2 = ["kwiatek", "doniczka", "ziemia", "woda"]
list3 = []
list4 = [1, "dwa", 3, 4]
list5 = list(range(2,5))
```
Możemy indeksować, slice'ować. Do elementu odwołujemy się przez indeks.

Sposoby na manipulacje listami:

```python
L[i], L[i:j], L[i:j:k]

len(), min(), max(), del(L[i])

L.append(), L.extend(), L.count(), L.index(), L.insert(),
L.pop(), L.remove(), L.reverse(), L.sort()
```

Listy zagnieżdżone:

```python
lista = [[1,2,3],[4,5,6],[7,8,9]]
lista = [[1,2,3],
[4,5,6],
[7,8,9]]
lista[1][2] => 6
```

Jeśli przypiszemy listę do innej zmiennej to tak naprawdę przypiszemy adres w pamięci do listy
możemy użyć kopiowania list:

```python
nowa_lista = lista.copy()
nowa_lista = list(stara_lista)
nowa_lista = stara_lista[:]
```
do głębokiego kopiowania (kopiowanie wszystkiego jako wartość) używamy modułu copy i metody deepcopy()
```python
import copy
nowy = copy.deepcopy(stary)
```

Więcej: [Kopiowanie list i słowników](http://analityk.edu.pl/kopiowanie-list-i-slownikow/)

### Set

unordered collection of distinct [hashable](https://docs.python.org/3/glossary.html#term-hashable) objects. 

https://www.programiz.com/python-programming/set



## Nawiasy klamrowe `{...}` 

Słownik: `{'key': 'value'}` -  odwołujemy się poprzez klucz a nie indeks:

```python
x = {"nazwisko":"kowalski", "pesel":88120134567}
x['pesel']
```



**klucz** – musi być typem niezmiennym (string, tuple, liczba), musi być unikalny (tylko jeden w słowniku)

**wartość** – mogą być powtórzone


Przykład słownika:

```python
my_dict = {
    'Jan': 4,
    'Adam': 5,
    'Ania': 5,
    'Adrian': 3,
    'Agnieszka': 4
}
```

```python
osoby = {"studenci":["Ala", "Jan", "Ania"], "wykladowcy":["doktor", "profesor"]}

print(osoby["studenci"][1])

osoby["wykladowcy"].append("magister")
osoby["administracja"] = ["pani Basia z dziekanatu"]
osoby.update({"ochrona":"Impel"})

print(osoby.keys)
print(osoby.values)

for key, item in osoby.items():
	print(key, item)
```

Definiowanie pustego słownika
```python
testable = {}
testable['key'] = 'value'
 
print(testable['key'])  # value
```

- random_dict.keys() - ['a', 'b', 'c', 'd', 'e']
- random_dict.values() - ['20', '40', '60', '80', '100']
- random_dict.items() - [('a', '20'), ('b', '40'), ('c', '60'), ('d', '80'), ('e', '100')]

```python
# get method does not throw an error
print(testable.get('key'))  # value
print(testable.get('not_a_key'))  # None
```

```python
testable = {'key1': 'value1', 'key2': 'value2'}
 
del testable['key1']  # this will remove both the key and the value from dictionary object
print(testable)  # {'key2', 'value2'}
 
del testable['not_a_key']  # throws a KeyError
del testable['key1']  # throws a KeyError as we've already deleted the object by the key
del testable  # deletes the whole dictionary

catalog = {'green table': 5000, 'brown chair': 1500, 'blue sofa': 15000, 'wardrobe': 10000}
print('blue sofa' in catalog)    # True
print('yellow chair' in catalog)    # False
```

