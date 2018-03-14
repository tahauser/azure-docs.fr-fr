---
title: "Utiliser Hadoop Hive avec Curl dans HDInsight - Azure | Microsoft Docs"
description: "Découvrez comment transmettre à distance des tâches Pig vers HDInsight à l'aide de Curl."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/26/2018
ms.author: larryfr
ms.openlocfilehash: dfe9efdb57a0ce2506abd251267f39020568d081
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Exécuter des requêtes Hive avec Hadoop dans HDInsight à l’aide de REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Découvrez comment utiliser l’API REST WebHCat pour exécuter des requêtes Hive avec Hadoop sur un cluster Azure HDInsight.

## <a name="prerequisites"></a>Prérequis

* Un cluster Hadoop Linux sur HDInsight version 3.4 ou ultérieure.

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Client REST. Ce document utilise des exemples Windows PowerShell et [Curl](http://curl.haxx.se/).

    > [!NOTE]
    > Azure PowerShell fournit des applets de commande dédiées pour utiliser Hive dans HDInsight. Pour plus d’informations, consultez le document [Exécution de requêtes Hive avec PowerShell](apache-hadoop-use-hive-powershell.md).

Ce document utilise également Windows PowerShell et [Jq](http://stedolan.github.io/jq/) pour traiter les données JSON retournées par les requêtes REST.

## <a id="curl"></a>Exécuter une requête Hive

> [!NOTE]
> Lorsque vous utilisez cURL ou toute autre communication REST avec WebHCat, vous devez authentifier les requêtes en fournissant le nom d’utilisateur et le mot de passe de l’administrateur du cluster HDInsight.
>
> L’API REST est sécurisée à l’aide de l’ [authentification de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Pour aider à vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur, procédez toujours aux requêtes via le protocole HTTP sécurisé (HTTPS).

1. Pour définir la connexion de cluster qui est utilisée par les scripts de ce document, utilisez l’une des commandes suivantes :

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Pour définir le nom du cluster, utilisez l’une des commandes suivantes :

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Pour vérifier que vous pouvez vous connecter à votre cluster HDInsight, utilisez l’une des commandes suivantes :

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status)
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    La réponse reçue est similaire au texte suivant :

    ```json
    {"status":"ok","version":"v1"}
    ```

    Les paramètres utilisés dans cette commande sont les suivants :

    * `-u` : nom d’utilisateur et mot de passe utilisés pour authentifier la requête.
    * `-G` : indique que la requête correspond à une opération GET.

   Le début de l’URL (`https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`) est le même pour toutes les requêtes. Le chemin (`/status`) indique que la requête doit retourner l’état de WebHCat (également appelé Templeton) au serveur. Vous pouvez également prendre connaissance de la version de Hive à l'aide de la commande suivante :

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Cette requête renvoie une réponse similaire au texte suivant :

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Utilisez la commande suivante pour créer une table nommée **log4jLogs** :

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Cette requête utilise la méthode POST, qui envoie des données dans le cadre de la requête à l’API REST. Les valeurs de données suivantes sont envoyées avec la requête :

     * `user.name` : utilisateur qui exécute la commande.
     * `execute` : instructions HiveQL qui doivent être exécutées.
     * `statusdir`: répertoire dans lequel l’état de cette tâche est écrit.

   Ces instructions effectuent les opérations suivantes :
   
   * `DROP TABLE` : si la table existe déjà, elle est supprimée.
   * `CREATE EXTERNAL TABLE` : crée une nouvelle table externe dans Hive. Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à l'emplacement d'origine.

     > [!NOTE]
     > Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, par exemple, par un processus de téléchargement de données automatisé ou une autre opération MapReduce.
     >
     > La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.

   * `ROW FORMAT` : formatage des données. Les champs de chaque journal sont séparés par un espace.
   * `STORED AS TEXTFILE LOCATION` : emplacement de stockage des données (répertoire example/data) stockées sous forme de texte.
   * `SELECT` : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette instruction renvoie la valeur **3**, car trois lignes contiennent cette valeur.

     > [!NOTE]
     > Notez que les espaces entre les instructions HiveQL sont remplacés par le caractère `+` lorsqu'ils sont utilisés avec Curl. Les valeurs citées qui contiennent un espace, tel que le séparateur, ne doivent pas être remplacées par `+`.

      Cette commande retourne un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche.

5. Pour vérifier le statut de la tâche, utilisez la commande suivante :

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Si la tâche est terminée, l’état est **TERMINÉ**.

6. Une fois que le statut de la tâche est passé à **TERMINÉ**, vous pouvez récupérer les résultats depuis le stockage blob Azure. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie. Dans notre cas `/example/rest`. Cette adresse stocke le résultat dans le répertoire `example/curl` dans le stockage en cluster par défaut.

    Vous pouvez répertorier et télécharger ces fichiers à l’aide de l' [interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Pour plus d’informations sur l’utilisation de l’interface CLI Azure avec Stockage Azure, consultez le document [Utiliser Azure CLI 2.0 avec Stockage Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs).

## <a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive avec HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Si vous utilisez Tez avec Hive, consultez les documents suivants pour les informations de débogage :

* [Utilisez la vue Tez Ambari sur HDInsight Linux](../hdinsight-debug-ambari-tez-view.md)

Pour plus d’informations sur l’API REST utilisée dans ce document, consultez le document [Référence WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


