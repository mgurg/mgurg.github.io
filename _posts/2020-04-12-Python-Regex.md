---
layout: post
title: "Python: Regular Expressions"
categories: Python
author: "Michał"
---



# Regex

`[aeiou]` - dowolna samogłoska

`[^aeiou]` - dowolna spółgłoska (^ - negacja)

`[a-z]` - dowolna mała litera z zakresu a-z

`\d` - dowolna liczba dziesiętna, odpowiednik `[0-9]`

`\D` - dowolna "nie-cyfra", odpowiednik `[^0-9]`

`\s` - dowolny "biały znak", odpowiednik `[\t\n\r\f\v]`

`\S` - dowolny "nie-biały znak" znak, odpowiednik `[^\t\n\r\f\v]`

`\w` - dowolny znak alfanumeryczny, odpowiednik `[a-zA-Z0-9_]`

`\W` -  dowolny znak nie-alfanumeryczny, odpowiednik `[^a-zA-Z0-9_]`

## Powtórzenia

`?` - 0 lub 1 

`* ` - 0 lub więcej

`+` - 1 lub więcej

`{n}` - powtórzenie dokładnie n razy

`^string` - początek rozpoczyna się od string

`string$` - kończy się na string



```python
import re
pattern = re.compile(r'text') # r - raw string

pattern.match('text') #szuka na początku tekstu
pattern.search('sample text') # szuka w całym ciągu znaków
```

