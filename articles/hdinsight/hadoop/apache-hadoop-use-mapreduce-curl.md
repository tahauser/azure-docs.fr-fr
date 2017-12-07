---
title: Utiliser MapReduce et Curl avec Hadoop dans HDInsight - Azure | Microsoft Docs
description: "Apprenez à exécuter à distance des tâches MapReduce avec Hadoop sur HDInsight à l’aide de Curl."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: dd3e5904ee21ee74da5adaa65abd7865a82c8b36
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>Exécution des tâches MapReduce avec Hadoop sur HDInsight avec REST

Découvrez comment utiliser l’API REST WebHCat pour exécuter des tâches MapReduce sur un Hadoop sur le cluster HDInsight. Curl est utilisé pour illustrer comment interagir avec HDInsight en utilisant des requêtes HTTP brutes pour exécuter des tâches MapReduce.

> [!NOTE]
> Si vous vous êtes déjà familiarisé avec l’utilisation de serveurs Hadoop sous Linux, mais que vous découvrez HDInsight, consultez le document [Ce qu’il faut savoir sur Hadoop dans HDInsight sous Linux](../hdinsight-hadoop-linux-information.md).


## <a id="prereq"></a>Configuration requise

* Un cluster Hadoop sur HDInsight
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Exécution de tâches MapReduce à l'aide de Curl

> [!NOTE]
> Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les requêtes en fournissant le nom d’utilisateur et le mot de passe d’administrateur de cluster HDInsight. Vous devez utiliser le nom du cluster dans l’URI utilisé pour envoyer les requêtes au serveur.
>
> Pour les commandes de cette section, remplacez **admin** par l’utilisateur pour l’authentification auprès du cluster. Remplacez **CLUSTERNAME** par le nom de votre cluster. Lorsque vous y êtes invité, indiquez le mot de passe du compte de l’utilisateur.
>
> L’API REST est sécurisée à l’aide de l’ [authentification d’accès de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours effectuer les requêtes à l’aide de HTTPS pour vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.


1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    La réponse reçue est similaire au JSON suivant :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivants :

   * **-u**: indique le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande.
   * **-G**: indique que cette opération est une requête GET

   Le début de l’URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sera le même pour toutes les demandes.

2. Pour envoyer une tâche MapReduce, utilisez la commande suivante :

    ```bash
    curl -u admin -d user.name=admin -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
    ```

    La fin de l’URI (/mapreduce/jar) indique à WebHCat que cette demande lance une tâche MapReduce à partir d’une classe dans un fichier jar. Les paramètres utilisés dans cette commande sont les suivants :

   * **-d** : étant donné que `-G` n’est pas utilisé, la demande passe par défaut à la méthode POST. `-d` spécifie les valeurs de données envoyées avec la demande.
    * **user.name**: l’utilisateur qui exécute la commande
    * **jar**: l’emplacement du fichier jar contenant la classe à exécuter
    * **class**: la classe contenant la logique MapReduce
    * **arg**: les arguments à transmettre à la tâche MapReduce. Dans le cas présent, le fichier texte d’entrée et le répertoire utilisés pour la sortie

   Cette commande doit retourner un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche :

       {"id":"job_1415651640909_0026"}

3. Pour vérifier le statut de la tâche, utilisez la commande suivante :

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Remplacez **JOBID** par la valeur retournée à l’étape précédente. Par exemple, si la valeur de retour était `{"id":"job_1415651640909_0026"}`, le JOBID sera `job_1415651640909_0026`.

    Si la tâche est terminée, l’état est `SUCCEEDED`.

   > [!NOTE]
   > Cette requête Curl renvoie un document JSON avec des informations sur la tâche. Jq est utilisé pour récupérer uniquement la valeur d’état.

4. Lorsque l’état de la tâche passe à `SUCCEEDED`, vous pouvez récupérer les résultats depuis le Stockage Blob Azure. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie. Dans cet exemple, l’emplacement est `/example/curl`. Cette adresse stocke le résultat de la tâche dans le stockage par défaut du cluster : `/example/curl`.

Vous pouvez répertorier et télécharger ces fichiers à l’aide de l’[interface Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Pour plus d’informations sur l’utilisation des objets blob à partir de l’interface CLI Azure, consultez le document [Utiliser Azure CLI 2.0 avec Stockage Azure](../../storage/common/storage-azure-cli.md#create-and-manage-blobs).

## <a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur l’interface REST utilisée dans cet article, consultez la [Référence WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).