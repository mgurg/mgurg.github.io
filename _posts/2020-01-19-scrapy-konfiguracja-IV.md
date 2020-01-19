---
layout: post
title: "Konfiguracja Scrapy (część III)"
categories: python
author: "Michał"
---

### Automatyczne pobieranie stron (cron + Scrapy)

Ostatni wpis poświęcony Scrapy. Skoro doszedłem do miejsca w którym mam działający skrypt do pobierania treści strony, oraz skonfigurowany serwer VPS, to kolejnym krokiem jest sprawienie żeby zadanie to wykonywało się samo co jakiś czas.


W moim przypadku będzie to raz na dobę

Sprawdzenie lokalizacji interpretera Bash

```
$ which bash
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

Zrobienie z lpiku detdata.sh pliku wykonywalnego
```
$ chmod +x hello_world.sh 
```

### cron

Dodanie zadania uruchamianego codziennie o 4:33

```
crontab -e
```

```
# Edit this file to introduce tasks to be run by cron.
#
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
#
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').#
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
#
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
#
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
#
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command
MAILTO=""
33 4 * * * ~/scrapy/getdata.sh
```

Weryfikacja działania cron

```
sudo grep CRON /var/log/syslog
```

```
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
