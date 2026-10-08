Lister les VMs Zombies !  

Depuis ESXi (SSH activé, il faut activer le service ssh dans la webui de l’esx : http://sxxxx)  

Liste les fichiers .vmx trouvés sur les datastores :  
find /vmfs/volumes/ -name "*.vmx"  

Afficher plus de lignes  

Ensuite vérifie si l’UUID est enregistré :  
vim-cmd vmsvc/getallvms | grep -f <(find /vmfs/volumes/ -name "*.vmx")  

Afficher plus de lignes  

Si un fichier .vmx apparaît dans le datastore mais pas dans getallvms → zombie.  

Type de zombie — Comment le détecter ?  
Inventaire vCenter cassé → VM = Orphaned / Invalid  
VM supprimée mais fichiers restants → find .vmx ≠ getallvms  
World ESXi actif sans VM → esxcli vm process list ≠ getallvms  
VM inutilisée → Zéro activité CPU/NET/IO sur 30 jours  
VM en "Unknown" → Power state non reconnu  


        vm_netmask              = 24  
         disks                   = [ {label="Hard disk 1", size=20, unit_number=0} ]  
         is_template                = false  
     },  

     /*#folder: 00_NE_PAS_DEMARRER  
     "SRV01 - Viaduc-esb Appli- PRE" = {  
         vm_name                 = "SRV01 - Viaduc-esb Appli- PRE"  
         vm_hostname             = "SRV01"  
         vm_domain               = "local.intranet"  
         vm_ip_address           = "None"  
         vm_gateway              = "None"  
         template_name           = "SRV01 - Viaduc-esb Appli- PRE"  
         datastore_name          = "SITEA_APPLIS_03"  
         network_name            = [{ name="VLAN PREPROD" }]  
         vm_folder               = "/00_NE_PAS_DEMARRER"  
         cpu                     = 4  
         memory                  = 16384  
         scsi_controller_count   = 1  
         enable_logging          = true  
         sata_controller_count   = 1  
         enable_disk_uuid        = false  
         site                    = "SITEA"  
         type                    = "APPLIS"  
         vm_netmask              = 24  
         disks                   = [ {label="Hard disk 1", size=80, unit_number=0} ]  
         is_template                = false  
     },  
*/  

     #folder: 00_NE_PAS_DEMARRER  
     "SRV02 - Biblibre Appli - PRE" = {  
         vm_name                 = "SRV02 - Biblibre Appli - PRE"  
         vm_hostname  

Pour Décomissionner ajouter /* avant #folder  
*/ sur la ligne apres },  
