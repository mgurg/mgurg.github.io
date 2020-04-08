---
layout: post
title: "Python: string (łancuch znaków)"
categories: python
author: "Michał"
---

## Podstawowe operacje
`text.lower()` - wszystkie litery z małej

`text.upper()` - wszystkie litery z małej

`text.title()` - wszystkie wyrazy z dużej

`text.strip()` - usunięcie białych znaków z końca i początku

`text.split(' ')` - podział tekstu (spacja)

`" ".join(words)` - łączenie wyrazów

`'80,5'.replace(',', '.')`- zamiana znaków

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

Więcej: [Python 3's f-Strings: An Improved String Formatting Syntax (Guide)](https://realpython.com/python-f-strings/)



### TODO:

str() vs repr() in Python

stdin, stdout, stderr vs print()



try:

except

sys.stderr.write('')