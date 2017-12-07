1. Connectez-vous au [Portail Azure].
2. Cliquez sur **+NOUVEAU** > **Web + Mobile** > **Application mobile**, puis indiquez le nom de votre serveur principal Mobile App.
3. Pour **Groupe de ressources**, sélectionnez un groupe de ressources existant ou créez-en un (en utilisant le même nom que votre application). 
   
    Sélectionnez un autre plan App Service ou créez-en un. Pour plus d’informations sur les plans App Service et sur la création d’un plan à un autre niveau de tarification et à l’emplacement souhaité, voir [Présentation détaillée des plans Azure App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
4. Pour le **plan App Service**, le plan par défaut (du [niveau Standard](https://azure.microsoft.com/pricing/details/app-service/)) est sélectionné. Vous pouvez sélectionner un autre plan ou en [créer un](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). Les paramètres du plan App Service déterminent [l’emplacement, les fonctionnalités, les coûts et les ressources de calcul](https://azure.microsoft.com/pricing/details/app-service/) associés à votre application. 
   
    Après avoir choisi le plan, cliquez sur **Créer**. Cette action crée le serveur principal Mobile App. 
5. Dans le panneau **Paramètres** du nouveau serveur principal Mobile App, cliquez sur **Démarrage rapide** > Votre plateforme d’application cliente > **Se connecter à une base de données**. 
   
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
6. Dans le panneau **Ajouter une connexion de données**, cliquez sur **Base de données SQL** > **Créer une base de données**, tapez le **nom** de la base de données, choisissez un niveau de tarification, puis cliquez sur **Serveur**.  Vous pouvez réutiliser cette nouvelle base de données. Si vous disposez déjà d’une base de données au même emplacement, vous pouvez choisir à la place **Utiliser une base de données existante**. Il est déconseillé d’utiliser une base de données dans un autre emplacement en raison de la latence plus importante et des frais de bande passante supplémentaires.
   
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
7. Dans le panneau **Nouveau serveur**, tapez un nom unique dans le champ **Nom du serveur**, indiquez l’identifiant de connexion et le mot de passe, cochez la case **Autoriser des services Azure à accéder au serveur**, puis cliquez sur **OK**. Cette action crée la base de données.
8. Dans le panneau **Ajouter une connexion de données**, cliquez sur **Chaîne de connexion**, tapez l’identifiant de connexion et le mot de passe pour votre base de données, puis cliquez sur**OK**. Patientez quelques minutes jusqu’au déploiement de la base de données, puis continuez.

<!-- URLs. -->
[Portail Azure]: https://portal.azure.com/
