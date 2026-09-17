# Aide-mémoire Git

Commandes usuelles pour la gestion du dépôt `sysres-notes` (PowerShell).

## Table des matières

- [Cloner un projet](#cloner-un-projet)
- [Se connecter à un dépôt existant](#se-connecter-à-un-dépôt-existant)
- [Créer un nouveau dossier suivi par Git](#créer-un-nouveau-dossier-suivi-par-git)
- [Ajouter un fichier](#ajouter-un-fichier)
- [Mettre à jour tous les fichiers modifiés](#mettre-à-jour-tous-les-fichiers-modifiés)
- [Supprimer un fichier ou un dossier](#supprimer-un-fichier-ou-un-dossier)
- [Renommer un fichier ou un dossier](#renommer-un-fichier-ou-un-dossier)
- [Créer une arborescence de sous-dossiers](#créer-une-arborescence-de-sous-dossiers)
- [Déplacer un fichier](#déplacer-un-fichier)

---

## Cloner un projet

```powershell
git clone https://github.com/ph2lx/sysres-notes.git C:\Users\moi\sysres-notes
```

## Se connecter à un dépôt existant

```powershell
cd C:\Users\moi\sysres-notes
git pull
```

## Créer un nouveau dossier suivi par Git

> Git ne suit pas les dossiers vides : il faut toujours y placer un fichier (par convention `.gitkeep`) pour qu'il soit pris en compte.

```powershell
New-Item -ItemType Directory -Path Linux
New-Item -ItemType File -Path Linux\.gitkeep

git add Linux\.gitkeep
git status
git commit -m "Ajout du sous-dossier Linux avec .gitkeep"
git push
```

## Ajouter un fichier

```powershell
New-Item -ItemType File -Path Windows-ActiveDirectory-Exploitation\GPO\truc-machin

git add Windows-ActiveDirectory-Exploitation\GPO\truc-machin
git status
git commit -m "Ajout de truc-machin"
git push
```

## Mettre à jour tous les fichiers modifiés

`git add -u` ajoute uniquement les fichiers déjà suivis et modifiés (ou supprimés) — pas les nouveaux fichiers.

```powershell
git add -u
git commit -m "Mise à jour de tout"
git push
```

> Pour inclure aussi les nouveaux fichiers non suivis, utiliser `git add .` à la place de `git add -u`.

## Supprimer un fichier ou un dossier

```powershell
git rm .\Httpd
git commit -m "Suppression du dossier"
git push
```

## Renommer un fichier ou un dossier

```powershell
git mv .\Commande-Git .\Commande-Git.md
git commit -m "Renommage du fichier"
git push
```

> Si le fichier a déjà été renommé manuellement (hors Git) avant de lancer `git mv`, celui-ci échoue avec `fatal: bad source`. Dans ce cas, valider directement le renommage déjà effectué :
>
> ```powershell
> git add -A
> git commit -m "Renommage du fichier"
> git push
> ```

## Créer une arborescence de sous-dossiers

Exemple : structure `GPO` avec plusieurs sous-thèmes.

**1. Créer les sous-dossiers**

```powershell
New-Item -ItemType Directory -Path Windows-ActiveDirectory-Exploitation\GPO
New-Item -ItemType Directory -Path Windows-ActiveDirectory-Exploitation\GPO\Concepts
New-Item -ItemType Directory -Path Windows-ActiveDirectory-Exploitation\GPO\Securite
New-Item -ItemType Directory -Path Windows-ActiveDirectory-Exploitation\GPO\Administration
New-Item -ItemType Directory -Path Windows-ActiveDirectory-Exploitation\GPO\Scripts
New-Item -ItemType Directory -Path Windows-ActiveDirectory-Exploitation\GPO\Astuces
```

**2. Ajouter un fichier dans chaque sous-dossier** (sinon Git les ignore)

```powershell
New-Item -ItemType File -Path Windows-ActiveDirectory-Exploitation\GPO\Concepts\.gitkeep
New-Item -ItemType File -Path Windows-ActiveDirectory-Exploitation\GPO\Securite\.gitkeep
New-Item -ItemType File -Path Windows-ActiveDirectory-Exploitation\GPO\Administration\.gitkeep
New-Item -ItemType File -Path Windows-ActiveDirectory-Exploitation\GPO\Scripts\.gitkeep
New-Item -ItemType File -Path Windows-ActiveDirectory-Exploitation\GPO\Astuces\.gitkeep
```

**3. Vérifier ce que Git voit** — les `.gitkeep` doivent apparaître en *untracked* ou *modified* :

```powershell
git status
```

**4. Ajouter, committer et pousser**

```powershell
git add Windows-ActiveDirectory-Exploitation/GPO
git commit -m "Ajout des sous-dossiers GPO avec .gitkeep"
git push
```

> Si Git indique qu'il n'y a pas de branche amont (*upstream*), le définir une fois pour toutes :
>
> ```powershell
> git push --set-upstream origin Windows-ActiveDirectory-Exploitation/Comptes_Acces
> ```

**Autre exemple, plus court** (un seul dossier) :

```powershell
New-Item -ItemType Directory -Path Windows-ActiveDirectory-Exploitation\Comptes_et_Acces
New-Item -ItemType File -Path Windows-ActiveDirectory-Exploitation\Comptes_et_Acces\.gitkeep

git status
git add Windows-ActiveDirectory-Exploitation/Comptes_et_Acces
git commit -m "Ajout du dossier Comptes_et_Acces avec .gitkeep"
git push
```

## Déplacer un fichier

Déplacer un `.txt` (ou tout autre fichier) via l'explorateur Windows ou la commande `mv`, puis se placer à la racine du dépôt et valider le déplacement :

```powershell
git add .
git commit -m "Déplacement des fichiers"
git push
```

## Ajouter une image

Créer un dossier images  
puis insérer dans le texte à l'endroit voulu
![Proxmox](images/proxmox1.png)


