# Sommaire

1. [Mode admin](#1-mode-admin)
2. [Mode configuration](#2-mode-configuration)
3. [Changer le nom du routeur](#3-changer-le-nom-du-routeur)
4. [Attribution d'adresse IP](#4-attribution-dadresse-ip)
5. [Sécurisation du routeur](#5-sécurisation-du-routeur)
6. [Sauvegarde](#6-sauvegarde)
7. [Commandes d’information](#7-commandes-dinformation)
8. [Installation d’un firmware depuis la ROM et le réseau](#8-installation-dun-firmware-depuis-la-rom-et-le-réseau)
9. [Réinitialisation / Reset mot de passe perdu](#9-réinitialisation--reset-mot-de-passe-perdu)
10. [Accès TELNET / SSH](#10-accès-telnet--ssh)
11. [Update firmware](#11-update-firmware)
12. [Démarrer depuis le réseau + bannière + DHCP](#12-démarrer-depuis-le-réseau--bannière--dhcp)
13. [Serveur DHCP](#13-serveur-dhcp)
14. [Relay DHCP](#14-relay-dhcp)
15. [Routage inter‑VLAN (router‑on‑the‑stick)](#15-routage-inter-vlan-router-on-the-stick)
16. [Routes statiques](#16-routes-statiques)

---

# 1. Mode admin

```
enable
```

---

# 2. Mode configuration

```
conf t
```

---

# 3. Changer le nom du routeur

```
hostname tartempion
```

---

# 4. Attribution d'adresse IP

```
interface fastethernet 0/0
ip address 192.168.0.1 255.255.255.0
no shutdown
```

Active l’interface  
Lui attribue une IP + masque  
Empêche l’interface de passer en *down*

---

# 5. Sécurisation du routeur

## Sécurisation du mode utilisateur (console)

```
conf t
line console 0
password ciscoforever
login
```

## Sécurisation du mode admin

```
enable secret ciscoforever
```

## Activation du chiffrement des mots de passe

```
service password-encryption
```

---

# 6. Sauvegarde

## RAM → NVRAM

```
copy running-config startup-config
```

ou :

```
write wr
```

## RAM → TFTP

```
copy run tftp
```

## RAM → FTP

```
ip ftp username cisco
ip ftp password cisco
do copy run ftp
```

---

# 7. Commandes d’information

```
sh run
sh vlan
sh vtp status
sh inter trunk
sh vtp password
```

---

# 8. Installation d’un firmware depuis la ROM et le réseau

Pendant le boot :  
**Ctrl+C**

```
rommon1 > tftpdnld
IP_ADDRESS=192.168.0.254
IP_SUBNET_MASK=255.255.255.0
DEFAULT_GATEWAY=192.168.0.1
TFTP_SERVER=192.168.0.1
TFTP_FILE=c2800nm-advipservicesk9-mz.151-4.M4.bin
rommon7 > tftpdnld
```

Confirmation :  
`y`

Beaucoup de `!` pendant le transfert.

## Suppression du firmware

```
enable
conf t
boot system tftp c2800nm-advipservicesk9-mz.151-4.M4.bin 192.168.0.1
config register 0x210F
interface FastEthernet0/0
ip address 192.168.0.254 255.255.255.0
no shut
do wr
```

---

# 9. Réinitialisation / Reset mot de passe perdu

## Effacement de la configuration

```
erase startup-config
```

## Reset du mot de passe

Pendant le boot :  
**Ctrl+C**

```
rommon1 > confreg 0x2142
rommon2 > reset
```

Ne pas entrer dans le dialogue initial :  
**Ctrl+C**

## Restauration

```
copy start run
conf t
enable secret ciscoforever
config-register 0x2102
do wr
```

---

# 10. Accès TELNET / SSH

## TELNET

```
username admin secret bonjour
enable secret bonjour
line vty 0 4
login local
password bonjour
```

## SSH

```
enable
hostname R1
ip domain-name ciscoforever.fr
username admin secret ciscoforever
line vty 0 4
transport input ssh
login local
crypto key generate rsa
2048
```

---

# 11. Update firmware

## Suppression

```
enable
dir flash:
delete flash:/c2800nm-advipservicesk9-mz.124-15.T1.bin
```

## Ajout

```
copy tftp: flash:
192.168.0.1
c2800nm-advipservicesk9-mz.151-4.M4.bin
reload
```

---

# 12. Démarrer depuis le réseau + bannière + DHCP

## Boot réseau

```
enable
conf t
boot system tftp c2800nm-advipservicesk9-mz.151-4.M4.bin 192.168.0.1
config register 0x210F
interface FastEthernet0/0
ip address 192.168.0.254 255.255.255.0
no shut
do wr
```

## Bannière

```
banner motd * TITREjgghjgj *
```

---

# 13. Serveur DHCP

## Configuration des interfaces

```
inter fa 0/0
ip address 192.168.1.254 255.255.255.0
no shut

inter fa 0/1
ip address 192.168.2.254 255.255.255.0
no shut
```

## Pool DHCP

```
ip dhcp pool LAN1
network 192.168.1.0 255.255.255.0
default-router 192.168.1.254
dns-server 8.8.8.8
option 150 ip 192.168.0.100
ip dhcp excluded-address 192.168.1.1 192.168.1.99
ip dhcp excluded-address 192.168.1.254
```

---

# 14. Relay DHCP

```
inter fa 0/0
ip address 192.168.1.254 255.255.255.0
no shut

inter fa 0/1
ip address 192.168.2.254 255.255.255.0
ip helper-address 192.168.1.1
no shut

ip forward-protocol udp 517
no ip forward-protocol udp 37
no ip forward-protocol udp 39
no ip forward-protocol udp 137
no ip forward-protocol udp 138
```

---

# 15. Routage inter‑VLAN (router‑on‑the‑stick)

Les switch doivent être en **mode trunk**.  
Dot1Q ajoute un **tag VLAN** dans la trame Ethernet.

## VLAN

```
vlan 101
name VERT
vlan 102
name VIOLET
vlan 103
name BLEU
```

## Trunk / Access

```
interface fa0/1
switchport mode trunk

interface fa0/2
switchport access vlan 101
switchport mode access

interface fa0/3
switchport access vlan 102
switchport mode access
```

Ou :

```
switchport trunk allowed vlan 10,20,30
switchport trunk native vlan 30
```

## VTP

```
vtp domain ciscoforever
vtp password ciscoforever
vtp version 2
vtp mode client
```

## VLAN de gestion

```
vlan 150
interface vlan 150
ip add 10.10.150.9 255.255.255.0
ip default-gateway 10.10.150.254
```

## Encapsulation dot1Q (router‑on‑the‑stick)

```
interface fa 0/0
no shut

interface fa 0/0.101
encapsulation dot1Q 101
ip address 10.10.101.254 255.255.255.0
ip helper-address 10.10.200.1

interface fa 0/0.102
encapsulation dot1Q 102
ip address 10.10.102.254 255.255.255.0
ip helper-address 10.10.200.1
```

## Mise en UP des sous‑interfaces

```
interface range fa0/0.101,fa0/0.102,fa0/0.103,fa0/0.104,fa0/0.160,fa0/0.200,fa0/0.150
no shut
```

---

# 16. Routes statiques

## Route connectée

```
ip route 192.168.2.0 255.255.255.0 Serial 0/1
ip route 192.168.0.0 255.255.255.0 Serial 0/2
```

## Route récursive

```
ip route 192.168.2.0 255.255.255.0 192.168.1.1
ip route 192.168.0.0 255.255.255.0 192.168.1.254
```

## Route entièrement spécifiée

```
ip route 192.168.2.0 255.255.255.0 se0/1 192.168.1.1
ip route 192.168.0.0 255.255.255.0 se0/2 192.168.1.254
```

## Route par défaut

```
ip route 0.0.0.0 0.0.0.0 serial 0/1
ip route 0.0.0.0 0.0.0.0 192.168.1.254 10
```

## Route récapitulative

```
ip route 192.168.2.0 255.255.254.0 192.168.1.1
ip route 192.168.2.0 255.255.254.0 serial 0/1 10
```

## Route flottante

```
ip route 192.168.2.0 255.255.255.0 se0/1 10
ip route 192.168.2.0 255.255.255.0 192.168.1.1 10
```

Une route flottante sert de **backup**.  
Distance administrative plus élevée → utilisée seulement si la route principale tombe.

