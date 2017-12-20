---
title: "Utilisation de bcp pour charger des données dans SQL Data Warehouse | Microsoft Docs"
description: "Découvrez bcp et apprenez comment utiliser cette solution avec les scénarios d’entreposage de données."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 7596eac10fdf53380d85128265430ce07b551fe3
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="load-data-with-bcp"></a>Chargement des données avec BCP
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)  
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

**[bcp][bcp]** est un utilitaire de ligne de commande de chargement par lots qui vous permet de charger des données entre SQL Server, des fichiers de données et SQL Data Warehouse. Utilisez bcp pour importer un nombre important de lignes dans les tables SQL Data Warehouse ou pour exporter des données des tables SQL Server dans les fichiers de données. L’utilitaire bcp, sauf lorsqu’il est utilisé avec l’option queryout, ne nécessite aucune connaissance de Transact-SQL.

bcp permet d’importer et d’exporter rapidement et simplement des ensembles de données plus petits de la base de données SQL Data Warehouse. La quantité exacte de données qu’il est recommandé de charger/extraire via bcp dépend de la connexion de votre réseau au centre de données Microsoft Azure.  En règle générale, les tables de dimension peuvent être chargées et extraites facilement avec bcp. Toutefois, bcp n’est pas recommandé pour le chargement ou l’extraction de volumes de données élevés.  Polybase est l’outil recommandé pour le chargement et l’extraction de volumes de données élevés, car il exploite plus efficacement l’architecture MPP (Massively Parallel Processing) de SQL Data Warehouse.

Avec bcp, vous pouvez :

* Utiliser un utilitaire en ligne de commande simple pour charger des données dans SQL Data Warehouse.
* Utiliser un utilitaire en ligne de commande simple pour extraire des données de SQL Data Warehouse.

Ce didacticiel vous explique comment :

* Importer des données dans une table à l’aide de la commande bcp in
* Exporter des données d’une table à l’aide de la commande bcp out

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>Configuration requise
Pour parcourir ce didacticiel, vous avez besoin des éléments suivants :

* Base de données SQL Data Warehouse
* Utilitaire en ligne de commande bcp installé
* Utilitaire en ligne de commande SQLCMD installé

> [!NOTE]
> Pour télécharger les utilitaires bcp et sqlcmd, accédez au [Centre de téléchargement Microsoft][Microsoft Download Center].
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>Importer des données dans SQL Data Warehouse
Dans ce didacticiel, vous allez créer une table dans Azure SQL Data Warehouse et importer des données dans la table.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Étape 1 : Créer une table dans Azure SQL Data Warehouse
À partir d’une invite de commandes, utilisez sqlcmd pour exécuter la requête suivante, afin de créer une table sur votre instance :

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

> [!NOTE]
> Consultez la page [Table Overview][Table Overview] (Vue d’ensemble des tables) ou la [syntaxe de CREATE TABLE][CREATE TABLE syntax] pour en savoir plus sur la création d’une table dans SQL Data Warehouse et les options disponibles dans la clause WITH.
> 
> 

### <a name="step-2-create-a-source-data-file"></a>Étape 2 : Créer un fichier de données source
Ouvrez le Bloc-notes, copiez les lignes de données suivantes dans un nouveau fichier texte, puis enregistrez ce fichier dans votre répertoire temporaire local C:\Temp\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> Il est important de se souvenir que bcp.exe ne prend pas en charge le codage de fichier UTF-8. Utilisez des fichiers ASCII ou codés en UTF-16 lors de l’utilisation de bcp.exe.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>Étape 3 : Connecter et importer les données
Grâce à bcp, vous pouvez connecter et importer les données à l’aide de la commande suivante, qui remplace de manière appropriée les valeurs :

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Vous pouvez vérifier que les données ont été chargées en exécutant la requête suivante à l’aide de sqlcmd :

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Les résultats suivants doivent s’afficher :

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Étape 4 : Créer des statistiques sur vos données nouvellement chargées
Azure SQL Data Warehouse ne prend pas encore en charge les statistiques à création ou mise à jour automatique. Pour optimiser les performances de vos requêtes, il est important de créer les statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou après toute modification substantielle dans les données. Pour une explication détaillée des statistiques, consultez la rubrique [Statistiques][Statistics] dans le groupe de rubriques sur le développement. Voici un exemple rapide de la création de statistiques sur le tableau chargé dans cet exemple

À partir d’une invite sqlcmd, exécutez les instructions CREATE STATISTICS suivantes :

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exporter des données de SQL Data Warehouse
Dans ce didacticiel, vous allez créer un fichier de données à partir d’une table de SQL Data Warehouse. Nous allons exporter les données créées plus haut vers un nouveau fichier de données, DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Étape 1 : Exporter les données
L’utilitaire bcp vous permet de connecter et d’exporter les données à l’aide de la commande suivante, qui remplace de manière appropriée les valeurs :

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Pour vérifier que les données ont été exportées, ouvrez le nouveau fichier. Les données du fichier doivent correspondre au texte ci-dessus :

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> En raison de la nature des systèmes distribués, l’ordre des données peut ne pas être identique entre les différentes bases de données SQL Data Warehouse. Une autre option consiste à utiliser la fonction **queryout** de l’utilitaire bcp pour écrire un extrait de requête au lieu d’exporter la totalité de la table.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Pour accéder à une vue d’ensemble du chargement, consultez [Charger des données dans SQL Data Warehouse][Load data into SQL Data Warehouse].
Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Vue d’ensemble sur le développement SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
