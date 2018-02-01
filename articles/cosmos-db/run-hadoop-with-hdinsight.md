---
title: "Exécuter un travail Hadoop avec Azure Cosmos DB et HDInsight | Microsoft Docs"
description: "Découvrez comment exécuter un travail Hive, Pig ou MapReduce simple avec Azure Cosmos DB et Azure HDInsight."
services: cosmos-db
author: dennyglee
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 06f0ea9d-07cb-4593-a9c5-ab912b62ac42
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 01/19/2018
ms.author: denlee
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 181954e4657166db8aa94021ad093437d8c7abfd
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="Azure Cosmos DB-HDInsight"></a>Exécuter un travail Apache Hive, Pig ou Hadoop avec Azure Cosmos DB et HDInsight
Ce didacticiel vous montre comment exécuter des travaux [Apache Hive][apache-hive], [Apache Pig][apache-pig] et [Apache Hadoop][apache-hadoop] MapReduce dans Azure HDInsight avec le connecteur Hadoop de Cosmos DB. Le connecteur Hadoop de Cosmos DB permet à Cosmos DB d’agir en tant que source et récepteur pour les travaux Hive, Pig et MapReduce. Ce didacticiel utilise Cosmos DB en tant que source de données et destination pour les travaux Hadoop.

> [!IMPORTANT] 
> Le connecteur Spark-Azure Cosmos DB est l’option recommandée pour connecter Azure HDInsight à Azure Cosmos DB. Pour plus d’informations, consultez [Accélérer l’analyse en temps réel des données volumineuses grâce au connecteur Spark-Azure Cosmos DB](spark-connector.md).

Après avoir terminé ce didacticiel, vous serez en mesure de répondre aux questions suivantes :

* Comment charger des données à partir de Cosmos DB à l’aide d’un travail Hive, Pig ou MapReduce ?
* Comment stocker des données dans Cosmos DB à l’aide d’un travail Hive, Pig ou MapReduce ?

Nous vous recommandons de commencer par visionner la vidéo suivante, où nous détaillons l’exécution d’un travail Hive avec Cosmos DB et HDInsight.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Use-Azure-DocumentDB-Hadoop-Connector-with-Azure-HDInsight/player]
>
>

Revenez ensuite à cet article pour obtenir des détails complets sur l’exécution de travaux d’analyse concernant vos données Cosmos DB.

> [!TIP]
> Ce didacticiel part du principe que vous avez déjà utilisé Apache Hadoop, Hive et/ou Pig. Si vous débutez avec Apache Hadoop, Hive et Pig, nous vous recommandons de commencer par consulter la [documentation Apache Hadoop][apache-hadoop-doc]. Ce didacticiel suppose également que vous avez déjà utilisé Cosmos DB et que vous possédez un compte Cosmos DB. Si vous n’avez jamais utilisé Cosmos DB ou si vous ne possédez pas de compte Cosmos DB, consultez notre page [Prise en main][getting-started].
>
>

Vous n'avez pas le temps de suivre le didacticiel et vous souhaitez seulement obtenir des scripts PowerShell d'exemple pour Hive, Pig et MapReduce ? Ce n’est pas un problème. Vous pouvez les obtenir [ici][hdinsight-samples]. Le téléchargement contient également les fichiers hql, pig et java pour ces exemples.

## <a name="NewestVersion"></a>Version la plus récente
<table border='1'>
    <tr><th>Version du connecteur Hadoop</th>
        <td>1.2.0</td></tr>
    <tr><th>URI du script</th>
        <td>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</td></tr>
    <tr><th>Date de modification</th>
        <td>26/04/2016</td></tr>
    <tr><th>Versions de HDInsight prises en charge</th>
        <td>3.1, 3.2.</td></tr>
    <tr><th>Journal des modifications</th>
        <td>Mise à jour du Kit de développement logiciel (SDK) Azure Cosmos DB vers la version 1.6.0</br>
            Prise en charge ajoutée pour les collections partitionnées en tant que source et récepteur</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>Configuration requise
Avant de suivre les instructions de ce didacticiel, assurez-vous de disposer des éléments suivants :

* Un compte Cosmos DB, une base de données et une collection de documents. Pour plus d’informations, consultez la page [Getting Started with Cosmos DB (Prise en main de Cosmos DB)][getting-started]. Importez des exemples de données dans votre compte Cosmos DB avec [l’outil d’importation de Cosmos DB][import-data].
* Débit. Les lectures et écritures à partir de HDInsight seront comptabilisées sur vos unités de demande allouées pour vos collections.
* Capacité pour une procédure stockée supplémentaire dans chaque collection de sortie. Les procédures stockées sont utilisées pour le transfert des documents résultants.
* Capacité pour les documents obtenus à partir des tâches Hive, Pig ou MapReduce.
* [*Facultatif*] Capacité pour une collection supplémentaire.

> [!WARNING]
> Pour empêcher la création d'une collection au cours d'une des tâches, vous pouvez imprimer les résultats vers stdout, enregistrer la sortie dans votre conteneur WASB ou sélectionner une collection existante. Si vous spécifiez une collection existante, des documents seront créés au sein de la collection et les documents existants ne seront affectés qu’en cas de conflit dans *ids*. **Le connecteur écrasera automatiquement les documents existants présentant des conflits d’ID**. Vous pouvez désactiver cette fonctionnalité en réglant l'option upsert sur false. Si l'option upsert est réglée sur false et qu'un conflit se produit, la tâche Hadoop échoue et une erreur liée à un conflit ID est renvoyée.
>
>

## <a name="ProvisionHDInsight"></a>Étape 1 : créer un cluster HDInsight
Ce didacticiel utilise une action de script à partir du portail Azure pour personnaliser votre cluster HDInsight. Dans ce didacticiel, nous allons utiliser le portail Azure pour créer votre cluster HDInsight. Pour connaître les instructions liées à l’utilisation des applets de commande PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight, consultez l’article [Personnaliser les clusters HDInsight à l’aide d’une action de script][hdinsight-custom-provision].

1. Connectez-vous au [portail Azure][azure-portal].
2. Cliquez sur **+ Nouveau** dans la barre de navigation en haut à gauche, recherchez **HDInsight** dans la barre de recherche supérieure du panneau Nouveau.
3. **HDInsight** publié par **Microsoft** s’affiche en haut des résultats. Cliquez dessus, puis cliquez sur **Créer**.
4. Dans le panneau de création du cluster HDInsight, saisissez le **nom du cluster** et sélectionnez **l’abonnement** de votre choix pour l’approvisionnement de cette ressource.

    <table border='1'>
        <tr><td>Nom du cluster</td><td>Nom du cluster.<br/>
Le nom DNS doit commencer et finir par un caractère alphanumérique et peut contenir des traits d’union.<br/>
Le champ doit être une chaîne comportant entre 3 et 63 caractères.</td></tr>
        <tr><td>Nom d'abonnement</td>
            <td>Si vous avez plusieurs abonnements Azure, sélectionnez celui qui hébergera votre cluster HDInsight. </td></tr>
    </table>
5. Cliquez sur **Sélectionner un type de cluster** et définissez les propriétés suivantes avec les valeurs spécifiées.

    <table border='1'>
        <tr><td>Type de cluster</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>Niveau de cluster</td><td><strong>Standard</strong></td></tr>
        <tr><td>Système d’exploitation</td><td><strong>Windows</strong></td></tr>
        <tr><td>Version</td><td>version la plus récente</td></tr>
    </table>

    Maintenant, cliquez sur **SÉLECTIONNER**.

    ![Fournir des détails du cluster initial Hadoop HDInsight][image-customprovision-page1]
6. Cliquez sur **Informations d’identification** pour configurer votre connexion et les informations d’identification d’accès à distance. Choisissez le **nom de connexion au cluster** et le **mot de passe de connexion au cluster**.

    Si vous souhaitez accéder à votre cluster à distance, sélectionnez *Oui* en bas du panneau et indiquez un nom d’utilisateur et un mot de passe.
7. Cliquez sur **Source de données** pour définir votre emplacement principal pour accéder aux données. Choisissez la **méthode de sélection** et spécifiez un compte de stockage existant ou créez-en un.
8. Dans le même panneau, spécifiez un **conteneur par défaut** et un **emplacement**. Et cliquez sur **SÉLECTIONNER**.

   > [!NOTE]
   > Pour de meilleures performances, sélectionnez un emplacement proche de la région de votre compte Cosmos DB
   >
   >
9. Cliquez sur **Tarification** pour sélectionner le nombre et le type de nœuds. Vous pouvez conserver la configuration par défaut et augmenter le nombre de nœuds Worker ultérieurement.
10. Cliquez sur **Configuration facultative**, puis sur **Actions de script** dans le panneau Configuration facultative.

     Dans Actions de script, tapez les informations suivantes pour personnaliser votre cluster HDInsight.

     <table border='1'>
         <tr><th>Propriété</th><th>Valeur</th></tr>
         <tr><td>NOM</td>
             <td>Indiquez un nom pour l'action de script.</td></tr>
         <tr><td>URI du script</td>
             <td>Spécifiez l'URI du script appelé pour personnaliser le cluster.</br></br>
Entrez : </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong>.</td></tr>
         <tr><td>Nœud principal</td>
             <td>Cliquer sur la case à cocher pour exécuter le script PowerShell sur le nœud principal.</br></br>
             <strong>Activez cette case à cocher</strong>.</td></tr>
         <tr><td>Worker</td>
             <td>Cliquer sur la case à cocher pour exécuter le script PowerShell sur le nœud Worker.</br></br>
             <strong>Activez cette case à cocher</strong>.</td></tr>
         <tr><td>Zookeeper</td>
             <td>Cliquer sur la case à cocher pour exécuter le script PowerShell sur le Zookeeper.</br></br>
             <strong>Inutile</strong>.
             </td></tr>
         <tr><td>parameters</td>
             <td>Spécifiez les paramètres, si le script le demande.</br></br>
             <strong>Aucun paramètre requis</strong>.</td></tr>
     </table>
11. Créez un **Groupe de ressources** ou utilisez un groupe de votre abonnement Azure.
12. À présent, activez la case à cocher **Épingler au tableau de bord** pour effectuer le suivi de son déploiement, puis cliquez sur **Créer**.

## <a name="InstallCmdlets"></a>Étape 2 : installer et configurer Azure PowerShell
1. Installez Azure PowerShell. Vous trouverez des instructions [ici][powershell-install-configure].

   > [!NOTE]
   > Pour les requêtes Hive, vous pouvez également utiliser l'éditeur Hive en ligne de HDInsight. Pour ce faire, connectez-vous au [portail Azure][azure-portal], cliquez sur **HDInsight** dans le volet gauche pour afficher la liste de vos clusters HDInsight. Cliquez sur le cluster sur lequel vous souhaitez exécuter des requêtes Hive, puis sur **Console de requête**.
   >
   >
2. Ouvrez l'environnement de script intégré Azure PowerShell :

   * Sur un ordinateur exécutant au moins Windows 8 ou Windows Server 2012, vous pouvez utiliser l’outil de recherche intégré. Dans l’écran d’accueil, tapez **powershell ise**, puis cliquez sur **Entrée**.
   * Sur un ordinateur exécutant une version antérieure à Windows 8 ou Windows Server 2012, utilisez le menu Démarrer. Dans la zone de recherche du menu Démarrer, tapez **Invite de commandes**, puis, dans la liste des résultats, cliquez sur **Invite de commandes**. Dans l’invite de commandes, tapez **powershell_ise**, puis cliquez sur **Entrée**.
3. Ajoutez votre compte Azure.

   1. Dans le volet de la console, tapez **Add-AzureAccount**, puis cliquez sur **Entrée**.
   2. Tapez l’adresse de messagerie associée à votre abonnement Azure, puis cliquez sur **Continuer**.
   3. Tapez le mot de passe de votre abonnement Azure.
   4. Cliquez sur **Se connecter**.
4. Le schéma suivant identifie les éléments importants de votre environnement de script Azure PowerShell.

    ![Diagramme d’Azure PowerShell][azure-powershell-diagram]

## <a name="RunHive"></a>Étape 3 : exécuter un travail Hive à l’aide d’Azure Cosmos DB et HDInsight
> [!IMPORTANT]
> Toutes les variables indiquées par < > doivent être renseignées à l’aide de vos paramètres de configuration.
>
>

1. Définissez les variables suivantes dans le volet Script PowerShell.

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"
2. <p>Commençons à construire votre chaîne de requête. Nous allons écrire une requête Hive qui prend les horodatages générés par le système de tous les documents (_ts) et les ID uniques (_rid) de tous les documents d’une collection Azure Cosmos DB, comptabilise tous les documents à la minute et stocke les résultats dans une nouvelle collection Azure Cosmos DB.</p>

    <p>Commençons par créer une table Hive à partir de notre collection Azure Cosmos DB. Ajoutez l’extrait de code suivant dans le volet Script PowerShell <strong>après</strong> l’extrait de code 1 . Veillez à inclure le paramètre de requête facultatif pour réduire vos documents à _ts et _rid.</p>

   > [!NOTE]
   > **L’attribution du nom DocumentDB.inputCollections n’était pas une erreur.** Oui, nous autorisons l'ajout de plusieurs collections en tant qu'entrée : </br>
   >
   >

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.

        # Create a Hive table using data from Azure Cosmos DB. Pass Azure Cosmos DB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

1. Créons maintenant une table Hive pour la collection de sortie. Les propriétés des documents de sortie seront le mois, le jour, l'heure, les minutes et le nombre total d'occurrences.

   > [!NOTE]
   > **Une fois encore, l’attribution du nom DocumentDB.outputCollections n’était pas une erreur.** Oui, nous autorisons l'ajout de plusieurs collections en tant que sortie : </br>
   > '*DocumentDB.outputCollections*' = '*\<DocumentDB Output Collection Name 1\>*,*\<DocumentDB Output Collection Name 2\>*' </br> Les noms de collection sont séparés sans espace, en utilisant uniquement une virgule. </br></br>
   > Les documents seront distribués en tourniquet (round robin), sur plusieurs collections. Un lot de documents sera stocké dans une collection, puis un deuxième lot de documents sera stocké dans la collection suivante, etc.
   >
   >

       # Create a Hive table for the output data to Azure Cosmos DB.
       $queryStringPart2 = "drop table DocumentDB_analytics; " +
                             "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                             "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                             "tblproperties ( " +
                                 "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                 "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                 "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                 "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "
2. Pour terminer, nous allons calculer les documents par mois, jour, heure et minutes et insérer les résultats dans la table Hive de sortie.

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "
3. Ajoutez l'extrait de script suivant pour créer une définition de tâche Hive à partir de la requête précédente.

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    Vous pouvez également utiliser le commutateur -File pour spécifier un fichier de script HiveQL sur HDFS.
4. Ajoutez l'extrait suivant pour enregistrer l'heure de début et soumettre la tâche Hive.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
5. Ajoutez le script suivant pour attendre la fin de la tâche Hive.

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
6. Ajoutez le code suivant pour imprimer la sortie standard et les heures de début et de fin.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
7. **Exécutez** votre nouveau script ! **Cliquez** sur le bouton d’exécution vert.
8. Vérifiez les résultats. Connectez-vous au [portail Azure][azure-portal].

   1. Cliquez sur <strong>Parcourir</strong> dans le panneau gauche. </br>
   2. Cliquez sur <strong>Tout</strong> en haut à droite du volet de navigation. </br>
   3. Recherchez les <strong>comptes Azure Cosmos DB</strong>, puis cliquez dessus. </br>
   4. Recherchez ensuite votre <strong>compte Azure Cosmos DB</strong>, puis votre <strong>base de données Azure Cosmos DB</strong> et votre <strong>collection Azure Cosmos DB</strong> associées à la collection de sortie spécifiée dans votre requête Hive.</br>
   5. Pour finir, cliquez sur <strong>Explorateur de documents</strong> sous <strong>Outils de développement</strong>.</br></p>

   Vous verrez les résultats de votre requête Hive.

   ![Résultats de la requête Hive][image-hive-query-results]

## <a name="RunPig"></a>Étape 4 : exécuter un travail Pig à l’aide de Cosmos DB et HDInsight
> [!IMPORTANT]
> Toutes les variables indiquées par < > doivent être renseignées à l’aide de vos paramètres de configuration.
>
>

1. Définissez les variables suivantes dans le volet Script PowerShell.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"
2. <p>Commençons à construire votre chaîne de requête. Nous allons écrire une requête Pig qui accepte les horodatages générés par le système de tous les documents (_ts) et des ID uniques (_rid) à partir d’une collection Azure Cosmos DB, comptabilise tous les documents à la minute, puis stocke les résultats dans une nouvelle collection Azure Cosmos DB.</p>
    <p>Chargez d’abord des documents Cosmos DB dans HDInsight. Ajoutez l’extrait de code suivant dans le volet Script PowerShell <strong>après</strong> l’extrait de code 1 . Veillez à ajouter une requête au paramètre de requête DocumentDB facultatif pour réduire vos documents à _ts et _rid.</p>

   > [!NOTE]
   > Oui, nous autorisons l'ajout de plusieurs collections en tant qu'entrée : </br>
   > '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*'</br> Les noms de collection sont séparés sans espace, en utilisant uniquement une virgule. </b>
   >
   >

    Les documents seront distribués en tourniquet (round robin), sur plusieurs collections. Un lot de documents sera stocké dans une collection, puis un deuxième lot de documents sera stocké dans la collection suivante, etc.

        # Load data from Cosmos DB. Pass the Azure Cosmos DB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
3. Nous allons ensuite calculer les documents par mois, jour, heure et minutes et le nombre total d'occurrences.

       # GROUP BY minute and COUNT entries for each.
       $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                           "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                           "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "
4. Pour terminer, nous allons stocker les résultats dans notre nouvelle collection de sortie.

   > [!NOTE]
   > Oui, nous autorisons l'ajout de plusieurs collections en tant que sortie : </br>
   > '\<DocumentDB Output Collection Name 1\>,\<DocumentDB Output Collection Name 2\>'</br> Les noms de collection sont séparés sans espace, en utilisant uniquement une virgule.</br>
   > Les documents seront distribués en séquence, sur plusieurs collections. Un lot de documents sera stocké dans une collection, puis un deuxième lot de documents sera stocké dans la collection suivante, etc.
   >
   >

        # Store output data to Cosmos DB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "
5. Ajoutez l'extrait de script suivant pour créer une définition de tâche Pig à partir de la requête précédente.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    Vous pouvez également utiliser le commutateur -File pour spécifier un fichier de script Pig sur HDFS.
6. Ajoutez l'extrait suivant pour enregistrer l'heure de début et soumettre la tâche Pig.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition
7. Ajoutez le script suivant pour attendre la fin de la tâche Pig.

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600
8. Ajoutez le code suivant pour imprimer la sortie standard et les heures de début et de fin.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
9. **Exécutez** votre nouveau script ! **Cliquez** sur le bouton d’exécution vert.
10. Vérifiez les résultats. Connectez-vous au [portail Azure][azure-portal].

    1. Cliquez sur <strong>Parcourir</strong> dans le panneau gauche. </br>
    2. Cliquez sur <strong>Tout</strong> en haut à droite du volet de navigation. </br>
    3. Recherchez les <strong>comptes Azure Cosmos DB</strong>, puis cliquez sur ces derniers. </br>
    4. Recherchez ensuite votre <strong>compte Azure Cosmos DB</strong>, puis votre <strong>base de données Azure Cosmos DB</strong> et votre <strong>collection Azure Cosmos DB</strong> associées à la collection de sortie spécifiée dans votre requête Pig.</br>
    5. Pour finir, cliquez sur <strong>Explorateur de documents</strong> sous <strong>Outils de développement</strong>.</br></p>

    Vous verrez les résultats de votre requête Pig.

    ![Résultats de la requête Pig][image-pig-query-results]

## <a name="RunMapReduce"></a>Étape 5 : exécuter une tâche MapReduce à l’aide d’Azure Cosmos DB et HDInsight
1. Définissez les variables suivantes dans le volet Script PowerShell.

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name
2. Nous allons exécuter une tâche MapReduce qui compte le nombre d’occurrences de chaque propriété de document de votre collection Azure Cosmos DB. Ajoutez cet extrait de script **après** l’extrait ci-dessus.

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

   > [!NOTE]
   > TallyProperties-v01.jar est fourni avec l’installation personnalisée du connecteur Hadoop Cosmos DB.
   >
   >
3. Ajoutez la commande suivante pour soumettre la tâche MapReduce.

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    En plus de la définition de la tâche MapReduce, fournissez également le nom du cluster HDInsight sur lequel vous souhaitez exécuter la tâche MapReduce, ainsi que les informations d'identification. Start-AzureHDInsightJob est un appel asynchrone. Pour vérifier que la tâche est terminée, utilisez la cmdlet *Wait-AzureHDInsightJob* .
4. Ajoutez la commande suivante pour déterminer si l'exécution de la tâche MapReduce génère des erreurs.

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
5. **Exécutez** votre nouveau script ! **Cliquez** sur le bouton d’exécution vert.
6. Vérifiez les résultats. Connectez-vous au [portail Azure][azure-portal].

   1. Cliquez sur <strong>Parcourir</strong> dans le panneau gauche.
   2. Cliquez sur <strong>Tout</strong> en haut à droite du volet de navigation.
   3. Recherchez les <strong>comptes Azure Cosmos DB</strong>, puis cliquez sur ces derniers.
   4. Recherchez ensuite votre <strong>compte Azure Cosmos DB</strong>, puis votre <strong>base de données Azure Cosmos DB</strong> et votre <strong>collection Azure Cosmos DB</strong> associés à la collection de sortie spécifiée dans votre tâche MapReduce.
   5. Pour finir, cliquez sur <strong>Explorateur de documents</strong> sous <strong>Outils de développement</strong>.

      Vous verrez les résultats de votre tâche MapReduce.

      ![Résultats de la requête MapReduce][image-mapreduce-query-results]

## <a name="NextSteps"></a>Étapes suivantes
Félicitations ! Vous venez d’exécuter vos premiers travaux Hive, Pig et MapReduce à l’aide d’Azure Cosmos DB et HDInsight.

Le code source de notre connecteur Hadoop est disponible gratuitement. Si vous êtes intéressé, vous pouvez apporter votre contribution sur [GitHub][github].

Pour en savoir plus, consultez les articles suivants :

* [Développer une application Java avec Azure Cosmos DB][sql-api-java-application]
* [Développement de programmes MapReduce en Java pour Hadoop dans HDInsight][hdinsight-develop-deploy-java-mapreduce]
* [Prise en main de Hadoop avec Hive dans HDInsight pour analyser l’utilisation des téléphones mobiles][hdinsight-get-started]
* [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Personnaliser des clusters HDInsight à l'aide d'une action de script][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: sql-api-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[github]: https://github.com/Azure/azure-documentdb-hadoop
[sql-api-java-application]: sql-api-java-application.md
[import-data]: import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md
[hdinsight-develop-deploy-java-mapreduce]:../hdinsight/hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:../hdinsight/hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight/hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-pig]:../hdinsight/hadoop/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0
