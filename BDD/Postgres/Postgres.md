🟦 1. DUMP d’un schéma (export)

bash

pg\_dump -U postgres -n mon\_schema ma\_base > mon\_schema.sql

Dump structure uniquement

bash

pg\_dump -U postgres -n mon\_schema --schema-only ma\_base > mon\_schema\_structure.sql

Dump données uniquement

bash

pg\_dump -U postgres -n mon\_schema --data-only ma\_base > mon\_schema\_data.sql

🟧 2. DUMP d’une base complète

bash

pg\_dump -U postgres ma\_base > ma\_base.sql

Dump complet compressé

bash

pg\_dump -U postgres ma\_base | gzip > ma\_base.sql.gz

🟩 3. RESTAURATION d’un dump (import)

bash

psql -U postgres -d ma\_base -f mon\_schema.sql

Import d’une base complète

bash

psql -U postgres -d nouvelle\_base -f ma\_base.sql

🟪 4. CLONER un schéma (méthode standard DBA)

PostgreSQL n’a pas de commande native “clone schema”, donc on fait :

dump → sed → import.



Étape 1 : dump du schéma source

bash

pg\_dump -U postgres -n ancien\_schema ma\_base > clone.sql

Étape 2 : remplacer le nom du schéma

bash

sed -i 's/ancien\_schema/nouveau\_schema/g' clone.sql

Étape 3 : import dans la base

bash

psql -U postgres -d ma\_base -f clone.sql

➡ Résultat : nouveau\_schema = copie complète de ancien\_schema.



🟫 5. Copier un schéma vers une autre base

bash

pg\_dump -U postgres -n mon\_schema base\_source > schema.sql

psql -U postgres -d base\_cible -f schema.sql

🟨 6. Copier une table (structure + données)

sql

CREATE TABLE nouveau\_schema.ma\_table AS

SELECT \* FROM ancien\_schema.ma\_table;

🟧 7. Copier une table (structure uniquement)

sql

CREATE TABLE nouveau\_schema.ma\_table (LIKE ancien\_schema.ma\_table INCLUDING ALL);

🟦 8. Copier toutes les tables d’un schéma (structure + données)

Méthode SQL interne (sans dump) :



sql

DO $$

DECLARE

&#x20;   t text;

BEGIN

&#x20;   FOR t IN

&#x20;       SELECT tablename FROM pg\_tables WHERE schemaname='ancien\_schema'

&#x20;   LOOP

&#x20;       EXECUTE format(

&#x20;           'CREATE TABLE nouveau\_schema.%I AS SELECT \* FROM ancien\_schema.%I',

&#x20;           t, t

&#x20;       );

&#x20;   END LOOP;

END $$;

