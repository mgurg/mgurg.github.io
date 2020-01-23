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

Ustawienie hasła:

```
jupyter notebook password
```


Instalacja JupyterLab

```
sudo -H pip3 install jupyterlab
```

uruchomienie JupyterLab

```
$ jupyter lab
```

Ustawienie tunelu w SSH

SSH -> Tunnels

```
L8000 localhost:8888
```

Połączenie (na komputerze lokalnym):

```
http://localhost:8000
```

### Git

```
sudo apt-get install git
```

Konfiguracja:

```
git config --global user.name "user_name"
git config --global user.email "email_id"
```

### Serwer FTP


```
sudo apt install vsftpd
```


### Pozostałe:
strefa czasowa

```
sudo dpkg-reconfigure tzdata
```