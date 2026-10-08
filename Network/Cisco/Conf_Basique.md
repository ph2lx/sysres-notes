# Sommaire

1. [Mode admin](#1-mode-admin)
2. [Mode configuration](#2-mode-configuration)
3. [Changer le nom du routeur](#3-changer-le-nom-du-routeur)
4. [Attribution d’adresse IP](#4-attribution-dadresse-ip)
5. [Sécurisation du routeur](#5-sécurisation-du-routeur)
6. [Sauvegarde](#6-sauvegarde)
7. [Commandes de diagnostic](#7-commandes-de-diagnostic)
8. [Installation d’un firmware depuis la ROM et le réseau](#8-installation-dun-firmware-depuis-la-rom-et-le-réseau)
9. [Réinitialisation / Reset mot de passe perdu](#9-réinitialisation--reset-mot-de-passe-perdu)
10. [Accès TELNET / SSH](#10-accès-telnet--ssh)
11. [Update firmware](#11-update-firmware)
12. [Boot réseau + Bannière + DHCP](#12-boot-réseau--bannière--dhcp)
13. [Serveur DHCP](#13-serveur-dhcp)
14. [Relay DHCP](#14-relay-dhcp)
15. [Routage inter-VLAN (router-on-the-stick)](#15-routage-inter-vlan-router-on-the-stick)

---

# 1. Mode admin

```
enable
```

---

# 2. Mode configuration

```
configure terminal
```

---

# 3. Changer le nom du routeur

```
hostname tartempion
```

---

# 4. Attribution d’adresse IP

```
interface fastethernet0/0
ip address 192.168.0.1 255.255.255.0
no shutdown
```

---

# 5. Sécurisation du routeur

## Mode utilisateur (console)

```
conf t
line console 0
password ciscoforever
login
```

## Mode administrateur

```
enable secret ciscoforever
```

## Chiffrement des mots de passe

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
write memory
```

## RAM → TFTP

```
copy running-config tftp
```

## RAM → FTP

```
ip ftp username cisco
ip ftp password cisco
copy running-config ftp
```

---

# 7. Commandes de diagnostic

```
show running-config
show vlan
show vtp status
show interfaces trunk
show vtp password
```

---

# 8. Installation d’un firmware depuis la ROM et le réseau

## Depuis ROMMON

```
Ctrl+C pendant le boot
rommon1 > tftpdnld
IP_ADDRESS=192.168.0.254
IP_SUBNET_MASK=255.255.255.0
DEFAULT_GATEWAY=192.168.0.1
TFTP_SERVER=192.168.0.1
TFTP_FILE=c2800nm-advipservicesk9-mz.151-4.M4.bin
rommon7 > tftpdnld
```

---

# 9. Réinitialisation / Reset mot de passe perdu

## Effacement de la configuration

```
erase startup-config
```

## Reset mot de passe

```
Ctrl+C
rommon1 > confreg 0x2142
rommon2 > reset
```

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

# 12. Boot réseau + Bannière + DHCP

## Boot réseau

```
boot system tftp c2800nm-advipservicesk9-mz.151-4.M4.bin 192.168.0.1
config-register 0x210F
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

## Interfaces

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

# 15. Routage inter-VLAN (router-on-the-stick)

## VLANs

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

## Sub‑interfaces (dot1Q)

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

