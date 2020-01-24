---
layout: post
title: "Konfiguracja serwera VPS dla data science/ML"
categories: python
author: "Michał"
---

# Podstawowa konfiguracja

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


restart


```bash
sudo service ssh restart
```


# Python

## Instalacja podstawowych pakietów 

```bash
python3 -V
> Python 3.6.9
```

Instalacja PIP

```bash
sudo apt install python3-pip
```

```bash
pip3 -V
>pip 9.0.1 from /usr/lib/python3/dist-packages (python 3.6)
```

Biblioteki systemowe:
```bash
sudo apt install build-essential libssl-dev libffi-dev python3-dev
```


## Virtual Environment

Instalacja `venv`

```bash
sudo apt install python3-venv
```

Utworzenie folderu `environments` w którym znajda się kolejne środowiska wirtualne:

```bash
mkdir environments
cd environments
```
Stworzenie pierwszego środowiska: `ml_env` 

```bash
python3.6 -m venv ml_env
```

weryfikacja poprzedniej komendy:

```bash
$ ls ml_env
>bin  include  lib  lib64  pyvenv.cfg  share
```

Aktywacja środowiska:

```bash
source ~/environments/ml_env/bin/activate
```

ewentualnie przejście do folderu bin i uruchomić

```bash
source ./activate
```

Poprawność wykonywania komendy powinna być widoczna w oknie konsoli:

```
(ml_env) user@host:~$
```

W nowo utworzonym (i aktywnym!) środowisku można doinstalować pozostałe biblioteki:

```
pip install -r requirements.txt
```

Zawartość pliku `requirements.txt`:

> numpy
> scipy
> pandas
> matplotlib
> scikit-learn
> seaborn

Eksport zainstalowanych bibliotek (razem z numerem wersji) jest możliwy poprzez komendę:

```bash
pip freeze > requirements.txt
```

 

## Jupyter

Instalacja:

```bash
python3 -m pip install jupyter
```

Test po instalacji:

```bash
jupyter notebook
```

Wygenerowanie pliku konfiguracyjnego:

```
jupyter notebook --generate-config
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

Instalacja JupyterLab

```bash
sudo -H pip3 install jupyterlab
```

uruchomienie JupyterLab

```bash
$ jupyter lab
```

Ustawienie tunelu w SSH

SSH -> Tunnels

```
L8000 localhost:8888
```

Połączenie (na komputerze lokalnym):

```bash
http://localhost:8000
```

### Automatyczny start usługi

Należy utworzyć w plik `jupyter.config` w folderze `/etc/systemd/system/`:

```
[Unit]
Description=Jupyter Notebook

[Service]
Type=simple
PIDFile=/run/jupyter.pid
ExecStart=/bin/bash -c ". ~/environments/ml_env/bin/activate;jupyter-notebook --notebook-dir=/home/lambda/notebooks"
User=lambda
Group=lambda
WorkingDirectory=/home/lambda/notebooks
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target

```

Następnie należy wykonać polecenia:

```bash
sudo systemctl enable jupyter.service
sudo systemctl daemon-reload
sudo systemctl start jupyter.service

```

Sprawdzenie statusu:

```bash
systemctl status jupyter.service
```

```bash
* jupyter.service - Jupyter Notebook
   Loaded: loaded (/etc/systemd/system/jupyter.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2020-01-24 12:44:10 CET; 31min ago
 Main PID: 13486 (bash)
    Tasks: 15 (limit: 4681)
   CGroup: /system.slice/jupyter.service
           |-13486 /bin/bash -c . ~/environments/ml_env/bin/activate;jupyter-notebook --notebook-dir=/home/lambda/notebooks
           |-13487 /home/lambda/environments/ml_env/bin/python3 /home/lambda/environments/ml_env/bin/jupyter-notebook --notebook-dir=/home/lambda/notebooks
           `-13570 /home/lambda/environments/ml_env/bin/python3 -m ipykernel_launcher -f /home/lambda/.local/share/jupyter/runtime/kernel-60191e35-aef5-42cf-9859-2b68d5298c35.json
```

## Git

```
sudo apt-get install git
```

Konfiguracja:

```bash
git config --global user.name "user_name"
git config --global user.email "email_id"
```

Klonowanie repozytorium (po przejściu do własciwego folderu):

```bash
cd ~/github
git clone https://github.com/xxx/py_otomoto.git
```



## Serwer FTP


```bash
sudo apt install vsftpd
```


## Pozostałe:
Konfiguracja strefy czasowej:

```bash
sudo dpkg-reconfigure tzdata
```


