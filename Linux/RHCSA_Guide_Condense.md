> **RHCSA EX200**
>
> Red Hat Certified System Administrator
>
> Guide condensé --- L\'essentiel pour l\'examen

  -----------------------------------------------------------------------
  **Durée exam**       2h30 • pas de livre, pas            
                       d\'internet                         
  -------------------- ----------------------------------- --------------
  **Format**           Labo pratique sur RHEL 9 --- tout   
                       en ligne de commande                

  **Score**            Minimum 210/300 points pour valider 

  **Objectif**         Ce guide = les commandes qui        
                       tombent vraiment                    

  **DOMAINE EXAM**                                         **% des
                                                           points**

  Comprendre et                                            \~15%
  utiliser les outils                                      
  essentiels                                               

  Créer des scripts                                        \~10%
  shell simples                                            

  Opérer sur des                                           \~15%
  systèmes en cours                                        
  d\'exécution                                             

  Configurer le                                            \~15%
  stockage local (LVM,                                     
  partitions)                                              

  Créer et configurer                                      \~10%
  des systèmes de                                          
  fichiers                                                 

  Déployer, configurer                                     \~15%
  et maintenir des                                         
  systèmes                                                 

  Gérer les                                                \~10%
  utilisateurs et                                          
  groupes                                                  

  Gérer la sécurité                                        \~10%
  (SELinux, firewall,                                      
  sudo)                                                    
  -----------------------------------------------------------------------

# 1. COMMANDES ESSENTIELLES

> **Navigation & fichiers**

  -----------------------------------------------------------------------
  **ls -lZ \# -Z = contexte SELinux**
  -----------------------------------------------------------------------
  **find / -name \'\*.conf\' -user root 2\>/dev/null**

  **find / -perm -4000 2\>/dev/null \# fichiers SUID**

  **find / -size +100M 2\>/dev/null**

  **cp -a src/ dst/ \# -a = archive (droits+liens conservés)**

  **tar czf archive.tar.gz /dossier**

  **tar xzf archive.tar.gz -C /dest**

  **tar cjf archive.tar.bz2 /dossier \# bzip2**

  **ln -s /chemin/cible lien_symbolique**
  -----------------------------------------------------------------------

> **Recherche dans fichiers**

  -----------------------------------------------------------------------
  **grep -r \'mot\' /etc/ \# récursif**
  -----------------------------------------------------------------------
  **grep -i \'mot\' fichier \# insensible casse**

  **grep -n \'mot\' fichier \# numéros de ligne**

  **grep -v \'mot\' fichier \# inverser (exclure)**

  **grep -E \'mot1\|mot2\' fichier \# regex étendue**
  -----------------------------------------------------------------------

> **Redirections & pipes**

  -----------------------------------------------------------------------
  **commande \> fichier \# écrase**
  -----------------------------------------------------------------------
  **commande \>\> fichier \# ajoute**

  **commande 2\> erreurs \# stderr**

  **commande &\> tout \# stdout + stderr**

  **commande \| tee fichier \# affiche ET écrit**
  -----------------------------------------------------------------------

> **Archivage & compression**

+----------------------------------+-----------------------------------+
| > **Commande**                   | > **Action**                      |
+==================================+===================================+
| > tar czf f.tar.gz dir/          | > Créer archive gzip              |
+----------------------------------+-----------------------------------+
| > tar xzf f.tar.gz               | > Extraire gzip                   |
+----------------------------------+-----------------------------------+
| > tar czf - dir/ \| ssh h \'tar  |                                   |
| > xzf - -C /dst\'                |                                   |
+----------------------------------+-----------------------------------+
| > gzip / gunzip fichier          | > Compresser/décompresser         |
+----------------------------------+-----------------------------------+
| > zip -r f.zip dir/              | > Zip récursif                    |
+----------------------------------+-----------------------------------+
| > unzip f.zip -d /dest           | > Extraire zip                    |
+----------------------------------+-----------------------------------+

> **Gestion utilisateurs**

  -----------------------------------------------------------------------
  **useradd -m -s /bin/bash -G wheel,sudo username**
  -----------------------------------------------------------------------
  **useradd -M -s /sbin/nologin svcuser \# sans home, sans shell**

  **useradd -u 1500 -g 1000 username \# UID/GID forcés**

  **usermod -aG groupname username \# ajouter à un groupe**

  **usermod -L username \# verrouiller compte**

  **usermod -U username \# déverrouiller**

  **userdel -r username \# supprime user + home**

  **passwd username \# changer mot de passe**

  **chage -l username \# voir expiration**

  **chage -E 2025-12-31 username \# date expiration**

  **chage -M 90 username \# max 90 jours**

  **chage -d 0 username \# forcer changement au prochain login**

  **id username \# UID, GID, groupes**
  -----------------------------------------------------------------------

> **Gestion groupes**

  -----------------------------------------------------------------------
  **groupadd -g 1500 groupname**
  -----------------------------------------------------------------------
  **groupmod -n newname oldname**

  **groupdel groupname**

  **gpasswd -a username groupname \# ajouter membre**

  **gpasswd -d username groupname \# retirer membre**

  **newgrp groupname \# changer groupe actif**
  -----------------------------------------------------------------------

> **Fichiers clés**
>
> **Sudo**

  -----------------------------------------------------------------------
  \# Dans /etc/sudoers ou /etc/sudoers.d/custom :
  -----------------------------------------------------------------------
  **username ALL=(ALL) ALL**

  **username ALL=(ALL) NOPASSWD: ALL**

  **%groupname ALL=(ALL) NOPASSWD: /bin/systemctl restart httpd**

  \# Créer un fichier dédié (meilleure pratique) :

  **echo \'username ALL=(ALL) NOPASSWD: ALL\' \>
  /etc/sudoers.d/username**

  **chmod 440 /etc/sudoers.d/username**
  -----------------------------------------------------------------------

# 3. PERMISSIONS, ACL & SELinux

> **Permissions classiques**

  -----------------------------------------------------------------------
  **chmod 755 fichier \# rwxr-xr-x**
  -----------------------------------------------------------------------
  **chmod u+x,g-w fichier**

  **chown user:group fichier**

  **chown -R user:group dossier/**

  \# Bits spéciaux :

  **chmod u+s fichier \# SUID : s\'exécute en tant que propriétaire**

  **chmod g+s dossier/ \# SGID : fichiers héritent du groupe**

  **chmod +t dossier/ \# Sticky : seul le proprio peut supprimer**

  **chmod 4755 fichier \# SUID + 755**

  **chmod 2755 dossier/ \# SGID + 755**

  **chmod 1777 dossier/ \# Sticky + 777 (ex: /tmp)**

  **umask 022 \# valeur par défaut = 644 fichiers, 755 dossiers**
  -----------------------------------------------------------------------

> **ACL (Access Control Lists)**
>
> **getfacl fichier**
>
> **SELinux --- CRITIQUE pour l\'examen**

# 4. STOCKAGE : PARTITIONS, LVM, SYSTÈMES DE FICHIERS

> **Partitionnement (parted / fdisk)**

  -----------------------------------------------------------------------
  **lsblk \# vue arborescente des disques**
  -----------------------------------------------------------------------
  **lsblk -f \# avec systèmes de fichiers**

  **blkid \# UUID et types de FS**

  **fdisk -l /dev/sdb \# lister partitions**

  \# Partitionner avec parted (préféré pour GPT) :

  **parted /dev/sdb**

  **(parted) mklabel gpt**

  **(parted) mkpart primary ext4 1MiB 10GiB**

  **(parted) print**

  **(parted) quit**

  \# Partitionner avec fdisk (MBR) :

  **fdisk /dev/sdb \# n=new, p=primary, w=write**

  \# Formater :

  **mkfs.xfs /dev/sdb1**

  **mkfs.ext4 /dev/sdb1**

  **mkswap /dev/sdb2**

  **swapon /dev/sdb2**

  **swapon -s \# voir swap actif**
  -----------------------------------------------------------------------

> **LVM --- Commandes fondamentales**

+-----+--------------------------------------------------+-----+-----+
| > * | > **Créer**                                      | > * | > * |
| *Ni |                                                  | *Li | *Su |
| vea |                                                  | ste | ppr |
| u** |                                                  | r** | ime |
|     |                                                  |     | r** |
+=====+==================================================+=====+=====+
| >   | olume)pvcreate /dev/sdb1                         | >   | >   |
|  PV |                                                  | pvs |  pv |
| >   |                                                  | > / | rem |
| (Ph |                                                  | >   | ove |
| ysi |                                                  | pvd | >   |
| cal |                                                  | isp | /de |
| > V |                                                  | lay | v/s |
|     |                                                  |     | db1 |
+-----+--------------------------------------------------+-----+-----+
| >   |                                                  | >   | >   |
|  VG |                                                  | vgs |  vg |
| > ( |                                                  | > / | rem |
| Vol |                                                  | >   | ove |
| ume |                                                  | vgd | > v |
| >   |                                                  | isp | g_d |
| Gro |                                                  | lay | ata |
+-----+--------------------------------------------------+-----+-----+
| >   | lume)lvcreate -L 10G -n lv_data vg_da            | ta  |     |
|  LV |                                                  | lvs |     |
| >   |                                                  | /   |     |
|  (L |                                                  | lvd |     |
| ogi |                                                  | isp |     |
| cal |                                                  | lay |     |
| >   |                                                  |     |     |
|  Vo |                                                  |     |     |
+-----+--------------------------------------------------+-----+-----+

> lvremove /dev/vg_data/lv_data **LVM --- Resize (ce qui tombe à
> l\'exam)**
>
> **Montage persistant (/etc/fstab)**

  -----------------------------------------------------------------------
  \# Format :
  -----------------------------------------------------------------------
  **UUID=xxxx-xxxx /data xfs defaults 0 2**

  **/dev/vg_data/lv_data /data xfs defaults 0 0**
  -----------------------------------------------------------------------

# 5. SYSTEMD --- SERVICES, TARGETS, JOURNAUX

> **Gestion des services**

  -----------------------------------------------------------------------
  **systemctl status httpd**
  -----------------------------------------------------------------------
  **systemctl start httpd**

  **systemctl stop httpd**

  **systemctl restart httpd**

  **systemctl reload httpd \# recharge config sans couper**

  **systemctl enable httpd \# active au démarrage**

  **systemctl disable httpd**

  **systemctl enable \--now httpd \# active ET démarre**

  **systemctl mask httpd \# empêche tout démarrage**

  **systemctl unmask httpd**

  **systemctl is-enabled httpd**

  **systemctl is-active httpd**

  **systemctl list-units \--type=service \--state=running**

  **systemctl list-units \--type=service \--state=failed**

  **systemctl list-dependencies httpd**
  -----------------------------------------------------------------------

> **Targets (remplacent les runlevels)**

+---+----------+----------------------------------------------+-----------+---+
| > |          | > **Équivalent runlevel**                    | >         |   |
|   |          |                                              | **Usage** |   |
| * |          |                                              |           |   |
| * |          |                                              |           |   |
| T |          |                                              |           |   |
| a |          |                                              |           |   |
| r |          |                                              |           |   |
| g |          |                                              |           |   |
| e |          |                                              |           |   |
| t |          |                                              |           |   |
| * |          |                                              |           |   |
| * |          |                                              |           |   |
+===+==========+==============================================+===========+===+
| > |          | > 0                                          | > E       |   |
|   |          |                                              | xtinction |   |
| p |          |                                              |           |   |
| o |          |                                              |           |   |
| w |          |                                              |           |   |
| e |          |                                              |           |   |
| r |          |                                              |           |   |
| o |          |                                              |           |   |
| f |          |                                              |           |   |
| f |          |                                              |           |   |
| . |          |                                              |           |   |
| t |          |                                              |           |   |
| a |          |                                              |           |   |
| r |          |                                              |           |   |
| g |          |                                              |           |   |
| e |          |                                              |           |   |
| t |          |                                              |           |   |
+---+----------+----------------------------------------------+-----------+---+
| > |          | > 1                                          | > Mode    |   |
|   |          |                                              | > ma      |   |
| r |          |                                              | intenance |   |
| e |          |                                              | > (root   |   |
| s |          |                                              | > seul)   |   |
| c |          |                                              |           |   |
| u |          |                                              |           |   |
| e |          |                                              |           |   |
| . |          |                                              |           |   |
| t |          |                                              |           |   |
| a |          |                                              |           |   |
| r |          |                                              |           |   |
| g |          |                                              |           |   |
| e |          |                                              |           |   |
| t |          |                                              |           |   |
+---+----------+----------------------------------------------+-----------+---+
| > |          |                                              | > Multiut |   |
|   |          |                                              | ilisateur |   |
| m |          |                                              | > sans    |   |
| u |          |                                              | > GUI     |   |
| l |          |                                              |           |   |
| t |          |                                              |           |   |
| i |          |                                              |           |   |
| - |          |                                              |           |   |
| u |          |                                              |           |   |
| s |          |                                              |           |   |
| e |          |                                              |           |   |
| r |          |                                              |           |   |
| . |          |                                              |           |   |
| t |          |                                              |           |   |
| a |          |                                              |           |   |
| r |          |                                              |           |   |
| g |          |                                              |           |   |
| e |          |                                              |           |   |
| t |          |                                              |           |   |
+---+----------+----------------------------------------------+-----------+---+
| > |          | > 5                                          | >         |   |
|   |          |                                              | Multiutil |   |
| g |          |                                              | isateur + |   |
| r |          |                                              | > GUI     |   |
| a |          |                                              |           |   |
| p |          |                                              |           |   |
| h |          |                                              |           |   |
| i |          |                                              |           |   |
| c |          |                                              |           |   |
| a |          |                                              |           |   |
| l |          |                                              |           |   |
| . |          |                                              |           |   |
| t |          |                                              |           |   |
| a |          |                                              |           |   |
| r |          |                                              |           |   |
| g |          |                                              |           |   |
| e |          |                                              |           |   |
| t |          |                                              |           |   |
+---+----------+----------------------------------------------+-----------+---+
| > |          | > 6                                          | > Re      |   |
|   |          |                                              | démarrage |   |
| r |          |                                              |           |   |
| e |          |                                              |           |   |
| b |          |                                              |           |   |
| o |          |                                              |           |   |
| o |          |                                              |           |   |
| t |          |                                              |           |   |
| . |          |                                              |           |   |
| t |          |                                              |           |   |
| a |          |                                              |           |   |
| r |          |                                              |           |   |
| g |          |                                              |           |   |
| e |          |                                              |           |   |
| t |          |                                              |           |   |
+---+----------+----------------------------------------------+-----------+---+
| > |          | > \-                                         | > Urgence |   |
|   |          |                                              | > (seul   |   |
| e |          |                                              | > root,   |   |
| m |          |                                              | > ro)     |   |
| e |          |                                              |           |   |
| r |          |                                              |           |   |
| g |          |                                              |           |   |
| e |          |                                              |           |   |
| n |          |                                              |           |   |
| c |          |                                              |           |   |
| y |          |                                              |           |   |
| . |          |                                              |           |   |
| t |          |                                              |           |   |
| a |          |                                              |           |   |
| r |          |                                              |           |   |
| g |          |                                              |           |   |
| e |          |                                              |           |   |
| t |          |                                              |           |   |
+---+----------+----------------------------------------------+-----------+---+
| * |          |                                              |           |   |
| * |          |                                              |           |   |
| s |          |                                              |           |   |
| y |          |                                              |           |   |
| s |          |                                              |           |   |
| t |          |                                              |           |   |
| e |          |                                              |           |   |
| m |          |                                              |           |   |
| c |          |                                              |           |   |
| t |          |                                              |           |   |
| l |          |                                              |           |   |
| g |          |                                              |           |   |
| e |          |                                              |           |   |
| t |          |                                              |           |   |
| - |          |                                              |           |   |
| d |          |                                              |           |   |
| e |          |                                              |           |   |
| f |          |                                              |           |   |
| a |          |                                              |           |   |
| u |          |                                              |           |   |
| l |          |                                              |           |   |
| t |          |                                              |           |   |
| \ |          |                                              |           |   |
| # |          |                                              |           |   |
| t |          |                                              |           |   |
| a |          |                                              |           |   |
| r |          |                                              |           |   |
| g |          |                                              |           |   |
| e |          |                                              |           |   |
| t |          |                                              |           |   |
| p |          |                                              |           |   |
| a |          |                                              |           |   |
| r |          |                                              |           |   |
| d |          |                                              |           |   |
| é |          |                                              |           |   |
| f |          |                                              |           |   |
| a |          |                                              |           |   |
| u |          |                                              |           |   |
| t |          |                                              |           |   |
| * |          |                                              |           |   |
| * |          |                                              |           |   |
+---+----------+----------------------------------------------+-----------+---+
| * |          |                                              |           |   |
| * |          |                                              |           |   |
| s |          |                                              |           |   |
| y |          |                                              |           |   |
| s |          |                                              |           |   |
| t |          |                                              |           |   |
| e |          |                                              |           |   |
| m |          |                                              |           |   |
| c |          |                                              |           |   |
| t |          |                                              |           |   |
| l |          |                                              |           |   |
| s |          |                                              |           |   |
| e |          |                                              |           |   |
| t |          |                                              |           |   |
| - |          |                                              |           |   |
| d |          |                                              |           |   |
| e |          |                                              |           |   |
| f |          |                                              |           |   |
| a |          |                                              |           |   |
| u |          |                                              |           |   |
| l |          |                                              |           |   |
| t |          |                                              |           |   |
| m |          |                                              |           |   |
| u |          |                                              |           |   |
| l |          |                                              |           |   |
| t |          |                                              |           |   |
| i |          |                                              |           |   |
| - |          |                                              |           |   |
| u |          |                                              |           |   |
| s |          |                                              |           |   |
| e |          |                                              |           |   |
| r |          |                                              |           |   |
| . |          |                                              |           |   |
| t |          |                                              |           |   |
| a |          |                                              |           |   |
| r |          |                                              |           |   |
| g |          |                                              |           |   |
| e |          |                                              |           |   |
| t |          |                                              |           |   |
| * |          |                                              |           |   |
| * |          |                                              |           |   |
+---+----------+----------------------------------------------+-----------+---+
| * |          |                                              |           |   |
| * |          |                                              |           |   |
| s |          |                                              |           |   |
| y |          |                                              |           |   |
| s |          |                                              |           |   |
| t |          |                                              |           |   |
| e |          |                                              |           |   |
| m |          |                                              |           |   |
| c |          |                                              |           |   |
| t |          |                                              |           |   |
| l |          |                                              |           |   |
| i |          |                                              |           |   |
| s |          |                                              |           |   |
| o |          |                                              |           |   |
| l |          |                                              |           |   |
| a |          |                                              |           |   |
| t |          |                                              |           |   |
| e |          |                                              |           |   |
| r |          |                                              |           |   |
| e |          |                                              |           |   |
| s |          |                                              |           |   |
| c |          |                                              |           |   |
| u |          |                                              |           |   |
| e |          |                                              |           |   |
| . |          |                                              |           |   |
| t |          |                                              |           |   |
| a |          |                                              |           |   |
| r |          |                                              |           |   |
| g |          |                                              |           |   |
| e |          |                                              |           |   |
| t |          |                                              |           |   |
| \ |          |                                              |           |   |
| # |          |                                              |           |   |
| b |          |                                              |           |   |
| a |          |                                              |           |   |
| s |          |                                              |           |   |
| c |          |                                              |           |   |
| u |          |                                              |           |   |
| l |          |                                              |           |   |
| e |          |                                              |           |   |
| r |          |                                              |           |   |
| m |          |                                              |           |   |
| a |          |                                              |           |   |
| i |          |                                              |           |   |
| n |          |                                              |           |   |
| t |          |                                              |           |   |
| e |          |                                              |           |   |
| n |          |                                              |           |   |
| a |          |                                              |           |   |
| n |          |                                              |           |   |
| t |          |                                              |           |   |
| * |          |                                              |           |   |
| * |          |                                              |           |   |
+---+----------+----------------------------------------------+-----------+---+

> **Journald / logs**

  -----------------------------------------------------------------------
  **journalctl \# tous les logs**
  -----------------------------------------------------------------------
  **journalctl -u httpd \# logs d\'un service**

  **journalctl -f \# follow (temps réel)**

  **journalctl -n 50 \# 50 dernières lignes**

  **journalctl \--since \'1 hour ago\'**
  -----------------------------------------------------------------------

  -----------------------------------------------------------------------
  **journalctl \--since \'2025-01-01\' \--until \'2025-01-02\'**
  -----------------------------------------------------------------------
  **journalctl -p err \# niveau : err, warning, info, debug**

  **journalctl -b \# depuis le dernier boot**

  **journalctl -b -1 \# boot précédent**

  **journalctl \--disk-usage**

  \# Logs persistants (par défaut volatile) :

  **mkdir -p /var/log/journal**

  **systemd-tmpfiles \--create \--prefix /var/log/journal**
  -----------------------------------------------------------------------

> **NetworkManager --- nmcli (méthode RHCSA)**

  -----------------------------------------------------------------------
  **nmcli device status \# état des interfaces**
  -----------------------------------------------------------------------
  **nmcli connection show \# connexions configurées**

  **nmcli connection show eth0 \# détails d\'une connexion**

  \# Configurer une IP statique :

  **nmcli con mod eth0 ipv4.addresses 192.168.1.10/24**

  **nmcli con mod eth0 ipv4.gateway 192.168.1.1**

  **nmcli con mod eth0 ipv4.dns 8.8.8.8**

  **nmcli con mod eth0 ipv4.method manual**

  **nmcli con up eth0**

  \# IP dynamique (DHCP) :

  **nmcli con mod eth0 ipv4.method auto**

  **nmcli con up eth0**

  \# Créer une nouvelle connexion :

  **nmcli con add type ethernet con-name eth1 ifname eth1 \\**

  **ipv4.addresses 10.0.0.5/24 ipv4.gateway 10.0.0.1 \\**

  **ipv4.method manual**

  \# Hostname :

  **hostnamectl set-hostname srv01.domain.lab**

  **hostnamectl status**
  -----------------------------------------------------------------------

> **Diagnostics réseau**

  -----------------------------------------------------------------------
  **ip a \# adresses IP**
  -----------------------------------------------------------------------
  **ip a show eth0**

  **ip r \# table de routage**

  **ip route show**

  **ss -tuln \# ports en écoute**

  **ss -tulnp \# avec processus**

  **ss -tulnp \| grep :80**

  **ping -c 4 8.8.8.8**

  **dig google.com**

  **nslookup google.com**

  **traceroute 8.8.8.8**

  **curl -I http://localhost \# tester HTTP**

  **wget -O /dev/null http://url \# tester download**
  -----------------------------------------------------------------------

> **Firewalld**

  -----------------------------------------------------------------------
  **systemctl enable \--now firewalld**
  -----------------------------------------------------------------------
  **firewall-cmd \--state**

  **firewall-cmd \--get-active-zones**

  **firewall-cmd \--list-all**

  **firewall-cmd \--list-all \--zone=public**

  \# Autoriser un service :

  **firewall-cmd \--permanent \--add-service=http**

  **firewall-cmd \--permanent \--add-service=https**

  **firewall-cmd \--permanent \--add-service=ssh**

  \# Autoriser un port :

  **firewall-cmd \--permanent \--add-port=8080/tcp**

  **firewall-cmd \--permanent \--add-port=3306/tcp**

  \# Retirer :
  -----------------------------------------------------------------------

> **Configuration SSH**

  -----------------------------------------------------------------------
  \# /etc/ssh/sshd_config --- paramètres importants :
  -----------------------------------------------------------------------
  **Port 22**

  **PermitRootLogin no \# ou without-password**

  **PasswordAuthentication yes**

  **PubkeyAuthentication yes**

  **AllowUsers user1 user2**

  **systemctl reload sshd \# après modification config**

  \# Générer une paire de clés :

  **ssh-keygen -t rsa -b 4096 -f \~/.ssh/id_rsa**

  **ssh-keygen -t ed25519**

  \# Copier la clé publique :

  **ssh-copy-id -i \~/.ssh/id_rsa.pub user@serveur**

  \# Connexion :

  **ssh -i \~/.ssh/id_rsa user@serveur**

  **ssh -p 2222 user@serveur**

  \# Tunnel SSH :

  **ssh -L 8080:localhost:80 user@serveur \# local** → **remote**
  -----------------------------------------------------------------------

> **Crontab**

  -----------------------------------------------------------------------
  **crontab -e \# éditer crontab de l\'utilisateur courant**
  -----------------------------------------------------------------------
  **crontab -l \# lister**

  **crontab -r \# supprimer TOUT (attention !)**

  **crontab -e -u username \# éditer pour un autre user (root)**

  \# Format : MIN HEURE JOUR MOIS JOUR_SEMAINE commande

  \# \*=tous ,=liste -=plage /=fréquence

  **30 2 \* \* \* /usr/bin/backup.sh \# tous les jours à 2h30**

  **0 \*/4 \* \* \* /usr/bin/check.sh \# toutes les 4h**

  **0 9 \* \* 1-5 /usr/bin/report.sh \# lun-ven à 9h**

  **\*/5 \* \* \* \* /usr/bin/monitor.sh \# toutes les 5 minutes**

  **0 0 1 \* \* /usr/bin/monthly.sh \# 1er du mois à minuit**

  \# Crontab système (/etc/cron.d/) --- avec champ USER :

  **30 2 \* \* \* root /usr/bin/backup.sh**

  \# Logs cron :

  **journalctl -u crond**

  **grep CRON /var/log/cron**
  -----------------------------------------------------------------------

> **At --- tâche unique**
>
> **at now + 5 minutes**

  -----------------------------------------------------------------------
  **at 14:30**
  -----------------------------------------------------------------------
  **at 14:30 tomorrow**

  **at 9:00 2025-06-15**

  **\> /usr/bin/script.sh**

  **\> Ctrl+D pour terminer**

  **atq \# lister les tâches**

  **atrm 2 \# supprimer tâche n°2**

  **systemctl enable \--now atd**
  -----------------------------------------------------------------------

# 9. BOOT, GRUB & RÉCUPÉRATION

> **Modifier GRUB (kernel params)**

  -----------------------------------------------------------------------
  \# Fichier de config (NE PAS éditer directement) :
  -----------------------------------------------------------------------
  **/etc/default/grub \# paramètres GRUBCMDLINE_LINUX**

  \# Regénérer grub.cfg :

  **grub2-mkconfig -o /boot/grub2/grub.cfg \# BIOS/MBR**

  **grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg \# UEFI**

  \# Passer un param au boot (temporaire, au menu GRUB) :

  \# Sélectionner le kernel → \'e\' pour éditer

  \# Sur la ligne linux/linuxefi, ajouter en fin :

  **systemd.unit=rescue.target \# mode rescue**

  **systemd.unit=emergency.target \# mode emergency (plus bas niveau)**

  **rd.break \# shell avant montage root**

  **init=/bin/bash \# shell direct (Debian/Ubuntu)**
  -----------------------------------------------------------------------

# 10. GESTION DES PAQUETS (DNF / YUM)

> **DNF --- commandes essentielles**

  -----------------------------------------------------------------------
  **dnf install httpd -y**
  -----------------------------------------------------------------------
  **dnf remove httpd -y**

  **dnf update httpd \# mettre à jour un paquet**

  **dnf update -y \# tout mettre à jour**

  **dnf search httpd**

  **dnf info httpd**

  **dnf list installed**

  **dnf list available \| grep httpd**

  **dnf provides /usr/bin/python3 \# quel paquet fournit ce binaire**

  **dnf provides \'\*/httpd.conf\'**

  **dnf history \# historique**

  **dnf history undo 5 \# annuler transaction n°5**

  **dnf autoremove \# supprimer orphelins**

  **dnf clean all \# vider le cache**
  -----------------------------------------------------------------------

> **Dépôts (repos)**

  -----------------------------------------------------------------------
  **dnf repolist \# repos actifs**
  -----------------------------------------------------------------------
  **dnf repolist all \# tous les repos**

  **dnf config-manager \--add-repo=URL**

  **dnf config-manager \--enable repo-name**

  **dnf config-manager \--disable repo-name**

  \# Créer un repo local (ex : DVD/ISO) :

  \# /etc/yum.repos.d/local.repo :

  **\[local\]**

  **name=Local Repo**

  **baseurl=file:///mnt/dvd**

  **enabled=1**

  **gpgcheck=0**

  \# Installer depuis un RPM local :

  **dnf install ./package.rpm**

  **rpm -ivh package.rpm \# sans résolution dépendances**

  **rpm -qa \| grep httpd \# vérifier installation**

  **rpm -ql httpd \# lister fichiers du paquet**

  **rpm -qf /etc/httpd/conf/httpd.conf \# à quel paquet appartient ce
  fichier**
  -----------------------------------------------------------------------

# 11. VARIABLES D\'ENVIRONNEMENT & SHELL SCRIPTING

> **Variables**

  -----------------------------------------------------------------------
  **export MYVAR=\'valeur\' \# exporter (visible par sous-processus)**
  -----------------------------------------------------------------------
  **echo \$MYVAR**

  **unset MYVAR**

  **env \# toutes les variables**

  **printenv PATH**

  \# Persistant pour un user :

  **echo \'export MYVAR=val\' \>\> \~/.bashrc**

  **source \~/.bashrc \# ou : . \~/.bashrc**

  \# Persistant pour tous :

  **echo \'export MYVAR=val\' \>\> /etc/profile.d/custom.sh**

  **chmod +x /etc/profile.d/custom.sh**

  \# PATH :

  **export PATH=\$PATH:/nouveau/chemin**
  -----------------------------------------------------------------------

> **Scripts shell --- bases pour l\'examen**

  -----------------------------------------------------------------------
  #!/bin/bash
  -----------------------------------------------------------------------
  \# Variables et paramètres :

  **VAR=\'valeur\'**

  **echo \$1 \$2 \$@ \# arguments positionnels**

  \# Conditions :

  **if \[ -f /fichier \]; then echo \'existe\'; fi**

  **if \[ -d /dossier \]; then echo \'dossier\'; fi**

  **if \[ \$VAR -eq 5 \]; then echo \'égal\'; fi**

  **if \[ \$STR = \'test\' \]; then echo \'ok\'; fi**

  **if \[ -z \$VAR \]; then echo \'vide\'; fi**

  **if \[ -n \$VAR \]; then echo \'non vide\'; fi**

  \# Boucles :

  **for i in {1..5}; do echo \$i; done**

  **for f in /etc/\*.conf; do echo \$f; done**

  **while \[ \$i -lt 10 \]; do echo \$i; i=\$((i+1)); done**

  \# Fonctions :

  **ma_fonction() { echo \'hello\'; }**

  **ma_fonction**
  -----------------------------------------------------------------------

  -----------------------------------------------------------------------
  \# Codes de retour :
  -----------------------------------------------------------------------
  **commande && echo \'OK\' \|\| echo \'ERREUR\'**

  **\[ \$? -eq 0 \] && echo \'succès\'**
  -----------------------------------------------------------------------

# 12. POINTS CRITIQUES --- NE PAS RATER

## ■ SELinux toujours actif

> Ne pas le désactiver ! Utiliser semanage + restorecon.
>
> Si service ne démarre pas → ausearch -m avc -ts recent

## ■ fstab = piège mortel

> Tester avec \'mount -a\' AVANT de rebooter.
>
> UUID \> nom de device (/dev/sdb1 peut changer)
>
> ■ **Firewalld \--permanent**
>
> Sans \--reload après, le changement n\'est pas appliqué
>
> ■ **XFS pas réductible**
>
> xfs_growfs = agrandir uniquement. Réduire = ext4 obligatoire
>
> ■ **touch /.autorelabel**
>
> Obligatoire après reset mot de passe root si SELinux activé
>
> ■ **visudo obligatoire**
>
> Jamais éditer /etc/sudoers directement. visudo vérifie la syntaxe
>
> ■ **enable vs enable \--now**
>
> enable = active au boot seulement. enable \--now = active + démarre
>
> ■ **chage -d 0**
>
> Force le changement de mdp au prochain login (souvent demandé)
>
> ■ **restorecon après semanage**
>
> semanage fcontext sans restorecon -Rv = aucun effet

## ■ grub2-mkconfig

> Ne pas éditer /boot/grub2/grub.cfg directement. Éditer
> /etc/default/grub puis regénérer
>
> **Méthode de travail recommandée**

1.  Lire ce guide une fois entier.

2.  Monter une VM RHEL 9 (ou AlmaLinux 9, RockyLinux 9 = identique).

3.  Reproduire chaque commande à la main --- la mémoire musculaire
    compte.

4.  Pratiquer les scénarios complets : reset mot de passe root, création
    LVM + montage persistant, configuration SELinux+ firewalld pour un
    service web.

5.  **Faire les labs officiels Red Hat** (access.redhat.com) ou les labs
    RHCSA de TechWorld avec Nana (YouTube).
