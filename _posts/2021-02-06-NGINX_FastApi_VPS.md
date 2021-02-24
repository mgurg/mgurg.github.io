---
layout: post
title: "FastApi: NGINX & Gunicorn (II)"
categories: Python
author: "Michał"
---

W [I części](https://mgurg.github.io/python/2020/12/16/FastApi.html) zbudowałem mikro-serwis działający lokalnie. Teraz pora przenieś go do internetu.

## Pzzygotowanie środowiska


Utworzenie wirtualnego środowiska na serwerze VPS:

```bash
python3 -m venv --copies fast_ve
source fast_ve/bin/activate
pip install --upgrade pip
pip install setuptools
pip install  wheel
```

Instalacja FastApi, Unicorn, Gunicorn
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

Tkaj ka w przypadku instrukcji dla Flask zaczniemy testy od ręcznego uruchomienia  (`~/py_env/www/app`):
```bash
gunicorn main:app -w 2 -k uvicorn.workers.UvicornWorker -b "0.0.0.0:5000"
```


Przejście na `remontmaszyn.pl:5000` powinno pokazać testową stronę-zaślepkę


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
WorkingDirectory=/home/lambda/py_env/www/app
Environment="PATH=/home/lambda/py_env/fast_ve/bin"
ExecStart=/home/lambda/py_env/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker -b "0.0.0.0:5000"

[Install]
WanterBy=multi-user.target
```

Jest to odpowiednik ręcznej komendy którą testowałem akapit wcześniej.

Uruchomianie 

```bash
sudo systemctl start fastapi
```


Ponieważ strona ma działać na standardowym porcie i być obsługiwana przez NGNIX to finalnie plik service wygląda jak poniżej:

```bash
[Unit]
Description=Gunicorn instance daemon to serve FastAPI
After=network.target

[Service]
User=lambda
Group=www-data
WorkingDirectory=/home/lambda/py_env/www/app
Environment="PATH=/home/lambda/py_env/fast_ve/bin"
ExecStart=/home/lambda/py_env/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker -b unix:fastapi.sock -m 007

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
     Active: active (running) since Mon 2021-02-15 21:10:41 CET; 1 weeks 1 days ago
   Main PID: 266519 (gunicorn)
      Tasks: 5 (limit: 4656)
     Memory: 116.7M
     CGroup: /system.slice/fastapi.service
             ├─266519 /home/lambda/py_env/fast_ve/bin/python3 /home/lambda/py_env/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.work>
             ├─305477 /home/lambda/py_env/fast_ve/bin/python3 /home/lambda/py_env/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.work>
             ├─305478 /home/lambda/py_env/fast_ve/bin/python3 /home/lambda/py_env/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.work>
             ├─305479 /home/lambda/py_env/fast_ve/bin/python3 /home/lambda/py_env/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.work>
             └─305480 /home/lambda/py_env/fast_ve/bin/python3 /home/lambda/py_env/fast_ve/bin/gunicorn main:app -w 4 -k uvicorn.work>
```



[Dodatkowe informacje](https://www.fatalerrors.org/a/uvicorn-a-lightweight-and-fast-python-asgi-framework.html)

## Konfiguracja NGINX

Utworzenie pliku konfiguracyjnego (analogicznie do [Flask](https://mgurg.github.io/python/2019/12/23/VPS-konfiguracja-NGINX-Flask-SSL.html)):

```bash
cd /etc/nginx/sites-available/
sudo nano helloworld
```

Zawartość:

```
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

## Wdrażanie zmian na serwerze

Ponieważ kod serwisu znajduje się w repozytorium to wszelkie zmiany na serwer można wprowadzać pobierając aktualną wersję z gałęzi `master`

```bash
sudo systemctl stop fastapi
git pull
sudo systemctl start fastapi
```

