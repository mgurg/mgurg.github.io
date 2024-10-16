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
#After=network.target
After=multi-user.target
StartLimitBurst=60
StartLimitIntervalSec=60

[Service]
User=lambda
Group=www-data
WorkingDirectory=/var/www/fastapi_www/app
Environment="PATH=/var/www/fastapi_www/fast_ve/bin"
ExecStart=/var/www/fastapi_www/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker -b unix:fastapi.sock -m 007
Restart=on-failure
RestartSec=60

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
        # listen 5000;
        listen [::]:80;

        root /var/www/html;
        index index.html index.htm index.ngnix-debian.html;

        server_name 192.166.219.228 remontmaszyn.pl;

        location / {
                try_files $uri /index.html;
                # include proxy_params;
                # proxy_pass http://unix:/var/www/fastapi_www/app/fastapi.sock;
                }
}

server {
        listen 5000;
        server_name 192.166.219.228 remontmaszyn.pl;

        location / {
                proxy_pass http://unix:/var/www/fastapi_www/app/fastapi.sock;
        }
}
```

alternatywnie

```nginx
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        set $path_front /opt/fake-front;
        set $path_api /opt/fake-api;

        root $path_front;

        index index.html;

        server_name _;

        error_log  /var/log/nginx/error.log;
        access_log /var/log/nginx/access.log;

        location ~ ^/api/(.+\.php)$ {
                index index.php;
                root $path_api;
                try_files /$1 =404;
                fastcgi_split_path_info ^(.+?\.php)(/.*)$;
                fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
                include fastcgi_params;
                fastcgi_param  SCRIPT_NAME $1;
                fastcgi_param SCRIPT_FILENAME $path_api/$1;
                fastcgi_param PATH_INFO $fastcgi_path_info;
                error_log  /var/log/nginx/api.error.log;
                access_log /var/log/nginx/api.access.log;
        }

        location ~ ^/api/(.*) {
                index index.php;
                root $path_api;
                try_files $1 =404 /api/index.php?$args;
                error_log  /var/log/nginx/api.error.log;
                access_log /var/log/nginx/api.access.log;
        }

        location /favicon.ico {
                log_not_found off;
                access_log off;
        }

        location /robots.txt {
                allow all;
                log_not_found off;
                access_log off;
        }
        location / {
                index index.html
                root $path_front;
                try_files $uri $uri/ /index.php?$args;
                error_log  /var/log/nginx/front.error.log;
                access_log /var/log/nginx/front.access.log;
        }

        location ~ \.php$ {
                index index.php
                root $path_front;
                try_files $uri =404;
                fastcgi_split_path_info ^(.+?\.php)(/.*)$;
                fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
                include fastcgi_params;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                fastcgi_param PATH_INFO $fastcgi_path_info;
                error_log  /var/log/nginx/front.error.log;
                access_log /var/log/nginx/front.access.log;
        }
        location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
                index index.html
                root $path_front;
                expires max;
                log_not_found off;
                error_log  /var/log/nginx/front.error.log;
                access_log /var/log/nginx/front.access.log;
        }
}

```

Ubuntu 20.04 (remontmaszyn + imion)

```nginx
server {
        root /var/www/html;
        index index.html index.htm index.ngnix-debian.html;

        server_name imion.eu www.imion.eu;


        location / {
                root /var/www/html;
  try_files $uri $uri/ /index.html;
  add_header 'Access-Control-Allow-Origin' 'origin-list';
                proxy_set_header   Host             $host;
         proxy_set_header   X-Real-IP        $remote_addr;
         proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
         proxy_headers_hash_max_size 512;
       proxy_headers_hash_bucket_size 128; 
  }

#        location /idd {
#                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#                proxy_set_header Host $http_host;
#                proxy_http_version 1.1;
#  proxy_pass http://unix:/var/www/fastapi_www/app/fastapi.sock;
#        }

#        location /api {
#                include proxy_params;
#  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#                proxy_set_header Host $http_host;
#                proxy_http_version 1.1;
#                proxy_pass http://192.166.219.228:5000/api;
#        }


    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/imion.eu/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/imion.eu/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
        root /var/www/api;
        index api_index.html;
        server_name api.imion.eu www.api.imion.eu;

        location / {
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
                proxy_http_version 1.1;
                proxy_pass http://unix:/var/www/fastapi_www/app/fastapi.sock;
         proxy_set_header   Host             $host;
         proxy_set_header   X-Real-IP        $remote_addr;
         proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
       proxy_headers_hash_max_size 512;
       proxy_headers_hash_bucket_size 128; 
        }


    listen [::]:443 ssl; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/api.imion.eu/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/api.imion.eu/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}

server {
        root /var/www/manuto;
        index index.html index.htm index.ngnix-debian.html;

        server_name remontmaszyn.pl www.remontmaszyn.pl;


        location / {
                root /var/www/manuto;
                try_files $uri $uri/ /index.html;
                add_header 'Access-Control-Allow-Origin' 'origin-list';
                }




    listen 80; # managed by Certbot

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/remontmaszyn.pl/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/remontmaszyn.pl/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}
server {
    if ($host = www.imion.eu) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    if ($host = imion.eu) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


        listen 80;
        listen [::]:80;

        server_name imion.eu www.imion.eu;
    return 404; # managed by Certbot
}


server {
    if ($host = api.imion.eu) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


        listen 80;
        listen [::]:80;
        server_name api.imion.eu www.api.imion.eu;
    return 404; # managed by Certbot


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
