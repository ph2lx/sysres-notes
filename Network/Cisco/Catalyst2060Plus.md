Pour reset :

Appuyer sur lebouton devant jusqu'apparaisse en terminal



Using driver version 3 for media type 1

Base ethernet MAC Address: 40:a6:e8:e2:7a:80

Xmodem file system is available.

The password-recovery mechanism is enabled.



Relacher et apparait



The system has been interrupted prior to initializing the

flash filesystem.  The following commands will initialize

the flash filesystem, and finish loading the operating

system software:



&#x20;   flash\_init

&#x20;   boot





switch:



flash\_init

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

&#x20;

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

copy flash:cat3k\_caa-universalk9.SPA.03.07.04.E.152-3.E4.bin tftp:

Etc…



IP\_ADDRESS=192.168.0.97

IP\_SUBNET\_MASK=255.255.255.0

DEFAULT\_GATEWAY=192.168.0.18

TFTP\_SERVER=192.168.0.18

TFTP\_FILE=cat3k\_caa-universalk9.SPA.03.03.03.SE.150-1.EZ3.bin

Normalement çà aurait du etre çà :

copy tftp://192.168.0.18/cat3k\_caa-universalk9.SPA.03.03.03.SE.150-1.EZ3.bin flash:

Mais read only file system

j'ai reussie a booter en ram

boot flash:cat3k\_caa-universalk9.SPA.03.03.03.SE.150-1.EZ3.bin



Et a copié en tftp.

copy tftp://192.168.0.18/cat3k\_caa-universalk9.SPA.03.03.03.SE.150-1.EZ3.bin flash:

&#x20;Ensuite on place le .bin en boot

Switch# configure terminal

Switch(config)# no boot system

Switch(config)# boot system flash:cat3k\_caa-universalk9.SPA.03.03.03.SE.150-1.EZ3.bin

Switch(config)# end

Switch# write memory








