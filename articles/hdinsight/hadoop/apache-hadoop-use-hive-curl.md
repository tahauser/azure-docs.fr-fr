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
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: 07386c2fbb5fe4ce4fe1ca6844f6308cf59cbfb4
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Exécuter des requêtes Hive avec Hadoop dans HDInsight à l’aide de REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Découvrez comment utiliser l’API REST WebHCat pour exécuter des requêtes Hive avec Hadoop sur un cluster Azure HDInsight.

[Curl](http://curl.haxx.se/) est utilisé pour illustrer comment interagir avec HDInsight en utilisant des requêtes HTTP brutes. L’utilitaire [jq](http://stedolan.github.io/jq/) est utilisé pour traiter les données JSON renvoyées à partir de demandes REST.

> [!NOTE]
> Si vous êtes déjà familiarisé avec l’utilisation de serveurs Hadoop sous Linux, mais que vous découvrez HDInsight, consultez le document [Informations sur l’utilisation de Hadoop sur HDInsight sous Linux](../hdinsight-hadoop-linux-information.md).

## <a id="curl"></a>Exécuter des requêtes Hive

> [!NOTE]
> Lorsque vous utilisez cURL ou toute autre communication REST avec WebHCat, vous devez authentifier les requêtes en fournissant le nom d’utilisateur et le mot de passe de l’administrateur du cluster HDInsight.
>
> Pour les commandes de cette section, remplacez **admin** par l’utilisateur pour l’authentification auprès du cluster. Remplacez **CLUSTERNAME** par le nom de votre cluster. Quand vous y êtes invité, entrez le mot de passe du compte d’utilisateur.
>
> L’API REST est sécurisée à l’aide de l’ [authentification de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Pour aider à vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur, procédez toujours aux requêtes via le protocole HTTP sécurisé (HTTPS).

1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    La réponse reçue est similaire au texte suivant :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-u** : le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande.
    * **-G**: indique que la requête correspond à une opération GET.

   Le début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, est le même pour toutes les demandes. Le chemin d’accès, **/status**, indique que la demande doit renvoyer le statut de WebHCat (également appelé Templeton) au serveur. Vous pouvez également prendre connaissance de la version de Hive à l'aide de la commande suivante :

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    Cette requête renvoie une réponse similaire au texte suivant :

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Utilisez la commande suivante pour créer une table nommée **log4jLogs** :

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Les paramètres suivants sont utilisés avec cette demande :

   * **-d** : étant donné que `-G` n’est pas utilisé, la demande passe par défaut à la méthode POST. `-d` spécifie les valeurs de données envoyées avec la demande.

     * **user.name** : l’utilisateur qui exécute la commande.
     * **execute** : les instructions HiveQL qui doivent être exécutées.
     * **statusdir**: le répertoire dans lequel les statuts de cette tâche sont écrits.

   Ces instructions effectuent les opérations suivantes :
   
   * **DROP TABLE** : si la table existe déjà, elle est supprimée.
   * **CREATE EXTERNAL TABLE** : crée une table « externe » dans Hive. Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à l'emplacement d'origine.

     > [!NOTE]
     > Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, par exemple, par un processus de téléchargement de données automatisé ou une autre opération MapReduce.
     >
     > La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.

   * **ROW FORMAT** : le mode de formatage des données. Les champs de chaque journal sont séparés par un espace.
   * **STORED AS TEXTFILE LOCATION** : l’endroit où sont stockées les données (répertoire example/data) et indique qu’elles sont stockées sous forme de texte.
   * **SELECT** : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette instruction renvoie la valeur **3**, car trois lignes contiennent cette valeur.

     > [!NOTE]
     > Notez que les espaces entre les instructions HiveQL sont remplacés par le caractère `+` lorsqu'ils sont utilisés avec Curl. Les valeurs citées qui contiennent un espace, tel que le séparateur, ne doivent pas être remplacées par `+`.

   * **INPUT__FILE__NAME LIKE ’%25.log’** : cette instruction limite la recherche aux seuls fichiers se terminant par .log.

     > [!NOTE]
     > Notez que `%25` est la forme codée de l’URL de %, donc la condition réelle est `like '%.log'`. La valeur % doit être une URL encodée, car elle est traitée comme un caractère spécial dans les URL.

   Cette commande retourne un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche.

       {"id":"job_1415651640909_0026"}

3. Pour vérifier le statut de la tâche, utilisez la commande suivante :

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Remplacez **JOBID** par la valeur retournée à l’étape précédente. Par exemple, si la valeur de retour était `{"id":"job_1415651640909_0026"}`, le **JOBID** est `job_1415651640909_0026`.

    Si la tâche est terminée, l’état est **TERMINÉ**.

   > [!NOTE]
   > Cette requête Curl renvoie un document JSON (JavaScript Object Notation) avec des informations sur la tâche. Jq est utilisé pour récupérer uniquement la valeur d’état.

4. Une fois que le statut de la tâche est passé à **TERMINÉ**, vous pouvez récupérer les résultats depuis le stockage blob Azure. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie. Dans notre cas : **/example/curl**. Cette adresse stocke le résultat dans le répertoire **exemple/curl** dans le stockage par défaut du cluster.

    Vous pouvez répertorier et télécharger ces fichiers à l’aide de l' [interface de ligne de commande Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Pour plus d’informations sur l’utilisation de l’interface CLI Azure avec Stockage Azure, consultez le document [Utiliser Azure CLI 2.0 avec Stockage Azure](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli#create-and-manage-blobs).

5. Utilisez les instructions suivantes pour créer une table « interne » nommée **errorLogs**:

    ```bash
    curl -u admin -d user.name=admin -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    Ces instructions effectuent les opérations suivantes :

   * **CREATE TABLE IF NOT EXISTS** : crée une table, si elle n'existe pas déjà. Cette instruction crée une table interne, qui est stockée dans l’entrepôt de données Hive et gérée par Hive.

     > [!NOTE]
     > Contrairement aux tables externes, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.

   * **STORED AS ORC** : stocke les données au format ORC (Optimized Row Columnar). ORC est un format particulièrement efficace et optimisé pour le stockage de données Hive.
   * **INSERT OVERWRITE ... SELECT** : sélectionne des lignes de la table **log4jLogs** qui contiennent **[ERROR]**, puis insère les données dans la table **errorLogs**.
   * **SELECT** : sélectionne toutes les lignes de la nouvelle table **errorLogs**.

6. Utilisez l'identificateur de la tâche renvoyé pour vérifier l'état de la tâche. Une fois la tâche terminée, utilisez la CLI Azure, comme indiqué précédemment, pour télécharger et afficher les résultats. La sortie doit comporter trois lignes, qui contiennent toutes la valeur **ERROR**.

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


