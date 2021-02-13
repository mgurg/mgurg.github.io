---
layout: post
title: "FastApi: NGINX & Gunicorn"
categories: Python
author: "Michał"
---




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

Kod aplikacji:
```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "Hello World"}
```

Ręczne uruchomienie  (`~/py_env/www/app`):
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

Uruchominie 
```
sudo systemctl start fastapi
```


Finalny plik service:

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

[Dodatkowe informacje](https://www.fatalerrors.org/a/uvicorn-a-lightweight-and-fast-python-asgi-framework.html)