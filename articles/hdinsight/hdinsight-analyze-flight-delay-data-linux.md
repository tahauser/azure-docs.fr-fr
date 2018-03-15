---
title: "Analyse des données sur les retards de vol avec Hive dans HDInsight - Azure | Microsoft Docs"
description: "Découvrez comment utiliser Hive pour analyser les données sur les retards de vol avec HDInsight basé sur Linux, et exporter les données vers SQL Database à l’aide de Sqoop."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0c23a079-981a-4079-b3f7-ad147b4609e5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: b2eca1ab7eff006311269c78b1e507cb1417fcc6
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="analyze-flight-delay-data-by-using-hive-on-linux-based-hdinsight"></a>Analyse des données sur les retards de vol avec Hive dans HDInsight basé sur Linux

Découvrez comment analyser les données sur les retards de vol avec Hive sur HDInsight basé sur Linux, et comment exporter les données vers Azure SQL Database à l’aide de Sqoop.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur Azure HDInsight version 3.4 ou ultérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Prérequis

* **Un cluster HDInsight**. Consultez la rubrique [Bien démarrer avec Hadoop dans HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) pour savoir comment créer un cluster HDInsight basé sur Linux.

* **Base de données SQL Azure**. Vous allez utiliser une base de données SQL Azure comme magasin de données cible. Si vous n’avez pas de base de données SQL, consultez [Créer une base de données Azure SQL dans le portail Azure](../sql-database/sql-database-get-started.md).

* **Azure CLI**. Si vous n’avez pas installé l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure 1.0](../cli-install-nodejs.md) pour connaître les étapes à suivre.

## <a name="download-the-flight-data"></a>Téléchargement des données de vol

1. Accédez à [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].

2. Sur la page, sélectionnez les valeurs suivantes :

   | NOM | Valeur |
   | --- | --- |
   | Filtre année |2013 |
   | Filtre période |Janvier |
   | Champs |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Effacez tous les autres champs. 

3. Sélectionnez **Télécharger**.

## <a name="upload-the-data"></a>Téléchargement des données

1. Utilisez la commande suivante pour charger le fichier zip sur le nœud principal du cluster HDInsight :

    ```
    scp FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

    Remplacez *FILENAME* par le nom du fichier zip. Remplacez *USERNAME* par la connexion SSH du cluster HDInsight. Remplacez *CLUSTERNAME* par le nom du cluster HDInsight.

   > [!NOTE]
   > Si vous utilisez un mot de passe pour authentifier votre connexion SSH, vous êtes invité à l’indiquer. Si vous utilisez une clé publique, vous pouvez avoir besoin d’utiliser le paramètre `-i` et de spécifier le chemin de la clé privée correspondante. Par exemple, `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. À la fin du chargement, connectez-vous au cluster par l’intermédiaire de SSH :

    ```ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net```

    Pour en savoir plus, consultez la page [Se connecter à HDInsight (Hadoop) à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Utilisez la commande suivante pour décompresser le fichier .zip :

    ```
    unzip FILENAME.zip
    ```

    Cette commande extrait un fichier .csv d’environ 60 Mo.

4. Utilisez la commande suivante pour créer un répertoire de stockage HDInsight, puis copiez le fichier dans le répertoire :

    ```
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="create-and-run-the-hiveql"></a>Création et exécution du HiveQL

Suivez les étapes décrites ici pour importer des données à partir du fichier .csv dans une table Hive nommée **Delays**.

1. Pour créer et modifier un fichier nommé **flightdelays.hql**, utilisez la commande suivante :

    ```
    nano flightdelays.hql
    ```

    Utilisez le texte suivant comme contenu de ce fichier :

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. Pour enregistrer le fichier, utilisez Ctrl+X, puis Y.

3. Pour démarrer Hive et exécuter le fichier **flightdelays.hql**, utilisez la commande suivante :

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Dès l’exécution du script __flightdelays.hql__ terminée, utilisez la commande suivante pour ouvrir une session Beeline interactive :

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Lorsque vous recevez l’invite `jdbc:hive2://localhost:10001/>`, utilisez la requête suivante pour récupérer des données à partir des données relatives aux retards de vol qui ont été importées :

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Cette requête récupère la liste des villes qui ont enregistré des retards liés aux conditions météo, ainsi que le temps de retard moyen, et l’enregistre dans `/tutorials/flightdelays/output`. Sqoop lit ensuite les données à partir de cet emplacement et les exporte vers Azure SQL Database.

6. Pour quitter Beeline, entrez `!quit` à l’invite de commandes.

## <a name="create-a-sql-database"></a>Créer une base de données SQL

Si vous disposez déjà d’une base de données SQL, vous devez obtenir le nom du serveur. Pour trouver le nom du serveur sur le [portail Azure](https://portal.azure.com), sélectionnez **Bases de données SQL**, puis filtrez sur le nom de la base de données que vous décidez d’utiliser. Le nom du serveur est répertorié dans la colonne **SERVEUR** .

Si vous n’avez pas de base de données SQL, consultez [Créer une base de données Azure SQL dans le portail Azure](../sql-database/sql-database-get-started.md) pour en créer une. Enregistrez le nom du serveur qui est utilisé pour la base de données.

## <a name="create-a-sql-database-table"></a>Créer une table de base de données SQL

> [!NOTE]
> Il existe de nombreuses façons de se connecter à la base de données SQL et de créer une table. Les étapes suivantes utilisent [FreeTDS](http://www.freetds.org/) à partir du cluster HDInsight.


1. Pour installer FreeTDS, utilisez la commande suivante à partir d’une connexion SSH avec le cluster :

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Dès l’installation terminée, utilisez la commande suivante pour vous connecter au serveur SQL Database. Remplacez **serverName** par le nom du serveur de base de données SQL. Remplacez **adminLogin** et **adminPassword** par les informations d’identification de la base de données SQL. Remplacez **databaseName** par le nom de la base de données.

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -p 1433 -D <databaseName>
    ```

    Lorsque vous y êtes invité, entrez le mot de passe de connexion administrateur de SQL Database.

    Le résultat ressemble au texte suivant :

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. À l’invite de commandes `1>` , entrez les lignes suivantes :

    ```
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Une fois l’instruction `GO` entrée, les instructions précédentes sont évaluées. Cette requête crée une table nommée **delays** avec un index cluster.

    Utilisez la requête suivante pour vérifier que la table a été créée :

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    Le résultat ressemble au texte suivant :

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo     delays      BASE TABLE
    ```

5. Entrée `exit` at the `1>` .

## <a name="export-data-with-sqoop"></a>Exportation de données avec Sqoop

1. Utilisez la commande suivante pour vérifier que Sqoop peut voir votre base de données SQL :

    ```
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Cette commande retourne une liste de bases de données, dont la base de données dans laquelle vous avez créé précédemment la table des retards.

2. Utilisez la commande suivante pour exporter des données depuis hivesampletable dans la table des retards :

    ```
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop se connecte à la base de données qui contient la table des retards et exporte les données du répertoire `/tutorials/flightdelays/output` vers la table des retards.

3. Après l’exécution de la commande sqoop, connectez-vous à la base de données à l’aide de l’utilitaire tsql :

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Utilisez les instructions suivantes pour vérifier que les données ont été exportées dans la table des retards :

    ```
    SELECT * FROM delays
    GO
    ```

    Vous devez voir une liste des données dans la table. Tapez `exit` pour quitter l’utilitaire tsql.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir d’autres façons d’utiliser les données dans HDInsight, consultez les articles suivants :

* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation d’Oozie avec HDInsight][hdinsight-use-oozie]
* [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Développer des programmes MapReduce Java pour Hadoop sur HDInsight][hdinsight-develop-mapreduce]
* [Développer des programmes MapReduce de diffusion en continu Python pour HDInsight][hdinsight-develop-streaming]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
