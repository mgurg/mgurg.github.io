---
layout: post
title: "Python: Regular Expressions"
categories: Python
author: "Michał"
---



> Jeżeli rozwiązujesz problem przy pomocy Regexp to masz dwa problemy.

# Wyrażenia regularne

`[aeiou]` - dowolna samogłoska

`[^aeiou]` - dowolna spółgłoska (`^` - negacja)

`[a-z]` - dowolna mała litera z zakresu a-z

`\d` - dowolna liczba dziesiętna, odpowiednik `[0-9]`

`\D` - dowolna "nie-cyfra", odpowiednik `[^0-9]`

`\s` - dowolny "biały znak", odpowiednik `[\t\n\r\f\v]`

`\S` - dowolny "nie-biały znak" znak, odpowiednik `[^\t\n\r\f\v]`

`\w` - dowolny znak alfanumeryczny, odpowiednik `[a-zA-Z0-9_]`

`\W` -  dowolny znak nie-alfanumeryczny, odpowiednik `[^a-zA-Z0-9_]`

`\\` – po prostu ukośnik
`\.` – po prostu kropka (nie jest to dowolny znak)
`[AB]` – A lub B (umożliwia zdefiniowanie liczby powtórzeń)
`A|B` – A lub B

## Powtórzenia

`?` - 0 lub 1 

`* ` - 0 lub więcej

`+` - 1 lub więcej

`{n}` - powtórzenie dokładnie n razy

`{min, max}` - liczba wystąpień

`^string` - początek rozpoczyna się od string

`string$` - kończy się na string

### Greedy vs lazy

Domyślnie wszystkie kwantyfikatory (+, *, {n,m}, {n,} or {,m}, ?) są chciwe (greedy).

Wzorzec `a+` dla frazy `aaa` zwróci najdłuższy możliwy substring (`aaa`)

Jeśli chcemy zmienić to zachowanie, musimy postawić znak zapytania `?` tuż za kwantyfikatorem: `+?`, `*?`, `{n,m}?`. Wtedy otrzymamy najkrótsze dopasowanie.

Znak zapytania gdy następuje bezpośrednio po kwantyfikatorze to przełącza ten kwantyfikator z trybu "chciwego" na "leniwy". 

Przełączenie trybu nia działa dla `{n}` - ściśle określona liczba powtórzeń.


### Python 

```python
import re
pattern = re.compile(r'text') # r - raw string

pattern.match('text') #szuka na początku tekstu
pattern.search('sample text') # szuka w całym ciągu znaków
```

Przydatne metody dostępne w module re:
- compile(pattern) – kompiluje, co przyspiesza wyszukiwanie

- findall(pattern, string) – zwraca obiekt z wszystkimi dopasowaniami

- search(pattern, string) – zwraca obiekt z pierwszym dopasowaniem

- match(pattern, string) – zwraca dopasowanie o ile jest na początku string’a

  

### Regex101

Narzędzie do budowania i testowania zapytań : [Regex101](https://regex101.com/)

