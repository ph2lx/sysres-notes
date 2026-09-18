# Envoi de mail via Telnet

Cette procédure permet de tester l’envoi d’un mail directement via Telnet sur un serveur SMTP.

---

## 1. Connexion au serveur SMTP

Port utilisé dans l’exemple : **465** (SMTPS)

```bash
telnet xxxip 465
```

---

## 2. Dialogue SMTP

Une fois connecté, saisir les commandes suivantes :

```text
EHLO test
MAIL FROM:<test@local.fr>
RCPT TO:<psimon@local.fr>
DATA
Subject: Test

Ceci est un test.
.
QUIT
```

### Explications rapides :
- **EHLO test** : annonce du client SMTP  
- **MAIL FROM:** adresse expéditrice  
- **RCPT TO:** destinataire  
- **DATA** : début du contenu du mail  
- Ligne vide après le sujet → séparation entête / corps  
- **.** : fin du message  
- **QUIT** : fermeture de la session  

---

## 3. Illustration (capture d’écran)

```
![Telnet-mail](images/telmail.png)
```

*(À placer dans un fichier `.md` avec une image dans `images/telmail.png`)*

---

# Notes importantes

- Le port **465** est normalement utilisé pour SMTPS (TLS implicite).  
  → Telnet ne supporte pas TLS, donc cette commande ne fonctionnera **que si le serveur accepte une connexion non chiffrée sur 465**, ce qui est rare.  
- Pour un test SMTP standard, on utilise généralement :  
  - **25** (SMTP)  
  - **587** (Submission, STARTTLS)  

Exemple pour port 25 :

```bash
telnet xxxip 25
```

---

# Version alternative (si STARTTLS requis)

Telnet ne supporte pas STARTTLS → utiliser `openssl s_client` :

```bash
openssl s_client -connect xxxip:587 -starttls smtp
```

Puis :

```text
EHLO test
MAIL FROM:<test@local.fr>
RCPT TO:<psimon@local.fr>
DATA
Subject: Test

Ceci est un test.
.
QUIT
```

---

![Telnet-mail](images/telmail.png)
