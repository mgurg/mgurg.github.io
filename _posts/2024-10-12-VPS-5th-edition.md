---
layout: post
title: "VPS po raz 5 (kamal)"
categories: DevOps
author: "Michał"
math: false
---

Kolejna reinstalacja, tym razem kamal + obrazy docker

## Podstawowa konfiguracja

### Ruby

```bash
sudo apt install -y git curl autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm-dev
```

Instalacja RBENV

```bash
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash
```

```bash
rbenv install -l   # Lists all available Ruby versions
```

Instalacja wybranej wersji ruby i ustawienie jej jako domyślnej (globalnie):

```bash
rbenv install 3.3.5
rbenv global 3.3.5
ruby -v
```

Instalacja kamal

```bash
gem install kamal
kamal version
```

```bash
mkdir kamal
kamal init
```

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

```bash
sudo ufw app list
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status
```

Poprawna konfiguracja:

```bash
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

### Wysyłanie klucza SSH na wskazany serwer

```bash
ssh-copy-id root@1.2.3.4
```

`ssh-copy-id` może przyjąć dodatkowy parametr -i w którym, możemy wskazać który klucz ma być podpięty, jeśli mamy więcej niż jeden.

```bash
ssh-copy-id -i ~/.ssh/id_rsa root@1.2.3.4
```

Polecenie ssh-copy-id nie robi nic innego niż to, że wkleja nasz klucz publiczny SSH do .ssh/authorized_keys w folderze domowym użytkownika, na którego się logujemy. Możemy też ręcznie dopisać nasz klucz w tym pliku. Jeżeli nie istnieje to możemy stworzyć ten plik(oraz folder nadrzędny .ssh, jeżeli taki też nie istnieje).

### Fail2Ban

```bash
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

```bash
sudo systemctl restart fail2ban
```

```bash
sudo iptables -L f2b-sshd --line-numbers
```

O tym dlaczego warto to zrobić dla własnego bezpieczenstwa, można poczytać w artykule [Lessons Learned from SSH Credential Honeypots](https://systemoverlord.com/2020/09/04/lessons-learned-from-ssh-credential-honeypots.html)

Jeżeli mimo wszystko potrzebujesz naocznie przekonać się na jakie zagrożenia narażone sa komputery podłączone do internetu to polecam wykonanie komendy

```bash
sudo grep "Failed password" /var/log/auth.log
```

## Aktualizacja systemu

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

## Docker

<https://docs.docker.com/engine/install/ubuntu/>

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
