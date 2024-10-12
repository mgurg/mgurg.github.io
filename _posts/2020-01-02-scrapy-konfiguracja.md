---
layout: post
title: "Konfiguracja Scrapy"
categories: scrapy
author: "Michał"
---

Opisałem drogę w wyniku której zacząłem używać scrapy, dzisiaj chciałym pokazać jak skonfigurowałem to narzędzie żeby móc pobierać dane z otomoto.

## Instalacja i pierwsze testy

Scrapy instalowałem z pomocą pip:

```bash
pip install Scrapy
```

Poprawność instalacji możemy sprawdzić przy pomocy komendy:

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

```bash
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

```bash
>>> fetch("https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/")
```

Niestety pierwszy rezultat może być rozczarowujący, nie udało się pobrać niczego (przekroczony czas oczekiwania)

```bash
>>> fetch("https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/")
2020-01-09 18:51:05 [scrapy.core.engine] INFO: Spider opened
2020-01-09 18:54:05 [scrapy.downloadermiddlewares.retry] DEBUG: Retrying <GET https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/> (failed 1 times): User timeout caused connection failure: Getting https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/ took longer than 180.0 seconds..
2020-01-09 18:57:05 [scrapy.downloadermiddlewares.retry] DEBUG: Retrying <GET https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/> (failed 2 times): User timeout caused connection failure: Getting https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/ took longer than 180.0 seconds..
2020-01-09 19:00:05 [scrapy.downloadermiddlewares.retry] DEBUG: Gave up retrying <GET https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/> (failed 3 times): User timeout caused connection failure: Getting https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/ took longer than 180.0 seconds..
```

Zrobiłem ten test próbując inne adresy URL i w zależności od strony udawało się dane pobrać a raz nie. Okazało się że niektóre witryny bronią się przed dostępem do treści przy pomocy automatów. Rozwiązaniem okazało się podanie `user agent` :

```bash
 scrapy shell https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/ -s USER_AGENT='Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/46.0.2490.80 Safari/537.36'
```

```bash
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
Jeżeli chcemy zobaczyć kod strony to możemy go wyświetlić przy pomocy `print(response.text)`

---

## część II

Ponieważ  pierwsze próby używania Scrapy wyszły pomyślnie, to zacząłem dostosowywać to narzędzie do swoich potrzeb.

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

---

## częsć III

W części II  opisałem jak zbudować projekt na dysku, który nie działał. W tej części dokończę opis konfiguracji do etapu "produkcyjnego". Zacznę od prostego pająka:

```python
import scrapy

class OtomotoSpider(scrapy.Spider):
    name = 'otomoto'
    allowed_domains = ['otomoto.pl']
    start_urls = ['http://otomoto.pl/']

    def parse(self, response):
        pass
```

Pierwszym krokiem będzie dodanie `user-agent` tak żeby pająk zaczął spełniać swoje zadanie.

```python
# -*- coding: utf-8 -*-
import scrapy
import json
import datetime

class OtomotoSpider(scrapy.Spider):
    name = 'otomoto'

    custom_settings = {
        'USER_AGENT': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/46.0.2490.80 Safari/537.36',
    }

    allowed_domains = ['otomoto.pl']
    start_urls = ['https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/']

    def parse(self, response):
        print(response.status)

        lastPage =  response.xpath("//span[@class='page']//text()").extract()[-1]# extract offer list
        #print(lastPage)

        offers = response.xpath("//div[@class='offers list']").extract() # extract offer list
        #print(type(offers))

        data = json.loads(response.xpath('//script[@type="application/ld+json"]//text()').extract_first()) # extract of ld+json from page
        #print(type(data))

        current_page = response.meta.get("page", 1)
        next_page = current_page + 1

        if current_page < int(lastPage):
            isTruncated = True
        else:
            isTruncated = False

        if isTruncated == True:
            yield scrapy.Request(
                url="https://www.otomoto.pl/osobowe/toyota/yaris/ii-2005-2011/?page={page}".format(page=next_page),
                callback=self.parse,
                meta={'page': next_page},
                dont_filter=True
        )

        if isTruncated == False:
            print(lastPage)


        # with open('page'+str(current_page)+'.html', 'wb') as html_file:
        #     html_file.write(response.body)
        #print("procesing:"+response.url)

        now = datetime.datetime.now()

        with open('./data/otomoto_'+str(now.date())+'.html', 'a',encoding='utf-8') as f:
            for item in offers:
                f.write("%s\n" % item)

        with open('./data/otomoto_'+str(now.date())+'.json', 'a') as j:
            # this would place the entire output on one line
            # use json.dump(lista_items, f, indent=4) to "pretty-print" with four spaces per indent
            json.dump(data, j, indent=4)

    # def parse_item(self, response):
 #       with open('page.html', 'wb') as html_file:
 #          html_file.write(response.body)
    #     #pass

```

---

## Część IV

Kolejnym krokiem jest sprawienie żeby zadanie pobierania ogłoszeń wykonywało się samo co jakiś czas. W moim przypadku będzie to raz na dobę, w środku nocy. Korzystam przy tym z dobrodziejstwa własnego serwera VPS.

### Automatyczne pobieranie stron (cron + Scrapy)

Skrypt bash - Sprawdzenie lokalizacji interpretera Bash

```bash
which bash
```

Utworzenie pliku `getdata.sh`

```bash
#!/bin/bash
source ~/environments/ml_env/bin/activate
PATH=$PATH:~/environments/ml_env/bin/python
export PATH
cd ~/scrapy/
scrapy runspider otomoto.py
```

Zrobienie z pliku `getdata.sh` pliku wykonywalnego:

```bash
chmod +x hello_world.sh 
```

Dla pliku python skrypt bash będzie miał postać:

```bash
#!/bin/bash
source ~/environments/ml_env/bin/activate
PATH=$PATH:~/environments/ml_env/bin/python
export PATH

python ~/scrapy/carDataParser.py
```

Bardzo istotną kwestią jest kodowanie znaków końca linii. W systemach unixowych jest to wyłącznie znak `LF`.

Jeżeli skrypt nie działa (przykładowy błąd: `-bash: ./parsedata.sh: /bin/bash^M: bad interpreter: No such file or directory`) to warto to sprawdzić. Znaki końca linii można poprawić komendą:

```
sed -i -e 's/\r$//' plotdata.sh
```

lub z pomocą `dos2unix file.txt`

Skrypt bash można uruchomić ręcznie dodając `./` przed nazwą pliku wykonywalnego `./plotdata.sh`

### cron

Zadanie będzie wykonywane codziennie o 4:33 w nocy.

```
crontab -e
```

Składnia zadań wygląda następująco:

```
*     *     *     *     *  komenda do wykonania
^     ^     ^     ^     ^
|     |     |     |     |
|     |     |     |     +----- dzień tygodnia (0 - 7) (niedziela=0, poniedziałek=1, wtorek=2, ..., niedziela=7)
|     |     |     |     
|     |     |     +------- miesiąc (1 - 12)
|     |     |     
|     |     +--------- dzień miesiąca (1 - 31)
|     |     
|     +----------- godzina (0 - 23)
|     
+------------- minuta (0 - 59)

Zamiast pierwszych pięciu pól, można użyć jednego z ośmiu łańcuchów specjalnych:

łańcuch        znaczenie
-------        ---------
@reboot        uruchamia raz, przy rozruchu;
@yearly        uruchamia raz w roku, "0 0 1 1 *";
@annually      (to samo co @yearly);
@monthly       uruchamia raz w miesiącu, "0 0 1 * *";
@weekly        uruchamia raz w tygodniu, "0 0 * * 0";
@daily         uruchamia raz na dzień, "0 0 * * *";
@midnight      (to samo co @daily);
@hourly        uruchamia raz na godzinę, "0 * * * *".
```

Gotowe zadanie wygląda następująco:

```
# (...)
# m h  dom mon dow   command
MAILTO=""
33 4 * * * ~/scrapy/getdata.sh
```

Ułatwieniem przy definiowaniu powtarzalny reguł w cron może być strona [crontab.guru](https://crontab.guru/#33_4_*_*_*)

Weryfikacja działania cron:

```bash
sudo grep CRON /var/log/syslog
```

```bash
Jan 19 13:30:01 michal CRON[29749]: (lambda) CMD (cd ~/scrapy/ && getdata.sh)
Jan 19 13:30:01 michal CRON[29748]: (CRON) info (No MTA installed, discarding output)
Jan 19 13:33:01 michal CRON[29825]: (lambda) CMD (cd ~/scrapy/ && getdata.sh)
Jan 19 13:33:01 michal CRON[29824]: (CRON) info (No MTA installed, discarding output)
Jan 19 13:40:01 michal CRON[29992]: (lambda) CMD (cd ~/scrapy/ && getdata.sh >/dev/null 2>&1)
Jan 19 13:50:01 michal CRON[30207]: (lambda) CMD (cd ~/scrapy/ && getdata.sh >> /var/log/somelogfile.log)
Jan 19 13:50:01 michal CRON[30206]: (CRON) info (No MTA installed, discarding output)
Jan 19 14:14:01 michal CRON[30699]: (lambda) CMD (cd ~/scrapy/ && getdata.sh)
Jan 19 14:17:01 michal CRON[30767]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
Jan 19 14:24:01 michal CRON[30964]: (lambda) CMD (cd ~/scrapy/getdata.sh)
Jan 19 14:27:01 michal CRON[31037]: (lambda) CMD (~/scrapy/getdata.sh)
Jan 19 14:33:01 michal CRON[31247]: (lambda) CMD (~/scrapy/getdata.sh)
Jan 19 15:17:01 michal CRON[32144]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
```
