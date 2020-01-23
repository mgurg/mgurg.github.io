---
layout: post
title: "Python: statyczna strona w Flask"
categories: python
author: "Michał"
---

Próba zbudowania prostej, statycznej witryny opartej o HTML/CSS która w przyszłości będzie serwować przetworzone dane z otomoto.pl. Opis na podstawie: [How to build a web application using Flask and deploy it to the cloud](https://www.freecodecamp.org/news/how-to-build-a-web-application-using-flask-and-deploy-it-to-the-cloud-3551c985e492/)

## Rozbudowa aplikacji - testowanie zmian

Kod startowy

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "<h1 style='color:blue'>Hello There!</h1>"

if __name__ == "__main__":
    app.run(host='0.0.0.0')
```

Pierwszy problem: przeładowanie aplikacji po wprowadzeniu zmian. Automatyczne przeładowanie powinien umożliwić parametr `--reload`

Zmieniona zawartość pliku `myproject.py`:

```
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "<h1 style='color:blue'>Hello There!!!</h1>"

@app.route("/salvador")
def salvador():
    return "Hello, Salvador"

if __name__ == "__main__":
    app.run(host='0.0.0.0'debug=true )
```

Szybki test:

```bash
sudo nano /etc/systemd/system/helloworld.service
```

```

[Service]
...
ExecStart=/home/lambda/environments/ml_env/bin/gunicorn --workers 3 --reload --bind 
...

```

Poskutkował błędem:

```bash
* helloworld.service - Gunicorn instance to serve helloworld
   Loaded: loaded (/etc/systemd/system/helloworld.service; enabled; vendor preset: enabled)
   Active: failed (Result: exit-code) since Thu 2020-01-23 11:31:15 CET; 3min 3s ago
 Main PID: 16263 (code=exited, status=3)

Jan 23 11:31:15 michal gunicorn[16263]:   File "/home/lambda/environments/ml_env/lib/python3.6/site-packages/gunicorn/wo
Jan 23 11:31:15 michal gunicorn[16263]:     self.load_wsgi()
Jan 23 11:31:15 michal gunicorn[16263]:   File "/home/lambda/environments/ml_env/lib/python3.6/site-packages/gunicorn/wo
Jan 23 11:31:15 michal gunicorn[16263]:     self.reloader.add_extra_file(exc_val.filename)
Jan 23 11:31:15 michal gunicorn[16263]: AttributeError: 'NoneType' object has no attribute 'add_extra_file'
Jan 23 11:31:15 michal gunicorn[16263]: [2020-01-23 11:31:15 +0100] [16267] [INFO] Worker exiting (pid: 16267)
Jan 23 11:31:15 michal gunicorn[16263]: [2020-01-23 11:31:15 +0100] [16263] [INFO] Shutting down: Master
Jan 23 11:31:15 michal gunicorn[16263]: [2020-01-23 11:31:15 +0100] [16263] [INFO] Reason: Worker failed to boot.
Jan 23 11:31:15 michal systemd[1]: helloworld.service: Main process exited, code=exited, status=3/NOTIMPLEMENTED
Jan 23 11:31:15 michal systemd[1]: helloworld.service: Failed with result 'exit-code'.

```

Tymczasowe rozwiązanie - restart manualny:

```
sudo systemctl disable helloworld;sudo systemctl stop helloworld;sudo systemctl start helloworld;sudo systemctl enable helloworld
```

## Szablony HTML

Zmiany w `myproject.py`:

- from flask import Flask, **render_template**
- return render_template("home.html")

```python

from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def home():
	# template files are located in folder "templates"
    return render_template("home.html")

@app.route("/otomoto")
def otomoto():
    return render_template("otomoto.html")

if __name__ == "__main__":
    app.run(host='0.0.0.0' )

```



## Pliki CSS

Lokalizacja: `static/css`, struktura projektu:

```bash
tree
.
|-- helloworld.sock
|-- myproject.py
|-- __pycache__
|   |-- myproject.cpython-36.pyc
|   `-- wsgi.cpython-36.pyc
|-- static
|   `-- css
|       `-- template.css
|-- templates
|   |-- home.html
|   |-- otomoto.html
|   `-- template.html
`-- wsgi.py

```

