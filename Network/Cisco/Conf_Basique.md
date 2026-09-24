Petites commande pour montrer les informations



Sh run

Sh vlan

Sh vtp status

Sh inter trunk

Sh vtp password



Mode admin



enable





Mode configuration



conf t ou configure terminal



Changer le nom du routeur



Hostname tartempion



Sauvegarde



Sauvegarde RAM vers la NVRAM



copy running-config startup-config

Ou write 

wr



Sauvegarde

RAM vers TFTP



copy run tftp



Sauvegarde

RAM vers FTP



ip ftp username cisco

ip ftp password cisco

do copy run ftp



Attribution d'adresse ip



interface fastethernet 0/0 

ip address 192.168.0.1 255.255.255.0

no shutdown



Active l'interface

Lui attribut une ip masque

Et l'empêche de s'éteindre être continuellement "up"



Sécurisation du routeur

Sécurisation

du mode utilisateur





conf t

line console 0

password ciscoforever

login



Sécurisation

du mode admin



enable secret ciscoforever



Activation du service

chiffrement



service password-encryption



Installation d’un firmware depuis la ROM et le Réseau



Ctrl-c Pendant le boot

rommon1 > tftpdnld

The following variables are REQUIRED to be set for tftpdnld

IP\_ADDRESS: The IP address for this unit

IP\_SUBNET\_MASK: The subnet mask for this unit

DEFAULT\_GATEWAY: The default gateway for this unit

TFTP\_SERVER: The IP address of the server to fetch from

TFTP\_FILE: The filename to fetch

rommon2 > IP\_ADDRESS=192.168.0.254

rommon3 > IP\_SUBNET\_MASK=255.255.255.0

rommon4 > DEFAULT\_GATEWAY=192.168.0.1

rommon5 > TFTP\_SERVER=192.168.0.1

rommon6 > TFTP\_FILE c2800nm-advipservicesk9-mz.151-4.M4.bin

rommon7 > tftpdnld

Do you wish to continue? y/n: \[n]:

y

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!





Suppression du firmware





enable

conf t

boot system tftp c2800nm-advipservicesk9-mz.151-4.M4.bin 192.168.0.1

config register 0x210F

interface FastEthernet0/0

ip address 192.168.0.254 255.255.255.0

no shut

do wr



Réinitialisation d’un routeur / Reset mot de passe perdu

Effacement de la config



erase startup-config



Reset du mot de passe



program load complete, entry point: 0x8000f000, size: 0x3ed1338

Self decompressing the image :

\###################

Ctrl+C

monitor: command "boot" aborted due to user interrupt

rommon1 > confreg 0x2142

rommon2 > reset

\---

System Configuration Dialog

Would you like to enter the initial configuration dialog? \[yes/no]:

Ctrl+C





en

copy start run

Destination filename \[running

config]?

783 bytes copied in 0.416 secs (1882 bytes/sec)

R1#conf t

R1(config)#

enable secret ciscoforever

R1(config)#config-register 0x2102

R1(config)#do wr







Configurer accès TELNET OU SSH

TELNET

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

How many bits in the modulus \[512]:

2048			Nombre de bits utilisés pour le chiffrement

% Generating 2048 bit RSA keys, keys will be non			

exportable...\[OK]

ip ssh version 2			Activation de la version 2 du protocole ssh



Update firmware



Suppression du firmware



Router>

Router>enable

R1#dir flash:

Directory of flash:/

3 -rw- 50938004 <no date> c2800nm-advipservicesk9-mz.124-15.T1.bin

64016384 bytes total (12822561 bytes free)

R1#delete flash:

Delete filename \[]?c2800nm-advipservicesk9-mz.124-15.T1.bin

Delete flash:/c2800nm-advipservicesk9-mz.124-15.T1.bin? \[confirm]



Ajout d’un nouveau firmware



Router>

Router>enable

R1#copy tftp: flash:

Address or name of remote host \[]? 192.168.0.1

Source filename \[]? c2800nm-advipservicesk9-mz.151-4.M4.bin

Destination filename \[c2800nm-advipservicesk9-mz.151-4.M4.bin]?

Accessing tftp://192.168.0.1/c2800nm-advipservicesk9-mz.151-4.M4.bin....

Loading c2800nm-advipservicesk9-mz.151-4.M4.bin from 192.168.0.1: !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

\[OK - 33591768 bytes]

33591768 bytes copied in 3.513 secs (1003984 bytes/sec)

R1#reload



Démarrer depuis le réseau + bannière + DHCP



Suppression du firmware



Enable

conf t

boot system tftp c2800nm-advipservicesk9-mz.151-4.M4.bin 192.168.0.1

config register 0x210F

interface FastEthernet0/0

ip address 192.168.0.254 255.255.255.0

no shut

&#x20;do wr



Bannière d’accueil



banner motd \* TITREjgghjgj\*





Configuration d’un serveur DHCP sur un routeur



Configuration des IP



router>

conf t

inter fa 0/0

ip address 192.168.1.254 255.255.255.0

no shut

inter fa 0/1

ip address 192.168.2.254 255.255.255.0

no shut

Exit



Création du Pool 1

ip dhcp pool LAN1		Création du pool

network 192.168.1.0 255.255.255.0		Création de la plage réseau

default-router 192.168.1.254		Configuration de la passerelle

dns-server 8.8.8.8		Configuration du serveur DNS

option 150 ip 192.168.0.100		Exemple d’une configuration avec option 

ip dhcp excluded-address 192.168.1.1 192.168.1.99		Plage d’exclusion d’adresse

ip dhcp excluded-address 192.168.1.254		





Exemple

d’une configuration avec option

Plage

d’exclusion d’adresse



Routes Statiques



Route connectée

• Avec l’interface de sortie locale

• Se configure sur une liaison Point to Point

Ip route 192.168.2.0 255.255.255.0 Serial 0/1

Ip route 192.168.0.0 255.255.255.0 Serial 0/2



Route récursive

• Avec l’interface l’IP du routeur voisin

• Se configure sur une liaison multipoint



Ip route 192.168.2.0 255.255.255.0 192.168.1.1

Ip route 192.168.0.0 255.255.255.0 192.168.1.254



Route entièrement spécifiée

• Avec l’interface locale et l’IP du voisin

• Se configure sur une liaison Point to Point et multipoint



Ip route 192.168.2.0 255.255.255.0 se0/1 192.168.1.1

Ip route 192.168.0.0 255.2



55.255.0 se0/2 192.168.1.254







Route par défaut

• Route de dernier recours

• Pour contacter les réseaux inconnus de la table



Ip route 0.0.0.0 0.0.0.0 serial 0/1

Ip route 0.0.0.0 0.0.0.0 192.168.1.254 10



Route récapitulative

• Route servant a alléger la table de routeur

• Le masque de cette route en globe d’autres masques



Ip route 192.168.2.0 255.255.254.0 192.168.1.1

Ip route 192.168.2.0 255.255.254.0 serial 0/1 10



Route flottante

• Une route de backup

• Modification du poids par défaut



Ip route 192.168.2.0 255.255.255.0 se0/1 10

Ip route 192.168.2.0 255.255.255.0 192.168.1.1 10







Un autre type de route statique est une route statique flottante. Les routes statiques flottantes sont des routes statiques utilisées pour fournir un chemin de secours à une route statique ou une route dynamique principale, en cas d'échec de lien. La route statique flottante est utilisée uniquement lorsque la route principale n'est pas disponible.

Pour cela, la route statique flottante est configurée avec une distance administrative plus élevée que la route principale. Souvenez-vous que la distance administrative indique la fiabilité d'une route. Si plusieurs chemins vers la destination existent, le routeur choisira le chemin présentant la plus courte distance administrative. 



Configuration d’un Relay DHCP



Redirection des

Trames DHCP



Enable

conf t

inter fa 0/0

ip address 192.168.1.254 255.255.255.0

no shut

inter fa 0/1

ip address 192.168.2.254 255.255.255.0

ip helper-address 192.168.1.1		Identification du serveur DHCP

no shut

Exit



conf t

ip forward-protocol udp 517		Ajout d’un protocole dans la redirection

no ip forward-protocol udp 37		|

no ip forward-protocol udp 39		Blocaqe de protocole par defaut

no ip forward-protocol udp 137		|

no ip forward-protocol udp 138		|







Routage inter-Vlan  "router-on-the stick"



Les switch doivent avoir leurs interfaces configurées en mode Trunk



un trunk est une liaison ayant pour but de véhiculer le trafic de plusieurs vlans. Vu qu’une trame ethernet standard ne dispose pas d’information relative au vlan duquel elle provient, il est nécessaire d’ajouter ces informations par l’intermédiaire d’un protocole.

Dans la cas de dot1q (protocole standard IEEE 802.1q), un tag est inséré entre le champ d’adresse MAC source et le champ Type/Longueur de la trame





??????????Poser la question pour le code spanning-tree cisco command ????



<<<<<SWITCH>>>>>>

Creation de vlan



en

conf t

vlan 101

name VERT

vlan 102 

name VIOLET

vlan 103

name BLEU



Mise en mode Trunk ou access

Trunk=fournis les tables pour un autre switch

Access=ne fournis pas les tables pour un autre switch



interface fa0/1

switchport mode trunk

interface fa0/2

switchport access vlan 101

switchport mode access

&#x20;

interface fa0/3

switchport access vlan 102

switchport mode access 

Ou

switchport trunk allowed vlan 10,20,30		Autorise les vlan 10,20,30

switchport trunk native vlan 30



Le vlan natif, est le vlan dans lequel sont véhiculées les trames non taguées dot1q. Donc si un switch reçoit sur une interface trunk une trame ethernet standard, il la placera dans ce vlan natif, en quelque sorte, un vlan par défaut (de marquage).





Et avoir le vtp server en mode client ou server

vtp domain ciscoforever

vtp password ciscoforever

vtp version 2

vtp mode client	 Ou server

Un vlan de gestion (pas obligatoir)



vlan 150

interface vlan 150

ip add 10.10.150.9 255.255.255.0

ip default-gateway 10.10.150.254

<<<<<SWITCH>>>>>>

Encapsulation Vlan 802.11Q



en

conf t

activer l’interface fa 0/0 et la met up

interface fa 0/0

no shut

&#x20;

interface fa 0/0.101				crée par dessus fa0/0 l’interface fa 0/0.101

encapsulation dot1Q 101				Active le protocol 802.11q sur vlan 101

ip address 10.10.101.254 255.255.255.0				Qui sera à l'adresse

ip helper-address 10.10.200.1				DHCP Helper 'un serveur dans le réseau

&#x20;

interface fa 0/0.102

encapsulation dot1Q 102

ip address 10.10.102.254 255.255.255.0

ip helper-address 10.10.200.1



Empeche les interface de passer "down", elles sont mise en "up"

interface range fastethernet0/0.101,fa0/0.102,fa0/0.103,fa0/0.104,fa0/0.160, fa0/0.200, fa0/0.150

no shut



On aurait pu mettre aussi :





interface fa 0/0.102

encapsulation dot1Q 102

ip address 10.10.102.254 255.255.255.0

ip helper-address 10.10.200.1

no shut









