---
layout: post
title: "Python: string (łancuch znaków)"
categories: python
author: "Michał"
---

Manipulacja tekstem - podstawowe operacje

## Podstawowe operacje

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