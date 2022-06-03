---
layout: post
title: "FastApi: Autoryzacja"
categories: Python
author: "Michał"
---

Mam działający serwis z możliwością używania danych konfiguracyjnych, pora na zaproszenie na niego użytkowników



Doinstalowanie brakujących pakietów:

````
source fast_ve/bin/activate

pip install python-jose[cryptography]
pip install passlib[bcrypt]
pip install SQLAlchemy
pip install mysql-connector
````

```
~/py_env/www/app$ gunicorn main:app -w 2 -k uvicorn.workers.UvicornWorker -b "0.0.0.0:5000"
```

