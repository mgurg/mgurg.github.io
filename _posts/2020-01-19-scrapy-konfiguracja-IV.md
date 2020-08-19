---
layout: post
title: "Konfiguracja Scrapy (część IV) - cron"
categories: scrapy
author: "Michał"
---



W ostatniej części [opisu scrapy](https://mgurg.github.io/python/2020/01/15/scrapy-konfiguracja-III.html) dotarłem do miejsca w którym mam działający skrypt do pobierania treści strony. Mam też [skonfigurowany serwer VPS](https://mgurg.github.io/python/2020/01/16/SSH-VPS-konfiguracja-serwera.html). Kolejnym krokiem jest sprawienie żeby zadanie pobierania ogłoszeń wykonywało się samo co jakiś czas. W moim przypadku będzie to raz na dobę, w środku nocy.

## Automatyczne pobieranie stron (cron + Scrapy)

### Skrypt bash

Sprawdzenie lokalizacji interpretera Bash

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

```
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

