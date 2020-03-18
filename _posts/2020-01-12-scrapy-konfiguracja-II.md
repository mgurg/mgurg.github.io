---
layout: post
title: "Konfiguracja Scrapy (część II)"
categories: scrapy
author: "Michał"
---


W [poprzedniej części](https://mgurg.github.io/python/2020/01/02/scrapy-konfiguracja.html) pokazałem w jaki sposób przeprowadzić podstawowe testy z *scrapy* korzystając wyłącznie z okna shell bez tworzenia projektu. Ponieważ próby wyszły pomyślnie to zacząłem dostosowywać to narzędzie do swoich potrzeb.


### Tworzenie projektu
Wykorzystując komendę:

```bash
scrapy startproject scrapy_otomoto
```

Utworzymy na dysku następującą strukturę plików
```bash
>tree /f

D:.
└───scrapy_otomoto
    │   scrapy.cfg
    │
    └───scrapy_otomoto
        │   items.py
        │   middlewares.py
        │   pipelines.py
        │   settings.py
        │   __init__.py
        │
        ├───spiders
        │   │   __init__.py
        │   │
        │   └───__pycache__
        └───__pycache__

```
### Wygenerowanie pająka

Kolejnym krokiem jest wygenerowanie "pająka":
```batch
scrapy genspider otomoto otomoto.pl
```
Wynikiem tej komendy jest utworzenie nowego pliku w folderze `spiders`
```batch
D:.
│   scrapy.cfg
│
└───scrapy_otomoto
    │   items.py
    │   middlewares.py
    │   pipelines.py
    │   settings.py
    │   __init__.py
    │
    ├───spiders
    │   │   otomoto.py
    │   │   __init__.py
    │   │
    │   └───__pycache__
    │           __init__.cpython-37.pyc
    │
    └───__pycache__
            settings.cpython-37.pyc
            __init__.cpython-37.pyc
```
Zawartość pliku `otomoto.py`:
```python
# -*- coding: utf-8 -*-
import scrapy


class OtomotoSpider(scrapy.Spider):
    name = 'otomoto'
    allowed_domains = ['otomoto.pl']
    start_urls = ['http://otomoto.pl/']

    def parse(self, response):
        pass
```
Plik konfiguracyjny `scrapy.cfg`:
```
# Automatically created by: scrapy startproject
#
# For more information about the [deploy] section see:
# https://scrapyd.readthedocs.io/en/latest/deploy.html

[settings]
default = scrapy_otomoto.settings
shell = ipython

[deploy]
#url = http://localhost:6800/
project = scrapy_otomoto
```

### Uruchomienie projektu

Uruchomianie projektu wykonuje się komenda:

```bash
cd .\scrapy_otomoto\scrapy_otomoto\spiders
scrapy runspider otomoto.py
```

To jest minimalna konfiguracja projektu w *scrapy* jednak żeby zadziałał on ze stroną otomoto.pl konieczna będzie dalsza konfiguracja. W tym przypadku problemem jest (tak samo jak za poprzednim razem) brak podanego `user-agent`.

