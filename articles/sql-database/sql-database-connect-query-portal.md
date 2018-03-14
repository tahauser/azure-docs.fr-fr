---
title: "Portail Azure : interroger une base de données SQL Azure à l’aide de l’éditeur de requête | Microsoft Docs"
description: "Découvrez comment se connecter à une base de données SQL dans le portail Azure à l’aide de l’éditeur de requête SQL. Ensuite, exécutez des instructions Transact-SQL (T-SQL) pour interroger et modifier des données."
metacanonical: 
keywords: "se connecter à la base de données sql, portail azure, portail, éditeur de requête"
services: sql-database
documentationcenter: 
author: ayoolubeko
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/10/2018
ms.author: ayolubek
ms.openlocfilehash: aa4df1854b15198ede1ee143afeff47b24dc567f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="azure-portal-use-the-sql-query-editor-to-connect-and-query-data"></a>Portail Azure : utilisez l’éditeur de requête SQL pour vous connecter et effectuer des requêtes de données

L’éditeur de requête SQL est un outil de requête de navigateur qui offre un moyen efficace et léger pour exécuter des requêtes SQL sur votre base de données SQL Azure ou Azure SQL Data Warehouse sans quitter le portail Azure. Ce guide de démarrage rapide indique comment utiliser l’éditeur de requête pour se connecter à une base de données SQL, puis utiliser les instructions Transact-SQL pour interroger, insérer, mettre à jour et supprimer des données dans la base de données.

## <a name="prerequisites"></a>Prérequis

Ce guide de démarrage rapide utilise comme point de départ les ressources créées dans l’un de ces guides de démarrage rapide :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

> [!NOTE]
> Assurez-vous que le paramètre « Autoriser l’accès aux services Azure » est défini sur « ON » dans les paramètres du pare-feu de votre serveur SQL. Cette option fournit l’accès à l’éditeur de requête SQL pour vos bases de données et les entrepôts de données.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).


## <a name="connect-using-sql-authentication"></a>Se connecter à l’aide de l’authentification SQL

1. Cliquez sur **Bases de données SQL** depuis le menu de gauche et cliquez sur la base de données que vous souhaitez interroger.

2. Sur la page Bases de données SQL de votre base de données, recherchez **Éditeur de requête (préversion)** dans le menu de gauche et cliquez dessus.

    ![rechercher un éditeur de requête](./media/sql-database-connect-query-portal/find-query-editor.PNG)

3. Cliquez sur **Connexion**, puis, lorsque vous y êtes invité, sélectionnez **Authentification du serveur SQL Server** et indiquez l’identifiant et le mot de passe de connexion administrateur que vous avez indiqués lors de la création de la base de données.

    ![se connecter](./media/sql-database-connect-query-portal/login-menu.png)

4. Cliquez sur **OK pour vous connecter**.


## <a name="connect-using-azure-ad"></a>Se connecter avec Azure AD

La configuration d’un administrateur Active Directory vous permet d’utiliser une identité unique pour vous connecter au portail Azure et votre base de données SQL. Suivez les étapes ci-dessous pour configurer un administrateur Active Directory pour le serveur SQL que vous avez créé.

> [!NOTE]
> Les comptes de messagerie (par exemple outlook.com, hotmail.com, live.com, gmail.com, yahoo.com) ne sont pas encore pris en charge comme des administrateurs Active Directory. Veillez à choisir un utilisateur créé en mode natif dans Azure Active Directory, ou fédéré dans Azure Active Directory.

1. Sélectionnez **Serveurs SQL** depuis le menu de gauche, puis sélectionnez votre serveur SQL à partir de la liste de serveurs.

2. Sélectionnez le paramètre **Administrateur Active Directory** à partir du menu Paramètres de votre serveur SQL.

3. Dans le panneau d’administration Active Directory, cliquez sur la commande **Set admin** et sélectionnez l’utilisateur ou le groupe qui sera l’administrateur de Active Directory.

    ![Sélectionner Active Directory](./media/sql-database-connect-query-portal/select-active-directory.png)

4. En haut du panneau d’administration Active Directory, cliquez sur **Enregistrer** pour définir votre administrateur Active Directory.

Accédez à la base de données SQL que vous voulez interroger, cliquez sur **Explorateur de données (préversion préliminaire)** dans le menu de gauche. La page de l’Explorateur de données s’ouvre et vous connecte automatiquement à la base de données.


## <a name="run-query-using-query-editor"></a>Exécuter une requête à l’aide de l’éditeur de requête

Une fois authentifié, saisissez la requête suivante dans le volet de l’éditeur de requête afin de connaître les 20 premiers produits par catégorie.

```sql
 SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
 FROM SalesLT.ProductCategory pc
 JOIN SalesLT.Product p
 ON pc.productcategoryid = p.productcategoryid;
```

Cliquez sur **Exécuter**, puis passez en revue les résultats de la requête dans le volet **Résultats**.

![query editor results](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data-using-query-editor"></a>Insérer des données à l’aide de l’éditeur de requête

Utilisez le code suivant pour insérer un nouveau produit dans la table SalesLT.Product à l’aide de l’instruction Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx).

1. Dans la fenêtre de requête, remplacez la requête précédente par la requête suivante :

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Dans la barre d’outils, cliquez sur **Exécuter** pour insérer une nouvelle ligne dans la table Product.

## <a name="update-data-using-query-editor"></a>Mettre à jour des données à l’aide de l’éditeur de requête

Utilisez le code suivant pour mettre à jour le nouveau produit que vous avez ajouté précédemment à l’aide de l’instruction Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx).

1. Dans la fenêtre de requête, remplacez la requête précédente par la requête suivante :

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Dans la barre d’outils, cliquez sur **Exécuter** pour mettre à jour la ligne spécifiée dans la table Product.

## <a name="delete-data-using-query-editor"></a>Supprimer des données à l’aide de l’éditeur de requête

Utilisez le code suivant pour supprimer le nouveau produit que vous avez ajouté précédemment à l’aide de l’instruction Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx).

1. Dans la fenêtre de requête, remplacez la requête précédente par la requête suivante :

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Dans la barre d’outils, cliquez sur **Exécuter** pour supprimer la ligne spécifiée dans la table Product.


## <a name="query-editor-considerations"></a>Considérations relatives à l’éditeur de requête

Il y a quelques éléments à connaître lorsque vous travaillez avec l’éditeur de requête, alors qu’il s’agit d’une préversion :

1. Assurez-vous que le paramètre « Autoriser l’accès aux services Azure » est défini sur « ON » dans les paramètres du pare-feu de votre serveur SQL Azure. Cette option fournit l’accès de l’éditeur de requête SQL pour vos bases de données SQL et les entrepôts de données.

2. La connexion de l’administrateur de Azure Active Directory ne fonctionne pas lorsque l’authentification à 2 facteurs est activée.

3. Les comptes de messagerie (par exemple outlook.com, hotmail.com, live.com, gmail.com, yahoo.com) ne sont pas encore pris en charge comme des administrateurs Active Directory. Veillez à choisir un utilisateur créé en mode natif dans Azure Active Directory, ou fédéré dans Azure Active Directory

4. Les requêtes de types de données spatiales ne sont pas encore prises en charge dans l’éditeur de requête. L’interrogation de colonnes spatiales entraîne une erreur « System.IO.FileNotFoundException ».

5. IntelliSense n’est pas pris en charge pour les tables et les vues de base de données. Toutefois, l’éditeur prend en charge la saisie semi-automatique des noms déjà saisis.

6. Appuyer sur la touche F5 actualise la page de l’éditeur de requête et entraîne la perte de la requête en cours de traitement. Utilisez le bouton Exécuter dans la barre d’outils pour exécuter des requêtes.


## <a name="next-steps"></a>étapes suivantes

- Pour obtenir des informations sur le Transact-SQL pris en charge dans les bases de données SQL Azure, consultez [Différences de Transact-SQL dans la base de données SQL](sql-database-transact-sql-information.md).
