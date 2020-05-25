---
layout: post
title: "Python: Generators, Iterators, zip(), enumerate()"
categories: python
author: "Michał"
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

Python dla pętli `for`automatycznie utworzy iterator z danego obiektu iterowalnego i będzie otrzymywał jego elementy jeden po drugim za pomocą metody `next`, aż do wyczerpania obiektu iterowalnego.

```python
for item in my_list:
    print(item)
 
# 1
# 2
# 3
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