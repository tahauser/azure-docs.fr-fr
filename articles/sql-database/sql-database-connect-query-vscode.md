---
title: 'VS Code : Se connecter et interroger des données dans Azure SQL Database | Microsoft Docs'
description: Découvrez comment vous connecter à SQL Database sur Azure avec Visual Studio Code. Ensuite, exécutez des instructions Transact-SQL (T-SQL) pour interroger et modifier des données.
keywords: connexion à sql database
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.topic: quickstart
ms.date: 06/22/2017
ms.author: carlrab
ms.openlocfilehash: 689b54704b46f852f62f7d97496c7c04649f2ff1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL Database : utilisez Visual Studio Code pour vous connecter et interroger des données

[Visual Studio Code](https://code.visualstudio.com/docs) est un éditeur de code graphique pour Linux, macOS et Windows qui prend en charge les extensions, y compris [l’extension mssql](https://aka.ms/mssql-marketplace) pour l’exécution de requêtes dans Microsoft SQL Server, Azure SQL Database et SQL Data Warehouse. Ce guide de démarrage rapide explique comment utiliser Visual Studio Code pour se connecter à une base de données SQL Azure, puis utiliser des instructions Transact-SQL pour interroger, insérer, mettre à jour et supprimer des données dans la base de données.

## <a name="prerequisites"></a>Prérequis


Ce guide de démarrage rapide utilise comme point de départ les ressources créées dans l’un de ces guides de démarrage rapide :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

#### <a name="install-vs-code"></a>Installer VS Code

Avant de commencer, assurez-vous que vous avez installé la toute dernière version de [Visual Studio Code](https://code.visualstudio.com/Download) et chargé l’[extension mssql](https://aka.ms/mssql-marketplace). Pour obtenir des conseils d’installation concernant l’extension mssql, consultez la section [Install VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) (Installation de VS Code) et l’article [mssql for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) (mssql pour Visual Studio Code). 

## <a name="configure-vs-code"></a>Configurer Visual Studio Code 

### <a name="mac-os"></a>**Mac OS**
Pour macOS, vous devez installer OpenSSL qui est un composant requis pour DotNet Core utilisé par l’extension mssql. Ouvrez votre terminal et entrez les commandes ci-après pour installer **brew** et **OpenSSL**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Aucune configuration spéciale nécessaire.

### <a name="windows"></a>**Windows**

Aucune configuration spéciale nécessaire.

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

Obtenez les informations de connexion requises pour la connexion à la base de données SQL Azure. Vous aurez besoin du nom du serveur complet, du nom de la base de données et des informations de connexion dans les procédures suivantes.

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="set-language-mode-to-sql"></a>Définition du mode de langage sur SQL

Définissez le mode de langage sur **SQL** dans Visual Studio Code pour activer les commandes mssql et T-SQL IntelliSense.

1. Ouvrez une nouvelle fenêtre Visual Studio Code. 

2. Cliquez sur **Texte brut** dans le coin inférieur droit de la barre d’état.
3. Dans le menu déroulant **Sélectionner un mode de langage** qui s’ouvre, tapez **SQL**, puis appuyez sur **Entrée** pour définir le mode de langage sur SQL. 

   ![Mode de langage SQL](./media/sql-database-connect-query-vscode/vscode-language-mode.png)

## <a name="connect-to-your-database"></a>Connectez-vous à votre base de données

Utilisez Visual Studio Code pour établir une connexion à votre serveur Azure SQL Database.

> [!IMPORTANT]
> Avant de poursuivre, assurez-vous que votre serveur et votre base de données sont prêts, et que vous disposez de vos informations de connexion. Une fois que vous avez commencé à saisir les informations concernant le profil de connexion, si vous modifiez le focus à partir de Visual Studio Code, vous devez redémarrer la création du profil de connexion.
>

1. Dans VS Code, appuyez sur **CTRL+MAJ+P** (ou **F1**) pour ouvrir la Palette de commandes.

2. Tapez **sqlcon**, puis appuyez sur **ENTRÉE**.

3. Appuyez sur **ENTRÉE** pour sélectionner **Créer un profil de connexion**. Cela crée un profil de connexion à votre instance SQL Server.

4. Suivez les invites pour spécifier les propriétés de connexion de ce nouveau profil de connexion. Après avoir spécifié chaque valeur, appuyez sur **ENTRÉE** pour continuer. 

   | Paramètre       | Valeur suggérée | Description |
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nom du serveur | Nom complet du serveur | Le nom doit être similaire à ce qui suit : **mynewserver20170313.database.windows.net**. |
   | **Nom de la base de données** | mySampleDatabase | Nom de la base de données à laquelle se connecter. |
   | **Authentification** | Connexion SQL| L’authentification SQL est le seul type d’authentification que nous avons configuré dans ce didacticiel. |
   | **Nom d'utilisateur** | Compte d’administrateur de serveur | Il s’agit du compte que vous avez spécifié lorsque vous avez créé le serveur. |
   | **Mot de passe (connexion SQL)** | Mot de passe de votre compte d’administrateur de serveur | Il s’agit du mot de passe que vous avez spécifié lorsque vous avez créé le serveur. |
   | **Enregistrer le mot de passe ?** | Oui ou Non | Sélectionnez Oui si vous ne souhaitez pas entrer le mot de passe à chaque fois. |
   | **Entrez un nom pour ce profil** | Nom de profil de connexion, par exemple **mySampleDatabase** | Un nom de profil enregistré permet d’accélérer votre connexion lors des connexions suivantes. | 

5. Appuyez sur la touche **ÉCHAP** pour fermer le message d’information qui vous informe que le profil est créé et connecté.

6. Vérifiez votre connexion dans la barre d’état.

   ![État de la connexion](./media/sql-database-connect-query-vscode/vscode-connection-status.png)

## <a name="query-data"></a>Données de requête

Utilisez le code suivant pour rechercher les 20 premiers produits par catégorie à l’aide de l’instruction Transact-SQL [SELET](https://msdn.microsoft.com/library/ms189499.aspx).

1. Dans la fenêtre **Éditeur**, saisissez la requête suivante dans la fenêtre de requête vide :

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Appuyez sur **CTRL+MAJ+E** pour récupérer des données à partir des tables Product et ProductCategory.

    ![Requête](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Insertion des données

Utilisez le code suivant pour insérer un nouveau produit dans la table SalesLT.Product à l’aide de l’instruction Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx).

1. Dans la fenêtre **Éditeur**, supprimez la requête précédente et saisissez la requête suivante :

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

2. Appuyez sur **CTRL+MAJ+E** pour insérer une nouvelle ligne dans la table Product.

## <a name="update-data"></a>Mettre à jour des données

Utilisez le code suivant pour mettre à jour le nouveau produit que vous avez ajouté précédemment à l’aide de l’instruction Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx).

1.  Dans la fenêtre **Éditeur**, supprimez la requête précédente et saisissez la requête suivante :

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Appuyez sur **CTRL+MAJ+E** pour mettre à jour la ligne spécifiée dans la table Product.

## <a name="delete-data"></a>Suppression de données

Utilisez le code suivant pour supprimer le nouveau produit que vous avez ajouté précédemment à l’aide de l’instruction Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql).

1. Dans la fenêtre **Éditeur**, supprimez la requête précédente et saisissez la requête suivante :

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Appuyez sur **CTRL+MAJ+E** pour supprimer la ligne spécifiée dans la table Product.

## <a name="next-steps"></a>Étapes suivantes

- Pour vous connecter et envoyer des requêtes à l’aide de SQL Server Management Studio, consultez [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md).
- Pour vous connecter et exécuter des requêtes à l’aide du portail Azure, consultez [Se connecter et effectuer des requêtes avec l’éditeur de requêtes SQL du portail Azure](sql-database-connect-query-portal.md).
- Pour lire un article du magazine MSDN sur l’utilisation de Visual Studio Code, consultez le [billet de blog Créer un IDE de base de données avec une extension MSSQL](https://msdn.microsoft.com/magazine/mt809115).
