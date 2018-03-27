## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Créer une base de données SQL vide

Une base de données SQL Azure est créée avec un ensemble défini de [ressources de calcul et de stockage](../articles/sql-database/sql-database-service-tiers.md). La base de données est créée dans un [groupe de ressources Azure](../articles/azure-resource-manager/resource-group-overview.md) et dans un [serveur logique Azure SQL Database](../articles/sql-database/sql-database-features.md). 

Pour créer une base de données SQL vide, suivez la procédure suivante. 

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.

2. Dans la page **Nouveau**, sélectionnez **Bases de données**, puis **Créer** sous **SQL Database** dans **cette même** page.

   ![créer une base de données vide](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

3. Remplissez le formulaire de base de données SQL avec les informations suivantes, comme indiqué dans l’illustration précédente :   

   | Paramètre       | Valeur suggérée | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nom de la base de données** | mySampleDatabase | Pour les noms de base de données valides, consultez [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données). | 
   | **Abonnement** | Votre abonnement  | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources** | myResourceGroup | Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Sélectionner une source** | Base de données vide | Indique qu’une base de données vide doit être créée. |

4. Cliquez sur **Serveur** pour créer et configurer un serveur pour votre nouvelle base de données. Remplissez le **formulaire de nouveau serveur** avec les informations suivantes : 

   | Paramètre       | Valeur suggérée | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nom du serveur** | Nom globalement unique | Pour les noms de serveur valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). | 
   | **Connexion d’administrateur du serveur** | Nom valide | Pour les noms de connexion valides, consultez [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données).|
   | **Mot de passe** | Mot de passe valide | Votre mot de passe doit comporter au moins 8 caractères et contenir des caractères appartenant à trois des catégories suivantes : caractères en majuscules, caractères en minuscules, chiffres et caractères non alphanumériques. |
   | **Lieu** | Emplacement valide | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |

   ![create database-server](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

5. Cliquez sur **Sélectionner**.

6. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service, le nombre de DTU et la quantité de stockage. Explorez les options concernant la quantité de DTU et de stockage disponible pour chaque niveau de service. 

7. Pour ce tutoriel, sélectionnez le niveau de service **Standard** et utilisez le curseur pour sélectionner **100 DTU (S3)** et **400** Go de stockage.

   ![create database-s1](../articles/sql-database/media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. Acceptez les conditions d’utilisation de la préversion pour pouvoir utiliser l’option **Stockage de composants additionnels**. 

   > [!IMPORTANT]
   > \* Les tailles de stockage supérieures à la quantité de stockage inclue sont en version préliminaire et des coûts supplémentaires s’appliquent. Pour en savoir plus, voir [Tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\* Au niveau Premium, plus de 1 To de stockage est actuellement disponible dans les régions suivantes : Centre du Canada, Est du Canada, France-Centre, Centre de l’Allemagne, Japon de l’Est, Corée Centre, Sud du centre des États-Unis, Sud-Est asiatique, Est des États-Unis 2, Ouest des États-Unis, Gouvernement des États-Unis - Virginie et Europe de l’Ouest. Consultez [Limitations actuelles P11-P15](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. Après avoir sélectionné le niveau du serveur, le nombre de DTU et la quantité de stockage, cliquez sur **Appliquer**.  

10. Sélectionnez un **classement** pour la base de données vide (pour ce didacticiel, utilisez la valeur par défaut). Pour en savoir plus sur les classements, voir [Classements](https://docs.microsoft.com/sql/t-sql/statements/collations)

11. Cliquez sur **Créer** pour approvisionner la base de données. L’approvisionnement prend environ une minute et demie. 

12. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.
    
     ![notification](../articles/sql-database/media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Créer une règle de pare-feu au niveau du serveur

Le service SQL Database crée un pare-feu au niveau du serveur qui empêche les applications et les outils externes de se connecter au serveur ou à toute base de données sur le serveur, sauf si une règle de pare-feu est créée pour ouvrir le pare-feu à des adresses IP spécifiques. Suivez ces étapes pour créer une [règle de pare-feu au niveau du serveur de base de données SQL](../articles/sql-database/sql-database-firewall-configure.md) pour l’adresse IP de votre client afin de permettre la connectivité externe via le pare-feu de base de données SQL pour votre adresse IP uniquement. 

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

1. Une fois le déploiement terminé, cliquez sur **Bases de données SQL** dans le menu de gauche, puis cliquez sur **mySampleDatabase** sur la page **Bases de données SQL**. La page de présentation de votre base de données s’ouvre, affiche le nom de serveur complet (tel que **mynewserver20170824.database.windows.net**) et fournit des options pour poursuivre la configuration. 

2. Copiez le nom complet du serveur pour vous connecter à votre serveur et à ses bases de données dans les guides de démarrage rapide suivants. 

   ![nom du serveur](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 

3. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre. 

   ![règle de pare-feu de serveur](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Dans la barre d’outils, cliquez sur **Ajouter une adresse IP cliente** afin d’ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu. Une règle de pare-feu peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

5. Cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle et ouvre le port 1433 sur le serveur logique.

6. Cliquez sur **OK**, puis fermez la page **Paramètres de pare-feu**.

Vous pouvez maintenant vous connecter au serveur SQL Database et à ses bases de données à l’aide de SQL Server Management Studio ou de tout autre outil de votre choix à partir de cette adresse IP à l’aide du compte Administrateur de serveur créé au préalable.


> [!IMPORTANT]
> Par défaut, l’accès via le pare-feu SQL Database est activé pour tous les services Azure. Cliquez sur **ÉTEINT** sur cette page pour le désactiver pour tous les services Azure.

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

Obtenez le nom de serveur complet de votre serveur Azure SQL Database dans le portail Azure. Utilisez le nom de serveur complet pour vous connecter à votre serveur avec SQL Server Management Studio.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Dans le volet **Essentials** de la page du portail Azure pour votre base de données, recherchez et copiez le **nom du serveur**.

   ![informations de connexion](../articles/sql-database/media/sql-database-get-started-portal/server-name.png)
