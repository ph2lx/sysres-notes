Search-Mailbox -Identity "user@domain.com" -SearchDumpsterOnly `
 -TargetMailbox "psimon@local.fr" -TargetFolder "Recup"

Temps de retention de la boite cible
Get-Mailbox -Identity "sblanc@local.fr" | fl RetainDeletedItemsFor

Temps de retention d’une BDD
Get-MailboxDatabase -Identity "NOM_DE_TA_DB" | fl DeletedItemRetention

Temps de retention de ttes BDD
Get-MailboxDatabase | ft Name, DeletedItemRetention
