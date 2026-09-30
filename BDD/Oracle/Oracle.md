🟥 1. EXPORT Oracle avec Data Pump (expdp)

C’est la méthode moderne (remplace exp/imp).



Export d’un schéma

bash

expdp system/password schemas=MON\_SCHEMA directory=DATA\_PUMP\_DIR dumpfile=mon\_schema.dmp logfile=mon\_schema.log

Export de plusieurs schémas

bash

expdp system/password schemas=SCHEMA1,SCHEMA2 directory=DATA\_PUMP\_DIR dumpfile=multi.dmp logfile=multi.log

Export d’une base complète

bash

expdp system/password full=y directory=DATA\_PUMP\_DIR dumpfile=full.dmp logfile=full.log

Export d’une table

bash

expdp system/password tables=MON\_SCHEMA.MA\_TABLE directory=DATA\_PUMP\_DIR dumpfile=table.dmp logfile=table.log

🟦 2. IMPORT Oracle avec Data Pump (impdp)

Import d’un schéma

bash

impdp system/password schemas=MON\_SCHEMA directory=DATA\_PUMP\_DIR dumpfile=mon\_schema.dmp logfile=import.log

Import dans un schéma différent (remapping)

bash

impdp system/password directory=DATA\_PUMP\_DIR dumpfile=mon\_schema.dmp \\

&#x20;   remap\_schema=ANCIEN\_SCHEMA:NOUVEAU\_SCHEMA \\

&#x20;   logfile=clone.log

Import dans une autre tablespace

bash

impdp system/password directory=DATA\_PUMP\_DIR dumpfile=mon\_schema.dmp \\

&#x20;   remap\_tablespace=OLD\_TS:NEW\_TS \\

&#x20;   logfile=import\_ts.log

Import d’une table

bash

impdp system/password tables=MON\_SCHEMA.MA\_TABLE directory=DATA\_PUMP\_DIR dumpfile=table.dmp logfile=table\_import.log

🟩 3. CLONER un schéma Oracle (méthode Data Pump)

C’est la méthode standard DBA Oracle.



Étape 1 : export du schéma source

bash

expdp system/password schemas=SOURCE directory=DATA\_PUMP\_DIR dumpfile=source.dmp logfile=source.log

Étape 2 : import dans un nouveau schéma

bash

impdp system/password directory=DATA\_PUMP\_DIR dumpfile=source.dmp \\

&#x20;   remap\_schema=SOURCE:CLONE \\

&#x20;   logfile=clone.log

➡ Résultat : CLONE = copie complète de SOURCE.



🟪 4. Copier une table Oracle (structure + données)

sql

CREATE TABLE nouveau\_schema.ma\_table AS

SELECT \* FROM ancien\_schema.ma\_table;

🟧 5. Copier une table Oracle (structure uniquement)

sql

CREATE TABLE nouveau\_schema.ma\_table

AS SELECT \* FROM ancien\_schema.ma\_table WHERE 1=0;

🟫 6. Copier toutes les tables d’un schéma (SQL interne)

Oracle n’a pas de commande native, mais tu peux faire :



sql

BEGIN

&#x20; FOR t IN (SELECT table\_name FROM all\_tables WHERE owner='ANCIEN\_SCHEMA') LOOP

&#x20;   EXECUTE 'CREATE TABLE NOUVEAU\_SCHEMA.' || t.table\_name ||

&#x20;           ' AS SELECT \* FROM ANCIEN\_SCHEMA.' || t.table\_name;

&#x20; END LOOP;

END;

/

🟨 7. Export classique (exp) — ancien mais encore utilisé

Export schéma

bash

exp system/password owner=MON\_SCHEMA file=mon\_schema.dmp log=mon\_schema.log

Import schéma

bash

imp system/password fromuser=MON\_SCHEMA touser=MON\_SCHEMA file=mon\_schema.dmp log=import.log

🟥 8. RMAN (sauvegarde/restauration base complète)

RMAN = pour les bases complètes, pas pour les schémas.



Sauvegarde complète

bash

rman target / <<EOF

BACKUP DATABASE PLUS ARCHIVELOG;

EOF

Restauration complète

bash

rman target / <<EOF

RESTORE DATABASE;

RECOVER DATABASE;

EOF

