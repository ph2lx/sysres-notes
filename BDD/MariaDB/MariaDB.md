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

