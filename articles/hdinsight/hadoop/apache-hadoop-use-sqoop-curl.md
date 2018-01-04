---
title: Utiliser Hadoop Sqoop avec Curl dans HDInsight - Azure | Microsoft Docs
description: "Découvrez comment transmettre à distance des travaux Sqoop vers HDInsight à l’aide de Curl."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 08e7288d4d9d06f3a74464817892c2b03ae8a86c
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Exécution de travaux Sqoop avec Hadoop dans HDInsight via Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Apprenez à utiliser Curl pour exécuter des tâches Sqoop sur un cluster Hadoop dans HDInsight.

Curl est utilisé pour illustrer comment interagir avec HDInsight en utilisant des demandes HTTP brutes pour exécuter, analyser et récupérer des travaux Sqoop. Cela fonctionne à l’aide de l’API REST WebHCat (anciennement Templeton) fournie par votre cluster HDInsight.

## <a name="prerequisites"></a>configuration requise
Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Suivez la procédure [Utiliser Sqoop avec Hadoop dans HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database) pour configurer un environnement avec un cluster HDInsight et une base de données SQL Azure.
* [Curl](http://curl.haxx.se/). Curl est un outil pour transférer des données depuis ou vers un cluster HDInsight.
* [jq](http://stedolan.github.io/jq/). L’utilitaire jq est utilisé pour traiter les données JSON renvoyées à partir de demandes REST.

## <a name="submit-sqoop-jobs-by-using-curl"></a>Envoi de travaux Sqoop avec Curl
> [!NOTE]
> Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les demandes en fournissant le nom d'utilisateur et le mot de passe de l'administrateur du cluster HDInsight. Vous devez également utiliser le nom du cluster dans l’URI (Uniform Resource Identifier) utilisé pour envoyer les demandes au serveur.
> 
> Pour les commandes de cette section, remplacez **USERNAME** par l’utilisateur à authentifier sur le cluster et **PASSWORD** par le mot de passe du compte d’utilisateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
> 
> L’API REST est sécurisée à l’aide de l’ [authentification de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours effectuer les demandes à l’aide du protocole Secure HTTP (HTTPS) pour aider à vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.
> 
> 

1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Vous devez recevoir une réponse ayant l'aspect suivant :

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    Les paramètres utilisés dans cette commande sont les suivants :
   
   * **-u** : le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande.
   * **-G** : indique qu’il s’agit d’une demande GET.
     
     Le début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, est le même pour toutes les demandes. Le chemin d’accès, **/status**, indique que la demande doit renvoyer le statut de WebHCat (également appelé Templeton) au serveur. 
2. Pour envoyer un travail Sqoop, utilisez la commande suivante :

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-d** : étant donné que `-G` n’est pas utilisé, la demande passe par défaut à la méthode POST. `-d` spécifie les valeurs de données envoyées avec la demande.

        * **user.name** : l’utilisateur qui exécute la commande.

        * **command** : commande Sqoop à exécuter.

        * **statusdir** : le répertoire où seront enregistrés les statuts de cette tâche.

    Cette commande doit retourner un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche.

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. Pour vérifier le statut de la tâche, utilisez la commande suivante. Remplacez **JOBID** par la valeur retournée à l’étape précédente. Par exemple, si la valeur de retour était `{"id":"job_1415651640909_0026"}`, le **JOBID** est `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Si le travail est terminé, l’état est **TERMINÉ**.
   
   > [!NOTE]
   > Cette demande Curl retourne un document JSON (JavaScript Object Notation) avec des informations sur la tâche ; jq est utilisé pour récupérer uniquement la valeur de statut.
   > 
   > 
4. Une fois que le statut de la tâche est passé à **TERMINÉ**, vous pouvez récupérer les résultats depuis le stockage blob Azure. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie ; dans notre cas, **wasb:///exemple/data/sqoop/curl**. Cette adresse stocke la sortie de la tâche dans le répertoire **exemple/data/sqoop/curl** sur le conteneur de stockage par défaut utilisé par votre cluster HDInsight.
   
    Vous pouvez utiliser le portail Azure pour accéder aux objets BLOB stderr et stdout.  Vous pouvez également utiliser Microsoft SQL Server Management Studio pour vérifier les données chargées vers la table log4jlogs.

## <a name="limitations"></a>Limites
* Exportation en bloc : avec HDInsight sous Linux, le connecteur Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou la base de données SQL Azure ne prend pas en charge les insertions en bloc.
* Traitement par lots : avec HDInsight sous Linux, lorsque vous utilisez le commutateur `-batch` pour effectuer des insertions, Sqoop effectue plusieurs insertions plutôt qu’un traitement par lots des opérations d’insertion.

## <a name="summary"></a>Résumé
Comme illustré dans ce document, vous pouvez utiliser une demande HTTP brute pour exécuter, surveiller et afficher les résultats des travaux Sqoop sur votre cluster HDInsight.

Pour plus d’informations sur l’interface REST utilisée dans cet article, consultez le document <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API guide</a> (Guide de l’API REST Sqoop).

## <a name="next-steps"></a>étapes suivantes
Pour obtenir des informations générales sur Hive avec HDInsight :

* [Utilisation de Sqoop avec Hadoop dans HDInsight](hdinsight-use-sqoop.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour d’autres articles HDInsight impliquant curl :
 
* [Créer des clusters Hadoop à l’aide de l’API REST Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Exécuter des requêtes Hive avec Hadoop dans HDInsight à l’aide de REST](apache-hadoop-use-hive-curl.md)
* [Exécution des tâches MapReduce avec Hadoop sur HDInsight avec REST](apache-hadoop-use-mapreduce-curl.md)
* [Exécution à distance des tâches Pig avec Hadoop sur HDInsight à l’aide de Curl](apache-hadoop-use-pig-curl.md)



