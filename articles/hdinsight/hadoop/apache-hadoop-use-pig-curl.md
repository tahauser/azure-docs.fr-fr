---
title: Utiliser Hadoop Pig avec REST dans HDInsight - Azure | Documents Microsoft
description: "Découvrez comment utiliser REST pour exécuter des tâches Pig Latin sur un cluster Hadoop dans HDInsight Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ed5e10d1-4f47-459c-a0d6-7ff967b468c4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2018
ms.author: larryfr
ms.openlocfilehash: c2c441d92607a017b4451cd8a06fb78a5e8e7135
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-rest"></a>Exécution de tâches Pig avec Hadoop sur HDInsight à l’aide de REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Découvrez comment exécuter des tâches Pig Latin en effectuant des demandes REST pour un cluster Azure HDInsight. Curl est utilisé pour illustrer comment interagir avec HDInsight en utilisant l’API REST WebHCat.

> [!NOTE]
> Si vous connaissez déjà l’utilisation de serveurs Hadoop basés sur Linux, mais pas HDInsight, consultez la rubrique [Informations sur l’utilisation de HDInsight sur Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Configuration requise

* Un cluster Azure HDInsight (Hadoop sur HDInsight) Windows ou Linux

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Exécution de tâches Pig à l’aide de Curl

> [!NOTE]
> L’API REST est sécurisée à l’aide de l’ [authentification d’accès de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Pour garantir l’envoi en toute sécurité de vos informations d’identification sur le serveur, procédez toujours aux requêtes via le protocole HTTP sécurisé (HTTPS).
>
> Lorsque vous utilisez les commandes de cette section, remplacez `USERNAME` par l’utilisateur à authentifier sur le cluster et `PASSWORD` par le mot de passe du compte d’utilisateur. Remplacez `CLUSTERNAME` par le nom de votre cluster.
>


1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    La réponse JSON suivante doit s’afficher :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-u**: le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande
    * **-G** : indique que la requête correspond à une requête GET.

     Le début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, est le même pour toutes les demandes. Le chemin d’accès, **/status**, indique que la demande doit retourner le statut de WebHCat (également appelé Templeton) au serveur.

2. Utilisez le code suivant pour soumettre une tâche Pig Latin au cluster :

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-d** : étant donné que `-G` n’est pas utilisé, la demande passe par défaut à la méthode POST. `-d` spécifie les valeurs de données envoyées avec la demande.

    * **user.name**: l’utilisateur qui exécute la commande
    * **execute**: les instructions Pig Latin à exécuter
    * **statusdir** : le répertoire dans lequel les statuts de cette tâche sont écrits.

    > [!NOTE]
    > Notez que les espaces dans les instructions Pig Latin sont remplacées par le caractère `+` avec Curl.

    Cette commande doit retourner un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche, par exemple :

        {"id":"job_1415651640909_0026"}

3. Pour vérifier le statut de la tâche, utilisez la commande suivante

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Remplacez `JOBID` par la valeur renvoyée à l’étape précédente. Par exemple, si la valeur de retour était `{"id":"job_1415651640909_0026"}`, alors `JOBID` est `job_1415651640909_0026`.

    Si la tâche est terminée, l’état est **TERMINÉ**.

    > [!NOTE]
    > Cette requête Curl retourne un document JSON (JavaScript Object Notation) avec des informations sur la tâche et jq est utilisé pour récupérer uniquement la valeur d’état.

## <a id="results"></a>Affichage des résultats

Une fois que le statut de la tâche est passé à **TERMINÉ**, vous pouvez récupérer les résultats. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie. Dans notre cas `/example/pigcurl`.

HDInsight peut utiliser le stockage Azure ou Azure Data Lake Store comme magasin de données par défaut. Il existe différentes façons d’obtenir les données en fonction de celles que vous souhaitez utiliser. Pour plus d’informations, consultez la section relative au stockage, dans le document [Informations sur HDInsight sous Linux](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store).

## <a id="summary"></a>Résumé

Comme illustré dans ce document, vous pouvez utiliser les demandes HTTP brutes pour exécuter, surveiller et afficher les résultats de tâches Pig sur votre cluster HDInsight.

Pour plus d’informations sur l’interface REST utilisée dans cet article, consultez la [Référence WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Pig dans HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
