1. Connectez-vous au [portail Azure][Azure portal].
2. Dans le volet de navigation gauche du portail, cliquez sur  **+ Créer une ressource**, puis sur **Enterprise Integration** et afin sur **Relais**.
3. Dans la boîte de dialogue **Créer un espace de noms** , entrez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible.
4. Dans le champ **Abonnement** , sélectionnez un abonnement Azure dans lequel créer l’espace de noms.
5. Dans le champ **[Groupe de ressources](../articles/azure-resource-manager/resource-group-portal.md)**, choisissez un groupe de ressources existant dans lequel l’espace de noms sera utilisé, ou créez-en un nouveau.      
6. Dans **Emplacement**, sélectionnez le pays ou la région où votre espace de noms doit être hébergé.
   
    ![Créer un espace de noms][create-namespace]
7. Cliquez sur **Create**. Le système crée l'espace de noms de service et l'active. Après quelques minutes, le système approvisionne des ressources pour votre compte.

### <a name="obtain-the-management-credentials"></a>Obtenir les informations d’identification de gestion

1. Cliquez sur **Toutes les ressources**, puis sur le nom de l’espace de noms que vous venez de créer.
2. Dans la fenêtre Espace de noms du relais, cliquez sur **Stratégies d’accès partagé**.
3. Dans la fenêtre **Stratégies d’accès partagé**, cliquez sur **RootManageSharedAccessKey**.
   
    ![informations de connexion][connection-info]
4. Sur l’écran **Stratégie : RootManageSharedAccessKey**, cliquez sur le bouton **Copier** situé à côté de **Clé primaire de la chaîne de connexion**, pour copier la chaîne de connexion dans le presse-papiers pour une utilisation ultérieure. Copiez cette valeur dans le Bloc-notes ou un autre emplacement temporaire.
   
    ![connection-string][connection-string]

5. Répétez l’étape précédente, en copiant et collant la valeur de **Clé primaire** dans un emplacement temporaire pour l’utiliser ultérieurement.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
