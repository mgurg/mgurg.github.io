---
layout: post
title: "VPS po raz 4"
categories: DevOps
author: "Michał"
math: false
---

Kolejna reinstalacja, nowe założenia:

- Wordpress (LEMP)
- Statyczny frontend (Vue + Nginx)
- Brak backendu (mam API na AWS)

## Podstawowa konfiguracja



### Użytkownicy i logowanie

Dodanie nowego użytkownika którego będziemy używać do pracy zamiast konta `root`

```bash
adduser lambda
```

Dodanie użytkownika `lambda` do grupy administratorów:

```bash
gpasswd -a lambda sudo
```

Dodanie **niestandardowego** portu do połączeń przez SSH:

```bash
grep 'ssh' /etc/services

sudo ufw allow <port number>/tcp
```

Wyłączenie możliwości logowania użytkownika `root` poprzez SSH (+ zmiana domyslengo portu)

```bash
sudo nano /etc/ssh/sshd_config
```

```bash
PermitRootLogin yes
```

zamienić na: 

```bash
Protocol 2

PermitRootLogin no

Port <port number>
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::
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
12345/tcp                  ALLOW       Anywhere                  
OpenSSH                    ALLOW       Anywhere                  
12345/tcp (v6)             ALLOW       Anywhere (v6)             
OpenSSH (v6)               ALLOW       Anywhere (v6)  
```

restart

```bash
sudo service ssh restart
```

### Fail2Ban
```
sudo apt-get install fail2ban
sudo nano /etc/fail2ban/jail.local
```

```
[sshd]
enabled = true
port = 22
filter = sshd
logpath = /var/log/auth.log
maxretry = 5
banaction = ufw
banaction_allports = ufw
```

```
sudo systemctl restart fail2ban
```

```
sudo iptables -L f2b-sshd --line-numbers
```



O tym dlaczego warto to zrobić dla własnego bezpieczenstwa, można poczytać w artykule [Lessons Learned from SSH Credential Honeypots](https://systemoverlord.com/2020/09/04/lessons-learned-from-ssh-credential-honeypots.html)

Jeżeli mimo wszystko potrzebujesz naocznie przekonać się na jakie zagrożenia narażone sa komputery podłączone do internetu to polecam wykonanie komendy 

```bash
sudo grep "Failed password" /var/log/auth.log
```

## Aktualizacja systemu:

```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get --with-new-pkgs upgrade
```

```bash
sudo apt install unattended-upgrades
sudo dpkg-reconfigure -plow unattended-upgrades

# manual run
sudo unattended-upgrade -v
```



Przydatne programy:

```bash
sudo apt install mc
sudo apt-get install curl
sudo apt-get install htop
sudo apt install net-tools
```

W przypadku problemów z DNS (`Temporary failure resolving 'security.ubuntu.com'`):

```bash
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf > /dev/null
```

```bash
sudo nano /etc/systemd/resolved.conf

DNS=1.1.1.1 8.8.8.8
FallbackDNS=8.8.4.4
```

### Bezpieczeństwo

Wyświetlanie wszystkich bieżących połączeń, usług nasłuchowych i obsługujących je procesów.

```
netstat -tulpn
```

```
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.60:60           0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:12345           0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::12345                :::*                    LISTEN      -                   
udp        0      0 127.0.0.60:60           0.0.0.0:*                           -    
```

Wyświetlanie usług i ich stanu

```
service --status-all
```

```
 [ + ]  apparmor
 [ - ]  console-setup.sh
 [ + ]  cron
 [ + ]  dbus
 [ - ]  grub-common
 [ - ]  hwclock.sh
 [ - ]  irqbalance
 [ - ]  keyboard-setup.sh
 [ + ]  kmod
 [ + ]  networking
 [ + ]  plymouth
 [ + ]  plymouth-log
 [ + ]  procps
 [ - ]  rsync
 [ + ]  ssh
 [ + ]  udev
 [ + ]  ufw
 [ - ]  uuidd

```

Logi:

```bash
/var/log/message — Where whole system logs or current activity logs are available.
/var/log/auth.log — Authentication logs.
/var/log/kern.log — Kernel logs.
/var/log/cron.log — Crond logs (cron job).
/var/log/maillog — Mail server logs.
/var/log/boot.log — System boot log.
/var/log/mysqld.log — MySQL database server log file.
/var/log/secure — Authentication log.
/var/log/utmp or /var/log/wtmp — Login records file.
/var/log/apt — Apt package manager logs
```



### Python

### Instalacja podstawowych pakietów 

```bash
python3 -V
> Python 3.10.6
```

Instalacja i aktualizacja pip

```bash
sudo apt-get install python3-pip python3-dev python3-venv

sudo -H pip3 install --upgrade pip
```

```bash
pip3 -V
> pip 22.2.2 from /usr/local/lib/python3.10/dist-packages/pip (python 3.10)
```

Biblioteki systemowe:

```bash
sudo apt install build-essential libssl-dev libffi-dev 
```

#### Poetry

```bash
curl -sSL https://install.python-poetry.org | python3 -
poetry --version
```

```bash
poetry config --list

cache-dir = "/home/lambda/.cache/pypoetry"
experimental.new-installer = true
installer.parallel = true
virtualenvs.create = true
virtualenvs.in-project = null
virtualenvs.path = "{cache-dir}/virtualenvs"  # /home/lambda/.cache/pypoetry/virtualenvs
```

```bash
poetry config virtualenvs.in-project true
```

Usunięcie środowisk wirtualnych:
```
poetry env list
poetry env remove escaperoomvss-Rr4vsJhH-py3.10
```

## Docker

https://docs.docker.com/engine/install/ubuntu/

## Nginx

```
sudo apt-get install nginx
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx
nginx -v
```

### Firewall

Do konfiguracji firewalla można użyć (wyświetli dostępne domyślnie tryby):

```bash
sudo ufw app list
```

Wybranie któregoś z nich odbywa się poprzez:

```bash
sudo ufw allow 'Nginx Full'
```

Sprawdzenie statusu:

```bash
sudo ufw status
```

Wynik:

```
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx Full                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx Full (v6)            ALLOW       Anywhere (v6)  
```



`http://192.166.219.228/`



### PHP

```bash
sudo apt-get install php \
                 php-common \
                 php-fpm \
                 php-mysql \
                 php-bcmath \
                 php-curl \
                 php-imagick \
                 php-intl \
                 php-json \
                 php-mbstring \
                 php-mysql \
                 php-xml \
                 php-zip
```

```bash
sudo systemctl restart php8.1-fpm
```

Konfiguracja:

```
sudo nano /etc/php/8.1/fpm/php.ini
```

```
file_uploads = On
allow_url_fopen = On
short_open_tag = On
memory_limit = 256M
cgi.fix_pathinfo = 0
upload_max_filesize = 100M
max_execution_time = 360
date.timezone = America/Chicago
```



### MariaDB

```bash
sudo apt-get install mariadb-server mariadb-client
sudo systemctl start mariadb
sudo systemctl enable mariadb
systemctl status mariadb
```

```
sudo mysql_secure_installation
```

Ustawić logowanie jako `unix_socket`. Test logowania:

```
sudo mariadb -u root
```

Tworzenie bazy:

```
create database wordpress;
grant all privileges on wordpress.* to wpuser@localhost identified by 'your-password';
flush privileges;
exit;
```

### Wordpress

Konfiguracja NGINX:

```
sudo nano /etc/nginx/sites-available/default
```

```nginx
server {
	listen 80 default_server;
	listen [::]:80 default_server;

	# SSL configuration
	#
	# listen 443 ssl default_server;
	# listen [::]:443 ssl default_server;
	#
	# Note: You should disable gzip for SSL traffic.
	# See: https://bugs.debian.org/773332
	#
	# Read up on ssl_ciphers to ensure a secure configuration.
	# See: https://bugs.debian.org/765782
	#
	# Self signed certs generated by the ssl-cert package
	# Don't use them in a production server!
	#
	# include snippets/snakeoil.conf;

	root /var/www/html;

	# Add index.php to the list if you are using PHP
	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}

	# pass PHP scripts to FastCGI server
	#
	#location ~ \.php$ {
	#	include snippets/fastcgi-php.conf;
	#
	#	# With php-fpm (or other unix sockets):
	#	fastcgi_pass unix:/run/php/php7.4-fpm.sock;
	#	# With php-cgi (or other tcp sockets):
	#	fastcgi_pass 127.0.0.1:9000;
	#}

	# deny access to .htaccess files, if Apache's document root
	# concurs with nginx's one
	#
	#location ~ /\.ht {
	#	deny all;
	#}
}


# Virtual Host configuration for example.com
#
# You can move that to a different file under sites-available/ and symlink that
# to sites-enabled/ to enable it.
#
#server {
#	listen 80;
#	listen [::]:80;
#
#	server_name example.com;
#
#	root /var/www/example.com;
#	index index.html;
#
#	location / {
#		try_files $uri $uri/ =404;
#	}
#}

```

```nginx
server {
    listen 80;
    listen [::]:80;
    root /var/www/wordpress;
    index  index.php index.html index.htm;
    server_name  remontmaszyn.pl www.remontmaszyn.pl;

    client_max_body_size 100M;
    autoindex off;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location = /favicon.ico { log_not_found off; access_log off; }
    location = /robots.txt { log_not_found off; access_log off; allow all; }
    location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
        expires max;
        log_not_found off;
    }

    location ~ \.php$ {
         include snippets/fastcgi-php.conf;
         fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
         fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
         include fastcgi_params;
    }
}

```

```
sudo nginx -t
sudo systemctl reload nginx
```

Pobranie i instalacja WP:

```bash
cd /tmp
curl -LO https://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
cp /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.php
sudo cp -a /tmp/wordpress/. /var/www/wordpress
sudo chown -R www-data:www-data /var/www/wordpress
```

```
curl -s https://api.wordpress.org/secret-key/1.1/salt/
```

```
define('AUTH_KEY',         '...[SJ4AS-J(5VeH3@.giOLT`+P8JjC !i&^]vBW#TYrj...');
define('SECURE_AUTH_KEY',  '...2ZEnrgr2$s yy?m|{_u_(=VEiH--!sO4DM1eJ`VB5g...');
define('LOGGED_IN_KEY',    '...39SJA#L3^9[7x@V:UN)341Wy*r6?m-N>(~CnB-{Ptv...');
define('NONCE_KEY',        '...HI!FtQ QM<53q]cvKAxWkVpV w;?3wK! ->>](1+sL...');
define('AUTH_SALT',        '...X8E~37c-3+N@rc+tSD]Mldw8<%MMqfB+0?x{[uj.a$...');
define('SECURE_AUTH_SALT', '...7]k=C#XFs&ikM9FUn8Uf*2||plxn{e_+97~N!t&T8D...');
define('LOGGED_IN_SALT',   '...4l l1F&rk7tuI[{a$V1}E/b=KNxbVDg@(]+Oh+mFbR...');
define('NONCE_SALT',       '...%A}R9{9|Q[L?0s:tej}%>iD84ge([3hn<|(nwBfp>A...');
```

```
sudo nano /var/www/wordpress/wp-config.php
```

```
. . .

define( 'DB_NAME', 'wordpress' );

/** MySQL database username */
define( 'DB_USER', 'wordpressuser' );

/** MySQL database password */
define( 'DB_PASSWORD', 'password' );

. . .

define( 'FS_METHOD', 'direct' );
```





https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-lemp-on-ubuntu-20-04

https://www.linuxbabe.com/ubuntu/install-lemp-stack-ubuntu-20-04-server-desktop

https://websiteforstudents.com/how-to-install-wordpress-on-ubuntu-linux-with-nginx/



## Subdomeny

- Wordpress - domain.com
- app - app.domain.com

Pierwszy krok: dodanie rekordu `A`  w panelu ovsługi domeny: `beta IN A 192.166.219.228`



Zmiana konfiguracji `sudo nano /etc/nginx/sites-available/default`:

```
server {
    listen 80;
    listen [::]:80;
    root /var/www/wordpress;
    index  index.php index.html index.htm;
    server_name  remontmaszyn.pl www.remontmaszyn.pl;

    client_max_body_size 100M;
    autoindex off;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location = /favicon.ico { log_not_found off; access_log off; }
    location = /robots.txt { log_not_found off; access_log off; allow all; }
    location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
        expires max;
        log_not_found off;
    }

    location ~ \.php$ {
         include snippets/fastcgi-php.conf;
         fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
         fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
         include fastcgi_params;
    }
}

server {
    listen 80;
    listen [::]:80;
    root /var/www/beta;
    index index.html index.nginx-debian.html;
    server_name beta.remontmaszyn.pl;
    
    location / {
        try_files $uri $uri/ /index.html;
    }

}

server {
    listen 80;
    listen [::]:80;
    root /var/www/prod;
    index index.html index.nginx-debian.html;
    server_name app.remontmaszyn.pl;
    
    location / {
        try_files $uri $uri/ /index.html;
    }
}


```

Zatrzymanie Apache

```
sudo apachectl stop
```

### NGINX Amplify
[How NGINX Amplify Agent Works](https://amplify.nginx.com/docs/guide-how-nginx-amplify-agent-works.html#configuring-nginx-for-metric-collection?utm_source=email&utm_medium=amplify&utm_campaign=welcome)

Monitoring serwera (zasoby + NGINX):

```
    location /nginx_status {
        stub_status on;
        access_log off;
        allow 127.0.0.1;
        deny all;
    }
```

### SSL

Certbot:

```
sudo apt install snapd
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot

sudo certbot --nginx
```

