---
title: "Copier des données entre Data Lake Store et une base de données SQL Azure à l’aide de Sqoop | Microsoft Docs"
description: "Utiliser Sqoop pour copier des données entre Azure SQL Database et Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 2611b50114a9fae003fe6e77300780a9af0698cc
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Copier des données entre Data Lake Store et une base de données SQL Azure à l’aide de Sqoop
Découvrez comment utiliser Apache Sqoop pour importer et exporter des données entre Azure SQL Database et Data Lake Store.

## <a name="what-is-sqoop"></a>Qu'est-ce que Sqoop ?
Les applications Big Data sont un choix naturel pour le traitement des données non structurées et semi-structurées, telles que les journaux et les fichiers. Toutefois, il peut également être nécessaire de traiter des données structurées stockées dans des bases de données relationnelles.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) est un outil conçu pour transférer des données entre des bases de données relationnelles et un référentiel Big Data, par exemple Data Lake Store. Vous pouvez l’utiliser pour importer des données à partir d’un système de gestion de base de données relationnelle (SGBDR), comme Azure SQL Database, dans Data Lake Store. Vous pouvez ensuite transformer et analyser les données à l’aide de charges de travail Big Data, puis exporter les données dans un SGBDR. Dans ce didacticiel, vous utilisez une base de données SQL Azure comme base de données relationnelle pour les opérations d’importation et d’exportation.

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte Azure Data Lake Store**. Pour savoir comment en créer un, consultez [Prise en main d'Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** ayant accès à un compte Data Lake Store. Voir [Créer un cluster HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Cet article suppose que vous disposez d’un cluster Linux HDInsight avec accès à Data Lake Store.
* **Base de données SQL Azure**. Pour savoir comment en créer un, consultez [Créer une base de données SQL Azure](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Les vidéos vous permettent-elles d’apprendre rapidement ?
[Regardez cette vidéo](https://mix.office.com/watch/1butcdjxmu114) pour savoir comment copier des données entre des objets blob Azure Storage et Data Lake Store à l’aide de DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Créer des exemples de tables dans la base de données SQL Azure
1. Pour commencer, créez deux exemples de tables dans la base de données SQL Azure. Utilisez [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou Visual Studio pour vous connecter à la base de données SQL Azure, puis exécutez les requêtes suivantes.

    **Créer Table1**

        CREATE TABLE [dbo].[Table1](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO

    **Créer Table2**

        CREATE TABLE [dbo].[Table2](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO
2. Dans **Table1**, ajoutez des exemples de données. Laissez **Table2** vide. Nous importerons des données de **Table1** dans Data Lake Store. Ensuite, nous exporterons les données de Data Lake Store vers **Table2**. Exécutez l'extrait de code suivant.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Utiliser Sqoop à partir d’un cluster HDInsight avec accès à Data Lake Store
Un cluster HDInsight dispose déjà des packages Sqoop. Si vous avez configuré le cluster HDInsight pour utiliser un Data Lake Store comme un espace de stockage supplémentaire, vous pouvez utiliser Sqoop (sans aucune modification de configuration) pour importer/exporter des données entre une base de données relationnelle (dans cet exemple, Azure SQL Database) et un compte Data Lake Store.

1. Pour ce didacticiel, nous supposons que vous avez créé un cluster Linux ; vous devez donc utiliser SSH pour vous connecter au cluster. Consultez [Connexion à un cluster HDInsight sous Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Vérifiez si vous pouvez accéder au compte Data Lake Store à partir du cluster. Exécutez la commande suivante depuis une invite SSH :

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Vous devriez accéder à une liste des fichiers/dossiers contenus dans le compte Data Lake Store.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importer des données à partir d’Azure SQL Database dans Data Lake Store
1. Accédez au répertoire dans lequel se trouvent les packages Sqoop. En règle générale, il s’agit de `/usr/hdp/<version>/sqoop/bin`.
2. Importez les données de **Table1** dans le compte Data Lake Store. Utilisez la syntaxe suivante :

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Notez que l’espace réservé **sql-database-server-name** représente le nom du serveur sur lequel s’exécute la base de données SQL Azure. L’espace réservé **sql-database-name** désigne le nom de la base de données.

    Par exemple,


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Vérifiez que les données ont été transférées vers le compte Data Lake Store. Exécutez la commande suivante :

        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    La sortie suivante s'affiche.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Chaque fichier **part-m-*** correspond à une ligne dans la table source **Table1**. Vous pouvez afficher le contenu des fichiers part-m-* à vérifier.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Exporter les données de Data Lake Store dans la base de données SQL Azure
1. Exportez les données du compte Data Lake Store vers la table vide, **Table2**, dans la base de données SQL Azure. Utilisez la syntaxe suivante.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Par exemple,


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Vérifiez que les données ont été chargées sur la table de la base de données SQL. Utilisez [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou Visual Studio pour vous connecter à la base de données SQL Azure, puis exécutez la requête suivante.

        SELECT * FROM TABLE2

    Vous devriez obtenir le résultat suivant.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Considérations de performances lors de l’utilisation de Sqoop

Pour optimiser les performances de votre tâche Sqoop pour copier des données vers Data Lake Store, consultez le [document de performance Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Voir aussi
* [Copier des données d’objets blob Azure Storage vers Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
