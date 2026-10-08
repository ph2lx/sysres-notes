############################################################
🟥 CONTACTS X500 / ERREURS IMCEAEX
############################################################
## Contact X500  
Info de contact  
Get-MailContact -Identity "franck.martin@externe-partenaire.fr" | fl Name,EmailAddresses  

## Ajouter une adresse X500 au contact  
Message d’erreur :  
Serveur de génération : SRV15.local.intranet  
IMCEAEX-_o=CLIENT_ou=Exchange+20Administrative+20Group+20+28FYDIBOHF23SPDLT+29_cn=Recipients_cn=0a7099394a614acf970a60629e6015c0-Martin+20Franck@local.fr  
Remote Server returned '550 5.1.1 RESOLVER.ADR.ExRecipNotFound; not found'  

En-têtes de message d'origine :  
Received: from SRV16.local.intranet (10.20.1.160) by SRV15.local.intranet (10.20.1.158)  
with Microsoft SMTP Server (TLS) id 15.0.1497.42; Fri, 21 Mar 2025 16:36:46 +0100  
Received: from SRV16.local.intranet ([::1]) by SRV16.local.intranet ([fe80::5f9:2f1:b360:198f%16])  
with mapi id 15.00.1497.044; Fri, 21 Mar 2025 16:36:46 +0100  
Content-Type: application/ms-tnef; name="winmail.dat"  
Content-Transfer-Encoding: binary  
From: Simon Pierre <psimon@local.fr>  
To: Martin Franck <franck.martin@externe-partenaire.fr>  
Subject: test dsi  
Thread-Topic: test dsi  
Thread-Index: AduadwuaBEwiUIdXQ8utG1FTXM1aMQ==  
Date: Fri, 21 Mar 2025 16:36:46 +0100  
Message-ID: <dab29793436a472192e1d16d40627859@SRV16.local.intranet>  
Accept-Language: fr-FR, en-US  
Content-Language: fr-FR  
X-MS-Has-Attach: yes  
X-MS-TNEF-Correlator: <dab29793436a472192e1d16d40627859@SRV16.local.intranet>  
MIME-Version: 1.0  
X-MS-Exchange-Transport-FromEntityHeader: Hosted  
X-Originating-IP: [10.20.1.169]  
Return-Path: psimon@local.fr  

L’ancienne adresse Exchange de l’utilisateur supprimé était probablement stockée sous la forme X500.  
Il faut l'ajouter au contact de messagerie pour éviter l’erreur IMCEAEX :

1. Récupérer l'ancienne adresse X500  
   - Dans le message d'erreur, repère la partie après `IMCEAEX-`.  
   - Remplace tous les `+20` par des espaces.  
   - Ajoute `X500:` devant.  

X500:/o=CLIENT/ou=Exchange Administrative Group (FYDIBOHF23SPDLT)/cn=Recipients/cn=0a7099394a614acf970a60629e6015c0-Martin Franck  

Set-MailContact -Identity "franck.martin@externe-partenaire.fr" -EmailAddresses @{add="X500:/o=CLIENT/ou=Exchange Administrative Group (FYDIBOHF23SPDLT)/cn=Recipients/cn=0a7099394a614acf970a60629e6015c0-Martin Franck"}  
