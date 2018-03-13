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
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 146c6fdada651551c05b2cbcadc3e1248a40b613
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="load-data-with-bcp"></a>Charger des données avec bcp

**[bcp](/sql/tools/bcp-utility.md)** est un utilitaire de ligne de commande de chargement par lots qui vous permet de charger des données entre SQL Server, des fichiers de données et SQL Data Warehouse. Utilisez bcp pour importer un nombre important de lignes dans les tables SQL Data Warehouse ou pour exporter des données des tables SQL Server dans les fichiers de données. L’utilitaire bcp, sauf lorsqu’il est utilisé avec l’option queryout, ne nécessite aucune connaissance de Transact-SQL.

bcp permet d’importer et d’exporter rapidement et simplement des ensembles de données plus petits de la base de données SQL Data Warehouse. La quantité exacte de données qu’il est recommandé de charger/extraire via bcp dépend de la connexion du réseau à Azure.  Les petites tables de dimension peuvent être chargées et extraites facilement avec bcp. Toutefois, Polybase, et non bcp, est l’outil recommandé pour le chargement et l’extraction de grands volumes de données. PolyBase est conçu pour l’architecture de traitement parallèle massif de l’entrepôt de données SQL.

Avec bcp, vous pouvez :

* Utiliser un utilitaire en ligne de commande pour charger des données dans SQL Data Warehouse.
* Utiliser un utilitaire en ligne de commande pour extraire des données depuis SQL Data Warehouse.

Ce didacticiel :

* Importe des données dans une table à l’aide de la commande bcp in
* Exporte des données d’une table à l’aide de la commande bcp out

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>configuration requise
Pour parcourir ce didacticiel, vous avez besoin des éléments suivants :

* Base de données SQL Data Warehouse
* Utilitaires de ligne de commande bcp et sqlcmd. Vous pouvez les télécharger à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=36433). 

### <a name="data-in-ascii-or-utf-16-format"></a>Données au format ASCII ou UTF-16
Si vous suivez ce didacticiel avec vos propres données, l’encodage de ces dernières doit être au format ASCII ou UTF-16, étant donné que bcp ne prend pas en charge UTF-8. 

PolyBase prend en charge UTF-8, mais pas UTF-16 pour l’instant. Pour utiliser bcp pour exporter des données, puis PolyBase pour les charger, vous devez transformer les données au format UTF-8 après leur exportation depuis SQL Server. 

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

Pour plus d’informations sur la création d’une table, consultez [Vue d’ensemble des tables](sql-data-warehouse-tables-overview.md) ou la syntaxe [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md).
 

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
Après le chargement des données, une dernière étape consiste à créer ou mettre à jour des statistiques. Cela contribue à améliorer les performances des requêtes. Pour plus d’informations, consultez [Statistiques](sql-data-warehouse-tables-statistics.md). L’exemple suivant de sqlcmd crée des statistiques dans la table contenant les données nouvellement chargées.


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exporter des données de SQL Data Warehouse
Ce didacticiel crée un fichier de données à partir d’une table de SQL Data Warehouse. Il exporte les données importées dans la section précédente. Les résultats vous dans un fichier nommé DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Étape 1 : Exporter les données
L’utilitaire bcp vous permet de connecter et d’exporter les données à l’aide de la commande suivante, qui remplace de manière appropriée les valeurs :

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Pour vérifier que les données ont été exportées, ouvrez le nouveau fichier. Les données du fichier doivent correspondre au texte ci-dessous :

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
Pour concevoir votre processus de chargement, consultez la [vue d’ensemble de chargement](sql-data-warehouse-design-elt-data-loading).  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
