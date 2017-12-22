---
title: "Charger des données à partir de SQL Server dans Azure SQL Data Warehouse (bcp) | Microsoft Docs"
description: "Pour les données de taille réduite, utilise l’utilitaire de ligne de commande bcp pour exporter les données dans des fichiers plats à partir de SQL Server, puis les importer directement dans Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: f87d8d7c-8276-43c5-90e7-d4ccc0e3a224
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: dae7b5f7456f4ec0daf60d55f9c38b780896ff83
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>Charger des données à partir de SQL Server dans Azure SQL Data Warehouse (fichiers plats)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Pour les jeux de données de taille réduite, vous pouvez utiliser l’utilitaire de ligne de commande bcp pour exporter les données depuis SQL Server, puis les charger directement dans Azure SQL Data Warehouse.

Dans ce didacticiel, nous allons utiliser bcp pour :

* exporter une table à partir de SQL Server à l’aide de la commande bcp out (ou créer un fichier d’exemple simple) ;
* importer la table dans SQL Data Warehouse à partir d’un fichier plat ;
* créer des statistiques sur les données chargées.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="before-you-begin"></a>Avant de commencer
### <a name="prerequisites"></a>Composants requis
Pour parcourir ce didacticiel, vous avez besoin des éléments suivants :

* Base de données SQL Data Warehouse
* Utilitaire de ligne de commande bcp installé
* Utilitaire de ligne de commande sqlcmd installé

Pour télécharger les utilitaires bcp et sqlcmd, accédez au [Centre de téléchargement Microsoft][Microsoft Download Center].

### <a name="data-in-ascii-or-utf-16-format"></a>Données au format ASCII ou UTF-16
Si vous suivez ce didacticiel avec vos propres données, l’encodage de ces dernières doit être au format ASCII ou UTF-16, étant donné que bcp ne prend pas en charge UTF-8. 

PolyBase prend en charge UTF-8, mais pas UTF-16 pour l’instant. Notez que si vous souhaitez combiner bcp avec PolyBase, vous devrez convertir les données au format UTF-8 après les avoir exportées de SQL Server. 

## <a name="1-create-a-destination-table"></a>1. Créer une table de destination
Définissez une table dans SQL Data Warehouse qui sera la table de destination pour la charge. Les colonnes de la table doivent correspondre aux données dans chaque ligne du fichier de données.

Pour créer une table, ouvrez une invite de commandes et utilisez sqlcmd.exe pour exécuter la commande suivante :

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


## <a name="2-create-a-source-data-file"></a>2. Créer un fichier de données source
Ouvrez le Bloc-notes, copiez les lignes de données suivantes dans un nouveau fichier texte, puis enregistrez ce fichier dans votre répertoire temporaire local C:\Temp\DimDate2.txt. Ces données sont au format ASCII.

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

(Facultatif) Pour exporter vos données à partir d’une base de données SQL Server, ouvrez une invite de commandes et exécutez la commande suivante. Remplacez TableName, ServerName, DatabaseName, Username et Password par vos propres informations.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## <a name="3-load-the-data"></a>3. Chargement des données
Pour charger les données, ouvrez une invite de commandes et exécutez la commande suivante, en remplaçant les valeurs de ServerName, DatabaseName, Username, et Password par vos propres informations.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Cette commande permet de vérifier que les données ont été correctement chargées.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Le résultat doit avoir l’aspect suivant :

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

## <a name="4-create-statistics"></a>4. Create statistics
SQL Data Warehouse ne prend pas encore en charge les statistiques à création ou mise à jour automatique. Pour optimiser les performances de vos requêtes, il est important de créer les statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou après toute modification substantielle dans les données. Pour obtenir une explication détaillée des statistiques, consultez [Statistiques][Statistics]. 

Exécutez la commande suivante pour créer des statistiques sur la table nouvellement chargée.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5. Exporter des données de SQL Data Warehouse
Vous pouvez exporter les données que vous venez de charger à partir de SQL Data Warehouse.  La commande pour exporter est identique à celle pour exporter des données à partir de SQL Server.

Toutefois, les résultats ne sont pas les mêmes. Étant donné que les données sont stockées dans des emplacements distribués au sein de SQL Data Warehouse, chaque nœud de calcul écrit les données exportées dans le fichier de sortie. L’ordre des données dans le fichier de sortie sera peut-être différent de l’ordre des données du fichier d’entrée.

### <a name="export-a-table-and-compare-exported-results"></a>Exporter une table et comparer les résultats exportés
Pour afficher les données exportées, ouvrez une invite de commandes et exécutez la commande suivante avec vos propres paramètres. ServerName est le nom de votre serveur logique SQL Azure.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Pour vérifier que les données ont été exportées, ouvrez le nouveau fichier. Les données du fichier doivent correspondre au texte ci-dessous, mais leur ordre sera probablement différent :

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

### <a name="export-the-results-of-a-query"></a>Exporter les résultats d’une requête
Vous pouvez utiliser la fonction **queryout** de l’utilitaire bcp pour exporter les résultats d’une requête plutôt que d’exporter la table entière. 

## <a name="next-steps"></a>Étapes suivantes
Pour accéder à une vue d’ensemble du chargement, consultez [Charger des données dans SQL Data Warehouse][Load data into SQL Data Warehouse].
Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Vue d’ensemble sur le développement SQL Data Warehouse][SQL Data Warehouse development overview].
Pour plus d’informations sur la création d’une table sur SQL Data Warehouse, consultez [Vue d’ensemble des tables][Table Overview] ou la syntaxe [CREATE TABLE][CREATE TABLE syntax].

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
