---
layout: post
title: "Flask: statyczna strona"
categories: python
author: "Michał"
---

Próba zbudowania prostej, statycznej witryny opartej o HTML/CSS która w przyszłości będzie serwować przetworzone dane z otomoto.pl. Opis na podstawie: [How to build a web application using Flask and deploy it to the cloud](https://www.freecodecamp.org/news/how-to-build-a-web-application-using-flask-and-deploy-it-to-the-cloud-3551c985e492/)

Pomocny przy tym był również tutorial z [Alicja & IT](http://www.alicja.it/category/flask/)

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

Pierwszy problem: przeładowanie aplikacji po wprowadzeniu zmian. Automatyczne przeładowanie powinien umożliwić parametr `--reload` który skutkował błędem:

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

## Model View Controller (MVC)

Wzorzec MVC zakłada podział aplikacji na trzy główne części, aby uprościć projektowanie aplikacji posiadających graficzne interfejsy użytkownika:

**Model** – to reprezentacja problemu bądź logiki aplikacji + dane
**Widok** – opisuje, jak wyświetlić pewną część modelu w ramach interfejsu użytkownika; może składać się z podwidoków odpowiedzialnych za mniejsze części interfejsu.
**Kontroler** – przyjmuje dane wejściowe od użytkownika i reaguje na jego poczynania, zarządzając aktualizacje modelu oraz odświeżenie widoków.

![https://upload.wikimedia.org/wikipedia/commons/thumb/a/a0/MVC-Process.svg/500px-MVC-Process.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a0/MVC-Process.svg/500px-MVC-Process.svg.png)

Ze względu na ilość elementów składowych aplikacji webowej, logika biznesowa powinna znajdować się w kodzie w ustalonym miejscu - najlepiej w modelach, podążając w myśl idei “*fat models, thin views*”.

Ilość kodu odpowiedzialnego za logikę biznesową w poszczególnych elementach

```
Model > Kontroler > Widok
```

> MVT w Django jest nieco podobne do MVC. To co odróżnia jedno od drugiego to to że w MVT nie ma kontrollera czyli tego C. Controller ma za zadanie kontrolować modele i widoki. W Django mamy modele, widoki ale nie mamy controllera więc co je kontroluje? Okazuje się że controllerem jest sam framework Django, ustawiając np stringi w urls.py z logicznego punktu widznia konfigurujesz kontroler. Czyli to nie jest tak że kontrolera nie ma, po prostu w Django nie leży on w domenie zainteresowań programisty. Kolejna różnica to T czyli template. W MVT mamy rozdział pomiędzy widokiem (który wybiera które dane mają być przekazane do template), i template czyli sposobem, w jaki te dane zostaną zaprezentowane. Template w Django jest samą strukturą dokumentu html + właśnie te dane które dostarczył widok. Zauważ że działanie template jest uwarunkowane zgodnością nazw danych. Jeżeli zgodności nie będzie to template będzie błędny, a nie view ani model, czyli template jest elementem paradoksalnie najmniej znaczącym w tej układance. Jest to zaletą MVT. Dzięki temu rozdziałowi jeden template może być użyty w paru widokach.
>

[First Flask App](https://hackersandslackers.com/your-first-flask-application)
[MVC pattern in Python: Introduction and BasicModel](https://www.giacomodebidda.com/mvc-pattern-in-python-introduction-and-basicmodel/)

## Template language - Jinja2

Korzystanie z szablonów jest możliwe po imporcie odpowiedniego modułu:

```python
from flask import render_template
```

Zasada działania Jinja2 polega na umieszczaniu w plikach źródłowych (html) znaczników, które następnie są zastępowane generowaną przez aplikację treścią:
{% raw  %}
`{{ wyrażenie }}` - w tym miejscu podstawiona zostanie wartość wyrażenia
`{% block %}` - tak tworzymy blok, np., na potrzeby pętli, warunku; pamiętaj o zamknięciu bloku `{% endblock %}`

`{# … #}` – dla komentarzy, które nie będą wyświetlać się na stronie

`|` – znacznik oddzielający filtry od zmiennych

`{{ abcd }}` – podstawi wartość zmiennej abcd
`{{ url_for('hello') }}` – podstawi URL wywołujący funkcję hello()
{% endraw  %}
Przykład wykorzystania:

```python
from hello_world import app
from flask import render_template

# @app.route('/')
# def index():
#      return "Hello world!"

@app.route('/')
def index():
    navigation = [
        {
            'href': 'otomoto',
            'caption': 'Analiza ogłoszeń'
        },
        {
            'href': 'price_predict',
            'caption': 'Predykcja ceny'
        }
    return render_template('index.html', navigation=navigation)
```

Dodanie kolejnych podstron odbywa się poprzez dodanie włąsciwego `@app.route("/new_path")` Żeby uniknąć duplikowania nawigacji można wykorzystać **context processors**. Zmienne zdefiniowane w postaci słownika będą dodawane do każdego widoku w trakcie jego tworzenia:

```python
@app.context_processor
def inject_variables():
    return dict(
        navigation = [
            {
                'href': '/',
                'caption': 'Home'
            },
            {
                'href': 'otomoto',
                'caption': 'Analiza ogłoszeń'
            },
            {
                'href': 'price_predict',
                'caption': 'Predykcja ceny'
            }
        ])
```

## Blueprints 

Mechanizm umożliwiający rozbicie projektu na mniejsze podzbiory. Analiza na przykładzie kodu, `views.py`:

```python
# częśc kodu zostałą przeniesiona do pliku report.py 
from .report import report_blueprint
app.register_blueprint(report_blueprint)

#
# @app.route('/report')
# def report():
#     return render_template('report.html')

```

Plik `report.py`:

```python
from flask import render_template
from flask import Blueprint

report_blueprint = Blueprint('report_blueprint', __name__)

@report_blueprint.route('/report') #@report_blueprint zamiast org. @app
def report():
    return render_template('report.html')
```

Zmianie musiał ulec też plik base.html:

 `url_for('report_blueprint.report')` zamiast `url_for('report')`

W tej chwili doszedł wyłącznie plik report.py w głównym folderze projektu. Przeniosłem go do folderu blueprints/report pod nazwą `__init__.py`

```bash
│   forms.py
│   models.py
│   report.py # <- do usunięcia
│   views.py
│   __init__.py
│
├───blueprints
│   └───report
│           __init__.py # <- nowa lokalizacja report.py
│
├───static
│   ├───css
│   ├───img
│   └───js
│
├───templates
        404.html
        500.html
        base.html
        index.html
        price_predict.html
        report.html
```

W views.py wystarczy zmienić tylko jedną linijkę żeby całość działała:

```python
from .blueprints.report import report_blueprint
```



## Baza danych

Moduł Flask-SQLAlchemy realizuje wsparcie SQLAlchemy dla Flaska. O ile w głownym projekcie trzymałem się czystego SQL, to Flask był dobrtyym momentem żeby zainteresować się ORM *(Object Relation Mapper)*.

SQL:

Zalety:
- przydatny w procesie debugowania
- wydajny

Wady:
- implementacje zapytań mogą być podatne na atak SQL Injection
- implementacja zapytań zależna od rodzaju silnika
- zapytania wymagają aktualizacji podczas każdej modyfikacji struktury tabel
- Kod SQL wymieszany z kodem aplikacji

Mapowanie obiektowo - relacyjne polega na mapowaniu danych w postaci tabelarycznej (relacji w bazie danych) na obiekty i odwrotnie.

`__init__.py`

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['DEBUG'] = True
app.config['SECRET_KEY'] = 'verysecretkey'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///baza.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

from hello_world import views
```







## Bootstrap

Początkowo chciałem ominąć całkiem warstwę wizualną, ale w 2020 nie wypada nikomu pokazywać gołego HTML-a. Żeby nie tracić czasu na nadrabianie zaległości z CSS i HTML postanowiłem skorzystać z [Bootstrap](https://getbootstrap.com/).

 Z *Bootstrap* też nie mam żadnego doświadczenia, ale ma dobrą dokumentację i styluje wszystkie elementy HTML-a. Zastanawiałem się nad alternatywą w postaci czegoś nowszego (*Bulma*, *TailwindCSS*) , ale ponieważ nie zamierzam doktoryzować się z webdeveloperki to uznałem że warto zacząć od starszego ale pewniejszego rozwiązania.

Po godzinie pracy udało mi się uzyskać miłą dla oka stronę. Struktura projektu:

```
.
|-- helloworld.sock
|-- myproject.py
|-- static
|   |-- css
|   |   |-- bootstrap-grid.min.css
|   |   |-- bootstrap.min.css
|   |   |-- bootstrap-reboot.min.css
|   `-- js
|       |-- bootstrap.bundle.min.js
|       `-- bootstrap.min.js
|-- templates
|   |-- base.html
|   |-- home.html
|   `-- otomoto.html
`-- wsgi.py
```



Szablon strony składa się z dwóch części. Głównego pliku `base.html` - nagłówek, nawigacja etc. i plików z treścią: `home.html`, `otomoto.html`

## Formularze

na potrzeby interakcji z użytkownikiem będę potrzebował formularz w którym będzie można wprowadzić parametry ogłoszenia. Wygląda na to że w Flask odpowiada za to Flask-WTF

```
pip install flask-wtf
```

### Request Dispatching

```python
(ml_env) lambda@michal:~/environments/ml_env/www/helloworld$ python
>>> from myproject import app
>>> app.url_map
Map([<Rule '/otomoto' (GET, HEAD, OPTIONS) -> otomoto>,
 <Rule '/' (GET, HEAD, OPTIONS) -> home>,
 <Rule '/static/<filename>' (GET, HEAD, OPTIONS) -> static>])
>>>
```



```python
# name: Charlie
/hello/?name=Charlie

# name: Charlie
# favorite_color: green
/hello/?name=Charlie&favorite_color=green
```
