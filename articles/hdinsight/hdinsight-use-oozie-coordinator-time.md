---
title: "Utilisation du coordinateur Hadoop Oozie basé sur le temps dans HDInsight | Microsoft Docs"
description: "Utilisez le coordinateur Hadoop Oozie basé sur le temps dans HDInsight, un service pour les données volumineuses. Découvrez comment définir des workflows et des coordinateurs Oozie, et envoyer des tâches."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00c3a395-d51a-44ff-af2d-1f116c4b1c83
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 0fa8e3630610913d909a75bf76236d120c8f1a2b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Utilisez le coordinateur Oozie basé sur le temps avec Hadoop dans HDInsight pour définir des workflows et coordonner des tâches
Dans cet article, vous découvrirez comment définir des workflows et des coordinateurs, et comment déclencher les tâches du coordinateur en fonction de l'heure. Il est utile de lire l’article [Utilisation d'Oozie avec HDInsight][hdinsight-use-oozie] avant cet article-ci. En plus d’Oozie, vous pouvez utiliser Azure Data Factory pour programmer des tâches. Pour en savoir plus sur Azure Data Factory, consultez la rubrique [Utilisation de Pig et Hive avec Data Factory](../data-factory/transform-data.md).

> [!NOTE]
> Cet article requiert un cluster HDInsight basé sur Windows. Pour plus d’informations sur l’utilisation d’Oozie, notamment sur les travaux à durée définie sur un cluster basé sur Linux, consultez [Utiliser Oozie avec Hadoop pour définir et exécuter un flux de travail dans HDInsight basé sur Linux](hdinsight-use-oozie-linux-mac.md)

## <a name="what-is-oozie"></a>Présentation d'Oozie
Apache Oozie est un système de workflow/coordination qui gère les tâches Hadoop. Il est intégré à la pile Hadoop et prend en charge les tâches Hadoop pour Apache MapReduce, Apache Pig, Apache Hive et Apache Sqoop. Il peut également être utilisé pour planifier des tâches propres à un système comme des programmes Java ou des scripts shell.

L’image suivante montre le workflow que vous allez implémenter :

![Diagramme du workflow][img-workflow-diagram]

Le workflow contient deux actions :

1. Une action Hive exécute un script HiveQL pour compter les occurrences de chaque type de niveau de journalisation dans un fichier journal log4j. Chaque journal log4j est constitué d’une ligne de champs qui contient un champ [LOG LEVEL] pour indiquer le type et la gravité, par exemple :

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    La sortie du script Hive doit ressembler à ceci :

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Pour plus d’informations sur Hive, consultez l’article [Utilisation de Hive avec HDInsight][hdinsight-use-hive].
2. Une action Sqoop exporte la sortie de l'action HiveQL vers une table dans la base de données SQL Azure. Pour plus d'informations sur Sqoop, consultez la rubrique [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Pour obtenir la liste des versions Oozie prises en charge sur les clusters HDInsight, consultez la rubrique [Nouveautés des versions de cluster fournies par HDInsight][hdinsight-versions].
>
>

## <a name="prerequisites"></a>Prérequis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un poste de travail sur lequel est installé Azure PowerShell**.

    > [!IMPORTANT]
    > La prise en charge de la gestion des ressources HDInsight par Azure PowerShell à l’aide d’Azure Service Manager est **déconseillée** ; elle sera supprimée le 1er janvier 2017. Dans ce document, la procédure repose sur les nouvelles applets de commande HDInsight qui fonctionnent avec Azure Resource Manager.
    >
    > Suivez les étapes indiquées dans [Installation et de configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) pour installer la dernière version d’Azure PowerShell. Si vous devez modifier certains scripts pour utiliser les nouvelles applets de commande fonctionnant avec Azure Resource Manager, consultez [Migration vers les outils de développement Azure Resource Manager pour les clusters HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) pour plus d’informations.

* **Un cluster HDInsight**. Pour plus d’informations sur la création d’un cluster HDInsight, consultez la rubrique ou [Création de clusters HDInsight][hdinsight-provision] ou [Prise en main de HDInsight][hdinsight-get-started]. Vous aurez besoin des données suivantes pour suivre ce didacticiel :

    <table border = "1">
    <tr><th>Propriété du cluster</th><th>Nom de la variable Windows PowerShell</th><th>Valeur</th><th>Description</th></tr>
    <tr><td>Nom du cluster HDInsight</td><td>$clusterName</td><td></td><td>Cluster HDInsight sur lequel vous exécutez ce didacticiel.</td></tr>
    <tr><td>Nom d'utilisateur du cluster HDInsight</td><td>$clusterUsername</td><td></td><td>Nom d'utilisateur de cluster HDInsight. </td></tr>
    <tr><td>Mot de passe de l'utilisateur du cluster HDInsight </td><td>$clusterPassword</td><td></td><td>Mot de passe de l'utilisateur du cluster HDInsight.</td></tr>
    <tr><td>Nom du compte de stockage Azure</td><td>$storageAccountName</td><td></td><td>Il s'agit du compte de stockage Azure disponible sur le cluster HDInsight. Pour ce didacticiel, utilisez le compte de stockage par défaut que vous avez indiqué au cours du processus d'approvisionnement du cluster.</td></tr>
    <tr><td>Nom du conteneur d'objets blob Azure</td><td>$containerName</td><td></td><td>Dans cet exemple, utilisez le conteneur de stockage d'objets blob Azure utilisé pour le système de fichiers de cluster HDInsight par défaut. Par défaut, il porte le même nom que le cluster HDInsight.</td></tr>
    </table>

* **Une base de données SQL Azure**. Vous devez configurer une règle de pare-feu pour que le serveur de base de données SQL autorise l'accès à partir de votre poste de travail. Pour des instructions sur la création d'une base de données SQL Azure et la configuration d'un pare-feu, consultez la rubrique [Prise en main de la base de données SQL Azure][sqldatabase-get-started]. Cet article inclut un script Windows PowerShell pour la création de la table de base de données SQL Azure dont vous avez besoin pour ce didacticiel.

    <table border = "1">
    <tr><th>Propriété de base de données SQL</th><th>Nom de la variable Windows PowerShell</th><th>Valeur</th><th>Description</th></tr>
    <tr><td>Nom du serveur de base de données SQL</td><td>$sqlDatabaseServer</td><td></td><td>Serveur de la base de données SQL vers lequel Sqoop exporte des données. </td></tr>
    <tr><td>Nom de connexion à la base de données SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nom de connexion à la base de données SQL.</td></tr>
    <tr><td>Mot de passe de connexion à la base de données SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Mot de passe de connexion à la base de données SQL.</td></tr>
    <tr><td>Nom de la base de données SQL</td><td>$sqlDatabaseName</td><td></td><td>Base de données SQL Azure vers laquelle Sqoop exporte des données. </td></tr>
    </table>

  > [!NOTE]
  > Par défaut, une base de données SQL Azure autorise des connexions aux services Azure tels que Azure HDinsight. Si ce paramètre de pare-feu est désactivé, vous devez l’activer depuis le portail Azure. Pour obtenir des instructions sur la création d'une base de données SQL et la configuration des règles de pare-feu, consultez la rubrique [Création et configuration d'une base de données SQL][sqldatabase-get-started].

> [!NOTE]
> Remplissez les valeurs dans les tables. Cela vous sera utile pour ce didacticiel.

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Définition du workflow Oozie et du script HiveQL lié
Les définitions des workflows Oozie sont écrites en hPDL (un langage de définition du processus XML). Le nom du fichier de workflow par défaut est *workflow.xml*.  Enregistrez le fichier de workflow en local et déployez-le ensuite sur le cluster HDInsight en utilisant Windows PowerShell plus loin dans ce didacticiel.

L'action Hive dans le workflow appelle un fichier de script HiveQL. Le fichier de script contient trois instructions HiveQL :

1. **L'instruction DROP TABLE** supprime la table Hive log4j si elle existe.
2. **L'instruction CREATE TABLE** crée une table externe Hive log4j pointant vers l'emplacement du fichier journal log4j.
3. **L'emplacement du fichier journal log4j**. Le séparateur de champ est « , ». Le séparateur de ligne par défaut est « \n ». La table externe Hive est utilisée pour éviter que le fichier de données soit supprimé de son emplacement d'origine au cas où vous souhaiteriez exécuter à plusieurs reprises le workflow Oozie.
4. **L'instruction INSERT OVERWRITE** compte les occurrences de chaque type de niveau de journalisation à partir de la table Hive log4j et enregistre la sortie dans un emplacement de stockage d’objets blob Azure.

> [!NOTE]
> Il existe un problème connu de chemin d'accès à Hive. Vous le rencontrez lors de l'envoi d'une tâche Oozie. Les instructions permettant d'y remédier sont disponibles dans le Wiki TechNet : [Erreur Hive HDInsight : impossible de renommer][technetwiki-hive-error].

**Définition du fichier de script HiveQL appelé par le workflow**

1. Créez un fichier texte avec le contenu suivant :

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Voici les trois variables utilisées dans le script :

   * ${hiveTableName}
   * ${hiveDataFolder}
   * ${hiveOutputFolder}

     Le fichier de définition du workflow (workflow.xml dans ce didacticiel) transmet ces valeurs à ce script HiveQL au moment de l'exécution.
2. Enregistrez le fichier sous **C:\Tutorials\UseOozie\useooziewf.hql** en utilisant l’encodage ANSI (ASCII). (Utilisez le Bloc-notes si votre éditeur de texte ne dispose pas de cette option.) Le fichier de script est déployé sur le cluster HDInsight plus loin dans ce didacticiel.

**Définition d'un workflow**

1. Créez un fichier texte avec le contenu suivant :

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>

        <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.job.queue.name</name>
                        <value>${queueName}</value>
                    </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>

        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.compress.map.output</name>
                        <value>true</value>
                    </property>
                </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
            </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>

        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>

        <end name="end"/>
    </workflow-app>
    ```

    Voici les deux actions définies dans le workflow : l'action de démarrage est *RunHiveScript*. Si cette action fonctionne *correctement*, l'action suivante est *RunSqoopExport*.

    RunHiveScript a plusieurs variables. Vous transmettez ces valeurs lors de l'envoi de la tâche Oozie à partir de votre poste de travail en utilisant Azure PowerShell.

    Variable de workflow

    <table border = "1">
    <tr><th>Variable de workflow</th><th>Description</th></tr>
    <tr><td>${jobTracker}</td><td>Spécifie l'URL du suivi des tâches Hadoop. Utilisez <strong>jobtrackerhost:9010</strong> sur les versions 3.0 et 2.0 de HDInsight.</td></tr>
    <tr><td>${nameNode}</td><td>Spécifie l'URL du nœud de nom Hadoop. Utilisez l’adresse wasb:// du système de fichiers par défaut, par exemple <i>wasb://&lt;nom_conteneur&gt;@&lt;nom_compte_de_stockage&gt;.blob.core.windows.net</i>.</td></tr>
    <tr><td>${queueName}</td><td>Spécifie le nom de la file d’attente auquel est envoyée la tâche. Utilisez <strong>Default</strong>.</td></tr>
    </table>

    Variables de l'action Hive

    <table border = "1">
    <tr><th>Variable d'action Hive</th><th>Description</th></tr>
    <tr><td>${hiveDataFolder}</td><td>Répertoire source pour la commande Hive de création d'une table.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Dossier de sortie pour l'instruction INSERT OVERWRITE.</td></tr>
    <tr><td>${hiveTableName}</td><td>Nom de la table Hive référençant les fichiers de données log4j.</td></tr>
    </table>

    Variables d'action Sqoop

    <table border = "1">
    <tr><th>Variable d'action Sqoop</th><th>Description</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>Chaîne de connexion à la base de données SQL.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>Table de la base de données SQL Azure vers laquelle les données sont exportées.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Dossier de sortie pour l'instruction INSERT OVERWRITE de Hive. Il s'agit du même dossier pour Sqoop Export (export-dir).</td></tr>
    </table>

    Pour plus d'informations sur le workflow Oozie et l'utilisation des actions de workflow, consultez la rubrique [Documentation sur Apache Oozie 4.0][apache-oozie-400] (pour la version 3.0 du cluster HDInsight) ou [Documentation sur Apache Oozie 3.3.2][apache-oozie-332] (pour la version 2.1 du cluster HDInsight).

1. Enregistrez le fichier sous **C:\Tutorials\UseOozie\workflow.xml** en utilisant l'encodage ANSI (ASCII). (Utilisez le Bloc-notes si votre éditeur de texte ne dispose pas de cette option.)

**Définition du coordinateur**

1. Créez un fichier texte avec le contenu suivant :

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
            <workflow>
                <app-path>${wfPath}</app-path>
            </workflow>
        </action>
    </coordinator-app>
    ```

    Ce fichier de définition comporte cinq variables :

   | Variable | Description |
   | --- | --- |
   | ${coordFrequency} |Heure de pause de la tâche. La fréquence est toujours exprimée en minutes. |
   | ${coordStart} |Heure de début de la tâche. |
   | ${coordEnd} |Heure de fin de la tâche. |
   | ${coordTimezone} |Oozie traite les tâches du coordinateur dans un fuseau horaire fixe sans passage à l’heure d’été (généralement représenté à l'aide de UTC). Ce fuseau horaire est appelé le « fuseau horaire du traitement d’Oozie ». |
   | ${wfPath} |Le chemin d'accès de workflow.xml.  Si le nom du fichier de workflow n'est pas celui par défaut (workflow.xml), vous devez le spécifier. |
2. Enregistrez le fichier sous **C:\Tutorials\UseOozie\coordinator.xml** en utilisant l'encodage ANSI (ASCII). (Utilisez le Bloc-notes si votre éditeur de texte ne dispose pas de cette option.)

## <a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Déploiement du projet Oozie et préparation du didacticiel
Exécutez un script Azure PowerShell pour effectuer les opérations suivantes :

* Copie du script HiveQL (useoozie.hql) dans le stockage d’objets blob Azure, wasb:///tutorials/useoozie/useoozie.hql.
* Copie de workflow.xml dans wasb:///tutorials/useoozie/workflow.xml.
* Copie de coordinator.xml dans wasb:///tutorials/useoozie/coordinator.xml.
* Copie du fichier de données (/example/data/sample.log) dans wasb:///tutorials/useoozie/data/sample.log.
* Créer une table de base de données SQL Azure pour stocker les données d'exportation de Sqoop. Le nom de la table est *log4jLogCount*.

**Présentation du stockage HDInsight**

HDInsight utilise le stockage d’objets blob Azure pour stocker les données. wasb:// est l’implémentation Microsoft du système de fichiers distribués Hadoop (HDFS) dans le stockage d’objets blob Azure. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

Lors de l'approvisionnement d'un cluster HDInsight, un compte Azure Storage et un conteneur spécifique de ce compte sont désignés en tant que système de fichiers par défaut, comme dans HDFS. En plus de ce compte de stockage, pendant le processus d’approvisionnement, vous pouvez ajouter des comptes de stockage supplémentaires à partir du même abonnement Azure ou à partir d’autres abonnements Azure. Pour plus d'instructions sur l’ajout des comptes de stockage supplémentaires, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision]. Pour simplifier le script Azure PowerShell utilisé dans ce didacticiel, tous les fichiers sont stockés dans le conteneur de système de fichiers par défaut, à l'emplacement */tutorials/useoozie*. Par défaut, ce conteneur porte le même nom que le cluster HDInsight.
La syntaxe est :

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [!NOTE]
> Seule la syntaxe *wasb://* est prise en charge dans le cluster HDInsight version 3.0. L’ancienne syntaxe *asv://* est prise en charge dans les clusters HDInsight 2.1 et 1.6, mais elle n’est pas prise en charge dans les clusters HDInsight 3.0.
>
> Le chemin d'accès wasb:// est un chemin d'accès virtuel. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage] .

Vous pouvez accéder à un fichier stocké dans le conteneur du système de fichiers par défaut à partir de HDInsight en utilisant l'un des URI suivants (workflow.xml est utilisé comme exemple) :

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Pour accéder directement au fichier à partir du compte de stockage, le nom de l'objet blob du fichier est :

    tutorials/useoozie/workflow.xml

**Présentation des tables interne et externe Hive**

Voici quelques éléments à connaître sur les tables interne et externe Hive :

* La commande CREATE TABLE crée une table interne, également nommée table gérée. Le fichier de données doit se trouver dans le conteneur par défaut.
* La commande CREATE TABLE déplace le fichier de données vers le dossier /hive/warehouse/<TableName> dans le conteneur par défaut.
* La commande CREATE EXTERNAL TABLE permet de créer une table externe. Le fichier de données peut se trouver à l'extérieur du conteneur par défaut.
* La commande CREATE EXTERNAL TABLE ne déplace pas le fichier de données.
* La commande CREATE EXTERNAL TABLE n'autorise aucun sous-dossier dans le dossier spécifié dans la clause LOCATION. C'est la raison pour laquelle le didacticiel réalise une copie du fichier sample.log.

Pour plus d’informations, consultez la rubrique [HDInsight : introduction aux tables interne et externe Hive][cindygross-hive-tables].

**Préparation du didacticiel**

1. Ouvrez Windows PowerShell ISE (dans l'écran d'accueil Windows 8, tapez **PowerShell_ISE**, puis cliquez sur **Windows PowerShell ISE**. Pour plus d'informations, consultez la page [Démarrage de Windows PowerShell sur Windows 8 et Windows][powershell-start]).
2. Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

    ```powershell
    Add-AzureAccount
    ```

    Vous êtes invité à entrer les informations d'identification de votre compte Azure. Cette méthode d'ajout de la connexion d'abonnement expire, et vous devez réexécuter l’applet de commande au bout de 12 heures.

   > [!NOTE]
   > Si vous disposez de plusieurs abonnements Azure et que vous ne souhaitez pas utiliser l’abonnement défini par défaut, utilisez l’applet de commande <strong>Select-AzureSubscription</strong> pour sélectionner un abonnement.

3. Copiez le script suivant dans le volet de script, puis définissez les six premières variables :

    ```powershell
    # WASB variables
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<BlobStorageContainerName>"

    # SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    # Oozie files for the tutorial
    $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
    $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
    $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here
    ```

    Pour plus d'informations sur les variables, consultez la section [Conditions préalables](#prerequisites) de ce didacticiel.

4. Ajoutez ce qui suit au script dans le volet de script :

    ```powershell
    # Create a storage context object
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    function uploadOozieFiles()
    {
        Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
        Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
        Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
        Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
    }

    function prepareHiveDataFile()
    {
        Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
        Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
    }

    function prepareSQLDatabase()
    {
        # SQL query string for creating log4jLogsCount table
        $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [Level] [nvarchar](10) NOT NULL,
                [Total] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
            [Level] ASC
            )
            )"

        #Create the log4jLogsCount table
        Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $cmdCreateLog4jCountTable
        $cmd.executenonquery()

        $conn.close()
    }

    # upload workflow.xml, coordinator.xml, and ooziewf.hql
    uploadOozieFiles;

    # make a copy of example/data/sample.log to example/data/log4j/sample.log
    prepareHiveDataFile;

    # create log4jlogsCount table on SQL database
    prepareSQLDatabase;
    ```

5. Cliquez sur **Exécuter le script** ou appuyez sur **F5** pour exécuter le script. La sortie doit ressembler à ceci :

    ![Sortie de la préparation du didacticiel][img-preparation-output]

## <a name="run-the-oozie-project"></a>Exécution du projet Oozie
Azure PowerShell ne fournit actuellement aucune applet de commande pour la définition de tâches Oozie. Vous pouvez utiliser l’applet de commande **Invoke-RestMethod** pour appeler les services web Oozie. L'API des services web Oozie est une API JSON REST HTTP. Pour plus d'informations sur l'API des services web Oozie, consultez la page [Documentation sur Apache Oozie 4.0][apache-oozie-400] (pour la version 3.0 du cluster HDInsight) ou [Documentation sur Apache Oozie 3.3.2][apache-oozie-332] (pour la version 2.1 du cluster HDInsight).

**Envoi d'une tâche Oozie**

1. Ouvrez Windows PowerShell ISE (dans l'écran d'accueil Windows 8, tapez **PowerShell_ISE**, puis cliquez sur **Windows PowerShell ISE**. Pour plus d'informations, consultez la page [Démarrage de Windows PowerShell sur Windows 8 et Windows][powershell-start]).
2. Copiez le script qui suit dans le volet de script et définissez les quatorze premières variables (sauf la variable **$storageUri**).

    ```powershell
    #HDInsight cluster variables
    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterUserPassword>"

    #Azure Blob storage (WASB) variables
    $storageAccountName = "<StorageAccountName>"
    $storageContainerName = "<BlobContainerName>"
    $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

    #Azure SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"

    #Oozie WF/coordinator variables
    $coordStart = "2014-03-21T13:45Z"
    $coordEnd = "2014-03-21T13:45Z"
    $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
    $coordTimezone = "UTC"    #UTC/GMT

    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"  
    $sqlDatabaseTableName = "log4jLogsCount"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
    ```

    Pour plus d'informations sur les variables, consultez la section [Conditions préalables](#prerequisites) de ce didacticiel.

    $coordstart et $coordend représentent l'heure de début et de fin du workflow. Pour connaître l'heure UTC/GMT, recherchez « heure utc » sur bing.com. La valeur de $coordFrequency est la fréquence en minutes à laquelle vous voulez exécuter le workflow.
3. Ajoutez ce qui suit au script : Cette partie définit la charge utile d'Oozie :

    ```powershell
    #OoziePayload used for Oozie web service submission
    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
            <name>nameNode</name>
            <value>$storageUrI</value>
        </property>

        <property>
            <name>jobTracker</name>
            <value>jobtrackerhost:9010</value>
        </property>

        <property>
            <name>queueName</name>
            <value>default</value>
        </property>

        <property>
            <name>oozie.use.system.libpath</name>
            <value>true</value>
        </property>

        <property>
            <name>oozie.coord.application.path</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>wfPath</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>coordStart</name>
            <value>$coordStart</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>$coordEnd</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>$coordFrequency</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>$coordTimezone</value>
        </property>

        <property>
            <name>hiveScript</name>
            <value>$hiveScript</value>
        </property>

        <property>
            <name>hiveTableName</name>
            <value>$hiveTableName</value>
        </property>

        <property>
            <name>hiveDataFolder</name>
            <value>$hiveDataFolder</value>
        </property>

        <property>
            <name>hiveOutputFolder</name>
            <value>$hiveOutputFolder</value>
        </property>

        <property>
            <name>sqlDatabaseConnectionString</name>
            <value>&quot;$sqlDatabaseConnectionString&quot;</value>
        </property>

        <property>
            <name>sqlDatabaseTableName</name>
            <value>$SQLDatabaseTableName</value>
        </property>

        <property>
            <name>user.name</name>
            <value>admin</value>
        </property>

    </configuration>
    "@
    ```

   > [!NOTE]
   > La principale différence avec le fichier de charge utile d'envoi du workflow est la variable **oozie.coord.application.path**. Lors de l'envoi d'une tâche de workflow, vous utilisez **oozie.wf.application.path** .

4. Ajoutez ce qui suit au script : Cette partie vérifie l'état du service Web Oozie :

    ```powershell
    function checkOozieServerStatus()
    {
        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerSatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerSatus..."

        if($oozieServerSatus -notmatch "NORMAL")
        {
            Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
        }
    }
    ```

5. Ajoutez ce qui suit au script : Cette partie crée une tâche Oozie :

    ```powershell
    function createOozieJob()
    {
        # create Oozie job
        Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
        Write-Host "`n--------`n$OoziePayload`n--------"
        $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieJobId = $jsonResponse[0].("id")
        Write-Host "Oozie job id is $oozieJobId..."

        return $oozieJobId
    }
    ```

   > [!NOTE]
   > Lors de l'envoi d'une tâche de workflow, vous devez passer un autre appel de services web pour démarrer la tâche une fois qu'elle est créée. Dans ce cas, la tâche du coordinateur est déclenchée par l'heure. La tâche va démarrer automatiquement.

6. Ajoutez ce qui suit au script : Cette partie vérifie le statut de la tâche Oozie :

    ```powershell
    function checkOozieJobStatus($oozieJobId)
    {
        # get job status
        Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

        Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
        $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")

        while($JobStatus -notmatch "SUCCEEDED|KILLED")
        {
            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")
        }

        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
        if($JobStatus -notmatch "SUCCEEDED")
        {
            Write-Host "Check logs at http://headnode0:9014/cluster for detais."
        }
    }
    ```

7. (Facultatif) Ajoutez ce qui suit au script :

    ```powershell
    function listOozieJobs()
    {
        Write-Host "Listing Oozie jobs..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

        write-host "Job ID                                   App Name        Status      Started                         Ended"
        write-host "----------------------------------------------------------------------------------------------------------------------------------"
        foreach($job in $response.workflows)
        {
            Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
        }
    }

    function ShowOozieJobLog($oozieJobId)
    {
        Write-Host "Showing Oozie job info..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
        write-host $response
    }

    function killOozieJob($oozieJobId)
    {
        Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
        $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
        $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
    }
    ```

8. Ajoutez ce qui suit au script :

    ```powershell
    checkOozieServerStatus
    # listOozieJobs
    $oozieJobId = createOozieJob($oozieJobId)
    checkOozieJobStatus($oozieJobId)
    # ShowOozieJobLog($oozieJobId)
    # killOozieJob($oozieJobId)
    ```

Supprimez les signes # si vous souhaitez exécuter d'autres fonctions.

9. Si vous disposez du cluster HDInsight version 2.1, remplacez « https://$clusterName.azurehdinsight.net:443/oozie/v2/ » par « https://$clusterName.azurehdinsight.net:443/oozie/v1/ ». Le cluster HDInsight version 2.1 ne prend pas en charge la version 2 des services Web.
10. Cliquez sur **Exécuter le script** ou appuyez sur **F5** pour exécuter le script. La sortie doit ressembler à ceci :

     ![Sortie du workflow exécuté par le didacticiel][img-runworkflow-output]
11. Connectez-vous à votre base de données SQL pour voir les données exportées.

**Vérification du journal des erreurs de la tâche**

Pour résoudre les problèmes d'un workflow, vous pouvez consulter le fichier journal Oozie dans C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log depuis le nœud principal du cluster. Pour plus d’informations sur le protocole RDP, consultez la rubrique [Administration de clusters HDInsight à l’aide du portail Azure][hdinsight-admin-portal].

**Réexécution du didacticiel**

Pour réexécuter le workflow, vous devez effectuer les opérations suivantes :

* Suppression du fichier de sortie du script Hive.
* Suppression des données dans la table log4jLogsCount.

Voici un exemple d'un script Windows PowerShell que vous pouvez utiliser :

```powershell
$storageAccountName = "<AzureStorageAccountName>"
$containerName = "<ContainerName>"

#SQL database variables
$sqlDatabaseServer = "<SQLDatabaseServerName>"
$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
$sqlDatabaseName = "<SQLDatabaseName>"
$sqlDatabaseTableName = "log4jLogsCount"

Write-host "Delete the Hive script output file ..." -ForegroundColor Green
$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
$conn.open()
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.connection = $conn
$cmd.commandtext = "delete from $sqlDatabaseTableName"
$cmd.executenonquery()

$conn.close()
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à définir un workflow Oozie et un coordinateur Oozie, et à exécuter une tâche de coordinateur Oozie en utilisant Azure PowerShell. Pour en savoir plus, consultez les articles suivants :

* [Prise en main de HDInsight][hdinsight-get-started]
* [Utilisation du stockage d’objets blob Azure avec HDInsight][hdinsight-storage]
* [Administration de HDInsight à l'aide d'Azure PowerShell][hdinsight-admin-powershell]
* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-java-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
