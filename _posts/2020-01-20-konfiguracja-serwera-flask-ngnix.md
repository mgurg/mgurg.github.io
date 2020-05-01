---
layout: post
title: "Konfiguracja serwera VPS II: nginx + gunicorn + flask"
categories: python
author: "Michał"
---



## Teoria

📺 [WSGI for Web Developers](https://www.youtube.com/watch?v=WqrCnVAkLIo)

## Serwer WWW – nginx

Do wyboru jest Apache i nginx. Rzut monetą wskazał na drugi webserver (ok, tak naprawdę to wybrany z premedytacją). Komenda do instalacji:


```bash
sudo apt-get install ngnix
```

Po wpisaniu IP servera (`192.166.219.228`) powinien pojawić się komunikat powitalny nginx


```
Welcome to ngnix!

If you see this page, the ngnix web server is successfully installed and working. Further configuration is required.
```

W razie potrzeby można wymusić start/restart/stop serwera ręcznie poniższymi komendami:

```bash
sudo systemctl start ngnix
sudo systemctl restart ngnix
sudo systemctl stop ngnix
```

Status serwer nginx:

```bash
systemctl status ngnix
```

### Firewall

Do konfiguracji firewalla można użyć (wyświetli dostępne domyślnie tryby):

```bash
sudo ufw app list
```

Wybranie któregoś z nich odbywa się poprzez:

```bash
sudo ufw allow 'Nginx Full'
```

Sprawdzenie statusu:

```bash
sudo ufw enable
sudo ufw status
```

### Konfiguracja podstawowa

Stworzenie folderu w którym będzie się znajdowała strona "Hello World!" 


```bash
sudo mkdir ./environments/ml_env/www/
sudo mkdir ./environments/ml_env/www/helloworld/
```

Konfiguracja nginx (utworzona w osobnym pliku, bez modyfikacji domyślnej konfiguracji)

```bash
sudo nano /etc/ngnix/sites-available/helloworld
```


Zawartość pliku konfiguracyjnego

```bash
server {
        listen 80;
        listen [::]:80;

        root /home/lambda/environments/ml_env/www/helloworld/;
        index index.html index.htm index.ngnix-debian.html;

        server_name 192.166.219.228 nazwadomeny.pl;

        location / {
                try_files $uri $uri/ =404;
        }
}
```

Żeby zacząć korzystać z nowych ustawień dla serwera należy stworzyć link konfiguracji ( [dowiązanie symboliczne](https://pl.wikipedia.org/wiki/Dowi%C4%85zanie_symboliczne) ) w katalogu sites-enabled nginx.


```bash
sudo ln -s /etc/ngnix/sites-available/helloworld /etc/ngnix/sites-enabled/
```

Gdy używamy długich nazw domen, nginx potrafi zgłosić błąd `could not build the server_names_hash, you should increase server_names_hash_bucket_size: 32` można temu zapobiec poprzez zmianę ustawień w :

```bash
sudo nano /etc/nginx/nginx.conf
```

Trzeba zlokalizować `server_names_hash_bucket_size` usunąć `#` i zwiększyć wartość z 32 do 64:

```
...
http {
    ...
    server_names_hash_bucket_size 64;
    ...
}
...
```
Po wszystkich zmianach należy przeprowadzić test składni pliku konfiguracyjnego nginx files:

```bash
sudo nginx -t
```

Jeżeli wszystko jest ok, to można przeładować serwer żeby wczytać nowe ustawienia:
```bash
sudo systemctl restart nginx
```

Po wejściu na stronę główną pojawi się podmieniona strona.

Opisane na podstawie: [How To Install Nginx on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04) 


## Instalacja Flask

Prerequisites: instalacja `wheel`


```python
pip install wheel
```
Komenda do instalacji:

```python
pip install flask
```

Stworzenie folderu w którym będzie się znajdowała strona "Hello World!" 


```bash
sudo mkdir ./environments/ml_env/www/
sudo mkdir ./environments/ml_env/www/helloworld/
```

zmiana uprawnień do folderu:
```bash
sudo chown -R lambda:lambda ~/environments/ml_env/www/
```

Utworzenie pliku `myproject.py` w `~/environments/ml_env/www/helloworld/` o zawartości:

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "<h1 style='color:blue'>Hello There!</h1>"

if __name__ == "__main__":
    app.run(host='0.0.0.0')
```

W celu przetestowania pliku należy go wykonać:

```bash
python myproject.py
```

```bash
python myproject.py * Serving Flask app "myproject" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)

```

Po wpisaniu w przeglądarkę adresu `http://192.166.219.228:5000/` pojawi się zmieniona podstrona (niebieski napis **Hello There!**)

Po udanym teście można go przerwać poprzez `CTRL-C` , strona testowa przestanie być osiągalna.

## Instalacja Gunicorn

Instalacja:

```bash
pip install gunicorn
```

Następnie należy stworzyć **WSGI Entry Point** komendą `nano wsgi.py` o zawartości:

```bash
from myproject import app

if __name__ == "__main__":
    app.run()
```

Będąc w folderze w którym jest projekt wykonujemy test komendą

```bash
cd ~/environments/ml_env/www/helloworld/
gunicorn --bind 0.0.0.0:5000 wsgi:app
```

W rezultacie powinniśmy otrzymać informacje o statusie, bez żadnego błędu:

```bash
[2020-01-21 11:27:49 +0100] [25323] [INFO] Starting gunicorn 20.0.4
[2020-01-21 11:27:49 +0100] [25323] [INFO] Listening at: http://0.0.0.0:5000 (25323)
[2020-01-21 11:27:49 +0100] [25323] [INFO] Using worker: sync
[2020-01-21 11:27:49 +0100] [25327] [INFO] Booting worker with pid: 25327
```

Pod adresem `http://192.166.219.228:5000/`  powinien ponownie być widoczny niebieski napis  **Hello There!**

Zakończenie działania: `CTRL-C`

```bash
[2020-01-21 11:38:44 +0100] [25323] [INFO] Handling signal: int
[2020-01-21 11:38:44 +0100] [25327] [INFO] Worker exiting (pid: 25327)
[2020-01-21 11:38:44 +0100] [25323] [INFO] Shutting down: Master
```

Deaktywacja wirtualnego środowiska:

```
deactivate
```

### Konfiguracja

Stworzenie pliku *systemd unit* pozwoli systemowi init Ubuntu na automatyczne uruchomienie Gunicorna i obsługę aplikacji Flask przy każdym uruchomieniu serwera.

Należy stworzyć plik o rozszerzeniu `.service` w katalogu `/etc/systemd/system`:

```bash
sudo nano /etc/systemd/system/helloworld.service
```

Zawartość, sekcja `[Unit]`:

```b
[Unit]
Description=Gunicorn instance to serve helloworld
After=network.target
```

Sekcja `[Service]`:

```
[Unit]
Description=Gunicorn instance to serve helloworld
After=network.target

[Service]
User=lambda
Group=www-data

WorkingDirectory=/home/lambda/environments/ml_env/www/helloworld/
Environment="PATH=/home/lambda/environments/ml_env/bin"
ExecStart=/home/lambda/environments/ml_env/bin/gunicorn --workers 3 --bind unix:helloworld.sock -m 007 wsgi:app

```

Sekcja  `[Install]`:

```
[Unit]
Description=Gunicorn instance to serve helloworld
After=network.target

[Service]
User=lambda
Group=www-data

WorkingDirectory=/home/lambda/environments/ml_env/www/helloworld/
Environment="PATH=/home/lambda/environments/ml_env/bin"
ExecStart=/home/lambda/environments/ml_env/bin/gunicorn --workers 3 --bind unix:helloworld.sock -m 007 wsgi:app

[Install]
WantedBy=multi-user.target
```

To co jest powyżej to kompletny plik usługi `systemd`. Można teraz uruchomić usługę gunicorn

```bash
sudo systemctl start helloworld
sudo systemctl enable helloworld
```

W wyniku działania ostatniej komendy powinniśmy otrzymać informację

```bash
Created symlink /etc/systemd/system/multi-user.target.wants/helloworld.service -> /etc/systemd/system/helloworld.service.

```

Sprawdzenie statusu:

```bash
sudo systemctl status helloworld
```

W wyniku jej działania powinniśmy otrzymać:

```bash


   Loaded: loaded (/etc/systemd/system/helloworld.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2020-01-21 11:57:04 CET; 1min 59s ago
 Main PID: 25865 (gunicorn)
    Tasks: 4 (limit: 4681)
   CGroup: /system.slice/helloworld.service
           |-25865 /home/lambda/environments/ml_env/bin/python3.6 /home/lambda/environments/ml_env/bin/gunicorn --workers 3 --bind unix:myproject.sock -m 007 wsgi:app
           |-25867 /home/lambda/environments/ml_env/bin/python3.6 /home/lambda/environments/ml_env/bin/gunicorn --workers 3 --bind unix:myproject.sock -m 007 wsgi:app
           |-25868 /home/lambda/environments/ml_env/bin/python3.6 /home/lambda/environments/ml_env/bin/gunicorn --workers 3 --bind unix:myproject.sock -m 007 wsgi:app
           -25870 /home/lambda/environments/ml_env/bin/python3.6 /home/lambda/environments/ml_env/bin/gunicorn --workers 3 --bind unix:myproject.sock -m 007 wsgi:app

Jan 21 11:57:04 michal systemd[1]: Started Gunicorn instance to serve helloworld.
Jan 21 11:57:04 michal gunicorn[25865]: [2020-01-21 11:57:04 +0100] [25865] [INFO] Starting gunicorn 20.0.4
Jan 21 11:57:04 michal gunicorn[25865]: [2020-01-21 11:57:04 +0100] [25865] [INFO] Listening at: unix:myproject.sock (25865)
Jan 21 11:57:04 michal gunicorn[25865]: [2020-01-21 11:57:04 +0100] [25865] [INFO] Using worker: sync
Jan 21 11:57:04 michal gunicorn[25865]: [2020-01-21 11:57:04 +0100] [25867] [INFO] Booting worker with pid: 25867
Jan 21 11:57:04 michal gunicorn[25865]: [2020-01-21 11:57:04 +0100] [25868] [INFO] Booting worker with pid: 25868
Jan 21 11:57:04 michal gunicorn[25865]: [2020-01-21 11:57:04 +0100] [25870] [INFO] Booting worker with pid: 25870
```
Jeżeli potrzeba wprowadzić zmian w pliku to restart można zrobić poprzez:

```bash
sudo systemctl disable helloworld
sudo systemctl stop helloworld
sudo systemctl start helloworld
sudo systemctl enable helloworld
```



### Konfiguracja nginx - Proxy Requests

Należy zacząć od stworzenia nowego pliku konfiguracyjnego w katalogu nginx `sites-available`.  Będzie miał nazwę `helloworld` aby zachować zgodność z resztą opisu:

Zmiana konfiguracji w `/etc/nginx/sites-available/helloworld` poprzez dodanie wpisu:

```bash
include proxy_params;
proxy_pass http://unix:/home/lambda/environments/ml_env/www/helloworld/helloworld.sock;
```

Plik po zmianach:

```
server {
        listen 80;
        listen [::]:80;

        root /home/lambda/environments/ml_env/www/helloworld/;
        index index.html index.htm index.nginx-debian.html;

        server_name 192.166.219.228;

        location / {
                try_files $uri $uri/ =404;

                include proxy_params;
                proxy_pass http://unix:/home/lambda/environments/ml_env/www/helloworld/helloworld.sock;
        }
}
```

Powtórzenie kroków które były wykonane pod koniec konfiguracji nginx (weryfikacja poprawności, restart):

```
sudo nginx -t
sudo systemctl restart nginx
```

Informacja o braku błędów:

```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Po przejściu pod adres `http://192.166.219.228/` będzie ponownie widoczny niebieski napis **Hello There!** W przypadku błędu *502 Bad gateway* należy sprawdzić dokładnie składnie plików z dwóch ostatnich punktów

Opisane na podstawie: [How To Serve Flask Applications with Gunicorn and Nginx on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-gunicorn-and-nginx-on-ubuntu-18-04)
Dodatkowe informacje: [NGINX 502 Bad Gateway: Gunicorn](https://www.datadoghq.com/blog/nginx-502-bad-gateway-errors-gunicorn/)