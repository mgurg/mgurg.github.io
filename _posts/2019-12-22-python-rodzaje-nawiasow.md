---
layout: post
title: "Python: Rodzaje nawiasów"
categories: python
author: "Michał"
---



### Nawiasy okrągłe `(...)` 

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

### Nawiasy kwadratowe `[...]` 

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

Listy zagnieżdzone:

```python
lista = [[1,2,3],[4,5,6],[7,8,9]]
lista = [[1,2,3],
[4,5,6],
[7,8,9]]
lista[1][2] => 6
```

jeśli przypiszemy listę do innej zmiennej to tak naprawdę przypiszemy adres w pamięci do listy
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
### Nawiasy klamrowe `{...}` 

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




