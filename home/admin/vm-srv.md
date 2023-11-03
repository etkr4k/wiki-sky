---
title: Installation
description: 
published: true
date: 2023-11-03T00:28:37.683Z
tags: 
editor: markdown
dateCreated: 2022-07-11T08:42:41.619Z
---

# Install Docker
`apt-get update && apt-get upgrade -y`

```
apt install \
ca-certificates \
curl \
gnupg \
lsb-release
```

`curl -fsSL https://get.docker.com -o get-docker.sh`

`sh get-docker.sh`

# Install Portainer
`docker volume create portainer_data`
```
docker run -d -p 8000:8000 -p 9443:9443 --name portainer \
    --restart=always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v portainer_data:/data \
    portainer/portainer-ce:latest
```
`mkdir -p /etc/systemd/system/docker.service.d`

`nano /etc/systemd/system/docker.service.d/options.conf`


```
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H unix:// -H tcp://0.0.0.0:2375
```


`systemctl daemon-reload`

`systemctl restart docker`


# Install Nginx Proxy Manager

```
version: "3"
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: always
    ports:
      # These ports are in format <host-port>:<container-port>
      - '80:80' # Public HTTP Port
      - '443:443' # Public HTTPS Port
      - '81:81' # Admin Web Port
      # Add any other Stream port you want to expose
      # - '21:21' # FTP
    environment:
      DB_MYSQL_HOST: "db"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "user42"
      DB_MYSQL_PASSWORD: "please84WAITme1349"
      DB_MYSQL_NAME: "npm"
      # Uncomment this if IPv6 is not enabled on your host
      # DISABLE_IPV6: 'true'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    depends_on:
      - db

  db:
    image: 'jc21/mariadb-aria:latest'
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: '7tehbjguymkyhtjmgytjyi9kG'
      MYSQL_DATABASE: 'npm'
      MYSQL_USER: 'user42'
      MYSQL_PASSWORD: 'please84WAITme1349'
    volumes:
      - ./data/mysql:/var/lib/mysql
```

# Install Wiki.js
```
version: '2'
services:
  db:
    image: postgres:11-alpine
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: wikijsrocks
      POSTGRES_USER: wikijs
    logging:
      driver: "none"
    restart: unless-stopped
    volumes:
      - db-data:/var/lib/postgresql/data

  wiki:
    image: requarks/wiki:2
    depends_on:
      - db
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
    restart: unless-stopped
    ports:
      - "8080:3000"

volumes:
  db-data:
  ```
  
# Install ZeroTier Controller
#### Устанавливаем необходимые пакеты
```
apt-get install -y apt-transport-https gnupg mc iftop
```
#### Устанавливаем ZeroTierOne
```
curl -s https://install.zerotier.com | sudo bash
```
#### Устанавливаем систему управления ZeroTier Controller - ZTNCUI
```
curl -O https://s3-us-west-1.amazonaws.com/key-networks/deb/ztncui/1/x86_64/ztncui_0.7.1_amd64.deb
apt-get install ./ztncui_0.7.1_amd64.deb
```
#### Устанавливаем порт подключения 3443, или любой свой.
```
sh -c "echo 'HTTPS_PORT=3443' > /opt/key-networks/ztncui/.env"`
```
```
sh -c "echo 'NODE_ENV=production' >> /opt/key-networks/ztncui/.env"
```
#### Перезагружаем контроллер
```
systemctl restart ztncui
```

> Если по каким-то причинам ztncui не запускается
> ```
> sh -c "echo 'HTTPS_HOST=IP.XX.XX.XX' > /opt/key-networks/ztncui/.env"
> ```
>  
{.is-info}
