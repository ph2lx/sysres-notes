START

\# Tuto complet : SPF / DKIM / DMARC — Score maximal + Dépasser la limite SPF



\## 1. SPF — Sender Policy Framework

SPF indique quels serveurs sont autorisés à envoyer des emails pour ton domaine.  

Il protège contre le spoofing, l’usurpation d’identité et les envois frauduleux.



\### Fonctionnement

1\. Le serveur distant lit le domaine du MAIL FROM.

2\. Il récupère le TXT SPF dans le DNS.

3\. Il vérifie si l’IP de l’expéditeur est autorisée.

4\. Résultat : pass / softfail / fail / permerror.



\### Exemple de bon SPF

v=spf1 ip4:1.2.3.4 include:spf.protection.outlook.com include:sendgrid.net -all



\### Bonnes pratiques

\- Toujours finir par \*\*-all\*\* (fail strict)

\- Ne jamais utiliser +all

\- Ne pas dépasser \*\*10 DNS lookups\*\*

\- Tester avec MXToolbox ou DMARCIAN



\---



\## 2. DKIM — DomainKeys Identified Mail

DKIM signe cryptographiquement les emails pour prouver qu’ils n’ont pas été modifiés.



\### Fonctionnement

1\. Le serveur signe certains headers + le corps du message.

2\. La signature est ajoutée dans DKIM-Signature.

3\. Le serveur distant récupère la clé publique DKIM dans le DNS.

4\. Il vérifie la signature.



\### Exemple de clé DKIM DNS

selector.\_domainkey.domaine.com IN TXT "v=DKIM1; k=rsa; p=MIIBIjANBgkqh..."



\### Bonnes pratiques

\- Utiliser des clés \*\*2048 bits\*\*

\- Un selector unique par système

\- Tester avec MXToolbox ou DMARCIAN



\---



\## 3. DMARC — Domain-based Message Authentication, Reporting \& Conformance

DMARC définit la politique à appliquer si SPF/DKIM échouent et impose l’alignement des domaines.



\### Alignement DMARC

Le domaine du \*\*From:\*\* doit être aligné avec :

\- SPF (MAIL FROM)

\- ou DKIM (d=)



\### Exemple de DMARC parfait

\_dmarc.domaine.com IN TXT "v=DMARC1; p=reject; rua=mailto:dmarc@domaine.com; ruf=mailto:dmarc@domaine.com; fo=1; adkim=s; aspf=s"



\---



\## 4. Dépasser la limite SPF (10 DNS lookups)



\### Problème

SPF limite à \*\*10 lookups DNS\*\* :

include:, a, mx, ptr, exists, redirect=



Si tu dépasses → SPF permerror → fiabilité réduite.



\### Solution 1 : SPF Flattening (la meilleure)

Remplacer les includes par les IP réelles des services.



Avant :

v=spf1 include:sendgrid.net include:mailjet.com include:spf.protection.outlook.com -all



Après flattening :

v=spf1 ip4:167.89.0.0/17 ip4:185.28.196.0/22 ip4:40.92.0.0/14 ip4:40.107.0.0/16 -all



Avantages :

\- 0 lookup DNS

\- SPF ultra rapide

\- Fiabilité maximale



\### Solution 2 : Services de flattening automatique

\- DMARCIAN SPF Flattening

\- Postmark SPF Builder

\- PowerSPF (Global Cyber Alliance)



\### Solution 3 : Répartir les includes sur plusieurs sous-domaines

v=spf1 include:\_spf1.domaine.com include:\_spf2.domaine.com -all



\_spf1.domaine.com :

v=spf1 include:sendgrid.net include:mailjet.com -all



\_spf2.domaine.com :

v=spf1 include:spf.protection.outlook.com include:servers.mandrillapp.com -all



\### Solution 4 : Supprimer les includes inutiles

Audit SPF → suppression des services obsolètes.



\---



\## 5. Score de fiabilité maximal — Checklist



\### SPF

\- -all strict

\- Moins de 10 lookups

\- Flattening si nécessaire



\### DKIM

\- Clé 2048 bits

\- Signature sur tous les mails sortants

\- Alignement strict



\### DMARC

\- p=reject

\- adkim=s / aspf=s

\- Rapports activés



\### DNS

\- PTR correct

\- MX cohérents

\- A stable



\### TLS

\- Certificat valide

\- TLS 1.2 ou 1.3

\- Ciphers modernes



\### Réputation

\- IP propre

\- Domaine ancien

\- Pas de spam sortant

\- Pas de bounce massif



\---



\## 6. Tests finaux

Outils :

\- mail-tester.com

\- mxtoolbox.com

\- dmarcian.com

\- talosintelligence.com



Résultat attendu :

\- SPF strict + flattening

\- DKIM 2048 bits

\- DMARC reject + alignement strict

\- TLS moderne

\- IP propre



→ Score de fiabilité maximal  

→ Passage Gmail/Outlook garanti  

→ Anti-spoofing total  

→ Délivrabilité optimisée



END



