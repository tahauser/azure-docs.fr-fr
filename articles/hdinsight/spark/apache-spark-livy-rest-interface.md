---
title: "Utiliser Livy Spark pour envoyer des travaux à un cluster Spark sur Azure HDInsight | Documents Microsoft"
description: "Découvrez comment utiliser l’API REST Apache Spark pour envoyer des travaux Spark à distance à un cluster Azure HDInsight."
keywords: api rest apache spark,livy spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: nitinme
ms.openlocfilehash: 4f6c27fb651fc13877a889c26aa47903985625a0
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Utiliser l’API REST Spark Apache pour envoyer des travaux à distance à un cluster Spark HDInsight

Découvrez comment utiliser Livy, API REST Apache Spark servant à envoyer des travaux à distance à un cluster Spark HDInsight Azure. Pour obtenir une documentation détaillée, consultez [http://livy.incubator.apache.org/](http://livy.incubator.apache.org/).

Vous pouvez utiliser Livy pour exécuter des interpréteurs de commandes Spark interactifs ou soumettre des traitements par lots à exécuter sur Spark. Cet article traite de l’utilisation de Livy pour soumettre des traitements par lots. Les extraits de code dans cet article utilisent cURL pour effectuer des appels d’API REST au point de terminaison Livy Spark.

**Configuration requise :**

* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [cURL](http://curl.haxx.se/). Cet article utilise cURL pour montrer comment effectuer des appels d’API REST sur un cluster HDInsight Spark.

## <a name="submit-a-livy-spark-batch-job"></a>Envoyer un traitement par lots Livy Spark
Avant de soumettre un traitement par lots, vous devez télécharger le fichier .jar d’application sur le stockage associé au cluster. Pour ce faire, vous pouvez utiliser l’utilitaire en ligne de commande [**AzCopy**](../../storage/common/storage-use-azcopy.md). D’autres clients permettent également de charger des données. Pour en savoir plus à leur sujet, consultez [Téléchargement de données pour les travaux Hadoop dans HDInsight](../hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Exemples :**

* Si le fichier .jar se trouve sur le stockage de cluster (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Si vous souhaitez transmettre le nom de fichier .jar et le nom de classe dans un fichier d’entrée (dans cet exemple, input.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Obtenir des informations sur des lots Livy Spark en cours d’exécution sur le cluster
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Exemples :**

* Si vous souhaitez récupérer tous les lots Livy Spark en cours d’exécution sur le cluster :
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Si vous souhaitez récupérer un lot spécifique avec un ID de lot donné
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Supprimer un traitement par lots Livy Spark
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Exemple**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livy Spark et haute disponibilité
Livy assure une haute disponibilité des travaux Spark exécutés sur le cluster. Voici quelques exemples.

* Si le service Livy tombe en panne après avoir soumis un travail à distance à un cluster Spark, l’exécution du travail se poursuit en arrière-plan. La sauvegarde de Livy entraîne la restauration de l’état du travail et la création d’un rapport à ce sujet.
* Les blocs-notes Jupyter pour HDInsight sont alimentés par Livy sur le serveur principal. Si un bloc-notes exécute un travail Spark et que le service Livy est redémarré, le bloc-notes poursuit l’exécution des cellules de code. 

## <a name="show-me-an-example"></a>Afficher un exemple
Dans cette section, nous examinons des exemples d’utilisation de Livy Spark pour envoyer un traitement par lots, surveiller l’avancement du travail, puis supprimer celui-ci. L’application que nous utilisons dans cet exemple est décrite dans l’article [Créer une application Scala autonome et l’exécuter sur un cluster HDInsight Spark](apache-spark-create-standalone-application.md). Les étapes indiquées ici supposent que les conditions suivantes sont réunies :

* Vous avez déjà copié le fichier .jar de l’application dans le compte de stockage associé au cluster.
* CuRL est installé sur l’ordinateur sur lequel vous effectuez la procédure.

Procédez comme suit :

1. Vérifions tout d’abord que Livy Spark est en cours d’exécution sur le cluster. Pour ce faire, nous pouvons obtenir une liste des lots en cours d’exécution. Si vous exécutez un travail à l’aide de Livy pour la première fois, la valeur retournée doit être 0.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    La sortie doit ressembler à l’extrait de code suivant :
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Notez la dernière ligne qui indique **total:0**, ce qui suggère qu’aucun lot n’est en cours d’exécution.

2. Soumettons à présent un traitement par lots. L’extrait de code suivant utilise un fichier d’entrée (input.txt) pour transmettre le nom du fichier .jar et le nom de classe en tant que paramètres. Si vous exécutez ces étapes sur un ordinateur Windows, utiliser un fichier d’entrée est l’approche recommandée.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Les paramètres du fichier **input.txt** sont définis comme suit :
   
        { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    Le résultat doit ressembler à l’extrait de code suivant :
   
        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Notez la dernière ligne de la sortie qui indique **state:starting**. Elle indique également **id:0**. Ici, **0** correspond à l’ID du lot.

3. Vous pouvez maintenant récupérer l’état de ce lot à l’aide de l’ID correspondant.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Le résultat doit ressembler à l’extrait de code suivant :
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    La sortie indique maintenant **state:success**, ce qui suggère que le travail a été exécuté correctement.

4. Si vous le souhaitez, vous pouvez maintenant supprimer le lot.
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Le résultat doit ressembler à l’extrait de code suivant :
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    La dernière ligne de la sortie indique que le lot a été supprimé. Supprimer un travail, alors qu’il est en cours d’exécution, tue également le travail. Si vous supprimez un travail qui s’est terminé, les informations du travail sont entièrement supprimées.

## <a name="using-livy-spark-on-hdinsight-35-clusters"></a>Utilisation de Livy Spark sur des clusters HDInsight 3.5

Par défaut, un cluster HDInsight 3.5, désactive l’utilisation de chemins locaux pour accéder à des fichiers de données ou des fichiers JAR. Nous vous conseillons plutôt d'utiliser le chemin `wasb://` pour accéder aux fichiers JAR ou aux exemples de fichiers de données à partir du cluster. Si vous ne souhaitez pas utiliser le chemin d’accès local, vous devez mettre à jour la configuration Ambari en conséquence. Pour ce faire :

1. Accédez au portail Ambari pour le cluster. L’interface utilisateur Web d’Ambari est disponible sur votre cluster HDInsight à l’adresse https://**CLUSTERNAME**.azurehdidnsight.net, où CLUSTERNAME correspond au nom de votre cluster.

2. Dans le volet de navigation gauche, cliquez sur **Livy**, puis sur **Configurations**.

3. Sous **livy-default**, ajoutez le nom de la propriété `livy.file.local-dir-whitelist` et définissez sa valeur sur **"/"** si vous souhaitez autoriser un accès complet au système de fichiers. Si vous souhaitez autoriser uniquement l’accès à un répertoire spécifique, indiquez comme valeur le chemin d’accès à ce répertoire.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Envoi de travaux Livy pour un cluster dans un réseau virtuel Azure

Si vous vous connectez à un cluster HDInsight Spark à partir d’un réseau virtuel Azure, vous pouvez vous connecter directement à Livy sur le cluster. Dans ce cas, l’URL du point de terminaison Livy est `http://<IP address of the headnode>:8998/batches`. Ici, **8998** est le port sur lequel Livy s’exécute sur le nœud principal du cluster. Pour plus d’informations sur l’accès aux services sur des ports non publics, consultez [Ports utilisés par les services Hadoop sur HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Résolution des problèmes

Voici quelques problèmes que vous pouvez rencontrer lors de l’utilisation de Livy pour la soumission des travaux à distance à des clusters Spark.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>L’utilisation d’un fichier JAR externe à partir du stockage supplémentaire n’est pas prise en charge.

**Problème :** Si votre travail Livy Spark référence un fichier jar externe à partir du compte de stockage supplémentaire associé au cluster, le travail échoue.

**Résolution :** vérifiez que le fichier JAR que vous voulez utiliser est présent dans le stockage par défaut associé au cluster HDInsight.





## <a name="next-step"></a>Étape suivante

* [Documentation de l’API REST Livy](http://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)

