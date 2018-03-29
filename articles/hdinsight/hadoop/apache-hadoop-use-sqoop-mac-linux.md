---
title: Apache Sqoop avec Hadoop - Azure HDInsight | Documents Microsoft
description: Découvrez comment utiliser Apache Sqoop pour échanger des données entre Hadoop sur HDInsight et Azure SQL Database.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop, sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: larryfr
ms.openlocfilehash: f98345c012b8507818d0218fe43bc29badf0d4c8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Utiliser Apache Sqoop pour échanger des données entre Hadoop sur HDInsight et SQL Database

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Apache Sqoop pour échanger des données entre un cluster Hadoop dans Azure HDInsight et une base de données Azure SQL Database ou Microsoft SQL Server. Les étapes décrites dans ce document utilisent la commande `sqoop` directement à partir du nœud principal du cluster Hadoop. Vous utilisez SSH pour vous connecter au nœud principal et exécutez les commandes décrites dans ce document.

> [!IMPORTANT]
> Les étapes décrites dans ce document fonctionnent uniquement avec les clusters HDInsight sur Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!WARNING]
> Les étapes de ce document partent du principe que vous avez déjà créé une base de données SQL Azure nommée `sqooptest`.
>
> Ce document fournit des instructions T-SQL qui sont utilisées pour créer et interroger une table dans SQL Database. Il existe de nombreux clients avec lesquels vous pouvez utiliser ces instructions sur SQL Database. Nous recommandons les clients suivants :
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * Utilitaire [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)

## <a name="create-the-table-in-sql-database"></a>Créer la table dans la base de données SQL

> [!IMPORTANT]
> Si vous utilisez le cluster HDInsight et la base de données SQL Database créés à l’étape [Créer un cluster et une base de données SQL](hdinsight-use-sqoop.md), ignorez les étapes décrites dans cette section. La base de données et la table ont été créées dans le cadre de la procédure décrite dans le document [Créer un cluster et une base de données SQL](hdinsight-use-sqoop.md).

Utilisez un client SQL pour vous connecter à la base de données `sqooptest` dans votre SQL Database. Ensuite, utilisez le code T-SQL suivant pour créer une table nommée `mobiledata` :

```sql
CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder] [bigint])
GO
CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
GO
```

## <a name="sqoop-export"></a>Exportation de Sqoop

1. Utilisez SSH pour vous connecter au cluster HDInsight. Par exemple, la commande suivante établit une connexion au nœud principal d’un cluster nommé `mycluster` :

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pour vérifier que Sqoop peut voir votre SQL Database, utilisez la commande suivante :

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Lorsque vous y êtes invité, entrez le mot de passe de connexion à SQL Database.

    Cette commande retourne une liste des bases de données, notamment la base de données **sqooptest** utilisée précédemment.

3. Pour exporter des données de la table **hivesampletable** Hive vers la table **mobiledata** dans SQL Database, utilisez la commande suivante :

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Pour vérifier que les données ont été exportées, utilisez la requête suivante à partir de votre client SQL pour afficher les données exportées :

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    Cette commande répertorie les 50 lignes qui ont été importées dans la table.

## <a name="sqoop-import"></a>Importation de Sqoop

1. Utilisez la commande suivante pour importer des données à partir de la table **mobiledata** dans SQL Database vers le répertoire **wasb:///tutorials/usesqoop/importeddata** sur HDInsight :

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Les champs dans les données sont séparés par un caractère de tabulation et les lignes se terminent par un caractère de nouvelle ligne.

    > [!IMPORTANT]
    > Le chemin `wasb:///` fonctionne avec les clusters qui utilisent Stockage Azure comme stockage de cluster par défaut. Pour les clusters qui utilisent Azure Data Lake Store, utilisez plutôt `adl:///`.

2. Une fois l’importation terminée, utilisez la commande suivante pour répertorier les données dans le nouveau répertoire :

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Utilisation de SQL Server

Vous pouvez également utiliser Sqoop pour importer et exporter des données à partir de SQL Server. Les différences entre l’utilisation de la base de données SQL et SQL Server sont les suivantes :

* HDInsight et SQL Server doivent figurer sur le même réseau Azure Virtual Network.

    Pour voir un exemple, consultez le document [Connecter HDInsight à votre réseau local](./../connect-on-premises-network.md).

    Pour plus d’informations sur l’utilisation de HDInsight avec Azure Virtual Network, voir le document [Étendre HDInsight avec Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md). Pour plus d’informations sur Azure Virtual Network, voir [Présentation du réseau virtuel](../../virtual-network/virtual-networks-overview.md).

* Le serveur SQL Server doit également être configuré pour autoriser l’authentification SQL. Pour plus d’informations, consultez le document [Choisir un mode d’authentification](https://msdn.microsoft.com/ms144284.aspx).

* Vous devez configurer SQL Server pour qu’il accepte les connexions à distance. Pour plus d’informations, consultez le document [Résolution des problèmes de connexion au moteur de base de données SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

* Utilisez les instructions Transact-SQL suivantes pour créer la table **mobiledata**:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* Lors de la connexion à SQL Server à partir de HDInsight, vous devrez peut-être utiliser l’adresse IP du serveur SQL. Par exemple : 

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Limites

* Exportation en bloc : avec HDInsight sous Linux, le connecteur Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou Azure SQL Database ne prend pas en charge les insertions en bloc.

* Traitement par lots : avec HDInsight sous Linux, lorsque vous utilisez le commutateur `-batch` pour effectuer des insertions, Sqoop effectue plusieurs insertions plutôt qu’un traitement par lots des opérations d’insertion.

## <a name="next-steps"></a>Étapes suivantes

Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

* [Utilisation d'Oozie avec HDInsight](../hdinsight-use-oozie.md): utilisez l’action Sqoop dans un flux de travail Oozie.
* [Analyse des données sur les retards de vol avec HDInsight](../hdinsight-analyze-flight-delay-data.md): utilisez Hive pour analyser des données sur les retards de vol, puis utilisez Sqoop pour exporter ces données vers une base de données SQL Azure.
* [Téléchargement de données vers HDInsight](../hdinsight-upload-data.md): découvrez d'autres méthodes pour télécharger des données vers HDInsight ou le stockage d'objets blob Azure.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
