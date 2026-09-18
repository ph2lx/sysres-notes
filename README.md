# sysres-notes (EN CONSTRUCTION...IL ETAIT TEMPS !)
Portfolio technique et notes génériques (Linux, Windows, réseau, scripts, CI/CD). Aucun élément ne provient d’environnements réels.

# 📑 Table des matières — Portfolio Technique

---

# 🪟 1. Windows / Active Directory / Exploitation

## 1.1 Comptes & Accès
- Comptes gMSA
- Déverrouiller un compte AD
- Création de boîte partagée
- Accès à un dossier
- Admin local Windows (GLD / GUI / Core / PowerShell)

## 1.2 Fichiers / Stockage / Robocopy
- Noms trop longs (Robocopy)
- Chemins trop longs
- Accès lecteur M:\
- Vider les credentials Windows en CMD
- Restauration de fichier

## 1.3 GPO
- Appliquer une GPO
- Vérifier les GPO

## 1.4 Services / Processus / Système
- Services
- Processus
- Système / Infos
- Recherche
- Rechercher log shutdown / reboot

## 1.5 Comptes / Gestion AD
- Gestion compte
- Création / modification
- Attributs spécifiques
- Créer un user
- Modifier un user
- Ajouter dans un groupe ou GLD

## 1.6 Sécurité / Filtrage
- Forcepoint
- IronPort
- Certificats (Windows)
- Certificats Java
- CSR / CSR avec SAN
- Certificat IronPort remplacé

## 1.7 Divers Windows
- Installation fonctionnalités facultatives
- Pour accéder à un serveur quand ça passe pas
- WEB2
- Localstock.local.fr
- Installation Office

---

# 🐧 2. Linux / RHEL / Debian / Exploitation

## 2.1 Comptes / Accès / Domaine
- Jonction AD manuelle
- Réintégration domaine Linux
- Ajout de droits AD sur Linux
- Récupération root / désactivation SSSD / PAM
- Admin local Linux (adminlocvisudo.sh)

## 2.2 Système / Diagnostic
- Test de port
- Récupérer de la place (Hardcore)
- Resize partition
- Subscription RedHat
- Maj Red Hat (2 manières)

## 2.3 Systemd
- Configuration
- Options
- Utilisations courantes

## 2.4 Podman / Conteneurs
- Notes Podman

## 2.5 Swap
- Création partition swap
- Fichier swap

## 2.6 Vim
- Navigation
- Édition
- Recherche
- Buffers / fenêtres
- Commandes utiles
- Modes Vim
- Ressources pour progresser

## 2.7 Programmation / Automatisation
- Programmer une tâche

## 2.8 Administration Linux — La Bible
- Commandes essentielles
- Utilisateurs & groupes
- Permissions, ACL, SELinux
- Stockage : partitions, LVM, FS
- Systemd : services, journaux
- Boot, GRUB & récupération
- Gestion des paquets (DNF/YUM)
- Variables d’environnement & scripting
- Points critiques
- Reset mot de passe root (RHEL / Debian / Arch)

## 2.9 Projets
- lvm-à-Chaud.md
- NginX.md
- Httpd.md
- Vmware to Proxmox
- Container contrôlé par systemd
  

---

# 🌐 3. Réseau / Sécurité / SOC

## 3.1 Diagnostic réseau
- Savoir quel service écoute quel port
- Test de port
- Outils réseau

## 3.2 LDAPS / Active Directory
- Contexte & architecture
- Ordre de diagnostic
- Diagnostic côté AD
- Tableau de diagnostic rapide
- LDAPS sans SRV = fausse sécurité
- Plan de remise en ordre (court / long terme)
- Causes typiques post-migration

## 3.3 Sécurité / SOC
- Alerte SOC
- GDA / Grangle / Grand Angle
- Accès NEEVA

## 3.4 OpenSSL / Certificats Linux
- OpenSSL RHEL
- Baisser la sécurité sur RHEL

---

# 📧 4. Messagerie / Exchange / O365 / Ironport

## 4.1 SMTP / SMTPS
- Dépannage SMTPS
- Envoi de mail via Telnet

## 4.2 Outlook / BAL
- Message d’absence
- Modifier alias
- Réparer une BAL
- Récupérer mails supprimés (sans Veeam)

## 4.3 Exchange On-Premise / Online
- Migration Exchange → O365
- Migration on-premise → Exchange Online
- Commandes Exchange EMS
- Vérifier état base EDB
- Dirty shutdown
- Exportation compte Exchange (mail, SharePoint, dossier public)

## 4.4 Groupes / Listes
- Groupe de distribution dynamique
- Mise à jour liste d’adresses
- Contact X500
- Recréation de mail (OWA inaccessible)

## 4.5 Tickets / Problèmes
- Envoi / réception
- Mails qui disparaissent
- Lenteur
- Troubles divers

## 4.6 Cisco Ironport/ESA  


---

# 🖥️ 5. Virtualisation / VMware / OPCON / Oracle

## 5.1 VMware vSphere
- Snapshot
- Lister VMs zombies

## 5.2 OPCON
- Installation agent
- Création d’utilisateur
- Base de données
- Dump / clone / scripts

## 5.3 Oracle
- Connexion user Oracle
- Redémarrer
- Dump
- Anonymisation

---

# ☁️ 6. Azure / Cloud

## 6.1 Coûts / Billing
- Cost Management
- Coûts & consommation

## 6.2 Ressources critiques
- Machines virtuelles
- Réseau
- Stockage
- App Services / API
- Bases de données

## 6.3 Monitoring / Sécurité
- Service Health
- Entra ID (Azure AD)
- Identity Protection
- Azure AD Connect
- Defender for Cloud

## 6.4 Routine quotidienne
- Routine FR
- Routine EN (Azure Daily Routine)

## 6.5 Résumé rapide (noms anglais exacts)

---

# ⚙️ 7. CI/CD / Automatisation / Git

## 7.1 Git
- Interagir avec Git

## 7.2 Pipelines
- Pipelines génériques GitLab CI
- Workflows GitHub Actions

## 7.3 Automatisation
- Suppression VM via pipeline

## 7.4 Ansible
- (Section à compléter)
