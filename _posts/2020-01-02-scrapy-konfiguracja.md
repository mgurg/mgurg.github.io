---
layout: post
title: "Konfiguracja Scrapy (część I)"
categories: python
author: "Michał"
---
Opisałem drogę w wyniku której zacząłem używać scrapy, dzisiaj chciałym pokazać jak skonfigurowałem to narzędzie żeby móc pobierać dane z otomoto.

## Instalacja i pierwsze testy
Scrapy instalowałem z pomocą pip:

```batch
pip install Scrapy
```

Poprawnmość instalacji możemy sprawdzić przy pomocy komendy

```bash
C:\>scrapy --version
Scrapy 1.8.0 - no active project

Usage:
  scrapy <command> [options] [args]

Available commands:
  bench         Run quick benchmark test
  fetch         Fetch a URL using the Scrapy downloader
  genspider     Generate new spider using pre-defined templates
  runspider     Run a self-contained spider (without creating a project)
  settings      Get settings values
  shell         Interactive scraping console
  startproject  Create new project
  version       Print Scrapy version
  view          Open URL in browser, as seen by Scrapy

  [ more ]      More commands available when run from project directory

Use "scrapy <command> -h" to see more info about a command
```

### Scrapy - pierwszy test

Jeżeli nie ma potrzeby przetwarzania dużej ilości stron (lub tak jak u mnie chcesz sprawdzić czy to narzędzie zwróci oczekiwany wynik) to nie jest potrzebne konfigurowanie całego projektu. można przeprowadzić testy używając samej konsoli. Wystarczy rozpocząć od komendy `scrapy shell`

```batch
> scrapy shell
2020-01-09 18:40:34 [scrapy.utils.log] INFO: Scrapy 1.8.0 started (bot: scrapybot)

...

2020-01-09 18:40:35 [scrapy.extensions.telnet] INFO: Telnet console listening on 127.0.0.1:6023
[s] Available Scrapy objects:
[s]   scrapy     scrapy module (contains scrapy.Request, scrapy.Selector, etc)
[s]   crawler    <scrapy.crawler.Crawler object at 0x00C45C30>
[s]   item       {}
[s]   settings   <scrapy.settings.Settings object at 0x04871DB0>
[s] Useful shortcuts:
[s]   fetch(url[, redirect=True]) Fetch URL and update local objects (by default, redirects are followed)
[s]   fetch(req)                  Fetch a scrapy.Request and update local objects
[s]   shelp()           Shell help (print this help)
[s]   view(response)    View response in a browser
>>>
```
Pojawi się wtedy lista potrzebnych komend, spróbujmy pobrać przykładowa stronę komendą
```batch
>>> fetch("https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/")
```
Niestety pierwszy rezultat może być rozczarowujący, nie udało się pobrać niczego (przekroczony czas oczekiwania)
```batch
>>> fetch("https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/")
2020-01-09 18:51:05 [scrapy.core.engine] INFO: Spider opened
2020-01-09 18:54:05 [scrapy.downloadermiddlewares.retry] DEBUG: Retrying <GET https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/> (failed 1 times): User timeout caused connection failure: Getting https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/ took longer than 180.0 seconds..
2020-01-09 18:57:05 [scrapy.downloadermiddlewares.retry] DEBUG: Retrying <GET https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/> (failed 2 times): User timeout caused connection failure: Getting https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/ took longer than 180.0 seconds..
2020-01-09 19:00:05 [scrapy.downloadermiddlewares.retry] DEBUG: Gave up retrying <GET https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/> (failed 3 times): User timeout caused connection failure: Getting https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/ took longer than 180.0 seconds..
```

Zrobiłem ten test próbując inne adresy URL i w zależności od strony udawało się dane pobrać a raz nie. Okazało się że niektóre witryny bronią się przed dostępem do treści przy pomocy automatów. Rozwiązaniem okazało się podanie `user agent` :

```batch
 scrapy shell https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/ -s USER_AGENT='Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/46.0.2490.80 Safari/537.36'
```

```batch
2020-01-09 19:28:00 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/> (referer: None)
[s] Available Scrapy objects:
[s]   scrapy     scrapy module (contains scrapy.Request, scrapy.Selector, etc)
[s]   crawler    <scrapy.crawler.Crawler object at 0x03824250>
[s]   item       {}
[s]   request    <GET https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/>
[s]   response   <200 https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/>
[s]   settings   <scrapy.settings.Settings object at 0x043B1A50>
[s]   spider     <DefaultSpider 'default' at 0x47359f0>
[s] Useful shortcuts:
[s]   fetch(url[, redirect=True]) Fetch URL and update local objects (by default, redirects are followed)
[s]   fetch(req)                  Fetch a scrapy.Request and update local objects
[s]   shelp()           Shell help (print this help)
[s]   view(response)    View response in a browser
```

Podgląd pobranych danych w przeglądarce umożliwia komenda `view(response)`
Jeżeli chcemy zobaczyć kod strony to możemy go wyświetlić przy pomocy `print response.text`