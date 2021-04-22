---
layout: post
title: "FastApi: NGINX & Gunicorn (II)"
categories: Python
author: "Michał"
---

W [I części](https://mgurg.github.io/python/2020/12/16/FastApi.html) zbudowałem mikro-serwis działający lokalnie. Teraz pora przenieś go do internetu.

## Serwer WWW - NGINX

Do wyboru jest Apache i NGINX. Rzut monetą wskazał na drugi webserver (ok, tak naprawdę to wybrany z premedytacją). 


Instalacja serwera:

```bash
sudo apt-get install nginx
```

Po wpisaniu IP servera (`192.166.219.228`) powinien pojawić się komunikat powitalny nginx

```
Welcome to ngnix!

If you see this page, the ngnix web server is successfully installed and working. 
Further configuration is required.
```

W razie potrzeby można wymusić start/restart/stop serwera ręcznie poniższymi komendami:

```bash
sudo systemctl start ngnix
sudo systemctl restart ngnix
sudo systemctl stop ngnix
```

Status serwer nginx:

```bash
systemctl status nginx
```

Status usługi:

```bash
sudo systemctl status nginx
```

```bash
* nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2020-07-11 15:08:47 CEST; 4min 24s ago
       Docs: man:nginx(8)
   Main PID: 15737 (nginx)
      Tasks: 2 (limit: 4657)
     Memory: 6.5M
     CGroup: /system.slice/nginx.service
             |-15737 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
             `-15738 nginx: worker process
```


### Firewall

Do konfiguracji firewalla można użyć (wyświetli dostępne domyślnie tryby):

```bash
sudo ufw app list
```

Wybranie któregoś z nich odbywa się poprzez:

```bash
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
```

Sprawdzenie statusu:

```bash
sudo ufw enable
sudo ufw status
```

Wynik:

```
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx Full                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx Full (v6)            ALLOW       Anywhere (v6)  
```

## Przygotowanie środowiska

Kod serwisu będzie znajdował się w `var/www/`:

```bash
cd /var/www
git clone -b <branch> https://github.com/<repo>.git
```

Utworzenie wirtualnego środowiska na serwerze VPS:

```bash
python3 -m venv --copies fast_ve
source fast_ve/bin/activate
pip install --upgrade pip
pip install setuptools
pip install  wheel
```

Instalacja FastApi,  Gunicorn, Unicorn
```bash
pip install fastapi[all]
pip install gunicorn
pip install unicorn
```

Dotychczasowy kod aplikacji:
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hello World"}
```

Tak jak w przypadku instrukcji dla Flask zaczniemy testy od ręcznego uruchomienia  (`/var/www/fastapi_www/app`):
```bash
gunicorn main:app -w 2 -k uvicorn.workers.UvicornWorker -b "0.0.0.0:5000"
```

Musimy jeszcze odblokować port 5000:

```bash
sudo ufw allow 5000
```

Teraz przejście na `remontmaszyn.pl:5000` powinno pokazać naszą stronę.


### Konfiguracja systemd

Utworzenie pliku 
```bash
sudo nano /etc/systemd/system/fastapi.service
```

o zawartości:

```bash
[Unit]
Description=Gunicorn instance daemon to serve FastAPI
After=network.target

[Service]
User=lambda
Group=www-data
WorkingDirectory=/var/www/fastapi_www/app
Environment="PATH=/var/www/fastapi_www/fast_ve/bin"
ExecStart=/var/www/fastapi_www/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker -b "0.0.0.0:5000"

[Install]
WanterBy=multi-user.target
```

Jest to odpowiednik ręcznej komendy którą testowałem akapit wcześniej.

Uruchomianie 

```bash
sudo systemctl start fastapi
```

Ponieważ strona ma działać na standardowym porcie i być obsługiwana przez NGNIX to potrzebujemy wprowadzić kilka zmian:

### Konfiguracja NGINX

Utworzenie pliku konfiguracyjnego

```bash
cd /etc/nginx/sites-available/
sudo nano helloworld
```

Zawartość:

```nginx
server {
        listen 80;
        listen [::]:80;

        root /home/lambda/py_env/www_env/www/;
        index index.html index.htm index.ngnix-debian.html;

        server_name 192.166.219.228 remontmaszyn.pl;

        location / {
                include proxy_params;
                proxy_pass http://unix:/home/lambda/py_env/www/app/fastapi.sock;
 }
}
```

Żeby zacząć korzystać z nowych ustawień dla serwera należy stworzyć link konfiguracji ( [dowiązanie symboliczne](https://pl.wikipedia.org/wiki/Dowiązanie_symboliczne) ) w katalogu sites-enabled nginx.

```bash
sudo ln -s /etc/nginx/sites-available/helloworld /etc/nginx/sites-enabled/
```

Z niewiadomego (wiadomego: *literówka* ) powodu powyższa komenda nie chciała tym razem działać obszedłem to poprzez:

```bash
cd /etc/nginx/sites-enabled/
sudo ln -s ../sites-available/helloworld
ls -l
```

Rezultat:

```bash
total 0
lrwxrwxrwx 1 root root 34 Jul 13 14:35 default -> /etc/nginx/sites-available/default
lrwxrwxrwx 1 root root 29 Jul 15 10:35 helloworld -> ../sites-available/helloworld
```

Po wszystkich zmianach należy przeprowadzić test składni pliku konfiguracyjnego nginx files:

```bash
sudo nginx -t
```

Jeżeli wszystko jest ok, to można przeładować serwer żeby wczytać nowe ustawienia:

```
sudo systemctl restart nginx
```

Jeżeli wszystko poszło ok, to po wejściu na stronę główną pojawi się komunikat.... `502 Bad gateway`. Co oznacza że pora wrócić do konfiguracji Gunicorn.

### Uvicorn - finałowe zmiany

Ponieważ strona ma działać na standardowym porcie i być obsługiwana przez NGNIX to finalnie plik service wygląda jak poniżej:

```bash
[Unit]
Description=Gunicorn instance daemon to serve FastAPI
After=network.target

[Service]
User=lambda
Group=www-data
WorkingDirectory=/var/www/fastapi_www/app
Environment="PATH=/var/www/fastapi_www/fast_ve/bin"
ExecStart=/var/www/fastapi_www/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker -b unix:fastapi.sock -m 007

[Install]
WanterBy=multi-user.target
```

Weryfikacja poprawności działania:

```bash
sudo systemctl status fastapi
```

```bash
● fastapi.service - Gunicorn instance daemon to serve FastAPI
     Loaded: loaded (/etc/systemd/system/fastapi.service; static; vendor preset: enabled)
     Active: active (running) since Thu 2021-04-22 12:13:30 CEST; 5s ago
   Main PID: 50936 (gunicorn)
      Tasks: 5 (limit: 4656)
     Memory: 101.8M
     CGroup: /system.slice/fastapi.service
             ├─50936 /var/www/fastapi_www/fast_ve/bin/python3 /var/www/fastapi_www/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker -b unix:fastapi.sock -m 007
             ├─50948 /var/www/fastapi_www/fast_ve/bin/python3 /var/www/fastapi_www/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker -b unix:fastapi.sock -m 007
             ├─50949 /var/www/fastapi_www/fast_ve/bin/python3 /var/www/fastapi_www/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker -b unix:fastapi.sock -m 007
             ├─50950 /var/www/fastapi_www/fast_ve/bin/python3 /var/www/fastapi_www/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker -b unix:fastapi.sock -m 007
             └─50951 /var/www/fastapi_www/fast_ve/bin/python3 /var/www/fastapi_www/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker -b unix:fastapi.sock -m 007

```



[Dodatkowe informacje](https://www.fatalerrors.org/a/uvicorn-a-lightweight-and-fast-python-asgi-framework.html)

### Bonus Vue.js

Konfiguracja dla przypadku gdzie w pliku `index.html` mamy frontend w Vue.js, a API chcemy przenieść na port 5000

```nginx
server {
        listen 80;
        listen 5000;
        listen [::]:80;

        root /var/www/html;
        index index.html index.htm index.ngnix-debian.html;

        server_name 192.166.219.228 remontmaszyn.pl;

        location /api {
                #try_files $uri /index.html
                include proxy_params;
                proxy_pass http://unix:/var/www/fastapi_www/app/fastapi.sock;
 }
        location / {
                try_files $uri /index.html;
                # include proxy_params;
                # proxy_pass http://unix:/var/www/fastapi_www/app/fastapi.sock;
                }

}

```



## Wdrażanie zmian na serwerze

Ponieważ kod serwisu znajduje się w repozytorium to wszelkie zmiany na serwer można wprowadzać pobierając aktualną wersję z gałęzi `master`

```bash
sudo systemctl stop fastapi
git pull
sudo systemctl start fastapi
```

#### Więcej

➡️ [Deploy Python APIs to the Web With Linux](https://towardsdatascience.com/deploy-python-apis-to-the-web-with-linux-2f4c7be8a76d)