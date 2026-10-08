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




Oracle Database utilise un ensemble riche de commandes SQL et PL/SQL pour interagir avec les bases de données. Voici une liste des commandes et requêtes Oracle les plus couramment utilisées pour la gestion, la manipulation des données, et l'administration des bases de données Oracle :  

1. **Commandes SQL courantes dans Oracle**  
Ces commandes SQL sont standard et très souvent utilisées dans les bases de données Oracle.  

• SELECT : Extraire des données d'une table.  
  SELECT * FROM employees;  

• INSERT : Insérer des données dans une table.  
  INSERT INTO employees (employee_id, first_name, last_name)  
  VALUES (101, 'John', 'Doe');  

• UPDATE : Mettre à jour des données existantes dans une table.  
  UPDATE employees  
  SET salary = 5000  
  WHERE employee_id = 101;  

• DELETE : Supprimer des données d'une table.  
  DELETE FROM employees WHERE employee_id = 101;  

• CREATE TABLE : Créer une nouvelle table.  
  CREATE TABLE employees (  
    employee_id NUMBER PRIMARY KEY,  
    first_name VARCHAR2(50),  
    last_name VARCHAR2(50),  
    salary NUMBER  
  );  

• ALTER TABLE : Modifier une table existante.  
  ALTER TABLE employees ADD (email VARCHAR2(100));  

• DROP TABLE : Supprimer une table.  
  DROP TABLE employees;  

• TRUNCATE : Supprimer toutes les lignes d'une table.  
  TRUNCATE TABLE employees;  

• COMMIT : Valider les transactions.  
  COMMIT;  

• ROLLBACK : Annuler une transaction.  
  ROLLBACK;  

• GRANT : Accorder des privilèges.  
  GRANT SELECT, INSERT ON employees TO user1;  

• REVOKE : Révoquer des privilèges.  
  REVOKE SELECT, INSERT ON employees FROM user1;  

• CREATE INDEX : Créer un index.  
  CREATE INDEX emp_name_idx ON employees (last_name);  

• CREATE VIEW : Créer une vue.  
  CREATE VIEW emp_view AS  
  SELECT employee_id, first_name, last_name FROM employees;  


2. **Commandes PL/SQL (programmation procédurale)**  
PL/SQL est le langage procédural d'Oracle.  

• DECLARE : Déclarer des variables.  
  DECLARE  
    emp_id NUMBER;  
    emp_name VARCHAR2(50);  
  BEGIN  
    SELECT employee_id, first_name INTO emp_id, emp_name FROM employees WHERE employee_id = 101;  
  END;  

• BEGIN ... END : Bloc PL/SQL.  
  BEGIN  
    DBMS_OUTPUT.PUT_LINE('Hello World');  
  END;  

• EXCEPTION : Gestion des erreurs.  
  BEGIN  
    SELECT salary INTO v_salary FROM employees WHERE employee_id = 101;  
  EXCEPTION  
    WHEN NO_DATA_FOUND THEN  
      DBMS_OUTPUT.PUT_LINE('No data found');  
  END;  

• Boucles LOOP / FOR / WHILE  
  FOR i IN 1..10 LOOP  
    DBMS_OUTPUT.PUT_LINE(i);  
  END LOOP;  

• CURSOR : Parcourir des résultats.  
  DECLARE  
    CURSOR emp_cur IS SELECT employee_id, first_name FROM employees;  
    emp_row emp_cur%ROWTYPE;  
  BEGIN  
    OPEN emp_cur;  
    LOOP  
      FETCH emp_cur INTO emp_row;  
      EXIT WHEN emp_cur%NOTFOUND;  
      DBMS_OUTPUT.PUT_LINE(emp_row.first_name);  
    END LOOP;  
    CLOSE emp_cur;  
  END;  

• FUNCTION : Créer une fonction.  
  CREATE OR REPLACE FUNCTION get_employee_name(emp_id IN NUMBER) RETURN VARCHAR2 IS  
    emp_name VARCHAR2(50);  
  BEGIN  
    SELECT first_name INTO emp_name FROM employees WHERE employee_id = emp_id;  
    RETURN emp_name;  
  END;  

• PROCEDURE : Créer une procédure.  
  CREATE OR REPLACE PROCEDURE update_salary(emp_id IN NUMBER, new_salary IN NUMBER) IS  
  BEGIN  
    UPDATE employees SET salary = new_salary WHERE employee_id = emp_id;  
    COMMIT;  
  END;  


3. **Commandes pour l'administration Oracle (DBA)**  

• ALTER USER : Modifier un utilisateur.  
  ALTER USER user1 IDENTIFIED BY new_password;  

• CREATE USER : Créer un utilisateur.  
  CREATE USER user1 IDENTIFIED BY password;  

• CREATE ROLE : Créer un rôle.  
  CREATE ROLE manager_role;  

• GRANT : Accorder un rôle.  
  GRANT manager_role TO user1;  

• REVOKE : Révoquer un rôle.  
  REVOKE manager_role FROM user1;  

• SHOW PARAMETERS : Voir les paramètres Oracle.  
  SHOW PARAMETERS;  

• Informations sur la base :  
  SELECT * FROM v$database;  

• STARTUP / SHUTDOWN : Démarrer ou arrêter Oracle.  
  STARTUP;  
  SHUTDOWN IMMEDIATE;  


Conclusion :  
Ces commandes sont essentielles pour interagir avec les bases Oracle, gérer des données, écrire des blocs PL/SQL, ou administrer le système. Elles te donnent une base solide pour travailler efficacement avec Oracle.  

RECOVER DATABASE;  
EOF  
