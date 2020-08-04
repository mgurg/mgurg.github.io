---
layout: post
title: "NGINX Flask SSL"
categories: Python
author: "Michał"
math: true
---

Cześc druga poprawionej instrukcji stawiania środowiska do cełów nauki Pythona/ML. W stosunku do porzedniezj głowną zmianą jest chęć obsługi połączen poprzez HTTPS. 

# Flask

Wirtualne środowisko Python – kopia Pythona, ze specyficznymi  ustawieniami, zainstalowanymi modułami itp. Dzięki virtualenv możemy  mieć środowiska z różnymi wersjami tych samych modułów.

Instalacja `venv`

```
sudo apt install python3-venv
```

Utworzenie folderu `environments` w którym znajda się kolejne środowiska wirtualne:

```
mkdir py_env
cd py_env
```

Stworzenie pierwszego środowiska: `ml_env`

```
python -m venv ml_env
```

weryfikacja poprzedniej komendy:

```
$ ls ml_env
>bin  include  lib  lib64  pyvenv.cfg  share
```

Aktywacja środowiska:

```
source ~/py_env/www/bin/activate
```

ewentualnie przejście do folderu bin i uruchomić

```
source ./activate
```

Poprawność wykonywania komendy powinna być widoczna w oknie konsoli:

```
(www) (base) lambda@michal:~$ 
```
Mając aktywne środowisko www  zainstalujemy od razu Flask-a

```
pip install --upgrade pip
pip install wheel
pip install flask
```

zmiana uprawnień do folderu z root/root na lambda/lambda:

```
sudo chown -R lambda:lambda ~/py_env/www/
```

Utworzenie pliku `myproject.py` w `~/environments/ml_env/www/helloworld/` o zawartości:

```
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

```
pip install gunicorn
```

Następnie należy stworzyć **WSGI Entry Point** komendą `nano wsgi.py` o zawartości:

```
from myproject import app

if __name__ == "__main__":
    app.run()
```

Będąc w folderze w którym jest projekt wykonujemy test komendą

```
cd ~/environments/ml_env/www/helloworld/
gunicorn --bind 0.0.0.0:5000 wsgi:app
```

W rezultacie powinniśmy otrzymać informacje o statusie, bez żadnego błędu:

```
[2020-01-21 11:27:49 +0100] [25323] [INFO] Starting gunicorn 20.0.4
[2020-01-21 11:27:49 +0100] [25323] [INFO] Listening at: http://0.0.0.0:5000 (25323)
[2020-01-21 11:27:49 +0100] [25323] [INFO] Using worker: sync
[2020-01-21 11:27:49 +0100] [25327] [INFO] Booting worker with pid: 25327
```

Pod adresem `http://192.166.219.228:5000/`  powinien ponownie być widoczny niebieski napis  **Hello There!**




## Konfiguracja NGINX

Utworzenie katalogu na stronę WWW:

```
(www) (base) lambda@michal:~$ sudo mkdir ./py_env/www/helloworld
```

Konfiguracja NGINX (utworzona w osobnym pliku, bez modyfikacji domyślnej konfiguracji)

```
sudo nano /etc/ngnix/sites-available/helloworld
```

Zawartość pliku konfiguracyjnego

```
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

Żeby zacząć korzystać z nowych ustawień dla serwera należy stworzyć link konfiguracji ( [dowiązanie symboliczne](https://pl.wikipedia.org/wiki/Dowiązanie_symboliczne) ) w katalogu sites-enabled nginx.

```
sudo ln -s /etc/ngnix/sites-available/helloworld /etc/ngnix/sites-enabled/
```
Z niewiadomego powdu powyższa komenda nie chiała tym razem działać obszedłem to poprzez:
```
cd /etc/nginx/sites-enabled/
sudo ln -s ../sites-available/helloworld
ls -l
```
Rezultat:
```
total 0
lrwxrwxrwx 1 root root 34 Jul 13 14:35 default -> /etc/nginx/sites-available/default
lrwxrwxrwx 1 root root 29 Jul 15 10:35 helloworld -> ../sites-available/helloworld
```

Po wszystkich zmianach należy przeprowadzić test składni pliku konfiguracyjnego nginx files:

```
sudo nginx -t
```

Jeżeli wszystko jest ok, to można przeładować serwer żeby wczytać nowe ustawienia:

```
sudo systemctl restart nginx
```

Po wejściu na stronę główną pojawi się podmieniona strona.

### Certbot - certyfikat SSL

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