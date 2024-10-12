---
layout: post
title: "Testy: unittest i pytest"
categories: python
author: "Michał"
---


Testy w Python

## unittest

Przykład testowanego pliku *calculator.py*:

```python
# calculator.py file

def add(a, b):
    return a + b

def multiply(a, b):
    return a * b

def subtract(a, b):
    return a - b

def divide(x, y):
    if y == 0:
        raise ValueError('Can not divide by zero!')
    return x / y
```

Plik z testami *test_calculator.py*:

```python
# test_calculator.py file

import unittest
import calculator

class TestCalculator(unittest.TestCase):  # a test case for the calculator.py module

    def test_add(self):
        # tests for the add() function
        self.assertEqual(calculator.add(6, 4), 10, 'Error when adding two positive numbers')
        self.assertEqual(calculator.add(6, -4), 2)

    
    def test_divide(self):
        # tests for the divide() function
        self.assertRaises(ValueError, calculator.divide, 5, 0)
        # or:
        with self.assertRaises(ValueError):
            calculator.divide(6, 0)
```

Możliwe metody testowania:

| Metoda                                       | Co sprawdza?               |
|----------------------------------------------|----------------------------|
| assertEqual(a, b)                            | a == b                     |
| assertNotEqual(a, b)                         | a != b                     |
| assertTrue(x)                                | bool(x) is True            |
| assertFalse(x)                               | bool(x) is False           |
| assertIsNone(x)                              | x is None                  |
| assertIsNotNone(x)                           | x is not None              |
| assertGreater(a, b)                          | a > b                      |
| assertLess(a, b)                             | a < b                      |
| assertIsInstance(a, b)                       | isinstance(a, b)           |
| assertRaises(exception, function, arguments) | Czy funkcja zwraca wyjątek |

Wywołanie testów z poziomu konsoli:

```bash
python -m unittest test_calculator
```

 Komunikat w przypadku błędu:

 ```bash
 FAIL: test_add (test_calculator.TestCalculator)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "test_calculator.py", line 11, in test_add
    self.assertEqual(calculator.add(6, -4), 3)
AssertionError: 2 != 3

----------------------------------------------------------------------
Ran 1 test in 0.000s

 ```

### Gdy projekt nie ma płaskiej struktury

Powyższa instrukcja jest prawidłowa, gdy struktura projektu jest płaska jak Ziemia:

```bash
.
├── calculator.py
└── test_calculator.py
```

Zwykle jednak chemy wydzielić testy do osobnego folderu:

```bash
new_project
├── calculator
│   ├── __init__.py # make it a package
│   └── calculator.py
└── test
    ├── __init__.py # make it a package
    └── test_calculator.py 
```

Konieczne będzie wprowadzenie kilku zmian:

* *calculator.py* i *test_calculator.py* plik zastały przeniesione do osobnych folderów
* w każdym z folderów tworzymy pusty plik *__init__.py*
* modyfikujemy w pliku test_calculator.py* linię importu jak poniżej:

```python
# import calculator 
from calculator import calculator
```

Wywołanie testów:

```bash
cd new_project
python -m unittest test.test_calculator
```

Można też wywołać konkretną klasę testową:

```
python -m unittest test.test_calculator.TestCalculator

```

Wywołanie wszystkich testów (discover) z folderu test dla plików z przedrostkiem test_:

```
cd new_project
python -m unittest discover
```

## pytest (TBD)

```bash
python -m pytest ptest.py -v
```
