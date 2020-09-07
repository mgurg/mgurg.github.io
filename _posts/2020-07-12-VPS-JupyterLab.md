---
layout: post
title: "Instalacja JupyterLab na serwerze VPS"
categories: python
author: "Michał"
---

Od czasu, gdy pisałem pierwszą wersję instrukcji minęło ponad pół roku. W tym czasie wyszło bardzo wiele niedociągnięć, które popełniłem za pierwszym razem. Z okazji wyjścia Ubuntu 20.04 postanowiłem przeinstalować wszystko od zera, starając się wdrożyć to wszystko, o czym dowiedziałem się w tym czasie.


# 1) Podstawowa konfiguracja serwera

Opis na podstawie tekstu Łukasza Prokulskiego: [Stawiamy własny serwer](https://blog.prokulski.science/index.php/2018/06/14/serwer-vps-dla-r-python/) uzupełnianego o inne źródła. Za radą Łukasza wybrałem serwer webh.pl i ogólnie nie żałuję, miał być tani i taki jest. Ma jednak jedną istotną wadę, która początkującym może spędzać sen z powiek. 

## OpenBLAS

Jest nią niepoprawna obsługa rozszerzenia instrukcji procesora AVX2 przez wirtualna maszynę. Szerszy [opis dla dociekliwych](https://github.com/xianyi/OpenBLAS/issues/2306).

Rozwiązaniem jest określenie typu procesora, co można zrobić ręcznie z poziomu konsoli instrukcją:
```bash
export OPENBLAS_CORETYPE="Skylake"
```

Ewentualnie z poziomu kazdego skryptu pythona:
```python
import os
os.environ["OPENBLAS_CORETYPE"] ="Skylake"
os.getenv("OPENBLAS_CORETYPE")
```

Polecam dodać to w sposób permanenety, poprzez modyfikację `sudo nano /etc/environment` i dodanie w nim linii:

```bash
OPENBLAS_CORETYPE="Skylake"
```
Pozostaje przeładować zmienne systemowe i można sprawdzić czy wszystko jest ok:

```bash
source /etc/environment

echo $OPENBLAS_CORETYPE      
Skylake
```

Jeżeli pojawiałyby się dalsze problemy to nalezy zrestartwoać serwer.


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

Jeżeli planujemy instalację biblioteki OpenCV z `conda-forge` to można od razu doinstalować:
```
sudo apt install libgl1-mesa-glx
```

W ten sposób nie musimy pózniej google-ować co powoduje błąd (*ImportError: libGL.so.1: cannot open shared object file: No such file or directory*). Zaoszczedziliśmy właśnie 30s życia :)


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
curl -O https://repo.anaconda.com/archive/Anaconda3-2020.07-Linux-x86_64.sh
```

Instalacja:

```
sh Anaconda3-2020.07-Linux-x86_64.sh
```
W celu inicjalizacji po zakończeniu procesu instalacji, najpierw należy uruchomić: 
```
<ścieżka do conda>/bin/activate
```
a następnie uruchomić `conda init` MOżna to też zrobić w trakcie instalacji wyarżając zgodę pod koniec instalacji.

Po instalacji:

```bash
source ~/.bashrc
```

Sprawdzenie czy instalacja przebiegła poprawnie:

```bash
conda --version
```

Stworzenie środowiska

```bash
conda create --name env python=3
conda activate env
```

Instalacja JupyterLab:

```bash
conda install -c conda-forge jupyterlab
```

Polecam też doinstalować od razu podstawowe biblioteki:
```
conda install -y -c conda-forge numpy 
conda install -y -c conda-forge pandas
conda install -y -c conda-forge matplotlib 
conda install -y -c conda-forge opencv 
```

Jezeli zastanawiasz się czego jeszcze nie ma w utworzinym środowisku, to `conda list` powie co już mamy.
PS.
Korzystam zwykle z kanału *conda-forge* można na przyszłość [ustawić go jako domyślny](https://stackoverflow.com/a/39862730).

Doinstalowanie nodejs (w celu późniejszego korzystania z rozszerzeń)

```bash
cd /tmp
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs
```

Stworzenie pliku konfiguracyjnego:

```bash
jupyter-lab --generate-config

Writing default config to: /home/lambda/.jupyter/jupyter_notebook_config.py
```

Można od razu ustawić w nim domyślny folder roboczy:
```bash
nano /home/lambda/.jupyter/jupyter_notebook_config.py
```

poprzez ustawienie linii
```
#c.NotebookApp.notebook_dir = "./ntbks"
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

```bash
ssh -L 8888:localhost:8888 myuser@your_server_ip
```

```bash
conda activate env
jupyter lab
```

### Dodatki

Jeżeli wszystko działą to możemy na koniec doinstalować ciemny motyw:
```bash
jupyter labextension install @telamonian/theme-darcula
```

`CTRL + /` - komentowanie (jednej lub wielu) linii kodu

`SHIFT + L` - pokazuje ukrywa numery linii

Lokalnie pracuję korzystająć z [Dockera](https://mgurg.github.io/docker/2020/08/05/Docker.html)

SpellCheck:
```bash
jupyter labextension install @ijmbarr/jupyterlab_spellchecker
```

## Remote VS Code

```bash
sudo apt-get install openssh-server
sudo apt-get install sshfs
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


