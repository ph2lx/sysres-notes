1\. DUMP d’une base MariaDB (export complet)

bash

mysqldump -u root -p ma\_base > ma\_base.sql

Dump compressé

bash

mysqldump -u root -p ma\_base | gzip > ma\_base.sql.gz

🟧 2. DUMP d’un schéma (base = schéma en MariaDB)

MariaDB n’a pas de notion de “schéma” séparé :

➡ un schéma = une base.



Donc pour exporter un schéma, tu exportes la base :



bash

mysqldump -u root -p mon\_schema > mon\_schema.sql

🟩 3. DUMP structure uniquement (sans données)

bash

mysqldump -u root -p --no-data ma\_base > structure.sql

🟪 4. DUMP données uniquement (sans structure)

bash

mysqldump -u root -p --no-create-info ma\_base > data.sql

🟫 5. DUMP d’une seule table

Structure + données

bash

mysqldump -u root -p ma\_base ma\_table > ma\_table.sql

Structure seule

bash

mysqldump -u root -p --no-data ma\_base ma\_table > ma\_table\_structure.sql

Données seules

bash

mysqldump -u root -p --no-create-info ma\_base ma\_table > ma\_table\_data.sql

🟨 6. RESTAURATION (import)

bash

mysql -u root -p ma\_base < ma\_base.sql

Importer un schéma (donc une base) :



bash

mysql -u root -p mon\_schema < mon\_schema.sql

🟦 7. CLONER une base (méthode simple)

Étape 1 : dump

bash

mysqldump -u root -p base\_source > clone.sql

Étape 2 : créer la base cible

bash

mysql -u root -p -e "CREATE DATABASE base\_clone;"

Étape 3 : importer

bash

mysql -u root -p base\_clone < clone.sql

➡ Résultat : base\_clone = copie complète de base\_source.



🟧 8. CLONER une table (structure + données)

sql

CREATE TABLE nouvelle\_table AS SELECT \* FROM ancienne\_table;

🟩 9. Copier une table (structure uniquement)

sql

CREATE TABLE nouvelle\_table LIKE ancienne\_table;

🟪 10. Copier toutes les tables d’une base (structure + données)

Méthode SQL interne :



sql

SET @src = 'base\_source';

SET @dst = 'base\_clone';



SET SESSION group\_concat\_max\_len = 1000000;



SELECT GROUP\_CONCAT(CONCAT(

&#x20;   'CREATE TABLE ', @dst, '.', table\_name,

&#x20;   ' AS SELECT \* FROM ', @src, '.', table\_name, ';'

) SEPARATOR ' ')

INTO @sql

FROM information\_schema.tables

WHERE table\_schema = @src;



PREPARE stmt FROM @sql;

EXECUTE stmt;

DEALLOCATE PREPARE stmt;







\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_



🟦 1. Installation MariaDB (Debian / Ubuntu)

Ajouter le dépôt officiel (recommandé)

bash

sudo apt update

sudo apt install software-properties-common

sudo add-apt-repository 'deb \[arch=amd64] http://mirror.mariadb.org/repo/10.11/ubuntu focal main'

sudo apt update

Installer MariaDB

bash

sudo apt install mariadb-server mariadb-client

Démarrer et activer

bash

sudo systemctl enable mariadb

sudo systemctl start mariadb

🟥 2. Installation MariaDB (RHEL / CentOS / Rocky / Alma)

Ajouter le dépôt MariaDB

bash

sudo tee /etc/yum.repos.d/MariaDB.repo <<EOF

\[mariadb]

name = MariaDB

baseurl = http://yum.mariadb.org/10.11/rhel8-amd64

gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB

gpgcheck=1

EOF

Installer

bash

sudo dnf install MariaDB-server MariaDB-client

Démarrer et activer

bash

sudo systemctl enable mariadb

sudo systemctl start mariadb

🟩 3. Sécurisation initiale (OBLIGATOIRE)

MariaDB fournit un script de sécurisation :



bash

sudo mysql\_secure\_installation

Tu vas répondre :



Question	Réponse recommandée

Set root password?	YES

Remove anonymous users?	YES

Disallow root login remotely?	YES

Remove test database?	YES

Reload privilege tables?	YES





➡ Ça ferme les accès anonymes, supprime la base test, et force un mot de passe root.



🟦 4. Sécurisation avancée (production)

🔸 1. Désactiver l’accès root via TCP

Éditer /etc/mysql/mariadb.conf.d/50-server.cnf (Debian)

ou /etc/my.cnf.d/server.cnf (RHEL) :



ini

\[mysqld]

skip-networking

bind-address = 127.0.0.1

➡ Empêche root de se connecter depuis l’extérieur.



🔸 2. Créer un utilisateur admin dédié

Ne jamais utiliser root en production.



sql

CREATE USER 'admin'@'localhost' IDENTIFIED BY 'MotDePasseSolide!';

GRANT ALL PRIVILEGES ON \*.\* TO 'admin'@'localhost' WITH GRANT OPTION;

FLUSH PRIVILEGES;

🔸 3. Limiter les connexions externes

Si tu veux autoriser une IP précise :



sql

CREATE USER 'app'@'192.168.1.50' IDENTIFIED BY 'mdp';

GRANT SELECT, INSERT, UPDATE, DELETE ON ma\_base.\* TO 'app'@'192.168.1.50';

FLUSH PRIVILEGES;

➡ Jamais app'@'%' sauf si tu sais ce que tu fais.



🔸 4. Activer le chiffrement des connexions (SSL)

Vérifier si MariaDB supporte SSL

sql

SHOW VARIABLES LIKE 'have\_ssl';

Générer les certificats

bash

sudo mariadb-tls-create

Activer SSL dans la config

ini

\[mysqld]

ssl-ca=/etc/mysql/certs/ca.pem

ssl-cert=/etc/mysql/certs/server-cert.pem

ssl-key=/etc/mysql/certs/server-key.pem

🔸 5. Durcir les paramètres InnoDB

Dans 50-server.cnf :



ini

innodb\_buffer\_pool\_size = 1G

innodb\_log\_file\_size = 256M

innodb\_flush\_method = O\_DIRECT

innodb\_flush\_log\_at\_trx\_commit = 1

➡ Paramètres standards pour une prod.



🔸 6. Activer le slow query log

ini

slow\_query\_log = 1

slow\_query\_log\_file = /var/log/mysql/slow.log

long\_query\_time = 1

Redémarrer :



bash

sudo systemctl restart mariadb

🟧 5. Vérification de sécurité

Vérifier les utilisateurs

sql

SELECT user, host FROM mysql.user;

Vérifier les bases

sql

SHOW DATABASES;

Vérifier les ports ouverts

bash

sudo ss -tulpen | grep mysql

🟨 6. Bonnes pratiques en production

Ne jamais exposer le port 3306 sur Internet



Utiliser un reverse proxy ou un VPN pour les connexions externes



Toujours utiliser un utilisateur par application



Activer SSL si accès réseau



Activer le slow query log



Sauvegardes régulières (mysqldump ou mariabackup)



Monitoring (Prometheus, Grafana, Zabbix)

