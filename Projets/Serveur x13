Esp32  
Pour flasher la premiere fois en usb depuis le srv :  
docker run --rm -it   -v /opt/esphome/config:/config   esphome/esphome run EspCam1.yaml --device /dev/ttyUSB0  

Pyenv ne fonctionne pas sans docker  

Pour démarrer esp simplement :  
docker run --rm -it   -v /opt/esphome/config:/config   esphome/esphome run /config/EspCam2.yaml  

Et si il ne vois pas l'esp32 (.local)  
ping esp32cam2.local  
docker run --rm -it   -v /opt/esphome/config:/config   esphome/esphome run /config/EspCam2.yaml --device 192.168.0.16  


────────────────────────────────────────────────────────────  
                 TUTO INSTALLATION & UTILISATION NGINX  
────────────────────────────────────────────────────────────  

📌 1) Installation de NGINX  
sudo apt update  
sudo apt install nginx -y  

📌 2) Vérifier que NGINX fonctionne  
systemctl status nginx  
→ Doit afficher “active (running)”  

📌 3) Démarrer / Arrêter / Redémarrer NGINX  
sudo systemctl start nginx  
sudo systemctl stop nginx  
sudo systemctl restart nginx  
sudo systemctl reload nginx   (recharge la config sans couper)  

📌 4) Emplacement des fichiers importants  
/etc/nginx/nginx.conf          → config principale  
/etc/nginx/sites-available/    → configs des sites  
/etc/nginx/sites-enabled/      → sites activés  
/var/www/html/                 → dossier web par défaut  

📌 5) Créer un site web simple  
sudo mkdir -p /var/www/mon_site  
sudo nano /var/www/mon_site/index.html  

<h1>Bienvenue sur mon serveur NGINX</h1>  

📌 6) Créer une configuration NGINX pour ce site  
sudo nano /etc/nginx/sites-available/mon_site  

server {  
    listen 80;  
    server_name monsite.local;  

    root /var/www/mon_site;  
    index index.html;  

    location / {  
        try_files $uri $uri/ =404;  
    }  
}  

📌 7) Activer le site  
sudo ln -s /etc/nginx/sites-available/mon_site \  
           /etc/nginx/sites-enabled/  

📌 8) Tester la configuration  
sudo nginx -t  

📌 9) Recharger NGINX  
sudo systemctl reload nginx  

📌 10) Accéder au site  
http://monsite.local  
(ou l’IP de ton serveur)  


────────────────────────────────────────────────────────────  
                 UTILISATION COURANTE DE NGINX  
────────────────────────────────────────────────────────────  

▶️ Voir les logs d’accès  
sudo tail -f /var/log/nginx/access.log  

▶️ Voir les logs d’erreur  
sudo tail -f /var/log/nginx/error.log  

▶️ Modifier un site  
sudo nano /etc/nginx/sites-available/mon_site  
sudo systemctl reload nginx  

▶️ Désactiver un site  
sudo rm /etc/nginx/sites-enabled/mon_site  
sudo systemctl reload nginx  

▶️ Vérifier les ports ouverts  
sudo ss -tulpn | grep nginx  


────────────────────────────────────────────────────────────  
Esp8266  
────────────────────────────────────────────────────────────  

sudo apt update  
sudo apt upgrade -y  
sudo apt install python3 python3-pip python3-venv -y  
sudo mkdir -p /opt/esphome  
sudo chown -R $USER:$USER /opt/esphome  
cd /opt/esphome  
python3 -m venv venv  
source venv/bin/activate  
pip install --upgrade pip  
pip install esphome  

1) Compiler et flasher en Wi‑Fi  
esphome run EspCam2.yaml  

(si mDNS ne marche pas)  
esphome run EspCam2.yaml --device 192.168.0.16  

2) Compiler seulement (sans flasher)  
esphome compile EspCam2.yaml  

3) Voir les logs en Wi‑Fi  
esphome logs EspCam2.yaml  

4) Nettoyer le build (si erreurs)  
rm -rf /opt/esphome/config/.esphome/build  

5) Désactiver l’environnement virtuel  
deactivate  

6) Réactiver le venv plus tard  
cd /opt/esphome  
source venv/bin/activate  

source /opt/esphome/venv/bin/activate  


────────────────────────────────────────────────────────────  
                 TUTO INSTALLATION & UTILISATION BIND9  
────────────────────────────────────────────────────────────  

📌 1) Installation de BIND9  
sudo apt update  
sudo apt install bind9 bind9utils bind9-doc -y  

📌 2) Vérifier que BIND9 fonctionne  
systemctl status bind9  
→ Doit afficher “active (running)”  

📌 3) Fichiers importants  
/etc/bind/named.conf              → config principale  
/etc/bind/named.conf.options      → options globales  
/etc/bind/named.conf.local        → zones locales  
/var/cache/bind/                  → fichiers de zones dynamiques  


────────────────────────────────────────────────────────────  
                 CONFIGURATION D’UN DNS LOCAL (exemple)  
────────────────────────────────────────────────────────────  

📌 4) Activer le DNS récursif (LAN)  
sudo nano /etc/bind/named.conf.options  

options {  
    directory "/var/cache/bind";  

    recursion yes;  
    allow-recursion { 192.168.0.0/24; };  
    listen-on { 192.168.0.1; };  
    listen-on-v6 { none; };  

    forwarders {  
        1.1.1.1;  
        8.8.8.8;  
    };  
};  

📌 5) Créer une zone DNS locale  
sudo nano /etc/bind/named.conf.local  

zone "maison.local" {  
    type master;  
    file "/etc/bind/db.maison.local";  
};  

📌 6) Créer le fichier de zone  
sudo nano /etc/bind/db.maison.local  

$TTL 86400  
@   IN  SOA ns1.maison.local. admin.maison.local. (  
        2025011201 ; Serial  
        3600       ; Refresh  
        1800       ; Retry  
        604800     ; Expire  
        86400 )    ; Minimum  

    IN  NS  ns1.maison.local.  
ns1 IN  A   192.168.0.1  

cam2 IN A 192.168.0.16  
nas  IN A 192.168.0.20  
ha   IN A 192.168.0.30  

📌 7) Vérifier la configuration  
sudo named-checkconf  
sudo named-checkzone maison.local /etc/bind/db.maison.local  

📌 8) Redémarrer BIND9  
sudo systemctl restart bind9  


────────────────────────────────────────────────────────────  
                 UTILISATION COURANTE DE BIND9  
────────────────────────────────────────────────────────────  

▶️ Tester une résolution DNS locale  
dig cam2.maison.local @192.168.0.1  

▶️ Tester la récursion  
dig google.com @192.168.0.1  

▶️ Voir les logs  
sudo tail -f /var/log/syslog | grep named  

▶️ Modifier une zone  
sudo nano /etc/bind/db.maison.local  
→ Incrémenter le numéro de série  
sudo systemctl reload bind9  

▶️ Vérifier les ports ouverts  
sudo ss -tulpn | grep named  


────────────────────────────────────────────────────────────  
                 AJOUTER LE DNS SUR TON RÉSEAU  
────────────────────────────────────────────────────────────  

DNS primaire : 192.168.0.1  
DNS LAN : 192.168.0.1  


────────────────────────────────────────────────────────────  
                 TUTO INSTALLATION & UTILISATION FILEBROWSER  
────────────────────────────────────────────────────────────  

📌 1) Installer FileBrowser  
curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash  

📌 2) Créer un dossier de configuration  
sudo mkdir -p /etc/filebrowser  
sudo mkdir -p /var/lib/filebrowser  

📌 3) Initialiser la base de données  
sudo filebrowser config init \  
    --config /etc/filebrowser/filebrowser.json \  
    --database /var/lib/filebrowser/filebrowser.db  

📌 4) Définir le dossier à partager  
sudo filebrowser config set \  
    --config /etc/filebrowser/filebrowser.json \  
    --database /var/lib/filebrowser/filebrowser.db \  
    --root /home/ph  

📌 5) Créer un service systemd  
sudo nano /etc/systemd/system/filebrowser.service  

[Unit]  
Description=File Browser  
After=network.target  

[Service]  
User=ph  
ExecStart=/usr/local/bin/filebrowser -c /etc/filebrowser/filebrowser.json -d /var/lib/filebrowser/filebrowser.db  
Restart=always  

[Install]  
WantedBy=multi-user.target  

📌 6) Activer et démarrer  
sudo systemctl daemon-reload  
sudo systemctl enable filebrowser  
sudo systemctl start filebrowser  

📌 7) Vérifier  
systemctl status filebrowser  

📌 8) Accéder  
http://IP_DU_SERVEUR:8080  
admin / admin  


────────────────────────────────────────────────────────────  
                 UTILISATION COURANTE DE FILEBROWSER  
────────────────────────────────────────────────────────────  

▶️ Changer le mot de passe  
▶️ Modifier le dossier racine  
▶️ Voir les logs  
▶️ Redémarrer  
▶️ Changer le port  


────────────────────────────────────────────────────────────  
                 TUTO INSTALLATION & UTILISATION UFW  
────────────────────────────────────────────────────────────  

sudo apt update  
sudo apt install ufw -y  

sudo ufw status verbose  

sudo ufw allow ssh  
sudo ufw enable  

sudo ufw disable  


────────────────────────────────────────────────────────────  
                 RÈGLES COURANTES  
────────────────────────────────────────────────────────────  

sudo ufw allow 80/tcp  
sudo ufw allow 443/tcp  
sudo ufw allow 'Nginx Full'  
sudo ufw allow 8080/tcp  
sudo ufw allow from 192.168.0.10  
sudo ufw allow from 192.168.0.10 to any port 22  
sudo ufw deny 23/tcp  
sudo ufw delete allow 8080/tcp  


────────────────────────────────────────────────────────────  
                 CONFIGURATION AVANCÉE  
────────────────────────────────────────────────────────────  

sudo ufw logging on  
sudo tail -f /var/log/ufw.log  
sudo ufw reset  
sudo ufw allow from 192.168.0.0/24  
sudo ufw default deny incoming  
sudo ufw default allow outgoing  


────────────────────────────────────────────────────────────  
                 EXEMPLE CONFIG SERVEUR WEB  
────────────────────────────────────────────────────────────  

sudo ufw allow ssh  
sudo ufw allow 'Nginx Full'  
sudo ufw allow 53/udp  
sudo ufw allow 53/tcp  
sudo ufw enable  


────────────────────────────────────────────────────────────  
                 TUTO INSTALLATION & UTILISATION CERTBOT  
────────────────────────────────────────────────────────────  

sudo apt update  
sudo apt install certbot python3-certbot-nginx -y  

systemctl status nginx  

sudo certbot --nginx -d monsite.fr -d www.monsite.fr  

sudo certbot renew --dry-run  

/etc/letsencrypt/live/monsite.fr/fullchain.pem  
/etc/letsencrypt/live/monsite.fr/privkey.pem  

sudo apt install certbot python3-certbot-apache -y  
sudo certbot --apache -d monsite.fr  

sudo certbot renew --dry-run  
sudo certbot certificates  
sudo certbot delete  
sudo certbot certonly --nginx -d monsite.fr  
sudo certbot --nginx --force-renewal -d monsite.fr  

✔ Le domaine doit pointer vers ton serveur  
✔ Le port 80 doit être ouvert  
✔ Le port 443 doit être ouvert  
✔ NGINX doit fonctionner  
