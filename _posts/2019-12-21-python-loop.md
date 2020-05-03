---
layout: post
title: "Python: Pętle"
categories: python
author: "Michał"
---



## While

Kod wewnątrz pętli *while*, będzie powtarzany dopóki wartość logiczna (wyrażenia lub zmiennej) nie zmieni
się na False (chyba, że pętla zostanie wcześniej przerwana)

```python
while (wartość logiczna True):
	kod
	...
	update wartości logicznej na False
```

## For


For wykona kod tyle razy ile elementów znajduje się w zbiorze/zakresie (chyba, że wcześniej przerwiemy wykonywanie pętli). Poszczególne elementy zbioru/zakresu są do naszej dyspozycji w bloku pętli:
```
for element in zbiór/zakres :
	kod
	cos z element możemy zrobić
	...
	kod
```
Range:

```python
# range(stop)
range(3) # <0, 1, 2> // len() == 3

# range(start, stop)
range(4, 8) # <4, 5, 6, 7>

# range(start, stop, krok)
range(0, 10, 3) # <0, 3, 6, 9>
```

### continue, break

*continue* – program pomija pozostałe instrukcje w bloku i wraca do sprawdzenia warunku (while) lub do kolejnego elementu (for)

*break* – działanie pętli jest przerywane, program przechodzi do kolejnej instrukcji po całym bloku pętli

## enumerate(), zip()

*enumerate()* - funkcja przyjmuje na wejściu listę, zwraca też listę, składająca się z elementów `tuple`. Każdy `tuple` składa z dwóch elementów (pierwszy to indeks bieżącego elementu, drugi to oryginalna wartość)

```python
seasons = ['Spring', 'Summer', 'Fall', 'Winter']
list(enumerate(seasons))

# [(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
```
Szybkie gebnerowanie słownika:
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

