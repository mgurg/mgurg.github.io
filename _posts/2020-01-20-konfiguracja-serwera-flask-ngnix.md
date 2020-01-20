---
layout: post/
title: "Konfiguracja serwera VPS II: ngnix + flask"
categories: python
author: "Michał"
---

# Serwer WWW – ngnix

Do wyboru jest Apache i ngnix. Rzut monetą wskazał na drugi webserver. Komenda do instalacji:


```bash
sudo apt-get install nginx
```

Po wpisaniu IP servera (`192.166.219.228`) powinien pojawić się komunikat powitalny ngnix


```
Welcome to nginx!

If you see this page, the nginx web server is successfully installed and working. Further configuration is required.
```

Ewentualnie można wymusić start/restart/stop serwera ręcznie

```bash
sudo systemctl start nginx
sudo systemctl restart nginx
sudo systemctl stop nginx

```

Status serwer ngnix

```bash
systemctl status nginx
```

Konfiguracja firewalla:

```bash
sudo ufw app list
```

Konfiguracja ngnix (w osobnym pliku, bez modyfikacji podstawowej konfiguracji)

```bash
sudo nano /etc/nginx/sites-available/helloworld
```


Zawartość pliku konfiguracyjnego

```bash
server {
        listen 80;
        listen [::]:80;

        root /home/lambda/environments/ml_env/www/helloworld/;
        index index.html index.htm index.nginx-debian.html;

        server_name 192.166.219.228;

        location / {
                try_files $uri $uri/ =404;
        }
}
```


Next, let’s enable the file by creating a link from it to the sites-enabled directory, which Nginx reads from during startup:


```bash
sudo ln -s /etc/nginx/sites-available/helloworld /etc/nginx/sites-enabled/
```

default: Will respond to any requests on port 80 that do not match the other two blocks.

To avoid a possible hash bucket memory problem that can arise from adding additional server names, it is necessary to adjust a single value in the /etc/nginx/nginx.conf file. Open the file:

```bash
sudo nano /etc/nginx/nginx.conf
```

Find the server_names_hash_bucket_size directive and remove the # symbol to uncomment the line:
/etc/nginx/nginx.conf

```
...
http {
    ...
    server_names_hash_bucket_size 64;
    ...
}
...
```
Test składni pliku konfiguracyjnego nginx files:

```
sudo nginx -t
```

Jeżeli wszystko jest ok, tomożna przeładować serwer
```
sudo systemctl restart nginx
```

Po wejściu na stronę główną pojawi sie podmieniona strona.

Pisane na podstawie: 
https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04
https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-uswgi-and-nginx-on-ubuntu-18-04
https://vladikk.com/2013/09/12/serving-flask-with-nginx-on-ubuntu/


### Instalacja Flask

Prerequisites: insatlacja `wheel`

Komenda do instalacji:

```
pip install flask
```

Stworzenie folderu w ktorym będzie się znajdowała strona "Hello World!" 


```
sudo mkdir ./environments/ml_env/www/
sudo mkdir ./environments/ml_env/www/helloworld/
```

zmiana uprawnien do folderu:
```
sudo chown -R lambda:lambda ~/environments/ml_env/www/
```

Utworzenie pliku `hello.py` w `~/environments/ml_env/www/helloworld/`

```
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=8080)
```
