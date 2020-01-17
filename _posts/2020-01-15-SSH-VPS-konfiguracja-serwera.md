---
layout: post
title: "Konfiguracja serwera VPS dla data science/machine learning"
categories: python
author: "Michał"
---

# Podstawowa konfiguracja

```bash
adduser phi
```

```bash
gpasswd -a phi sudo
```

```bash
sudo nano /etc/ssh/sshd_config
```

```bash
PermitRootLogin yes
```

zamienić na 

```bash
PermitRootLogin no
```


restart


```bash
sudo service ssh restart
```


# Python

## Podstawy

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

Instalacja venv

```bash
sudo apt install python3-venv
```

```bash
mkdir environments
cd environments
```
Stworzenie środowiska

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
source ml_env/bin/activate
```

ewentualnie przejście do folderu bin i uruchomić

```bash
source ./activate
```

lub bezposrednio o zalogowaniu:

```bash
source ~/environments/my_env/bin/activate
```

## Jupyter

Instalacja:

```
python3 -m pip install jupyter
```

Test po instalacji:

```
$ jupyter notebook
```

Wygenerowanie pliku konfiguracyjnego:

```
jupyter notebook --generate-config
Writing default config to: /home/phi/.jupyter/jupyter_notebook_config.py

```

ustawienie hasła

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