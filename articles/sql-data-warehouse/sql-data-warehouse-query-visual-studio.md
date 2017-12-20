---
title: "Se connecter à Azure SQL Data Warehouse - VSTS | Microsoft Docs"
description: Interrogez SQL Data Warehouse avec Visual Studio.
services: sql-data-warehouse
documentationcenter: NA
author: antvgski
manager: jhubbard
editor: 
ms.assetid: daace889-95e5-4826-b2fc-047eac9d6d95
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: connect
ms.date: 10/31/2016
ms.author: anvang;barbkess
ms.openlocfilehash: 1e44c6c3c47034a892753c69c5ef22a5eac18c0d
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="connect-to-sql-data-warehouse-with-visual-studio-and-ssdt"></a>Se connecter à SQL Data Warehouse avec Visual Studio et SSDT
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Utilisez Visual Studio pour interroger Azure SQL Data Warehouse en seulement quelques minutes. Cette méthode utilise l’extension de SQL Server Data Tools (SSDT) dans Visual Studio. 

## <a name="prerequisites"></a>Composants requis
Pour utiliser ce didacticiel, vous avez besoin des éléments suivants :

* Un entrepôt de données SQL existant. Pour en créer un, consultez la page [Créer un entrepôt de données SQL][Create a SQL Data Warehouse].
* SSDT pour Visual Studio. Si vous avez Visual Studio, vous en disposez probablement déjà. Pour obtenir des instructions et des options d’installation, consultez [Installation de Visual Studio et/ou SSDT][Installing Visual Studio and SSDT].
* Le nom complet du serveur SQL. Pour le trouver, consultez la page [Connect to SQL Data Warehouse][Connect to SQL Data Warehouse](Connexion à SQL Data Warehouse).

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Connexion à votre SQL Data Warehouse
1. Ouvrir Visual Studio 2013 ou 2015
2. Ouvrez l’Explorateur d’objets SQL Server. Pour ce faire, sélectionnez **Affichage** > **Explorateur d’objets SQL Server**.
   
    ![Explorateur d’objets SQL Server][1]
3. Cliquez sur l’icône **Ajouter SQL Server** .
   
    ![Ajouter SQL Server][2]
4. Renseignez les champs dans la fenêtre Se connecter au serveur.
   
    ![Se connecter au serveur][3]
   
   * **Nom du serveur**. Saisissez le **nom du serveur** précédemment identifié.
   * **Authentification**. Sélectionnez **Authentification SQL Server** ou **Authentification intégrée Active Directory**.
   * **Nom d’utilisateur** et **Mot de passe**. Entrez un nom d’utilisateur et mot de passe si l’authentification SQL Server a été sélectionnée plus haut.
   * Cliquez sur **Connecter**.
5. Pour voir plus d’informations, développez votre serveur SQL Azure. Vous pouvez afficher les bases de données associées au serveur. Développez AdventureWorksDW pour voir les tables de votre exemple de base de données.
   
    ![Explorer AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2. Exécuter un exemple de requête
Maintenant qu’une connexion à votre base de données a été établie, passons à l’écriture d’une requête.

1. Cliquez avec le bouton droit sur votre base de données dans l’Explorateur d’objets SQL Server.
2. Sélectionnez **Nouvelle requête**. Une nouvelle fenêtre de requête s’ouvre.
   
    ![Nouvelle requête][5]
3. Copiez la requête TSQL suivante dans la fenêtre de requête :
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Exécutez la requête. Pour ce faire, cliquez sur la flèche verte ou utilisez le raccourci `CTRL`+`SHIFT`+`E`.
   
    ![Exécuter une requête][6]
5. Passez en revue les résultats de la requête. Dans cet exemple, la table FactInternetSales a 60 398 lignes.
   
    ![Résultats de la requête][7]

## <a name="next-steps"></a>Étapes suivantes
Comme vous pouvez à présent vous connecter et exécuter des requêtes, essayez de [visualiser les données avec Power BI][visualizing the data with PowerBI].

Pour configurer votre environnement pour l’authentification Azure Active Directory, voir [Authentification vers SQL Data Warehouse][Authenticate to SQL Data Warehouse].

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png
