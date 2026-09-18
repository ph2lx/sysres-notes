# Procédure d'exploitation — LVM / systemd

Optimisation partitionnement LVM • Migration `/tmp` → tmpfs • Nettoyage systemd

**Système :** S2164 · **OS :** RHEL 8 · **Date :** 03/06/2026

---

## Table des matières

1. [Contexte initial](#1-contexte-initial)
2. [Objectifs](#2-objectifs)
3. [Passage en mode texte (multi-user.target)](#3-passage-en-mode-texte-multi-usertarget)
4. [Nettoyage des unités systemd](#4-nettoyage-des-unités-systemd)
5. [Suppression du LV /tmp](#5-suppression-du-lv-tmp)
6. [Migration des données hors de /dev/sda3](#6-migration-des-données-hors-de-devsda3)
7. [Suppression de /dev/sda3 et extension de /dev/sda2](#7-suppression-de-devsda3-et-extension-de-devsda2)
8. [Extension du LV /var](#8-extension-du-lv-var)
9. [Recréation de /tmp et /var/tmp](#9-recréation-de-tmp-et-vartmp)
10. [Retour en mode graphique](#10-retour-en-mode-graphique)
11. [Vérifications finales](#11-vérifications-finales)
12. [Résultat final](#12-résultat-final)
13. [Notes techniques](#13-notes-techniques)

---

## 1. Contexte initial

La VM `srvXX` dispose d'un disque virtuel de 100 Go. Le Volume Group (VG) `rhel` repose sur deux Physical Volumes :

- `/dev/sda2` — 41 Go (PV principal)
- `/dev/sda3` — 8 Go (PV secondaire)

L'espace libre du disque (~51 Go) se situe après `/dev/sda3`, ce qui rend l'extension directe de `/dev/sda2` impossible avec un schéma de partition MBR.

De plus, le VG est entièrement alloué (`Free PE = 0`), empêchant toute création ou extension de LV sans libération préalable.

> ⚠️ L'extension vSphere du disque est indisponible suite à un incident. La procédure ci-dessous résout le problème entièrement au niveau OS, sans intervention hyperviseur.

**État initial du partitionnement (`lsblk`) :**

```
sda                    100G
├─sda1                 512M  /boot
├─sda2                 41G   PV LVM (VG rhel)
│ rhel-root             15G  /
│ rhel-swap              4G  [SWAP]
│ rhel-var              10G  /var              ← 79% utilisé
│ rhel-home              5G  /home             ← 2% utilisé
│ rhel-opt                5G  /opt
│ rhel-var_log            2G  /var/log
│ rhel-var_log_audit      1G  /var/log/audit
│ rhel-var_tmp            2G  /var/tmp
│ rhel-tmp                5G  /tmp              ← quasi vide
└─sda3                 8G    PV LVM (VG rhel)
  └─rhel-var            10G  /var (straddling sda2+sda3)
```

## 2. Objectifs

- Augmenter `/var` (10 Go → 30 Go)
- Supprimer le LV `/tmp` (inutile) et migrer `/tmp` vers tmpfs
- Consolider le VG sur un seul PV (`/dev/sda2` étendu)
- Supprimer la partition `/dev/sda3` devenue obsolète
- Nettoyer les unités systemd liées à `/tmp` et `/var/tmp`

## 3. Passage en mode texte (multi-user.target)

GDM, Xwayland et les services graphiques maintiennent des handles ouverts sur `/tmp`, empêchant la suppression du LV. Il faut basculer en mode texte pour les libérer.

```bash
systemctl isolate multi-user.target
```

Vérifier que le LV `/tmp` est bien libéré :

```bash
lvdisplay /dev/rhel/tmp
# Attendu : # open = 0
```

> ℹ️ Si `# open` reste à 1 malgré l'isolation, c'est généralement `/var/tmp` remonté automatiquement par systemd qui maintient le handle. Voir étape 4.

## 4. Nettoyage des unités systemd

### 4.1 Analyse des montages actifs

```bash
mount | grep tmp
systemctl status tmp.mount
systemctl status var-tmp.mount
```

### 4.2 Démontage de `/var/tmp`

```bash
umount /var/tmp
```

### 4.3 Désactivation des unités automatiques

```bash
systemctl disable tmp.mount
systemctl disable var-tmp.mount
systemctl daemon-reload
```

### 4.4 Vérification via device-mapper (diagnostic)

Si `lvchange -an` échoue encore, vérifier l'état du device-mapper :

```bash
dmsetup info -c | grep rhel-tmp
dmsetup table rhel-tmp
```

En dernier recours (forcer) :

```bash
dmsetup remove -f rhel-tmp
```

> ⚠️ `dmsetup remove -f` peut échouer si un processus kernel garde encore le device actif. Dans ce cas, vérifier que `/var/tmp` est bien démonté (c'est la cause la plus fréquente du blocage résiduel).

## 5. Suppression du LV `/tmp`

```bash
# Désactiver le LV
lvchange -an /dev/rhel/tmp

# Supprimer le LV (libère 5 Go dans le VG)
lvremove /dev/rhel/tmp

# Vérifier l'espace libéré
vgdisplay rhel
# Attendu : Free PE / Size ≈ 9 GiB
```

## 6. Migration des données hors de `/dev/sda3`

`pvmove` déplace les extents LVM de `/dev/sda3` vers `/dev/sda2`. Cette opération est **non destructive** : les données sont copiées bit à bit et validées avant suppression de la source.

> ℹ️ `pvmove` nécessite au minimum autant d'espace libre dans le VG que la taille des extents à déplacer. C'est pourquoi la suppression du LV `/tmp` (5 Go) + `/home` (4 Go) a été nécessaire préalablement pour atteindre ~9 Go libres.

```bash
pvmove /dev/sda3
# Sortie attendue : /dev/sda3: Moved: 100.0%
```

Retirer `/dev/sda3` du VG :

```bash
vgreduce rhel /dev/sda3
```

## 7. Suppression de `/dev/sda3` et extension de `/dev/sda2`

### 7.1 Supprimer la partition `/dev/sda3`

```bash
parted /dev/sda
(parted) rm 3
(parted) quit
```

### 7.2 Étendre `/dev/sda2` jusqu'à la fin du disque

L'espace bloqué précédemment par `sda3` est maintenant libre. On peut étendre `sda2` :

```bash
parted /dev/sda
(parted) resizepart 2 100%
(parted) quit
```

### 7.3 Redimensionner le PV LVM

```bash
pvresize /dev/sda2
# Sortie attendue : Physical volume "/dev/sda2" changed

# Vérifier : ~50 Go libres dans le VG
vgdisplay rhel
```

## 8. Extension du LV `/var`

```bash
# Agrandir le LV de +20 Go
lvextend -L +20G /dev/rhel/var

# Étendre le système de fichiers XFS (en ligne, sans reboot)
xfs_growfs /var

# Vérifier
df -h /var
# Attendu : /var = 30 Go
```

> ℹ️ `xfs_growfs` opère en ligne sur un FS monté. Les données existantes ne sont jamais déplacées : seul l'espace disponible est augmenté en fin de volume.

## 9. Recréation de `/tmp` et `/var/tmp`

### 9.1 Monter `/tmp` en tmpfs

Ajouter dans `/etc/fstab` :

```
tmpfs /tmp tmpfs mode=1777,nosuid,nodev 0 0
```

Monter immédiatement et vérifier :

```bash
mount /tmp
mount | grep /tmp
# Attendu : tmpfs on /tmp type tmpfs (...)
```

### 9.2 Recréer `/var/tmp` comme dossier standard

Supprimer dans `/etc/fstab` la ligne (`/dev/mapper/rhel-var_tmp...`) si présente, puis :

```bash
mkdir -p /var/tmp
chmod 1777 /var/tmp

# Vérifier qu'aucune unité ne recrée le montage
systemctl status var-tmp.mount
# Attendu : Unit var-tmp.mount could not be found.
```

## 10. Retour en mode graphique

```bash
systemctl start gdm
# ou
systemctl isolate graphical.target
```

## 11. Vérifications finales

```bash
# Structure des volumes
lsblk -f
lvs
vgs

# Espace disque
df -h

# Montages actifs
mount | grep tmp
```

## 12. Résultat final

| Volume           | Action                           | Résultat                               |
| ---------------- | -------------------------------- | -------------------------------------- |
| `/dev/rhel/tmp`  | LV supprimé                      | ✔ 5 Go libérés dans le VG              |
| `/dev/rhel/home` | LV supprimé temporairement*      | ✔ 4 Go libérés (pvmove)                |
| `/dev/sda3`      | pvmove → vgreduce → rm partition | ✔ Données migrées, partition supprimée |
| `/dev/sda2`      | Étendu à ~91 Go                  | ✔ pvresize effectué                    |
| VG `rhel`        | Consolidé sur sda2 uniquement    | ✔ ~40 Go disponibles post-opération    |
| `/var`           | lvextend +20G + xfs_growfs       | ✔ 10 Go → 30 Go, en ligne              |
| `/tmp`           | Migré vers tmpfs                 | ✔ Rapide, sécurisé, recommandé RHEL    |
| `/var/tmp`       | Dossier standard sur `/var`      | ✔ Unité systemd supprimée              |
| systemd          | Unités parasites désactivées     | ✔ tmp.mount, var-tmp.mount nettoyés    |

\* Le LV `/home` a été supprimé pour libérer les extents nécessaires à `pvmove`. Les données utilisateur de `/home` étaient absentes (LV quasi vide — 2% utilisé). À recréer si nécessaire :

```bash
lvcreate -L XG -n home rhel && mkfs.xfs /dev/rhel/home
```

## 13. Notes techniques

### Intégrité des données

Aucune donnée applicative n'a été perdue ou corrompue :

- `pvmove` effectue une copie bit à bit vérifiée avant de libérer la source
- `xfs_growfs` n'altère jamais les données existantes (extension en fin de volume)
- `/tmp` ne contient jamais de données persistantes par définition
- `/home` était vide (4,9 Go libres sur 5 Go)

### Pourquoi `pvmove` a nécessité de libérer de l'espace

LVM implémente `pvmove` via un LV miroir temporaire (`pvmove0`). Ce miroir nécessite autant d'extents libres que les extents à déplacer. Avec un VG plein à 100%, `pvmove` échoue systématiquement — d'où la suppression préalable des LVs inutilisés.

### Pourquoi `/tmp` restait `open=1` après isolation

systemd génère automatiquement des units de montage depuis `/etc/fstab` (fstab-generator). Même après `systemctl isolate multi-user.target`, le montage `/var/tmp` (toujours actif via son propre LV) maintenait un handle kernel sur le device-mapper de `/tmp` via la hiérarchie systemd-tmpfiles. La solution : démonter `/var/tmp`, désactiver `var-tmp.mount`, puis recharger systemd.

---

*Procédure interne — Rédigé le 03/06/2026*
