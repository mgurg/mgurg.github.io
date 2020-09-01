---
layout: post
title: "MySQL - instalacja"
categories: SQL
author: "Michał"
math: false
---

Obecnie SQLite wystarcza w 100% do wszytkich zadań które wykonuje, ale na wypadek gdybym robił ajakiś wiekszy (lub powązniej wyglądający) projekt to napisałem opis instalcaji MySQL na zdalnym serwerze (z możliwoiścia zdalnego logowania).

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

Lokalny user:

```mysql
CREATE USER 'username'@'host' IDENTIFIED WITH authentication_plugin BY 'password';

CREATE USER 'lammy'@'localhost' IDENTIFIED BY 'password123';
```


```mysql
GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'lammy'@'localhost' WITH GRANT OPTION;
```

```mysql
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

```
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Zamiana wpisu `bind-address = 127.0.0.1` na 
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

Jeśli widzisz wyniki tylko z `localhost` i `127.0.0.1`, nie możesz połączyć się z zewnętrz. Jeśli widzisz inne adresy IP, ale nie ten, z którego się łączysz to połączenie równierz nie będzei możliwe.

```mysql
CREATE USER 'rammy'@'%' IDENTIFIED BY 'password123';

GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'rammy'@'%' WITH GRANT OPTION;

FLUSH PRIVILEGES;

```


