---
layout: post
title: "Konfiguracja serwera VPS II: nginx + gunicorn + flask"
categories: python
author: "Michał"
---



Serwer VPS z skonfigurowanym JupyterLab to doskonałe środowisko do pracy i rozwiajania swoich projektów. Ciężko się jednak nimi pochawalić, dlatego postanowiałem rozszerzyć konfigurację o serwer NGINX i Flask, tak żeby mieć włąsną stronę internetową.

Teoria: 📺 [WSGI for Web Developers](https://www.youtube.com/watch?v=WqrCnVAkLIo)

**v2**

Cześc druga poprawionej instrukcji stawiania środowiska do celów nauki Pythona/ML. W stosunku do porzedniej głowną zmianą jest chęć obsługi połączen poprzez HTTPS. 


## Serwer WWW - NGINX

Do wyboru jest Apache i nginx. Rzut monetą wskazał na drugi webserver (ok, tak naprawdę to wybrany z premedytacją). 


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
systemctl status ngnix
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
# Flask

Przed instalacją utwoorzymy wirtualne środowisko – kopię Pythona, ze specyficznymi  ustawieniami, zainstalowanymi modułami itp. Dzięki virtualenv możemy  mieć środowiska z różnymi wersjami tych samych modułów odseparowane od systemowego Pythona.

Instalacja `venv`

```bash
sudo apt install python3-venv
```

Utworzenie folderu `py_env` w którym znajda się kolejne środowiska wirtualne:

```bash
mkdir py_env
cd py_env
```

Stworzenie pierwszego środowiska: `www_env`

```bash
python -m venv www_env
```

weryfikacja poprzedniej komendy:

```bash
$ ls www_env
>bin  include  lib  lib64  pyvenv.cfg  share
```

Aktywacja środowiska:

```bash
source ~/py_env/www_env/bin/activate
```

ewentualnie przejście do folderu bin i uruchomić

```bash
source ./activate
```

Poprawność wykonywania komendy powinna być widoczna w oknie konsoli:

```bash
(www_env) (base) lambda@michal:~$ 
```
Mając aktywne środowisko www  zainstalujemy od razu Flask-a

```bash
pip install --upgrade pip
pip install wheel
pip install flask
```

Zmiana uprawnień do folderu z root/root na lambda/www-data:

```bash
sudo chown -R lambda:www-data ~/py_env/www_env/www
```

Utworzenie katalogu na stronę WWW:

```bash
(www) (base) lambda@michal:~$ sudo mkdir ./py_env/www_env/www/helloworld
```

W  pliku `myproject.py` w `~/py_env/www_env/www/helloworld/` o zawartości:

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "<h1 style='color:blue'>Hello There!</h1>"

if __name__ == "__main__":
    app.run(host='0.0.0.0')
```

### Gunicorn

Instalacja:

```bash
pip install gunicorn
```

Następnie należy stworzyć **WSGI Entry Point** w katalogu:
`~/py_env/www_env/www/helloworld` komendą `nano wsgi.py` o zawartości:

```python
from myproject import app

if __name__ == "__main__":
    app.run()
```
Odblokowujemy port 5000 żeby przerowadzić kolejny krok:

```bash
sudo ufw allow 5000
```

Będąc w folderze w którym jest projekt wykonujemy test komendą

```bash
cd ~/py_env/www_env/www/helloworld/
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

## Konfiguracja NGINX

Konfiguracja NGINX (utworzona w osobnym pliku, bez modyfikacji domyślnej konfiguracji)

```bash
cd /etc/ngnix/sites-available/
sudo nano helloworld
```

Zawartość pliku konfiguracyjnego

```
server {
        listen 80;
        listen [::]:80;

        root /home/lambda/py_env/www_env/www/helloworld/;
        index index.html index.htm index.ngnix-debian.html;

        server_name 192.166.219.228 nazwadomeny.pl;

        location / {
                try_files $uri $uri/ =404;
        }
}
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

Żeby zacząć korzystać z nowych ustawień dla serwera należy stworzyć link konfiguracji ( [dowiązanie symboliczne](https://pl.wikipedia.org/wiki/Dowiązanie_symboliczne) ) w katalogu sites-enabled nginx.

```bash
sudo ln -s /etc/ngnix/sites-available/helloworld /etc/ngnix/sites-enabled/
```
Z niewiadomego powdu powyższa komenda nie chiała tym razem działać obszedłem to poprzez:
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

Jeżeli wszystko poszło ok, to po wejściu na stronę główną pojawi się komunikat.... `403 Forbidden`. Co oznacza że pora wrócić do konfiguracji Gunicorn.

### Konfiguracja systemd 
Stworzenie pliku *systemd unit* pozwoli systemowi init Ubuntu na automatyczne uruchomienie Gunicorna i obsługę aplikacji Flask przy każdym uruchomieniu serwera.

Należy stworzyć plik o rozszerzeniu `.service` w katalogu `/etc/systemd/system`:

```bash
sudo nano /etc/systemd/system/helloworld.service
```

o zawartości:

```
[Unit]
Description=Gunicorn instance to serve helloworld website
After=network.target

[Service]
User=lambda
Group=www-data

WorkingDirectory=/home/lambda/py_env/www_env/www/helloworld/
Environment="PATH=/home/lambda/py_env/www_env/bin"
ExecStart=/home/lambda/py_env/www_env/bin/gunicorn --workers 3 --bind unix:helloworld.sock -m 007 wsgi:app

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
* helloworld.service - Gunicorn instance to serve helloworld website
     Loaded: loaded (/etc/systemd/system/helloworld.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2020-09-09 13:52:38 CEST; 1min 57s ago
   Main PID: 26713 (gunicorn)
      Tasks: 4 (limit: 4657)
     Memory: 51.5M
     CGroup: /system.slice/helloworld.service
             |-26713 /home/lambda/py_env/www_env/bin/python /home/lambda/py_env/www_env/bin/gunicorn --workers 3 -->
             |-26720 /home/lambda/py_env/www_env/bin/python /home/lambda/py_env/www_env/bin/gunicorn --workers 3 -->
             |-26721 /home/lambda/py_env/www_env/bin/python /home/lambda/py_env/www_env/bin/gunicorn --workers 3 -->
             `-26722 /home/lambda/py_env/www_env/bin/python /home/lambda/py_env/www_env/bin/gunicorn --workers 3 -->

Sep 09 13:52:38 michal systemd[1]: Started Gunicorn instance to serve helloworld website.
Sep 09 13:52:39 michal gunicorn[26713]: [2020-09-09 13:52:39 +0200] [26713] [INFO] Starting gunicorn 20.0.4
Sep 09 13:52:39 michal gunicorn[26713]: [2020-09-09 13:52:39 +0200] [26713] [INFO] Listening at: unix:helloworld.so>
Sep 09 13:52:39 michal gunicorn[26713]: [2020-09-09 13:52:39 +0200] [26713] [INFO] Using worker: sync
Sep 09 13:52:39 michal gunicorn[26720]: [2020-09-09 13:52:39 +0200] [26720] [INFO] Booting worker with pid: 26720
Sep 09 13:52:39 michal gunicorn[26721]: [2020-09-09 13:52:39 +0200] [26721] [INFO] Booting worker with pid: 26721
Sep 09 13:52:39 michal gunicorn[26722]: [2020-09-09 13:52:39 +0200] [26722] [INFO] Booting worker with pid: 26722

```
Jeżeli potrzeba wprowadzić zmian w pliku to restart można zrobić poprzez:

```bash
sudo systemctl disable helloworld
sudo systemctl stop helloworld
sudo systemctl start helloworld
sudo systemctl enable helloworld
```

### Konfiguracja NGINX - Proxy Requests

Należy zacząć od edycji wcześniej stworzonego pliku konfiguracyjnego:

`sudo nano /etc/nginx/sites-available/helloworld`

poprzez dodanie wpisu:

```bash
include proxy_params;
proxy_pass http://unix:/home/lambda/py_env/www_env/www/helloworld/helloworld.sock;
```

Plik po zmianach:

```
server {
        listen 80;
        listen [::]:80;

        root /home/lambda/py_env/www_env/www/helloworld/;
        index index.html index.htm index.ngnix-debian.html;

        server_name 192.166.219.228 nazwadomeny.pl;

        location / {
                include proxy_params;
                proxy_pass http://unix:/home/lambda/py_env/www_env/www/helloworld/helloworld.sock;
        }
}
```

Powtórzenie kroków które były wykonane pod koniec konfiguracji nginx (weryfikacja poprawności, restart):

```bash
sudo nginx -t
sudo systemctl restart nginx
```

Informacja o braku błędów:

```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Po przejściu pod adres `http://192.166.219.228/` będzie ponownie widoczny niebieski napis **Hello There!**

Oznacza to że nasza strona w Flask działa poprawnie

W przypadku błędu *502 Bad gateway* należy sprawdzić dokładnie składnie plików z dwóch ostatnich punktów.


## Domena

Powyższy opis był już pisany z uwzględnienime podpiecia domeny. Kroki po stronie dostawcy domeny:

| Domena             | TTL  |       |                 |
| ------------------ | ---- | ----- | --------------- |
| nazwadomeny.pl.     | 0    | A     | 192.166.219.228 |
| lab.nazwadomeny.pl. | 0    | CNAME | nazwadomeny.pl.  |
| lab.nazwadomeny.pl. | 0    | CNAME | nazwadomeny.pl.  |

w skrócie:

```bash
IN A 192.166.219.228
lab IN CNAME nazwadomeny.pl.
jupyter IN CNAME nazwadomeny.pl.
```

Plik `helloworld` w `/etc/nginx/sites-enabled/`:

```
server {
    listen 80;
    listen [::]:80;

    root /home/lambda/py_env/www_env/www/helloworld/;
    index index.html index.htm index.ngnix-debian.html;

    server_name 192.166.219.228 nazwadomeny.pl;

    location / {
            include proxy_params;
            proxy_pass http://unix:/home/lambda/py_env/www_env/www/helloworld/helloworld.sock;
 }
}
```

Restart NGINX żeby zmiany weszły w życie:

```bash
sudo systemctl reload nginx
```

### Wiecej informacji: 

Opisane na podstawie: [How To Serve Flask Applications with Gunicorn and Nginx on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-gunicorn-and-nginx-on-ubuntu-18-04)

Dodatkowe informacje: [NGINX 502 Bad Gateway: Gunicorn](https://www.datadoghq.com/blog/nginx-502-bad-gateway-errors-gunicorn/)

## Certbot - certyfikat SSL

[Oficjalna instrukcja instalacji](https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx.html)

```bash
sudo apt-get install certbot python3-certbot-nginx
```

Uruchomienie kreatora instalacji:
```bash
sudo certbot --nginx
```

Końcowy komunikat:

> Congratulations! You have successfully enabled https://your_domain.pl
>
> You should test your configuration at:
> https://www.ssllabs.com/ssltest/analyze.html?d=your_domain.pl
> - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
>
> IMPORTANT NOTES:
>  - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/your_domain.pl/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/your_domain.pl/privkey.pem
   Your cert will expire on 2020-10-11. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot again
   with the "certonly" option. To non-interactively renew *all* of
   your certificates, run "certbot renew"
>  - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.

Po przejściu na adres [strony głównej](https://your_domain.pl) powinnien być widoczny domyślny komunikat NGINX:

> Welcome to nginx!
> 
> If you see this page, the nginx web server is successfully installed and working. Further configuration is required.
> 
> For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.
> 
> Thank you for using nginx.
