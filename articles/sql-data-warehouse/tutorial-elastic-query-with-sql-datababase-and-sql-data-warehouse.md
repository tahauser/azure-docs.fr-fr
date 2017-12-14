---
title: "Tutoriel sur la fonctionnalité de requête élastique avec Azure SQL Data Warehouse | Microsoft Docs"
description: "Découvrez comment utiliser la fonctionnalité de requête élastique avec SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67g
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 11/03/2017
ms.author: elbutter
ms.openlocfilehash: 20bbdbbde7edc4351563685761785874870a3c82
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-elastic-query-with-sql-data-warehouse"></a>Configurer la fonctionnalité de requête élastique avec SQL Data Warehouse

Dans ce didacticiel, vous allez apprendre à utiliser la fonctionnalité de requête élastique pour exécuter une requête depuis SQL Database vers SQL Data Warehouse. La fonctionnalité de requête élastique existe entre les produits SQL Azure. Pour plus d’informations sur la fonctionnalité de requête élastique en tant que concept, consultez [**Guide pratique pour utiliser la fonctionnalité de requête élastique avec SQL Data Warehouse**][How to use Elastic Query with SQL Data Warehouse].

## <a name="prerequisites-for-the-tutorial"></a>Configuration requise pour le didacticiel

Avant de commencer le didacticiel, vous devez satisfaire aux prérequis suivants :

1. Vous avez installé SQL Server Management Studio (SSMS).
2. Vous avez créé un serveur SQL Azure comportant une base de données et un entrepôt de données.
3. Vous avez configuré des règles de pare-feu pour accéder au serveur SQL Azure.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Configurer la connexion entre les instances SQL Data Warehouse et SQL Database 

1. À l’aide de SSMS ou d’un autre client de requête, ouvrez une nouvelle requête pour la base de données **master** sur votre serveur logique.

2. Créez une connexion et un utilisateur représentant la connexion entre la base de données SQL et l’entrepôt de données.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. À l’aide de SSMS ou d’un autre client de requête, ouvrez une nouvelle requête pour **l’instance SQL Data Warehouse** sur votre serveur logique.

4. Créez un utilisateur sur l’instance de l’entrepôt de données avec la connexion que vous avez créée à l’étape 2.

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Accordez des autorisations à l’utilisateur créé à l’étape 4 destinées à être exécutées par la base de données SQL. Dans cet exemple, l’autorisation est accordée uniquement pour une opération SELECT sur un schéma spécifique, illustrant la possibilité de limiter les requêtes à partir de la base de données SQL à un domaine spécifique. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. À l’aide de SSMS ou d’un autre client de requête, ouvrez une nouvelle requête pour **l’instance de la base de données SQL** sur votre serveur logique.

7. Créez une clé principale si vous n’en avez pas déjà une. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Créez un fichier d’informations d’identification de niveau base de données en utilisant les informations d’identification que vous avez créées à l’étape 2.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Créez une source de données externe qui pointe vers l’instance de l’entrepôt de données.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. Vous pouvez à présent créer des tables externes qui référencent cette source de données externe. Les requêtes qui utilisent ces tables sont envoyées à l’instance de l’entrepôt de données afin d’être traitées, puis elles sont renvoyées à l’instance de la base de données.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Requête élastique entre la base de données SQL et l’entrepôt de données SQL

Dans les étapes suivantes, nous allons créer une table dans notre instance de l’entrepôt de données avec plusieurs valeurs. Ensuite, nous allons montrer comment configurer une table externe pour interroger l’instance de l’entrepôt de données à partir de l’instance de base de données.

1. À l’aide de SSMS ou d’un autre client de requête, ouvrez une nouvelle requête pour **SQL Data Warehouse** sur votre serveur logique.

2. Exécutez la requête suivante pour créer une table **OrdersInformation** dans l’instance de l’entrepôt de données.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. À l’aide de SSMS ou d’un autre client de requête, ouvrez une nouvelle requête pour **la base de données SQL** sur votre serveur logique.

4. Exécutez la requête suivante pour créer une définition de table externe pointant vers la table **OrdersInformation** dans l’instance de l’entrepôt de données.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. Comme vous pouvez le constater, vous disposez maintenant d’une définition de table externe dans votre **instance de base de données SQL**.

   ![Définition de table externe avec une requête élastique](./media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Exécutez la requête suivante, qui interroge l’instance de l’entrepôt de données. Vous devez obtenir les cinq valeurs que vous avez insérées à l’étape 2. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Notez que, malgré le peu de valeurs, l’exécution de cette requête prend beaucoup de temps. Quand vous utilisez une requête élastique avec un entrepôt de données, vous devez prendre en considération les coûts de traitement des requêtes et de déplacement sur le réseau. Utilisez l’exécution distante de requêtes élastiques quand la priorité est la puissance de calcul, et non la latence.

Félicitations, les notions de base de la fonctionnalité de requête élastique n’ont plus de secrets pour vous. 




<!--Image references-->

<!--Article references-->

[How to use Elastic Query with SQL Data Warehouse]: ./how-to-use-elastic-query-with-sql-data-warehouse.md

<!--MSDN references-->

<!--Other Web references-->
