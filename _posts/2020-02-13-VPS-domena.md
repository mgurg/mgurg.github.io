---
layout: post
title: "Serwer VPS - domena"
categories: python
author: "Michał"
---



Bazując na poradniku [Łukasza Prokulskiego](https://blog.prokulski.science/index.php/2018/06/14/serwer-vps-dla-r-python/)  

| Domena             | TTL  |       |                 |
| ------------------ | ---- | ----- | --------------- |
| mojadomena.pl.     | 0    | A     | 192.166.219.228 |
| lab.mojadomena.pl. | 0    | CNAME | mojadomena.pl.  |
| lab.mojadomena.pl. | 0    | CNAME | mojadomena.pl.  |

```bash
IN A 192.166.219.228
lab IN CNAME mojadomena.pl.
jupyter IN CNAME mojadomena.pl.
```



Plik `jupyter.conf` w `/etc/nginx/sites-enabled/`:

```
server {
  listen 80;
  
  server_name jupyter.mojadomena.pl;
  
  location / {
    proxy_pass http://localhost:8888;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Host $http_host;
    proxy_http_version 1.1;
    proxy_redirect off;
    proxy_buffering off;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_read_timeout 86400;
  }
  
  location ~ /api/kernels/ {
    proxy_pass            http://localhost:8888;
    proxy_set_header      Host $host;
    
    # websocket support
    proxy_http_version    1.1;
    proxy_set_header      Upgrade "websocket";
    proxy_set_header      Connection "Upgrade";
    proxy_read_timeout    86400;
  }
  
  location ~ /terminals/ {
    proxy_pass            http://localhost:8888;
    proxy_set_header      Host $host;
    
    # websocket support
    proxy_http_version    1.1;
    proxy_set_header      Upgrade "websocket";
    proxy_set_header      Connection "Upgrade";
    proxy_read_timeout    86400;
  }
}
```



Restart nginx

```
sudo systemctl reload nginx
```

