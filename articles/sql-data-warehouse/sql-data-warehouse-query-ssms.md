---
title: "Se connecter à Azure SQL Data Warehouse - SSMS | Microsoft Docs"
description: "Utilisez SQL Server Management Studio (SSMS) pour vous connecter à une base de données SQL Azure Warehouse et l’interroger."
services: sql-data-warehouse
documentationcenter: 
author: antvgski
manager: jhubbard
editor: 
ms.assetid: 299e50b3-e68a-471c-8aee-b0b9874781bd
ms.service: sql-data-warehouse
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: connect
ms.date: 10/31/2016
ms.author: anvang;barbkess
ms.openlocfilehash: 207fb9fd861c66039fbde89681aed3df3a2f4021
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Se connecter à SQL Data Warehouse à l’aide de SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Utilisez SQL Server Management Studio (SSMS) pour vous connecter à une base de données SQL Azure Warehouse et l’interroger. 

## <a name="prerequisites"></a>Composants requis
Pour utiliser ce didacticiel, vous avez besoin des éléments suivants :

* Un entrepôt de données SQL existant. Pour en créer un, consultez la page [Créer un entrepôt de données SQL][Create a SQL Data Warehouse].
* SQL Server Management Studio (SSMS) installé. [Installez SSMS][Install SSMS] gratuitement si vous ne l’avez pas déjà.
* Le nom complet du serveur SQL. Pour le trouver, consultez la page [Connect to SQL Data Warehouse][Connect to SQL Data Warehouse](Connexion à SQL Data Warehouse).

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Connexion à votre SQL Data Warehouse
1. Ouvrez SSMS.
2. Ouvrez l’Explorateur d'objets. Pour ce faire, sélectionnez **Fichier** > **Connecter l’Explorateur d’objets**.
   
    ![Explorateur d’objets SQL Server][1]
3. Renseignez les champs dans la fenêtre Se connecter au serveur.
   
    ![Se connecter au serveur][2]
   
   * **Nom du serveur**. Saisissez le **nom du serveur** précédemment identifié.
   * **Authentification**. Sélectionnez **Authentification SQL Server** ou **Authentification intégrée Active Directory**.
   * **Nom d’utilisateur** et **Mot de passe**. Entrez un nom d’utilisateur et mot de passe si l’authentification SQL Server a été sélectionnée plus haut.
   * Cliquez sur **Connecter**.
4. Pour voir plus d’informations, développez votre serveur SQL Azure. Vous pouvez afficher les bases de données associées au serveur. Développez AdventureWorksDW pour voir les tables de votre exemple de base de données.
   
    ![Explorer AdventureWorksDW][3]

## <a name="2-run-a-sample-query"></a>2. Exécuter un exemple de requête
Maintenant qu’une connexion à votre base de données a été établie, passons à l’écriture d’une requête.

1. Cliquez avec le bouton droit sur votre base de données dans l’Explorateur d’objets SQL Server.
2. Sélectionnez **Nouvelle requête**. Une nouvelle fenêtre de requête s’ouvre.
   
    ![Nouvelle requête][4]
3. Copiez la requête TSQL suivante dans la fenêtre de requête :
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Exécutez la requête. Pour ce faire, cliquez sur `Execute` ou utilisez le raccourci `F5`.
   
    ![Exécuter une requête][5]
5. Passez en revue les résultats de la requête. Dans cet exemple, la table FactInternetSales a 60 398 lignes.
   
    ![Résultats de la requête][6]

## <a name="next-steps"></a>Étapes suivantes
Comme vous pouvez à présent vous connecter et exécuter des requêtes, essayez de [visualiser les données avec Power BI][visualizing the data with PowerBI].

Pour configurer votre environnement pour l’authentification Azure Active Directory, voir [Authentification vers SQL Data Warehouse][Authenticate to SQL Data Warehouse].

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/en-US/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
