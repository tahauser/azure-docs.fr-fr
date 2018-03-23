---
title: Charger des données à partir d’un fichier CSV dans la base de données SQL Azure (BCP) | Microsoft Docs
description: Pour les données de taille réduite, utilise l’utilitaire de ligne de commande BCP pour importer les données dans la base de données SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.topic: article
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: c9bc6d2de6647d0ec047a600a52d14ad24ccbae9
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="load-data-from-csv-into-azure-sql-database-flat-files"></a>Charger des données à partir d’un fichier CSV une base de données SQL Azure (fichiers plats)
Vous pouvez utiliser l’utilitaire de ligne de commande BCP pour importer des données à partir d’un fichier CSV dans la base de données SQL Azure.

## <a name="before-you-begin"></a>Avant de commencer
### <a name="prerequisites"></a>Prérequis

Pour accomplir les étapes décrites dans cet article, vous avez besoin de ce qui suit :

* Serveur logique et base de données SQL Azure
* Utilitaire de ligne de commande bcp installé
* Utilitaire de ligne de commande sqlcmd installé

Pour télécharger les utilitaires bcp et sqlcmd, accédez au [Centre de téléchargement Microsoft][Microsoft Download Center].

### <a name="data-in-ascii-or-utf-16-format"></a>Données au format ASCII ou UTF-16
Si vous suivez ce didacticiel avec vos propres données, l’encodage de ces dernières doit être au format ASCII ou UTF-16, étant donné que bcp ne prend pas en charge UTF-8. 

## <a name="1-create-a-destination-table"></a>1. Créer une table de destination
Dans la base de données SQL, définissez une table en tant que table de destination. Les colonnes de la table doivent correspondre aux données dans chaque ligne du fichier de données.

Pour créer une table, ouvrez une invite de commandes et utilisez sqlcmd.exe pour exécuter la commande suivante :

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
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

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t , 
```

## <a name="3-load-the-data"></a>3. Chargement des données
Pour charger les données, ouvrez une invite de commandes et exécutez la commande suivante, en remplaçant les valeurs de ServerName, DatabaseName, Username, et Password par vos propres informations.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Cette commande permet de vérifier que les données ont été correctement chargées.

```bcp
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

## <a name="next-steps"></a>Étapes suivantes
Pour migrer une base de données SQL Server, consultez [Migration de base de données SQL Server](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
