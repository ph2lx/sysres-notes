\# OpenSSL – Gestion des certificats (p7b, crt, chain, key, pfx)



> ⚠️ Pour OpenSSL : utiliser la \*\*SRV13\*\*  

> ⚠️ Attention aux environnements \*\*Windows Server 2012\*\* (IIS 8) → nécessite PBE-SHA1-3DES



\---



\# 1. Extraire un `.crt` depuis un `.p7b`



```bash

openssl pkcs7 -in Z:\\CERTIF\_SSL\\odee.local.fr\\odee.local.fr.p7b -inform DER -out Z:\\CERTIF\_SSL\\odee.local.fr\\odee.local.fr.crt -print\_certs

```



\---



\# 2. Créer un fichier `.chain`



Créer un fichier texte contenant \*\*tous les certificats sauf le certificat final\*\*, dans \*\*l’ordre de la chaîne\*\* :



\- Root CA  

\- Intermediate CA 1  

\- Intermediate CA 2  

\- …  



⚠️ \*\*Uniquement les blocs :\*\*  

```

\-----BEGIN CERTIFICATE-----

\-----END CERTIFICATE-----

```



\---



\# 3. Vérification de la chaîne



```bash

openssl verify -CAfile Z:\\CERTIF\_SSL\\odee.local.fr\\odee.local.fr.chain Z:\\CERTIF\_SSL\\odee.local.fr\\odee.local.fr.crt

```



Exemple :



```text

Z:\\CERTIF\_SSL\\odee.local.fr\\odee.local.fr.crt: OK

```



\---



\# 4. Générer un `.key` depuis un `.pfx` ou `.p12`



```bash

openssl pkcs12 -in Z:\\CERTIF\_SSL\\odee.local.fr\\ssl\_eyJpZCI6Njk2OTk0OCwidHlwZSI6IlNTTCJ9.p12 -out Z:\\CERTIF\_SSL\\odee.local.fr\\odee.local.fr.key -nodes -nocerts

```



➡️ Entrer le mot de passe du fichier `.p12`



\---



\# 5. Générer un `.pfx` compatible IIS 2012



```bash

openssl pkcs12 -export -certpbe PBE-SHA1-3DES -keypbe PBE-SHA1-3DES -nomac -inkey hote.local.fr.key -in hote.local.fr.crt -out hote.local.fr.pfx

```



\---



\# 6. Méthode Windows (alternative)



1\. Ouvrir le `.p7b`  

2\. Exporter \*\*tous les certificats en Base64\*\*  

3\. Construire le `.chain` manuellement  

4\. Vérifier :



```bash

openssl verify -CAfile Z:\\CERTIF\_SSL\\laboratoire.local.fr\\laboratoire.local.fr.chain Z:\\CERTIF\_SSL\\laboratoire.local.fr\\laboratoire.local.fr.cer

```



\---



\# 7. Générer `.key` depuis `.pfx` / `.p12` (autre exemple)



```bash

openssl pkcs12 -in Z:\\CERTIF\_SSL\\laboratoire.local.fr\\ssl\_eyJpZCI6Njk2OTkzNCwidHlwZSI6IlNTTCJ9.p12 -out Z:\\CERTIF\_SSL\\laboratoire.local.fr\\laboratoire.local.fr.key -nodes -nocerts

```



➡️ Entrer le mot de passe du `.p12`



\---



\# 8. Retirer la passphrase d’un `.key` (pour IIS)



```bash

openssl rsa -in Z:\\CERTIF\_SSL\\laboratoire.local.fr\\laboratoire.local.fr.key -out Z:\\CERTIF\_SSL\\laboratoire.local.fr\\laboratoire.local.fr.nopass.key

```



➡️ Saisir la passphrase du `.key` si demandée.



\---



\# 9. Générer un `.pfx` sans mot de passe (IIS)



```bash

openssl pkcs12 -export \\

&#x20; -in Z:\\CERTIF\_SSL\\laboratoire.local.fr\\laboratoire.local.fr.cer \\

&#x20; -certfile Z:\\CERTIF\_SSL\\laboratoire.local.fr\\laboratoire.local.fr.chain \\

&#x20; -inkey Z:\\CERTIF\_SSL\\laboratoire.local.fr\\laboratoire.local.fr.nopass.key \\

&#x20; -out Z:\\CERTIF\_SSL\\laboratoire.local.fr\\laboratoire.local.fr.pfx

```



\---



\# 10. `.pfx` version 2012 (IIS 8)



```bash

openssl pkcs12 -export -certpbe PBE-SHA1-3DES -keypbe PBE-SHA1-3DES -nomac \\

&#x20; -inkey hote.local.fr.key \\

&#x20; -in hote.local.fr.crt \\

&#x20; -out hote.local.fr.pfx

```



\---



\# 11. Exemple wildcard



```bash

openssl pkcs12 -export -certpbe PBE-SHA1-3DES -keypbe PBE-SHA1-3DES -nomac \\

&#x20; -inkey Z:\\CERTIF\_SSL\\.rec.local.fr\\rec.local.fr.nopass.key \\

&#x20; -in Z:\\CERTIF\_SSL\\.rec.local.fr\\rec.local.fr.cer \\

&#x20; -out Z:\\CERTIF\_SSL\\.rec.local.fr\\\_.rec.local.fr.local.fr\_v2012.pfx

```



\---



\# 12. Extraire un `.crt` depuis `.p7b` (méthode générique)



```bash

openssl pkcs7 -print\_certs -in certificat.p7b -out certificat.crt

```



\---



\# 13. Créer un `.pfx` depuis `.crt` + `.key`



```bash

openssl pkcs12 -export -in certificat.crt -inkey privateKey.key -out output.pfx

```



➡️ Ne rien mettre comme mot de passe pour IIS.



\---



\# 14. Exemple wildcard local.fr



```bash

openssl pkcs12 -export \\

&#x20; -in Z:\\CERTIF\_SSL\\wildcard.local.fr\\wildcard.local.fr.cer \\

&#x20; -inkey Z:\\CERTIF\_SSL\\wildcard.local.fr\\wildcard.local.fr.decrypted.key \\

&#x20; -out Z:\\CERTIF\_SSL\\wildcard.local.fr\\wildcard.local.fr.pfx

```



\---



\# 15. Autodiscover



```bash

openssl pkcs12 -export \\

&#x20; -in Z:\\CERTIF\_SSL\\autodiscover\\autodiscover.local.fr.cer \\

&#x20; -inkey Z:\\CERTIF\_SSL\\autodiscover\\autodiscover.local.fr.key \\

&#x20; -out Z:\\CERTIF\_SSL\\autodiscover\\autodiscover.local.fr\_CRYPTEDPASS.pfx

```



Version 3DES :



```bash

openssl pkcs12 -export -certpbe PBE-SHA1-3DES -keypbe PBE-SHA1-3DES -nomac \\

&#x20; -inkey Z:\\CERTIF\_SSL\\autodiscover\\autodiscover.local.fr.key \\

&#x20; -in Z:\\CERTIF\_SSL\\autodiscover\\autodiscover.local.fr.cer \\

&#x20; -out Z:\\CERTIF\_SSL\\autodiscover\\autodiscover.local.fr\_3DES\_PASS.pfx

```



\---



\# 16. Vérification d’un certificat



```bash

openssl verify -CAfile Z:\\CERTIF\_SSL\\ged-asef\\ged-asef.local.fr.chain Z:\\CERTIF\_SSL\\ged-asef\\ged-asef.local.fr.cer

```



\---



\# 17. Vérifier que `.key` et `.crt` correspondent



```bash

openssl rsa -noout -modulus -in votre\_fichier.key | openssl md5

openssl x509 -noout -modulus -in votre\_fichier.crt | openssl md5

```



➡️ Les deux hash doivent être identiques.



\---



\# 18. Depuis Windows (certmgr)



⚠️ Si le certificat est sur un \*\*partage réseau\*\*, Windows peut dire \*certificat invalide\*.



\### Solution :



1\. Ouvrir :  

&#x20;  - `certmgr.msc` (certificats utilisateur)  

&#x20;  - `certlm.msc` (certificats machine locale)



2\. Double-cliquer sur le `.pfx`, `.p12`, `.p7b`, `.pem`, `.crt`



3\. Aller dans \*\*Personnel → Certificats\*\*



4\. Menu \*\*Toutes les tâches → Exporter\*\*



\---





