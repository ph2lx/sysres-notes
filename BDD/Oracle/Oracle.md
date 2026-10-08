🟥 1. EXPORT Oracle avec Data Pump (expdp)  
C’est la méthode moderne (remplace exp/imp).  

Export d’un schéma  
expdp system/password schemas=MON_SCHEMA directory=DATA_PUMP_DIR dumpfile=mon_schema.dmp logfile=mon_schema.log  

Export de plusieurs schémas  
expdp system/password schemas=SCHEMA1,SCHEMA2 directory=DATA_PUMP_DIR dumpfile=multi.dmp logfile=multi.log  

Export d’une base complète  
expdp system/password full=y directory=DATA_PUMP_DIR dumpfile=full.dmp logfile=full.log  

Export d’une table  
expdp system/password tables=MON_SCHEMA.MA_TABLE directory=DATA_PUMP_DIR dumpfile=table.dmp logfile=table.log  


🟦 2. IMPORT Oracle avec Data Pump (impdp)  

Import d’un schéma  
impdp system/password schemas=MON_SCHEMA directory=DATA_PUMP_DIR dumpfile=mon_schema.dmp logfile=import.log  

Import dans un schéma différent (remapping)  
impdp system/password directory=DATA_PUMP_DIR dumpfile=mon_schema.dmp \  
    remap_schema=ANCIEN_SCHEMA:NOUVEAU_SCHEMA \  
    logfile=clone.log  

Import dans une autre tablespace  
impdp system/password directory=DATA_PUMP_DIR dumpfile=mon_schema.dmp \  
    remap_tablespace=OLD_TS:NEW_TS \  
    logfile=import_ts.log  

Import d’une table  
impdp system/password tables=MON_SCHEMA.MA_TABLE directory=DATA_PUMP_DIR dumpfile=table.dmp logfile=table_import.log  


🟩 3. CLONER un schéma Oracle (méthode Data Pump)  
C’est la méthode standard DBA Oracle.  

Étape 1 : export du schéma source  
expdp system/password schemas=SOURCE directory=DATA_PUMP_DIR dumpfile=source.dmp logfile=source.log  

Étape 2 : import dans un nouveau schéma  
impdp system/password directory=DATA_PUMP_DIR dumpfile=source.dmp \  
    remap_schema=SOURCE:CLONE \  
    logfile=clone.log  

➡ Résultat : CLONE = copie complète de SOURCE.  


🟪 4. Copier une table Oracle (structure + données)  
CREATE TABLE nouveau_schema.ma_table AS  
SELECT * FROM ancien_schema.ma_table;  


🟧 5. Copier une table Oracle (structure uniquement)  
CREATE TABLE nouveau_schema.ma_table  
AS SELECT * FROM ancien_schema.ma_table WHERE 1=0;  


🟫 6. Copier toutes les tables d’un schéma (SQL interne)  
Oracle n’a pas de commande native, mais tu peux faire :  

BEGIN  
    FOR t IN (SELECT table_name FROM all_tables WHERE owner='ANCIEN_SCHEMA') LOOP  
        EXECUTE 'CREATE TABLE NOUVEAU_SCHEMA.' || t.table_name ||  
                ' AS SELECT * FROM ANCIEN_SCHEMA.' || t.table_name;  
    END LOOP;  
END;  
/  


🟨 7. Export classique (exp) — ancien mais encore utilisé  

Export schéma  
exp system/password owner=MON_SCHEMA file=mon_schema.dmp log=mon_schema.log  

Import schéma  
imp system/password fromuser=MON_SCHEMA touser=MON_SCHEMA file=mon_schema.dmp log=import.log  


🟥 8. RMAN (sauvegarde/restauration base complète)  
RMAN = pour les bases complètes, pas pour les schémas.  

Sauvegarde complète  
rman target / <<EOF  
BACKUP DATABASE PLUS ARCHIVELOG;  
EOF  

Restauration complète  
rman target / <<EOF  
RESTORE DATABASE;  
RECOVER DATABASE;  
EOF  
