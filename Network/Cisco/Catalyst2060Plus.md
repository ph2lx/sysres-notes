Pour reset :  
Appuyer sur le bouton devant jusqu'a qu'apparaisse en terminal :   

Using driver version 3 for media type 1  
Base ethernet MAC Address: 40:a6:e8:e2:7a:80  
Xmodem file system is available.  
The password-recovery mechanism is enabled.  

Relacher le bouton fait apparaître :

The system has been interrupted prior to initializing the  
flash filesystem.  The following commands will initialize  
the flash filesystem, and finish loading the operating  
system software:  

    flash_init  
    boot  

switch:  

flash_init  
del flash:config.text  
del flash:vlan.dat  
boot  

Latronche du BONUS :💡 Bonus : méthode ultra-complète (effacement total)  

Sur certains 3850, tu peux faire un reset complet de la flash :  

write erase  
delete /force /recursive flash:  
Reload  

Ne surtout pas faire çà !  
Depannage :  

Heureusement que j'vais un switch fonctionnel pour recup le .bin  

Conf réseau pour le sw1  
conf t  
interface vlan1  
ip address 192.168.0.97 255.255.255.0  
no shutdown  
exit  
ip default-gateway 192.168.0.1  
end  
write memory  

On copie les fichiers .bin et .pkg :  
copy flash:cat3k_caa-universalk9.SPA.03.07.04.E.152-3.E4.bin tftp:  
Etc…  

IP_ADDRESS=192.168.0.97  
IP_SUBNET_MASK=255.255.255.0  
DEFAULT_GATEWAY=192.168.0.18  
TFTP_SERVER=192.168.0.18  
TFTP_FILE=cat3k_caa-universalk9.SPA.03.03.03.SE.150-1.EZ3.bin  

Normalement çà aurait du etre çà :  
copy tftp://192.168.0.18/cat3k_caa-universalk9.SPA.03.03.03.SE.150-1.EZ3.bin flash:  

Mais read only file system  
j'ai reussie a booter en ram  
boot flash:cat3k_caa-universalk9.SPA.03.03.03.SE.150-1.EZ3.bin  

Et a copié en tftp.  
copy tftp://192.168.0.18/cat3k_caa-universalk9.SPA.03.03.03.SE.150-1.EZ3.bin flash:  

Ensuite on place le .bin en boot  
Switch# configure terminal  
Switch(config)# no boot system  
Switch(config)# boot system flash:cat3k_caa-universalk9.SPA.03.03.03.SE.150-1.EZ3.bin  
Switch(config)# end  
Switch# write memory  

