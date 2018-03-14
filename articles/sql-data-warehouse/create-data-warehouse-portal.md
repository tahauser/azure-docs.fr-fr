---
title: "Démarrage rapide : créer un entrepôt de données SQL Azure - Portail Azure | Microsoft Docs"
description: "Pour Azure SQL Data Warehouse, créez un serveur SQL, une règle de pare-feu de niveau serveur et un entrepôt de données dans le portail Azure. Ensuite, interrogez-le."
keywords: "didacticiel sql data warehouse, créer un entrepôt de données SQL"
services: sql-database
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: Active
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: quickstart
ms.date: 11/20/2017
ms.author: barbkess
ms.openlocfilehash: a620da9dbe9823b9876fa80dc0200aa91fbf9920
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Démarrage rapide : créer et interroger un entrepôt de données SQL Azure dans le portail Azure

Créez et interrogez rapidement un entrepôt SQL Azure dans le portail Azure.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer

Téléchargez et installez la dernière version de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-data-warehouse"></a>Créer un entrepôt de données

Un entrepôt SQL Azure est créé avec un ensemble défini de [ressources de calcul](performance-tiers.md). La base de données est créée dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) et dans un [serveur logique Azure SQL](../sql-database/sql-database-features.md). 

Suivez ces étapes pour créer un entrepôt de données SQL qui contient l’exemple de base de données AdventureWorksDW. 

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.

2. Sélectionnez **Bases de données** dans la page **Nouveau** et sélectionnez **SQL Data Warehouse** sous **Sélection** dans la page **Nouveau**.

    ![créer un entrepôt de données vide](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. Remplissez le formulaire SQL Data Warehouse avec les informations suivantes :   

    | Paramètre | Valeur suggérée | Description | 
    | ------- | --------------- | ----------- | 
    | **Nom de la base de données** | mySampleDataWarehouse | Pour les noms de base de données valides, consultez [Database Identifiers](/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données). Notez qu’un entrepôt de données est un type de base de données.| 
    | **Abonnement** | Votre abonnement  | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
    | **Groupe de ressources** | myResourceGroup | Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
    | **Sélectionner une source** | Exemple | Indique qu’il faut charger un exemple de base de données. Notez qu’un entrepôt de données est un type de base de données. |
    | **Sélectionner un exemple** | AdventureWorksDW | Indique qu’il faut charger l’exemple de base de données AdventureWorksDW.  |

    ![créer un entrepôt de données](media/create-data-warehouse-portal/select-sample.png)

4. Cliquez sur **Serveur** pour créer et configurer un serveur pour votre nouvelle base de données. Remplissez le **formulaire de nouveau serveur** avec les informations suivantes : 

    | Paramètre | Valeur suggérée | Description | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | **Nom du serveur** | Nom globalement unique | Pour les noms de serveur valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). | 
    | **Connexion d’administrateur du serveur** | Nom valide | Pour les noms de connexion valides, consultez [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données).|
    | **Mot de passe** | Mot de passe valide | Votre mot de passe doit comporter au moins 8 caractères et contenir des caractères appartenant à trois des catégories suivantes : majuscules, minuscules, chiffres et caractères non alphanumériques. |
    | **Lieu** | Emplacement valide | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |

    ![créer un serveur de base de données](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. Cliquez sur **Sélectionner**.

6. Cliquez sur **Niveau de performances** pour spécifier la configuration des performances de l’entrepôt de données.

7. Pour ce didacticiel, sélectionnez le niveau de performances **Optimisé pour l’élasticité**. Par défaut, le curseur est défini sur **DW400**.  Déplacez-le vers le haut et le bas pour voir son fonctionnement. 

    ![configurer les performances](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. Cliquez sur **Appliquer**.

9. Maintenant que vous avez rempli le formulaire SQL Database, cliquez sur **Créer** pour approvisionner la base de données. L’approvisionnement prend quelques minutes. 

    ![cliquer sur créer](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

10. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.
    
     ![notification](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Créer une règle de pare-feu au niveau du serveur

Le service SQL Data Warehouse crée un pare-feu au niveau du serveur qui empêche les applications et outils externes de se connecter au serveur ou à toute base de données sur le serveur. Pour activer la connectivité, vous pouvez ajouter des règles de pare-feu qui activent la connectivité pour des adresses IP spécifiques.  Suivez ces étapes pour créer une [règle de pare-feu au niveau du serveur](../sql-database/sql-database-firewall-configure.md) pour l’adresse IP de votre client. 

> [!NOTE]
> SQL Data Warehouse communique sur le port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut être bloqué par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

1. Une fois le déploiement terminé, cliquez sur **Bases de données SQL** dans le menu de gauche, puis cliquez sur **mySampleDatabase** sur la page **Bases de données SQL**. La page de vue d’ensemble de votre base de données s’ouvre. Elle affiche le nom de serveur complet (par exemple **mynewserver-20171113.database.windows.net**) et fournit des options pour poursuivre la configuration. 

2. Copiez le nom complet du serveur pour vous connecter à votre serveur et à ses bases de données dans les guides de démarrage rapide suivants. Pour ouvrir les paramètres du serveur, cliquez sur le nom du serveur.

   ![rechercher le nom du serveur](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. Pour ouvrir les paramètres du serveur, 
4. cliquez sur le nom du serveur.

   ![paramètres du serveur](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. Cliquez sur **Afficher les paramètres de pare-feu**. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre. 

   ![règle de pare-feu de serveur](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. Pour ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu, cliquez sur **Ajouter une adresse IP cliente** dans la barre d’outils. Une règle de pare-feu peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

5. Cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle et ouvre le port 1433 sur le serveur logique.

6. Cliquez sur **OK**, puis fermez la page **Paramètres de pare-feu**.

Vous pouvez maintenant vous connecter au serveur SQL et à ses entrepôts de données à l’aide de cette adresse IP. La connexion fonctionne à partir de SQL Server Management Studio ou d’un autre outil de votre choix. Quand vous vous connectez, utilisez le compte ServerAdmin que vous avez créé précédemment.  

> [!IMPORTANT]
> Par défaut, l’accès via le pare-feu SQL Database est activé pour tous les services Azure. Cliquez sur **OFF** dans cette page, puis sur **Enregistrer** pour désactiver le pare-feu pour tous les services Azure.

## <a name="get-the-fully-qualified-server-name"></a>Obtenir le nom complet du serveur

Obtenez le nom complet de votre serveur SQL dans le portail Azure. Vous utiliserez le nom complet du serveur par la suite pour vous connecter au serveur.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Dans le volet **Essentials** de la page du portail Azure pour votre base de données, recherchez et copiez le **nom du serveur**. Dans cet exemple, le nom complet est mynewserver-20171113.database.windows.net. 

    ![informations de connexion](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>Se connecter au serveur comme administrateur du serveur

Cette section utilise [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS) pour établir une connexion à votre serveur Azure SQL.

1. Ouvrez SQL Server Management Studio.

2. Dans la fenêtre **Se connecter au serveur**, entrez les valeurs suivantes :

   | Paramètre       | Valeur suggérée | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Type de serveur | Moteur de base de données | Cette valeur est obligatoire |
   | Nom du serveur | Nom complet du serveur | Voici un exemple : **mynewserver-20171113.database.windows.net**. |
   | Authentification | l’authentification SQL Server | L’authentification SQL est le seul type d’authentification configuré dans ce didacticiel. |
   | Connexion | Compte d’administrateur de serveur | Il s’agit du compte que vous avez spécifié lorsque vous avez créé le serveur. |
   | Mot de passe | Mot de passe de votre compte d’administrateur de serveur | Il s’agit du mot de passe que vous avez spécifié lorsque vous avez créé le serveur. |

    ![connect to server](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. Cliquez sur **Connecter**. La fenêtre Explorateur d’objets s’ouvre dans SSMS. 

5. Dans l’Explorateur d’objets, développez **Bases de données**. Ensuite, développez **mySampleDatabase** pour afficher les objets dans votre nouvelle base de données.

    ![objets de base de données](media/create-data-warehouse-portal/connected.png) 

## <a name="run-some-queries"></a>Exécuter des requêtes

SQL Data Warehouse utilise T-SQL comme langage de requête. Pour ouvrir une fenêtre de requête et exécuter des requêtes T-SQL, effectuez les étapes suivantes :

1. Cliquez avec le bouton droit sur **mySampleDataWarehouse** et sélectionnez **Nouvelle requête**.  Une nouvelle fenêtre de requête s’ouvre.
2. Dans la fenêtre de requête, entrez la commande suivante pour afficher la liste des bases de données.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Cliquez sur **Exécuter**.  Les résultats de requête montrent deux bases de données : **master** et **mySampleDataWarehouse**.

    ![Interroger des bases de données](media/create-data-warehouse-portal/query-databases.png)

4. Pour examiner des données, utilisez la commande suivante pour afficher le nombre de clients dont le nom de famille est Adams et qui ont trois enfants au foyer. Les résultats donnent six clients. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

    ![Interroger dbo.dimCustomer](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous êtes facturé pour les Data Warehouse Units et les données stockées dans votre entrepôt de données. Ces ressources de calcul et de stockage sont facturées séparément. 

- Si vous voulez conserver les données dans le stockage, vous pouvez suspendre le calcul quand vous n’utilisez pas l’entrepôt de données. Quand vous suspendez le calcul, vous êtes facturé uniquement pour le stockage des données. Vous pouvez reprendre le calcul chaque fois que vous êtes prêt à travailler avec les données.
- Si vous voulez éviter des frais ultérieurs, vous pouvez supprimer l’entrepôt de données. 

Suivez ces étapes pour nettoyer les ressources selon vos besoins.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et cliquez sur votre entrepôt de données.

    ![Supprimer des ressources](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Pour suspendre le calcul, cliquez sur le bouton **Suspendre**. Quand l’entrepôt de données est suspendu, un bouton **Démarrer** est visible.  Pour reprendre le calcul, cliquez sur **Démarrer**.

2. Pour supprimer l’entrepôt de données afin de ne pas être facturé pour le calcul ou le stockage, cliquez sur **Supprimer**.

3. Pour supprimer le serveur SQL que vous avez créé, cliquez sur **mynewserver-20171113.database.windows.net** dans l’image précédente, puis sur **Supprimer**.  N’oubliez pas que la suppression du serveur supprime également toutes les bases de données qui lui sont attribuées.

4. Pour supprimer le groupe de ressources, cliquez sur **myResourceGroup**, puis sur **Supprimer le groupe de ressources**.


## <a name="next-steps"></a>Étapes suivantes
Vous avez créé un entrepôt de données, créé une règle de pare-feu, vous vous êtes connecté à votre entrepôt de données et vous avez exécuté quelques requêtes. Pour en savoir plus sur Azure SQL Data Warehouse, continuez avec le didacticiel de chargement des données.
> [!div class="nextstepaction"]
>[Charger des données dans un entrepôt SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)