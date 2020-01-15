---
layout: post
title: "Konfiguracja Scrapy (część II)"
categories: python
author: "Michał"
---


W [poprzedniej części](https://mgurg.github.io/python/2020/01/02/scrapy-konfiguracja.html) pokazałem w jaki sposób przeprowadzić podstawowe testy z *scrapy* korzystając wyłącznie z okna shell bez tworzenia projektu. Ponieważ próby wyszły pomyślnie to zacząłem dostosowywać to narzędzie do swoich potrzeb.


### Tworzenie projektu
Wykorzystując komendę:

```batch
scrapy startproject scrapy_otomoto
```

Utworzymy na dysku następującą strukturę plików
```batch
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

### Uruchomienie projektu

Uruchomianie projektu wykonuje się komenda:

```batch
scrapy crawl quotes_spider
``` 

To jest minimalna konfiguracja projektu w *scrapy* jednak zeby zadziałałon ze stroną otomoto.pl konieczna będzie dalsza konfiguracja.