---
layout: post
title: "FastApi: konfiguracja (dotenv) (III)"
categories: Python
author: "Michał"
---

Jak pisałem wcześniej, sporo tutoriali dla FastAPI kończy się na kodzie hello world. Jeżeli myślimy o poważnym serwisie to trzeba przygotować sobie pod niego teren.

Dzisiaj przejdziemy od:

````python
import uvicorn
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hello World"}

if __name__ == '__main__':
    uvicorn.run("main:app")
````

```
└── project
    └── app
        ├── __init__.py
        └── main.py
```

do wersji:

```python
import uvicorn
from fastapi import FastAPI, Depends
from fastapi.security import OAuth2PasswordBearer
from config.settings import get_settings

settings = get_settings()

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

@app.get("/")
async def root():
    return {"message": "Hello World"}

@app.get("/users")
async def read_users(skip: int = 0, limit: int = 100):
    return {"skip" : skip,
	    "limit" : limit}

@app.get("/items/")
async def read_items(token: str = Depends(oauth2_scheme)):
    return {"token": token}

if __name__ == '__main__':
    if settings.env != "production":
        uvicorn.run("main:app", host=settings.HOST, port=settings.PORT, reload=True, debug=True)
    else:
        uvicorn.run("main:app")
```

```bash
├── __init__.py
├─ .env
├─ main.py
└─ config
    ├── __init__.py
    └── settings.py
```

### Plik env

Pojawił moduł z ustawieniami (`settings.py`) który pobiera informacje z plik `.env`:

```bash
# .env.sample
NODE_ENV=<production|development>
PORT=<open-port>
API_KEY=<github-key>

APP_NAME=<app-name>
APP_DEBUG=<true|false>

LOG_LEVEL=debug
WORKING_ENVIRONMENT=<production|development>
```

### Odczyt ustawień:

```python
import logging
import os

from pathlib import Path
from dotenv import load_dotenv

from functools import lru_cache
from pydantic import BaseSettings

env_path = Path(".") /"app"/".env"
load_dotenv(dotenv_path=env_path)

class Settings(BaseSettings):
    ENV: str = os.getenv("WORKING_ENVIRONMENT", "dev")
    NAME: str = os.getenv("APP_NAME", "no-name")

    HOST: str = os.getenv("HOST", "127.0.0.1")
    PORT: int = os.getenv("PORT", 5000)

    class Config:
        env_prefix = ""
        env_file_encoding = "utf-8"
        env_file = ".env"

@lru_cache()
def get_settings() -> BaseSettings:
    return Settings()
```

