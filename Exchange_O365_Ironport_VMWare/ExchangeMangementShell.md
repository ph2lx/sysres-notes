############################################################
🟥 ALIAS POWERSHELL
############################################################

#Pour créé un alias d’une commande :
Set-Alias NomAlias NomCommande
Set-Alias GetMre Get-ManagementRoleEntry

Pour la rappeler
Get -Alias


############################################################
🟥 DIAGNOSTIC EXCHANGE / LOGS / EDB
############################################################

Vous vous demandez combien de fichiers journaux sont générés par serveur à chaque minute ? Découvrez-le rapidement en tapant :

Get-MailboxDatabase -Server <Mailbox Server Name> | ?{ %{$_.DatabaseCopies | ?{$_.ReplayLagTime -ne [TimeSpan]::Zero -And $_.HostServerName -eq $env:ComputerName} } } | %{ $count = 0; $MinT = [DateTime]::MaxValue; $MaxT = [DateTime]::MinValue; Get-ChildItem -Path $_.LogFolderPath -Filter "*????.log" | %{ $count = $count + 1; if($_.LastWriteTime -gt $MaxT){ $MaxT = $_.LastWriteTime}; if($_.LastWriteTime -lt $MinT){ $MinT= $_.LastWriteTime} }; ($count / ($MaxT.Subtract($MinT)).TotalMinutes) } | Measure-Object -Min -Max -Ave

Réparer 

Réparer une BAL
New-MailboxRepairRequest -Mailbox "dialoguesocial@local.fr" -CorruptionType ProvisionedFolder,SearchFolder,AggregateCounts,FolderView

Verifier etat Base EDB
eseutil /mh "chemin\base.edb"

Réparer la base (dirty shutdown)
eseutil /r E00

et en dernier recour
eseutil /p base.edb


############################################################
🟥 CONNEXION EXCHANGE ONLINE
############################################################

Messagerie (toutes sortes)
#en Powershell only
Import-Module ExchangeOnlineManagement

Connect-ExchangeOnline -UserPrincipalName ton_utilisateur@local.fr -ShowProgress $true

#Si besoin
Disconnect-ExchangeOnline -Confirm:$false


############################################################
🟥 GROUPES DYNAMIQUES (DDG)
############################################################

#Mise en variable du filtre destinataire exchange distri dynamique
$filter = "((Alias -ne `$null) -and (ObjectClass -eq 'user') -and (-not(CustomAttribute1 -like '7*')) -and ((CustomAttribute10 -like '1000258 MTP PETIT SITEA') -or (CustomAttribute10 -like '1000000 MTP SITEA') -or (CustomAttribute10 -like '1000000 MTP HOTEL DU DEPARTEMENT')) -and (ExchangeUserAccountControl -eq 'None') -and (-not(Name -like 'SystemMailbox{*')) -and (-not(Name -like 'CAS_{*')) -and (-not(RecipientTypeDetailsValue -eq 'MailboxPlan')) -and (-not(RecipientTypeDetailsValue -eq 'DiscoveryMailbox')) -and (-not(RecipientTypeDetailsValue -eq 'ArbitrationMailbox')))"

#Appliqué le filtre
Set-DynamicDistributionGroup -Identity "1-AgentsSITEA@local.fr" -RecipientFilter $filter

#Créer groupe de distribution Dynamique :
New-DynamicDistributionGroup `
    -Name "GePEx - COMPTABLE" `
    -Alias "GePEx-COMPTABLE" `
    -OrganizationalUnit "local.intranet/Groupes de Distribution/Groupes de distribution Dynamiques" `
    -RecipientFilter "(MemberOfGroup -eq 'local.intranet/Groupes de Sécurité - Répertoires partagés/GG A - Applications/GePEx/GG A GePEx - COMPTABLE')"

#Verifier qui est dans le groupe dynamique (quel users ?)
Get-Recipient -RecipientPreviewFilter (Get-DynamicDistributionGroup "1- Agents SITEA").RecipientFilter

#Et vers un txt
$DDG = "1-toutlemondedynamique@local.fr"
$Filter = (Get-DynamicDistributionGroup $DDG).RecipientFilter
$OutputFile = "C:\Users\adm_psimon\Desktop\membres_DDG.txt"

Get-Recipient -RecipientPreviewFilter $Filter |
Select-Object Name, PrimarySmtpAddress |
Out-File -FilePath $OutputFile -Encoding UTF8

Ou CSV
$Group = "1-toutlemondedynamique@local.fr"
$OutputFile = "C:\Users\adm_psimon\Desktop\membres_toutlemondeHorsASetFoyer.csv"

Get-DistributionGroupMember -Identity $Group |
Select-Object Name, PrimarySmtpAddress |
Export-Csv -Path $OutputFile -NoTypeInformation -Encoding UTF8

#Et si + de 1000 users 
$DDG = "1-toutlemondedynamique"
$OutputFile = "C:\Users\adm_psimon\Desktop\membres_toutlemondeHorsASetFoyer.csv"

Get-Recipient -RecipientPreviewFilter (Get-DynamicDistributionGroup $DDG).RecipientFilter -ResultSize Unlimited |
Select-Object Name, PrimarySmtpAddress |
Export-Csv -Path $OutputFile -NoTypeInformation -Encoding UTF8


############################################################
🟥 GROUPES DE DISTRIBUTION (DG)
############################################################

#Qui se trouve dans le groupe de distri
Get-DistributionGroupMember -Identity "1-coxinelutilisateurs@local.intranet" | Get-Mailbox

Get-DistributionGroup -Identity "1-coxinelutilisateurs@local.intranet" | Format-List RequireSenderAuthenticationEnabled, AcceptMessagesOnlyFrom, AcceptMessagesOnlyFromSendersOrMembers

Sotie CSV :
$Group = "1-Toutlemonde-sansassfam"
$OutputFile = "C:\Users\adm_psimon\Desktop\membres_toutlemonde.csv"

Get-DistributionGroupMember -Identity $Group |
Select-Object Name, PrimarySmtpAddress |
Export-Csv -Path $OutputFile -NoTypeInformation -Encoding UTF8


############################################################
🟥 TRANSPORT RULES
############################################################

#pour bloquer un expediteur
New-TransportRule -Name "BloquerExpéditeur" -From "*@info.axcsz.schule" -DeleteMessage $true

#Rejeter les message ne provenant pas de @local.fr
New-TransportRule -Name "Restriction coxinel-arret" -SentTo "coxinel-arret@local.fr" -FromAddressContainsWords "@local.fr" -RejectMessageEnhancedStatusCode "5.7.1" -RejectMessageReasonText "Seuls les emails internes sont autorisés."


############################################################
🟥 SUPPRESSION / GESTION DES BAL
############################################################

#Supprimer une messagerie sans son compte AD
Disable-Mailbox -Identity "nom_utilisateur"
Disable-Mailbox -Identity "pmichel"

#Connaitre la bdd de la boite
Get-Mailbox -Identity "jdupont@local.fr" | Format-List Database

#lister les boite deconnecté
Get-MailboxStatistics -Database "NomDeLaBaseDeDonnées" | Where-Object {$_.DisconnectDate -ne $null} | Select-Object DisplayName, MailboxGuid, DisconnectDate

#Supprimer la Boîte aux Lettres Déconnectée
Remove-Mailbox -Database "NomDeLaBaseDeDonnées" -StoreMailboxIdentity "GUIDDeLaBoîteAuxLettres" -MailboxState Disabled

#info user 
Get-Mailbox -Identity "noreply@local.fr" | Format-List
Get-Mailbox -Identity "nepasrepondreGDA" | Format-List
Get-Mailbox -Identity "" | Format-List


############################################################
🟥 PERMISSIONS / SEND-AS / FULL ACCESS
############################################################

# Ajout delegation a un email.
Add-MailboxPermission -Identity "1accueilvillex@local.fr" -User "xxx" -AccessRights FullAccess, SendAs, ExternalAccount, DeleteItem, ReadPermission, ChangePermission, ChangeOwner

Pour le « Send-As », il faut
Get-ADUser -Filter {EmailAddress -eq "fdef.upe.villey.grands@local.fr"} | Select-Object DistinguishedName

Identity             User                 Deny  Inherited
--------             ----                 ----  ---------
local.intranet/U... LOCAL\ereynaud     False False

Add-ADPermission -Identity "CN=FDEF UPE VilleY grands,OU=Boites aux Lettres,OU=Users particuliers,OU=Users LOCAL,DC=local,DC=intranet" -User "apetit@local.fr" -ExtendedRights "Send As"

Add-ADPermission -Identity "fdef.upe.villey.grands@local.fr" -User "jdupont@local.fr" -ExtendedRights "Send-As"

Add-MailboxPermission -Identity "1accueilvillex@local.fr" -User "jdupont@local.fr" -AccessRights FullAccess


############################################################
🟥 MESSAGE TRACKING
############################################################

#afficher les transferts
Get-Mailbox -Identity "jdupont@local.fr" | Select-Object ForwardingSMTPAddress, DeliverToMailboxAndForward

#savoir si une boite a recu le mail d'une autre boite
$today = Get-Date
Get-MessageTrackingLog -Recipients “santeautravail@local.fr" -Start $today.Date -End $today.AddDays(1).Date | Where-Object {$_.Sender -eq "noreply@clientweb.fr"}

#Pour 1 ans :
$today = Get-Date
$startDate = $today.AddDays(-180)
Get-MessageTrackingLog -Recipients "santeautravail@local.fr" -Start $startDate -End $today | Where-Object {$_.Sender -eq "noreply@clientweb.fr"}


############################################################
🟥 CERTIFICATS EXCHANGE
############################################################

Import-Module ImportExcel
Install-Module PowerShellGet -Force -AllowClobber
Install-Module -Name ExchangeOnlineManagement -Force

# Liste les certificats installés
Get-ExchangeCertificate | Format-List Thumbprint, Services, Subject, NotAfter

# Voir les détails d'un certificat spécifique
Get-ExchangeCertificate -Thumbprint <Thumbprint_du_certificat>

# Obtenir le thumbprint du certificat
$certThumbprint = "123456789ABCDEF123456789ABCDEF1234567890"

# Créer le connecteur d'envoi
New-SendConnector -Name "Outbound to Office 365" -Usage Internet -AddressSpaces "smtp:*.outlook.com;1" -TlsCertificateName "<I>C=<Pays>, S=<Etat>, L=<Ville>, O=<Organisme>, CN=<Nom commun>"

# Assigner le certificat au connecteur d'envoi
Set-SendConnector -Identity "Outbound to Office 365" -TlsCertificateName $certThumbprint


############################################################
🟥 GAL / OAB / LISTES D’ADRESSES
############################################################

Get-DistributionGroup -Identity "1-coxinelutilisateurs@local.fr"

#Qui est dans la liste
Get-MailboxPermission -Identity "Direction des Systèmes d'Information" | Select-Object User, AccessRights, Deny, IsInherited, InheritanceType | Out-File -FilePath "C:\Users\adm_psimon\Desktop\dsi_details.txt"

#Liste les destinataires du groupe dynamique
Get-Recipient -RecipientPreviewFilter (Get-DynamicDistributionGroup -Identity "2-DGA_AG_DSI-Cadres@local.fr").RecipientFilter | Select-Object DisplayName, PrimarySmtpAddress

Get-GlobalAddressList | Format-Table Name
Get-OfflineAddressBook | Format-Table Name

Liste d'adresses en mode hors connexion par défaut (Ex2013)

Liste principal :
Update-GlobalAddressList -Identity "Liste d'adresses globale par défaut"

Update-OfflineAddressBook -Identity "Liste d'adresses en mode hors connexion par défaut (Ex2013)"
Get-OfflineAddressBook | Update-OfflineAddressBook

Savoir si l’adresse est caché volontairement
Get-Mailbox jdupont@local.fr | Select HiddenFromAddressListsEnabled

Et pour la rendre visible
Set-Mailbox jdupont@local.fr -HiddenFromAddressListsEnabled $false

Mettre a jours une liste d’adresse
Liste Lambda : Update-AddressList -Identity "Pole politiques insertion"
COMMENTAIRES : Connecté à SRV14.local.intranet.

Verifier si la case Masqué dans le canet d’adresse est coché :
Get-Mailbox "ekowalski" | fl HiddenFromAddressListsEnabled

#Fréquence de maj du cache du carnet d'adresse globale hors ligne
Get-OfflineAddressBook | Format-List Identity,Schedule

#Et pour le mettre a jours
Set-OfflineAddressBook -Identity "NomDeVotreOAB" -Schedule "Intervalle"


############################################################
🟥 SANTÉ EXCHANGE
############################################################

#Taux de message de @local.fr vers @local.Fr pendant 1 mois
Get-TransportServer | Get-MessageTrackingLog -Start (Get-Date).AddMonths(-1) -End (Get-Date) -EventId "SEND" | Where-Object {$_.Recipients -match "@local.fr"} | Measure-Object

#Santé du Srv
Get-MailboxDatabaseCopyStatus -server  "ServerName" | Format-Table Name, Status, CopyQueueLength, ReplayQueueLength, ContentIndexState

#Santé de la BDD
Get-MailboxDatabaseCopyStatus -Identity  "BDDName" | Format-Table Name, Status, CopyQueueLength, ReplayQueueLength, ContentIndexState

#Etat d’un Srv
Get-HealthReport -Server "NomDuServeurExchange"

#Exécuter un test de connectivité Exchange
Test-ServiceHealth



