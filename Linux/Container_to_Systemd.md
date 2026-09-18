# Documentation Symphonie – Podman / Systemd

## Table des matières
- Snapshot état d’origine
- Désactivation de cron
- Création du fichier `.env`
- Redémarrage des containers
- Génération des units systemd
- Stop + suppression des containers
- Rechargement + activation
- Correction Redis
- Amélioration 3 : dépendances systemd
- Scripts de gestion (restart / stop / start)

---

# Snapshot état d’origine

## Désactivation de cron

```bash
crontab -l
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
#MAILTO=root
#@reboot /bin/bash /Exploitation/Redhat_Update.sh
#@reboot sleep 120 && /astechReboot.sh >> /var/log/astechreboot.log 2>&1
#00 1 * * * sudo /bin/podman-compose -f /home/... exec -T php bash /checkDroits.sh >/dev/null 2>&1
```

---

# Création du fichier `.env`

```bash
podman-compose -f /home/symphoniedeploy/symphonie/docker-compose.yml down
systemctl disable --now podman-compose.service
echo "HOSTNAME=symphonie-box" > /home/symphoniedeploy/symphonie/.env
podman-compose -f /home/symphoniedeploy/symphonie/docker-compose.yml up -d
podman ps -a
```

---

# État des containers

```bash
CONTAINER ID  IMAGE                                     STATUS   NAMES
de9f0ff81a2d  elasticsearch:7.17.28                     Up       elasticsearch
f58d5bf5dc36  rabbitmq:3.13.6                           Up       rabbitmq
5c5ac3663377  redis:7.4                                 Exited   symphonie_redis_1
168b278fc8e2  mercure:v0.19.3                           Up       mercure-hub
69e2301dd1f8  prod-talend:6.07.921.00.00                Up       symphonie-box-talend
c8381f7b3a4b  prod-php:6.07.921.00.00                   Up       symphonie-box-php
77f57fb78dc1  prod-nginx:6.07.921.00.00                 Up       nginx
88c4463ed728  prod-cmd-esmaj:6.07.921.00.00             Up       symphonie-box-cmd-esmaj
6a46293d8bfe  prod-cmd-general:6.07.921.00.00           Up       symphonie-box-cmd-general
f4f3d5e00602  prod-cmd-elasticsearch:6.07.921.00.00     Up       symphonie-box-cmd-elasticsearch
0415751fc197  prod-cmd-elasticsearch-slow:6.07.921.00.00 Up      symphonie-box-cmd-elasticsearch-slow
8f4c6879fff9  prod-cmd-notifications:6.07.921.00.00     Up       symphonie-box-cmd-notifications
```

---

# Génération des units systemd

```bash
cd /home/symphoniedeploy/symphonie

podman generate systemd --name rabbitmq --files --new
podman generate systemd --name elasticsearch --files --new
podman generate systemd --name mercure-hub --files --new
podman generate systemd --name symphonie_redis_1 --files --new
podman generate systemd --name symphonie-box-talend --files --new
podman generate systemd --name symphonie-box-php --files --new
podman generate systemd --name nginx --files --new
podman generate systemd --name symphonie-box-cmd-esmaj --files --new
podman generate systemd --name symphonie-box-cmd-general --files --new
podman generate systemd --name symphonie-box-cmd-elasticsearch --files --new
podman generate systemd --name symphonie-box-cmd-elasticsearch-slow --files --new
podman generate systemd --name symphonie-box-cmd-notifications --files --new

mv container-*.service /etc/systemd/system/
```

---

# Stop + suppression des containers

```bash
systemctl stop \
  container-symphonie-box-cmd-esmaj.service \
  container-symphonie-box-cmd-general.service \
  container-symphonie-box-cmd-elasticsearch.service \
  container-symphonie-box-cmd-elasticsearch-slow.service \
  container-symphonie-box-cmd-notifications.service \
  container-nginx.service \
  container-symphonie-box-php.service \
  container-symphonie-box-talend.service \
  container-symphonie_redis_1.service \
  container-mercure-hub.service \
  container-rabbitmq.service \
  container-elasticsearch.service

podman rm -f \
  symphonie-box-cmd-esmaj \
  symphonie-box-cmd-general \
  symphonie-box-cmd-elasticsearch \
  symphonie-box-cmd-elasticsearch-slow \
  symphonie-box-cmd-notifications \
  nginx \
  symphonie-box-php \
  symphonie-box-talend \
  symphonie_redis_1 \
  mercure-hub \
  rabbitmq \
  elasticsearch
```

---

# Rechargement + activation

```bash
systemctl daemon-reload

systemctl enable --now container-rabbitmq.service
systemctl enable --now container-elasticsearch.service
systemctl enable --now container-mercure-hub.service
systemctl enable --now container-symphonie_redis_1.service
systemctl enable --now container-symphonie-box-php.service
systemctl enable --now container-symphonie-box-talend.service
systemctl enable --now container-nginx.service
systemctl enable --now container-symphonie-box-cmd-esmaj.service
systemctl enable --now container-symphonie-box-cmd-general.service
systemctl enable --now container-symphonie-box-cmd-elasticsearch.service
systemctl enable --now container-symphonie-box-cmd-elasticsearch-slow.service
systemctl enable --now container-symphonie-box-cmd-notifications.service
```

---

# Correction Redis

## Fichier mot de passe

```bash
echo "REDIS_PASSWORD=LEMOTDEPASSE" > /root/symphonie.env
chmod 600 /root/symphonie.env
```

## Modifier l’unit

Dans `/etc/systemd/system/container-symphonie_redis_1.service` :

```ini
EnvironmentFile=/root/symphonie.env
ExecStart=/usr/bin/redis-server --requirepass ${REDIS_PASSWORD}
```

## Redémarrer

```bash
systemctl daemon-reload
systemctl start container-symphonie_redis_1.service
systemctl status container-symphonie_redis_1.service
```

---

# Amélioration 3 – Dépendances systemd

## CMD-* dépendent de rabbitmq + elasticsearch + php

```bash
for svc in symphonie-box-cmd-esmaj symphonie-box-cmd-general symphonie-box-cmd-elasticsearch symphonie-box-cmd-elasticsearch-slow; do
  sed -i 's/^After=network-online.target/After=network-online.target container-rabbitmq.service container-elasticsearch.service container-symphonie-box-php.service\nRequires=container-rabbitmq.service container-elasticsearch.service container-symphonie-box-php.service/' \
  /etc/systemd/system/container-${svc}.service
done
```

## Notifications dépend de mercure-hub + php

```bash
sed -i 's/^After=network-online.target/After=network-online.target container-mercure-hub.service container-symphonie-box-php.service\nRequires=container-mercure-hub.service container-symphonie-box-php.service/' \
/etc/systemd/system/container-symphonie-box-cmd-notifications.service
```

## php dépend de elasticsearch

```bash
sed -i 's/^After=network-online.target/After=network-online.target container-elasticsearch.service\nRequires=container-elasticsearch.service/' \
/etc/systemd/system/container-symphonie-box-php.service
```

## nginx dépend de php

```bash
sed -i 's/^After=network-online.target/After=network-online.target container-symphonie-box-php.service\nRequires=container-symphonie-box-php.service/' \
/etc/systemd/system/container-nginx.service
```

---

# Scripts de gestion

## symphonie-restart.sh

```bash
#!/bin/bash

SERVICES_STOP="container-symphonie-box-cmd-esmaj.service container-symphonie-box-cmd-general.service container-symphonie-box-cmd-elasticsearch.service container-symphonie-box-cmd-elasticsearch-slow.service container-symphonie-box-cmd-notifications.service container-nginx.service container-symphonie-box-php.service container-symphonie-box-talend.service container-symphonie_redis_1.service container-mercure-hub.service container-rabbitmq.service container-elasticsearch.service"

CONTAINERS="symphonie-box-cmd-esmaj symphonie-box-cmd-general symphonie-box-cmd-elasticsearch symphonie-box-cmd-elasticsearch-slow symphonie-box-cmd-notifications nginx symphonie-box-php symphonie-box-talend symphonie_redis_1 mercure-hub rabbitmq elasticsearch"

SERVICES_START="container-rabbitmq.service container-elasticsearch.service container-mercure-hub.service container-symphonie_redis_1.service container-symphonie-box-php.service container-symphonie-box-talend.service container-nginx.service container-symphonie-box-cmd-esmaj.service container-symphonie-box-cmd-general.service container-symphonie-box-cmd-elasticsearch.service container-symphonie-box-cmd-elasticsearch-slow.service container-symphonie-box-cmd-notifications.service"

echo "=== Stop des services ==="
systemctl stop $SERVICES_STOP

echo "=== Suppression des containers ==="
podman rm -f $CONTAINERS 2>/dev/null

echo "=== Démarrage des services ==="
systemctl start $SERVICES_START

echo "=== Etat final ==="
podman ps -a
```

## symphonie-stop.sh

```bash
#!/bin/bash

SERVICES="container-symphonie-box-cmd-esmaj.service container-symphonie-box-cmd-general.service container-symphonie-box-cmd-elasticsearch.service container-symphonie-box-cmd-elasticsearch-slow.service container-symphonie-box-cmd-notifications.service container-nginx.service container-symphonie-box-php.service container-symphonie-box-talend.service container-symphonie_redis_1.service container-mercure-hub.service container-rabbitmq.service container-elasticsearch.service"

CONTAINERS="symphonie-box-cmd-esmaj symphonie-box-cmd-general symphonie-box-cmd-elasticsearch symphonie-box-cmd-elasticsearch-slow symphonie-box-cmd-notifications nginx symphonie-box-php symphonie-box-talend symphonie_redis_1 mercure-hub rabbitmq elasticsearch"

echo "=== Stop des services ==="
systemctl stop $SERVICES

echo "=== Suppression des containers ==="
podman rm -f $CONTAINERS 2>/dev/null

echo "=== Etat final ==="
podman ps -a
```

## symphonie-start.sh

```bash
#!/bin/bash

SERVICES="container-rabbitmq.service container-elasticsearch.service container-mercure-hub.service container-symphonie_redis_1.service container-symphonie-box-php.service container-symphonie-box-talend.service container-nginx.service container-symphonie-box-cmd-esmaj.service container-symphonie-box-cmd-general.service container-symphonie-box-cmd-elasticsearch.service container-symphonie-box-cmd-elasticsearch-slow.service container-symphonie-box-cmd-notifications.service"

echo "=== Démarrage des services ==="
systemctl start $SERVICES

echo "=== Etat final ==="
podman ps -a
```
