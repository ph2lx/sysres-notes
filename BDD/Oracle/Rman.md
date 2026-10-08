Politique de rétention  
Conserver les sauvegardes pendant 7 jours :  
CONFIGURE RETENTION POLICY TO RECOVERY WINDOW OF 7 DAYS;  

Conserver les deux dernières sauvegarde :  
CONFIGURE RETENTION POLICY TO REDUNDANCY 2;  

Combiné les deux :  
CONFIGURE RETENTION POLICY TO RECOVERY WINDOW OF 7 DAYS REDUNDANCY 2;  

Vérifier la conf :  
SHOW ALL;  

Quand on effectuera la purge avec DELETE OBSOLETE  
Rman l'executera en fonction de la politique.  


Faire une sauvegarde  

Connecter rman a un user :  
rman target sys/MdP@nom_base(sid)  

Sauvegarde complete :  
BACKUP DATABASE;  

Sauvegarde des archive logs :  
BACKUP ARCHIVELOG ALL;  

Sauvegarde du fichier de contrôle et le spfile :  
BACKUP CURRENT CONTROLFILE;  
BACKUP SPFILE;  

Verifie l'integrité de la sauvegarde :  
BACKUP VALIDATE DATABASE;  

??(Fichier de contrôle :  
BACKUP VALIDATE CONTROLFILE;)??  

SPFile :  
BACKUP VALIDATE SPFILE;  

Journeaux d'archive :  
BACKUP VALIDATE ARCHIVELOG ALL;  

Pour planifier une sauvegarde dans Windows utilisé le planificateur !  
