1. Mode admin
--------------

enable

2. Mode configuration
----------------------
conf t ou configure terminal

3. Changer le nom du routeur
--------------------------
Hostname tartempion

4. Attribution d'adresse ip
----------------------------

interface fastethernet 0/0 
ip address 192.168.0.1 255.255.255.0
no shutdown

Active l'interface
Lui attribut une ip masque
Et l'empêche de s'éteindre être continuellement "up"

5. Sécurisation du routeur
---------------------------
-=Sécurisation du mode utilisateur=-  


conf t
line console 0
password ciscoforever
login

-=Sécurisation du mode admin=-

enable secret ciscoforever

-=Activation du service chiffrement=-

service password-encryption

6. Sauvegarde
--------------

Sauvegarde RAM vers la NVRAM

copy running-config startup-config Ou write wr

Sauvegarde RAM vers TFTP

copy run tftp

Sauvegarde RAM vers FTP

ip ftp username cisco
ip ftp password cisco
do copy run ftp

7. Petites commande pour montrer les informations
-----------------------------------------------

Sh run
Sh vlan
Sh vtp status
Sh inter trunk
Sh vtp password

8. Installation d’un firmware depuis la ROM et le Réseau
---------------------------------------------------------

Ctrl-c Pendant le boot
rommon1 > tftpdnld
The following variables are REQUIRED to be set for tftpdnld
IP_ADDRESS: The IP address for this unit
IP_SUBNET_MASK: The subnet mask for this unit
DEFAULT_GATEWAY: The default gateway for this unit
TFTP_SERVER: The IP address of the server to fetch from
TFTP_FILE: The filename to fetch
rommon2 > IP_ADDRESS=192.168.0.254
rommon3 > IP_SUBNET_MASK=255.255.255.0
rommon4 > DEFAULT_GATEWAY=192.168.0.1
rommon5 > TFTP_SERVER=192.168.0.1
rommon6 > TFTP_FILE c2800nm-advipservicesk9-mz.151-4.M4.bin
rommon7 > tftpdnld
Do you wish to continue? y/n: [n]:
y
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!


-=Suppression du firmware=-



enable
conf t
boot system tftp c2800nm-advipservicesk9-mz.151-4.M4.bin 192.168.0.1
config register 0x210F
interface FastEthernet0/0
ip address 192.168.0.254 255.255.255.0
no shut
do wr

9. Réinitialisation d’un routeur / Reset mot de passe perdu
------------------------------------------------------------

-=Effacement de la config=-

erase startup-config

-=Reset du mot de passe=-

program load complete, entry point: 0x8000f000, size: 0x3ed1338
Self decompressing the image :
###################
Ctrl+C
monitor: command "boot" aborted due to user interrupt
rommon1 > confreg 0x2142
rommon2 > reset
---
System Configuration Dialog
Would you like to enter the initial configuration dialog? [yes/no]:
Ctrl+C


en
copy start run
Destination filename [running
config]?
783 bytes copied in 0.416 secs (1882 bytes/sec)
R1#conf t
R1(config)#
enable secret ciscoforever
R1(config)#config-register 0x2102
R1(config)#do wr

10. Configurer accès TELNET OU SSH TELNET
------------------------------------------

username admin secret bonjour	 On crée l’utilisateur "admin" avec mdp "bonjour"
enable secret bonjour	On active le mot de passe pour le système
line vty 0 4	On rentre dans le menu Telnet
login local	On demande à Telnet d’utiliser les utilisateurs local, enregistré sur le Switch/Routeur
password bonjour	On déclare le mot de passe Telnet

SSH
enable
hostname R1			Modification du nom du routeur
ip domain-name ciscoforever.fr			Configuration d’un nom de domaine
username admin secret ciscoforever			Création d’un compte utilisateur
line vty 0 4			
transport input ssh			
login local			L’authentification se fera par authentification on d’un compte local
crypto key generate rsa			Génération des clés de chiffrement
The name for the keys will be: R1.ciscoforever.fr			
Choose the size of the key modulus in the range of 360 to 2048 for your
General Purpose Keys. Choosing a key modulus greater than 512 may take
a few minutes.
How many bits in the modulus [512]:
2048			Nombre de bits utilisés pour le chiffrement
% Generating 2048 bit RSA keys, keys will be non			
exportable...[OK]
ip ssh version 2			Activation de la version 2 du protocole ssh
