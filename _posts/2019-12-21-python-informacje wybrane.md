---
layout: post
title: "Python: informacje wybrane"
categories: python
author: "Michał"
---


Python: informacje wybrane. Kiedyś myślałem że będę opisywał dla siebie i potomnych elementy i składnię pythona. Ale potem pojawił się [Jetbrains Academy](https://hyperskill.org/join/551d2dc61) i przestało mieć to sens. Pozbierałem i zarchiwizowałem tutaj wszystkie swoje stare notatki ,ale jeżeli potrzebuje dowiedzieć się czegoś w szczegółach to zaglądam do JetBrains Academy.

## enumerate(), zip()

*enumerate()* - funkcja przyjmuje na wejściu listę, zwraca też listę, składająca się z elementów `tuple`. Każdy `tuple` składa z dwóch elementów (pierwszy to indeks bieżącego elementu, drugi to oryginalna wartość)

```python
seasons = ['Spring', 'Summer', 'Fall', 'Winter']
list(enumerate(seasons))

# [(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
```
Szybkie generowanie słownika:
```python
dict( enumerate(['red', 'blue', 'green']) )

# {0: 'red', 1: 'blue', 2: 'green'}
```
```python
for (indeks, element) in enumerate(kolekcja):
	# kod pętli for
```

*zip()* - funkcja używana w pętli for; daje nam elementy z tej samej pozycji w kilku kolekcjach; gdy kolekcje są różnej długości, wielkość najkrótszej kolekcji będzie brana przy ilości powtórzeń pętli

```python
for (element_a, element_b) in zip(kolekcja_a, kolekcja_b):
	# kod pętli

```

## Tuple, list, dict

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
Do głębokiego kopiowania (kopiowanie wszystkiego jako wartość) używamy modułu copy i metody deepcopy()
```python
import copy
nowy = copy.deepcopy(stary)
```

### Set

Unordered collection of distinct [hashable](https://docs.python.org/3/glossary.html#term-hashable) objects. 

https://www.programiz.com/python-programming/set



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

## List Comprehension

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



---

## arg

Operator * przekazuje do funkcji po jednym argumencie z listy:

```python
args = [1, 2]
my_func(*args)
```

Można to rozpisać jako:

```python
args = [1, 2]
my_func(args[0], args[1])
```
Pozwala przekazywać większą ilość argumentów do funkcji:

```python
def add(a, b, *args):
    total = a + b
    for n in args:
        total += n
    return total

# The length of `args` is 3
print(add(1, 2, 3, 4, 5))
 
# The length of `args` is 0
print(add(1, 2))
```

```python
def will_survive(*names):
    for name in names:
        print("Will", name, "survive?")
 
 
will_survive("Daenerys Targaryen", "Arya Stark", "Brienne of Tarth")
```

```python
print(*"fun")        # f u n
print(*[5, 10, 15])  # 5 10 15
```



## kwarg

Dla słowników używamy zapisu z dwoma gwiazdkami **  (ang. *keyword argument*)

```python
xgb_params = {
    'max_depth' : 5,
    'n_estimators' : 100,
    'learning_rate' : 0.3,
    'random_state' : 0
}

model = xgb.XGBRegressor(**xgb_params)
```

Notice the difference: `*args` provides access to a **tuple** of remaining values, while `**kwargs` collects remaining key-value pairs in a **dictionary**.

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

Umożliwia razem z funkcją `lambda` szybkie iterowanie po kolumnach dataframe:

```python
df['GPS y'] = df['GPS y'].apply(lambda x : dms2dd(x))
```

Jeżeli chcemy przekazać do funkcji kilka argumentów (z jednego wiersza Pandas DataFrame):

```python
df['newcolumn'] = df.apply(lambda x: fxy(x['A'], x['B']), axis=1)
```

[Pythonizmy](https://python101.readthedocs.io/pl/latest/podstawy/pythonizmy.html)

---

## Generators

Przykład generatora: `range(10)`. Wartości są generowano dopiero wtedy gdy są potrzebne `l = list(range(10))`

Inny przykład:

```python
def gen(n):    # an infinite sequence generator that generates integers >= n
    while True:
        yield n
        n += 1
        
G = gen(3)     # starts at 3
print(next(G)) # 3
print(next(G)) # 4
print(next(G)) # 5
print(next(G)) # 6
```



## Iterators

```python
# This is a list...
my_list = [1, 2, 3]
 
# ... and this is how we create an iterator from it
my_iterator = iter(my_list)
print(my_iterator) # <list_iterator object at 0x000001F06D792B70>
```

```python
print(next(my_iterator))  # 1
print(next(my_iterator))  # 2
print(next(my_iterator))  # 3
print(next(my_iterator))  # StopIteration exception
```

Python dla pętli `for` automatycznie utworzy iterator z danego obiektu iterowalnego i będzie otrzymywał jego elementy jeden po drugim za pomocą metody `next`, aż do wyczerpania obiektu iterowalnego.

```python
for item in my_list:
    print(item)
 
# 1
# 2
# 3
```
```python
for element in characters:
    print(element)
    
# or

print(*characters)
```

## zip()

Iterator dla kilku elementów iterowalnych:

```python
short_list = [1, 2, 3]
long_list = [10, 20, 30, 40]
 
for a, b in zip(short_list, long_list):
    print(a, b)
 
# 1 10
# 2 20
# 3 30
```

## enumerate()

```python
months_list = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 
               'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
 
for n, month in enumerate(months_list):
    print(n + 1, month)
 
# 1 Jan
# 2 Feb
# 3 Mar
# 4 Apr
# 5 May
# 6 Jun
# etc.
```

## String - podstawowe operacje

`text.lower()` - wszystkie litery z małej

`text.upper()` - wszystkie litery z małej

`text.title()` - wszystkie wyrazy z dużej

`text.strip()` - usunięcie białych znaków z końca i początku

`text.split(' ')` - podział tekstu (spacja)

`" ".join(words)` - łączenie wyrazów

`'80,5'.replace(',', '.')`- zamiana znaków

`ord()` - zwraca numer Unicode pojedynczego znaku

`chr()` - zwraca znak na podstawie numeru Unicode



## Numerowanie znaków

`str = "Python"`

|  P   |  y   |  t   |  h   |  o   |  n   |
| :--: | :--: | :--: | :--: | :--: | :--: |
|  0   |  1   |  2   |  3   |  4   |  5   |
|  -6  |  -5  |  -4  |  -3  |  -2  |  -1  |

```python
str[3]     #h
str[:3]    #Pyth
str[-1]    #n
str[4:]    #on
str[-4:-2] # th
str[::-1]  #nohtyP
```



## Multiline string

Definiujemy jako potrójny apostrof`'''` lub cudzysłów `"""` 

```python
create_users = """
    INSERT INTO
    cars (offer_id,city,region,model,year,mileage,fuel_type,displacement,price,currency,pub_date,duration,end_price)
    VALUES
    (6069449316,'Prudnik','Opolskie','Toyota Yaris II',2009,153000,'Diesel',-1,12999,'PLN','2019-12-31',7,12999),
    (6068202189,'Włocławek','Kujawsko-pomorskie','Toyota Yaris II',2008,110000,'Benzyna',1298,17600,'PLN','2019-12-31',21,16900),
    (6067206317,'Łódź','Łódzkie','Toyota Yaris II',2010,167938,'Diesel',1364,13999,'PLN','2019-12-31',31,13900),
    (6069421596,'Katowice','Śląskie','Toyota Yaris II',2008,214548,'Benzyna+LPG',1298,12000,'PLN','2019-12-31',31,12000),
    (6068568066,'Katowice','Śląskie','Toyota Yaris II',2007,38000,'Benzyna',1298,19300,'PLN','2019-12-31',12,18500);
    """
```

Wtedy można dowolnie łamać ciąg znaków

Zapis bardzo długi stringów z wykorzystaniem nawiasu

```python
# long string
str = "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"

# use brackets to breake lines
str2 = ('ssssssssss'
'ssssssssssssss'
'ssssssssssss')
```


## f-string

String formatujący `f'string'` (Python >= 3.6)

```python
name = "Eric"
age = 74
f"Hello, {name}. You are {age}."

> 'Hello, Eric. You are 74.'
```

Opcje dostępne przed Python 3.6:

```python
name = 'Alan'
age = 53
string = 'Hello, {name}. You are {age}.'.format(name = name, age = age)
print(string)

name = 'Greg'
age = 17
string = 'Hello, {0}. You are {1}.'.format(name, age)
print(string)

name = 'George'
age = 27
string = 'Hello, {}. You are {}.'.format(name, age)
print(string)
```

Więcej: [Python 3's f-Strings: An Improved String Formatting Syntax (Guide)](https://realpython.com/python-f-strings/)

Formatowanie : [Using *%* and *.format()* for great good!](https://pyformat.info/)

## Pliki tekstowe

Otwarcie pliku:

```python
plik = open("scieżka_do_pliku", tryb, encoding="utf-8")
```

tryby:

* r – tylko do odczytu
* w – zapisywanie pliku (stary plik o tej samej nazwie będzie usunięty)
* r+ - do odczytu i zapisu
* a – dopisywanie do pliku (dane są dopisane do końca istniejącego pliku)



**plik.read()** – odczytanie całego pliku, zwracany jest string zawierający cały tekst pliku (włącznie ze
znakami \n) – opc. argument – int określająca ilość bajtów do wczytania
**plik.readline()** – odczytanie jednej linii z pliku, zwracany jest string z linijką testu, włącznie ze znakiem \n
**plik.readlines()** – odczytuje cały tekst – zwraca listę stringów - linijek

```python
for line in plik:
	print(line, end='')
```

Pliki należy zamykać po użyciu:

```python
plik = open("plik.txt")
	# kod
plik.close()
```

Otwarcie pliku za pomocą *with* pozwala na automatyczne zamykanie pliku przez Pythona

```python
with open("plik.txt", encoding="utf-8") as plik:
	print(plik.readline())
```

Context manager:

```python
# invoking a context manager
with statement as variable_name:
    ...
```



**plik.write(string)** – zapisuje string do pliku w obecnej pozycji kursora, zwraca liczbę zapisanych znaków – należy pamiętać o znaku `\n`
**plik.writelines(iterable)** – zapisuje elementy z kolekcji jako poszczególne linie w pliku

Plik musi być otworzony w trybie do zapisu aby móc go zmieniać!



### TODO:

str() vs repr() in Python

output of the `__str__` should be *highly readable* and the output of the `__repr__` should be *unambiguous*. In other words, `__str__` creates a representation for users and `__repr__` creates a representation for developers.

A good rule is to always define the `__repr__` method first since it is the method used by developers in debugging. It is also a fallback method for `__str__`which means that if the `__str__` method isn't defined, in the situations where it's needed, the `__repr__` will be called instead.



stdin, stdout, stderr vs print()



try:

except

sys.stderr.write('')