---
layout: post
title: "Instalacja JupyterLab na serwerze VPS"
categories: python
author: "Michał"
---

Od czasu gdy pisałem pierwszą instrukję minęło prawie pół roku i w tym czasie wyszło bardzo wiele niedociągnieć które popełniłem za pierwszym razem. Z okazji wyjścia Ubuntu 20.04 postanowiłem przeinstalować wszysyko od zera starając się jednocześnie poprawić jak najwięcej błędów z poprzedniej próby.

# 1) Podstawowa konfiguracja serwera

Opis na podstawie tekstu Łukasza Prokulskiego: [Stawiamy własny serwer](https://blog.prokulski.science/index.php/2018/06/14/serwer-vps-dla-r-python/) uzupełnianego o inne źródła. Za radą łukasza wybrałem serwer webh.pl i ogólnie nie żałuję, miał być tani i taki jest. Ma jednak jedną istotną wadę, która początkującym może spędzać sen z powiek. 

## OpenBLAS

Jest nią niepoprawna obsługa rozszerzenia instrukcji procesora AVX2 przez wirtualna maszynę. Szerszy [opis dla dociekliwych](https://github.com/xianyi/OpenBLAS/issues/2306).

Rozwiązanie: dodanie do zmiennych systemowych wpisu
```bash
OPENBLAS_CORETYPE=Skylake
```
Można to zrobić instrukcją:
```bash
export OPENBLAS_CORETYPE="Skylake"
```

ewentualnie z poziomu kazdego skryptu pythona:
```python
import os
os.environ["OPENBLAS_CORETYPE"] ="Skylake"
os.getenv("OPENBLAS_CORETYPE")
```

## 2) Użytkownicy i logowanie

Dodanie nowego użytkownika którego będziemy używać do pracy zamiast konta `root`

```bash
adduser lambda
```

Dodanie użytkownika `lambda` do grupy administratorów:

```bash
gpasswd -a lambda sudo
```

Wyłączenie możliwości logowania użytkownika `root` poprzez SSH

```bash
sudo nano /etc/ssh/sshd_config
```

```bash
PermitRootLogin yes
```

zamienić na: 

```bash
PermitRootLogin no
```

Aktywacja firewalla, dodanie reguły dla OpenSSH
```
sudo ufw app list
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status
```
Poprawna konfiguracja:
```
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere 
```

restart

```bash
sudo service ssh restart
```
Aktualizacja systemu:
```bash
sudo apt-get update
sudo apt-get upgrade
```

## 3) Python

## Instalacja podstawowych pakietów 

```bash
python3 -V
> Python 3.6.9
```

Instalacja i aktualizacja pip

```bash
sudo apt install python3-pip python3-dev
sudo -H pip3 install --upgrade pip
```

```bash
pip3 -V
>pip 9.0.1 from /usr/lib/python3/dist-packages (python 3.6)
```
Menadżer pakietów dla  Python, komendy w wierszu poleceń:
```bash
pip list 				# – lista zainstalowanych pakietów
pip search 				# – szuka pakietów w repozytorium online
pip install pakiet 		# – instalowanie modułu
pip uninstall pakiet	# - odinstalowanie
pip list –o 			# - sprawdzenie nieaktualnych pakietów
pip install –U pakiet 	# - update pakietu
pip freeze> plik.txt 	# – zapisanie informacji do pliku o pakietach
pip install –r plik.txt # – zainstaluje wszystkie wymagane pakiety
```

Biblioteki systemowe:

```bash
sudo apt install build-essential libssl-dev libffi-dev 
```
Przykład pliku requirements.txt:
```
# Moduł warunek wersja
numpy >= 1.15.2
pandas == 0.23.4
```
# Conda & JupyterLab
Za pierwszym razem instalowałem Jupyter Notebook i JupyterLab wykorzystując venv. Tym razem zdecydowałem się bardziej odseparować od systemowego pythona i wykorzystałem menadżer pakietów conda. 

Opisane na podstawie:
* [Upgrading to JupyterLab on Ubuntu](https://hackersandslackers.com/upgrading-to-jupyter-lab-on-ubuntu/)

* [Setting up a Jupyter Lab remote server ](https://agent-jay.github.io/2018/03/jupyterserver/)


Odblokowanie portów firewalla:

```bash
sudo ufw enable
sudo ufw allow 8888
sudo ufw allow 22
sudo ufw status
```

Wynik:

```bash
To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
8888                       ALLOW       Anywhere
```

Pobranie Anaconda, [najnowsza wersja Linux x64](https://www.anaconda.com/products/individual#Downloads):

```bash
cd /tmp
sudo apt install curl
curl -O https://repo.anaconda.com/archive/Anaconda3-2020.02-Linux-x86_64.sh
```

Instalacja:

```
sh Anaconda3-2020.02-Linux-x86_64.sh
```

Po instalacji:

```
source ~/.bashrc
```

Sprawdzenie czy instalacja przebiegła poprawnie:

```
conda --version
```

Stworzenie środowiska

```
conda create --name env python=3
conda activate env
```

Instalacja JupyterLab:

```
conda install -c conda-forge jupyterlab
```

Doinstalowanie nodejs (w celu późniejszego korzystania z rozszerzeń)

```
cd /tmp
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs
```

Stworzenie pliku konfiguracyjnego:

```
jupyter-lab --generate-config

Writing default config to: /home/lambda/.jupyter/jupyter_notebook_config.py
```

Ustawienie hasła (nie będzie konieczne podawanie długiego tokena do autoryzacji):

```bash
jupyter notebook password
```

```bash
Enter password: **********
Verify password: **********
[NotebookPasswordApp] Wrote hashed password to: /home/lambda/.jupyter/jupyter_notebook_config.py
```

Po połączniu przez SSH powinniśmy mieć dostęp do JupyterLab (pod adresem [localhost:8888](localhost:8888)):

```
ssh -L 8888:localhost:8888 myuser@your_server_ip
```

```
conda activate env
jupyter lab
```

# NGINX

W stosunku do poprzednij części doinstaluje też od razu serwer NGINX razem z certyfikatem 
Let's encrypt

Instalacja serwera:

```bash
sudo apt-get install nginx
sudo ufw app list
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
sudo ufw status
```

```bash
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx Full                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx Full (v6)            ALLOW       Anywhere (v6)  
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


