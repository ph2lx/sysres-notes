Rendez-vous sur centre d’administration Exchange Online : https://admin.exchange.microsoft.com  

Compte : admin@local.onmicrosoft.com  

Dans le volet gauche, cliquer sur migration  
Et ajouter un lot de migration, donner lui un nom  

![ExchMig](images/migr1.png) 

Dans la 2eme fenêtre sélectionner : Migration à distance (Migration hybride (Remote Move Migration))  

![ExchMig](images/migr2.png) 

Sélectionner dans le menu déroulant : CLIENT_O365  

![ExchMig](images/migr3.png) 

Sélectionner un csv contenant une liste d’utilisateur ou rechercher le/les manuellement.  

![ExchMig](images/migr4.png) 

Sélectionner le domaine cible, ici : local.mal.onmicrosoft.com  

![ExchMig](images/migr5.png) 

Puis planifier la migration et enregistrer :  

![ExchMig](images/migr6.png) 

Vérifier les statues en rafraichissant  

![ExchMig](images/migr7.png) 
![ExchMig](images/migr8.png) 

Finaliser la migration, à ce moment dans Centre d'administration Exchange on premise, le type de boite aux lettres passera d’utilisateur  

![ExchMig](images/migr9.png) 

A  

![ExchMig](images/migr10.png) 

La boite est migrée !  
