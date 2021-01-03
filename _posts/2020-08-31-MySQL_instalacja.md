---
layout: post
title: "MySQL - instalacja"
categories: SQL
author: "Michał"
math: false
---

Obecnie SQLite wystarcza w 100% do wszystkich zadań które wykonuje, ale na wypadek gdybym robił jakiś większy (lub poważniej wyglądający) projekt to napisałem opis instalacji MySQL na zdalnym serwerze (z możliwością zdalnego logowania).

Opis za [How To Install MySQL on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-20-04)

### Instalacja

```bash
sudo apt install mysql-server
```
Ustawienie hasła:

```bash
sudo mysql_secure_installation
```

### Konfiguracja (lokalny użytkownik)

```bash
sudo mysql
```

Tworzenie lokalnego użytkownika, ogólny zapis:

```sql
CREATE USER 'username'@'host' IDENTIFIED WITH authentication_plugin BY 'password';
```

Tworzenia lokalnego użytkownika `lammy` będzie wyglądało następująco: 
```sql
CREATE USER 'lammy'@'localhost' IDENTIFIED BY 'password123';
```

Nadanie uprwanień:

```sql
GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'lammy'@'localhost' WITH GRANT OPTION;
```

```bash
mysql> FLUSH PRIVILEGES;
mysql> exit
```

Logowanie: 
```bash
mysql -u lammy -p
```

### Test działania
```
systemctl status mysql.service
```

```bash
* mysql.service - MySQL Community Server
     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor prese>
     Active: active (running) since Mon 2020-08-31 12:34:39 CEST; 47min ago
   Main PID: 925635 (mysqld)
     Status: "Server is operational"
      Tasks: 40 (limit: 4657)
     Memory: 332.5M
     CGroup: /system.slice/mysql.service
             `-925635 /usr/sbin/mysqld
```

W razie problemów można uruchomic usługę ręcznie:

```bash
sudo systemctl start mysql
```

Dodatkowy test: połaczenie za pomocą *mysqladmin*

```bash
sudo mysqladmin -p -u lammy version

```
Wynik:

```bash
mysqladmin  Ver 8.0.21-0ubuntu0.20.04.4 for Linux on x86_64 ((Ubuntu))
Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version          8.0.21-0ubuntu0.20.04.4
Protocol version        10
Connection              Localhost via UNIX socket
UNIX socket             /var/run/mysqld/mysqld.sock
Uptime:                 11 min 0 sec

Threads: 2  Questions: 2  Slow queries: 0  Opens: 115  Flush tables: 3  Open tables: 36  Queries per second avg: 0.003
```

### Zdalne logowanie

W pliku
```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
należy dokonać zmiany wpisu z `bind-address = 127.0.0.1` na 
```
bind-address = 0.0.0.0
```

Firewall dozwolone adresy IP:
```bash
sudo ufw allow from IP_ADRESS to any port 3306

sudo ufw allow 3306
```
Restart:
```
sudo systemctl restart mysql
```

Logowanie:
```
mysql -u lammy -h 192.166.219.228 -p
```

Jeżeli nie działa to nalezy sprawdzić czy użytkownik może logować się zdalnie. Po zalogowaniu do MySQL:

```
mysql> SELECT host FROM mysql.user WHERE User = 'lammy';
+-----------+
| host      |
+-----------+
| localhost |
+-----------+
1 row in set (0.02 sec)
```

Jeśli widzisz wyniki tylko z `localhost` i `127.0.0.1`, nie możesz połączyć się z zewnętrz. Jeśli widzisz inne adresy IP, ale nie ten, z którego się łączysz to połączenie również nie będzie możliwe.

Znak `%` zamiast `localhost` pozwoli na łączenie się z dowolnego adresu IP. Utworzymy nowego użytkownika: 
```sql
CREATE USER 'rammy'@'%' IDENTIFIED BY 'password123';

GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'rammy'@'%' WITH GRANT OPTION;

FLUSH PRIVILEGES;

```

W przypadku problemów z logowaniem , naeży zmienić sposób uwierzytelniania na starszy:

```sql
ALTER USER 'rammy'@'%' IDENTIFIED WITH mysql_native_password BY 'password123';
```

## Docker
Do lokalnych testów postanowiłem nie instalować MySQL tylko skorzystać z gotowego obrazu [docker](https://mgurg.github.io/docker/2020/08/05/Docker.html)

Opis na podstawie: [Simple Persistent MySQL in Linux Server with Docker and Docker Compose](https://blog.usejournal.com/simple-persistent-mysql-in-linux-server-with-docker-and-docker-compose-66547e89a19e) 

Zawartość pliku `docker-compose.yml`:
```yaml
version: '3'
services:
 mysql:
  image: mysql:8
  environment:
   MYSQL_DATABASE: 'fastapi_db'
   MYSQL_USER: 'fuser'
   MYSQL_PASSWORD: 'fpass'
   MYSQL_ROOT_PASSWORD: 'root'
  ports:
   - "3306:3306"
  volumes:
   - mysql_volume:/var/lib/mysql
volumes:
 mysql_volume:
```

Uruchomienie: 
```bash
sudo docker-compose up -d
```

Zatrzymanie:
```bash
sudo docker-compose stop
```

Ustanowienie połączenia (dBeaver):
```
Connection Method : Standard (TCP/IP)
Hostname : 127.0.0.1 (or your remote server address)
Port : 3306
Username: root (or using other user)
Password: root (your root password)
```

Dodatkowe ustawienia dBeaver (Błąd: *Public Key Retrieval is not allowed*)
- "useSSL" - FALSE
- "allowPublicKeyRetrieval" - TRUE

