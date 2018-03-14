---
title: "Utiliser un flux structuré Apache Spark avec Event Hubs dans Azure HDInsight | Microsoft Docs"
description: "Créez un Exemple de diffusion en continu Apache Spark montrant comment envoyer un flux de données à Azure Event Hub, puis recevoir ces événements dans un cluster Spark HDInsight à l’aide d’une application Scala."
keywords: diffusion en continu apache spark,diffusion en continu spark, exemple spark,exemple de diffusion en continu apache spark,exemple azure event hubs
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: 14cc32c22653d5d8bd3dd5a1a41d2f64cfd8c73c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="apache-spark-structured-streaming-on-hdinsight-to-process-events-from-event-hubs"></a>Flux structuré Apache Spark sur HDInsight pour traiter des événements à partir d’Event Hubs

Dans cet article, vous apprenez à traiter les données de télémétrie en temps réel à l’aide du flux structuré Spark. Pour ce faire, vous devez effectuer les étapes suivantes :

1. Compiler et exécuter sur votre station de travail locale un exemple d’application de producteur d’événements qui génère des événements à envoyer à Event Hubs.
2. Utilisez [l’interpréteur de commandes Spark](apache-spark-shell.md) pour définir et exécuter une application de flux structuré Spark simple.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Kit de développement logiciel (SDK) Oracle Java. Vous pouvez l’installer à partir [d’ici](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* Apache Maven. Vous pouvez le télécharger [ici](https://maven.apache.org/download.cgi). Les instructions d’installation de Maven sont disponibles [ici](https://maven.apache.org/install.html).

## <a name="build-configure-and-run-the-event-producer"></a>Créer, configurer et exécuter le producteur d’événements
Dans cette tâche, vous clonez un exemple d’application qui crée des événements aléatoires et les envoie à un concentrateur d’événements configuré. Cet exemple d’application est disponible sur GitHub à l’adresse [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer).

1. Vérifiez que les outils git sont installés. Vous pouvez les télécharger à partir des [téléchargements GIT](http://www.git-scm.com/downloads). 
2. Ouvrez une invite de commandes ou un interpréteur de commandes de terminal et exécutez la commande suivante à partir du répertoire de votre choix pour cloner le projet.
        
        git clone https://github.com/hdinsight/eventhubs-sample-event-producer.git eventhubs-client-sample

3. Cette opération crée un dossier nommé eventhubs-client-sample. Dans l’interpréteur de commandes ou l’invite de commandes, accédez à ce dossier.
4. Exécutez Maven pour créer l’application à l’aide de la commande suivante :

          mvn package

5. Dans l’interpréteur de commandes ou l’invite de commandes, accédez au répertoire cible qui est créé et qui contient le fichier ``com-microsoft-azure-eventhubs-client-example-0.2.0.jar``.
6. Ensuite, vous devez créer la ligne de commande pour exécuter le producteur d’événements sur votre concentrateur d’événements. Pour ce faire, remplacez les valeurs de la commande comme suit :

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "<namespace>" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "<policyKey>" --message-length 32 --thread-count 1 --message-count -1

7. Par exemple, une commande complète devrait être semblable à ce qui suit :

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "sparkstreaming" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU=" --message-length 32 --thread-count 1 --message-count -1

8. La commande démarre et, si votre configuration est correcte, après quelques instants, vous pourrez voir une sortie liée aux événements qu’elle envoie à votre concentrateur d’événements. Ceci doit ressembler à ce qui suit :

        Map('policyKey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, 'eventhubsName -> hub1, 'policyName -> RootManageSharedAccessKey, 'eventhubsNamespace -> sparkstreaming, 'messageCount -> -1, 'messageLength -> 32, 'threadCount -> 1)
        Events per thread: -1 (-1 for unlimited)
        10 > Sun Jun 18 11:32:58 PDT 2017 > 1024 > 1024 > Sending event: ZZ93958saG5BUKbvUI9wHVmpuA2TrebS
        10 > Sun Jun 18 11:33:46 PDT 2017 > 2048 > 2048 > Sending event: RQorGRbTPp6U2wYzRSnZUlWEltRvTZ7R
        10 > Sun Jun 18 11:34:33 PDT 2017 > 3072 > 3072 > Sending event: 36Eoy2r8ptqibdlfCYSGgXe6ct4AyOX3
        10 > Sun Jun 18 11:35:19 PDT 2017 > 4096 > 4096 > Sending event: bPZma9V0CqOn6Hj9bhrrJT0bX2rbPSn3
        10 > Sun Jun 18 11:36:06 PDT 2017 > 5120 > 5120 > Sending event: H2TVD77HNTVyGsVcj76g0daVnYxN4Sqs

9. Laissez le producteur d’événement s’exécuter tandis que vous poursuivez avec les étapes suivantes.

## <a name="run-spark-shell-on-your-hdinsight-cluster"></a>Exécuter l’interpréteur de commandes Spark sur votre cluster HDInsight
Dans cette tâche, vous allez appliquer le protocole SSH dans le nœud principal de votre cluster HDInsight, lancer l’interpréteur de commandes Spark et exécuter une application de flux Spark qui récupère et traite les événements d’Event Hubs. 

À ce stade, votre cluster HDInsight doit être prêt. Si ce n’est pas le cas, vous devez attendre la fin de la configuration. Une fois qu’il est prêt, passez aux étapes suivantes :

1. Connectez-vous au cluster HDInsight à l’aide de SSH : Pour la marche à suivre, consultez [Se connecter à HDInsight (Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

5. L’application que vous créez requiert le package Event Hubs de flux Spark. Pour exécuter l’interpréteur de commandes Spark afin qu’il récupère automatiquement cette dépendance à partir du [répertoire Maven central](https://search.maven.org), vérifiez que l’approvisionnement que les packages remplacent avec le contenu Maven correspond à ce qui suit :

        spark-shell --packages "com.microsoft.azure:spark-streaming-eventhubs_2.11:2.1.5"

6. Une fois que l’interpréteur de commandes Spark a fini le chargement, vous devez voir :

        Welcome to
            ____              __
            / __/__  ___ _____/ /__
            _\ \/ _ \/ _ `/ __/  '_/
        /___/ .__/\_,_/_/ /_/\_\   version 2.1.1.2.6.2.3-1
            /_/
                
        Using Scala version 2.11.8 (OpenJDK 64-Bit Server VM, Java 1.8.0_151)
        Type in expressions to have them evaluated.
        Type :help for more information.

        scala> 

7. Copiez l’extrait de code suivant dans un éditeur de texte et modifiez-le de façon qu’il ait l’ensemble clé de stratégie/espace de nom approprié pour votre concentrateur d’événements.

        val eventhubParameters = Map[String, String] (
            "eventhubs.policyname" -> "RootManageSharedAccessKey",
            "eventhubs.policykey" -> "<policyKey>",
            "eventhubs.namespace" -> "<namespace>",
            "eventhubs.name" -> "hub1",
            "eventhubs.partition.count" -> "2",
            "eventhubs.consumergroup" -> "$Default",
            "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            "eventhubs.sql.containsProperties" -> "true"
            )
            
8. Si vous examinez votre point de terminaison compatible EventHub sous la forme suivante, la partie qui lit `iothub-xxxxxxxxxx` correspond au nom de votre espace de nom compatible EventHub, et peut être utilisée pour `eventhubs.namespace`. Le champ `SharedAccessKeyName` peut être utilisé pour `eventhubs.policyname`, et `SharedAccessKey` pour `eventhubs.policykey` : 

        Endpoint=sb://iothub-xxxxxxxxxx.servicebus.windows.net/;SharedAccessKeyName=xxxxx;SharedAccessKey=xxxxxxxxxx 

9. Collez l’extrait de code modifié dans l’invite scala> en attente et appuyez sur Entrée. La sortie doit ressembler à celle-ci :

        scala> val eventhubParameters = Map[String, String] (
            |       "eventhubs.policyname" -> "RootManageSharedAccessKey",
            |       "eventhubs.policykey" -> "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU",
            |       "eventhubs.namespace" -> "sparkstreaming",
            |       "eventhubs.name" -> "hub1",
            |       "eventhubs.partition.count" -> "2",
            |       "eventhubs.consumergroup" -> "$Default",
            |       "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            |       "eventhubs.sql.containsProperties" -> "true"
            |     )
        eventhubParameters: scala.collection.immutable.Map[String,String] = Map(eventhubs.sql.containsProperties -> true, eventhubs.name -> hub1, eventhubs.consumergroup -> $Default, eventhubs.partition.count -> 2, eventhubs.progressTrackingDir -> /eventhubs/progress, eventhubs.policykey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, eventhubs.namespace -> hdiz-docs-eventhubs, eventhubs.policyname -> RootManageSharedAccessKey)

10. Ensuite, vous commencez à créer une requête de flux structuré Spark en spécifiant la source. Collez le code suivant dans l’interpréteur de commandes Spark et appuyez sur Entrée.

        val inputStream = spark.readStream.
        format("eventhubs").
        options(eventhubParameters).
        load()

11. La sortie doit ressembler à celle-ci :

        inputStream: org.apache.spark.sql.DataFrame = [body: binary, offset: bigint ... 5 more fields]

12. Ensuite, créez la requête pour qu’elle écrive sa sortie dans la console. Pour ce faire, collez le code suivant dans l’interpréteur de commandes Spark et appuyez sur Entrée.

        val streamingQuery1 = inputStream.writeStream.
        outputMode("append").
        format("console").start().awaitTermination()

13. Vous devez voir certains lots démarrer avec une sortie identique à ce qui suit.

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        [Stage 0:>                                                          (0 + 2) / 2]

14. Cela est suivi par les résultats de la sortie du traitement de chaque petit lot d’événements. 

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        17/06/18 18:57:39 WARN TaskSetManager: Stage 1 contains a task of very large size (419 KB). The maximum recommended task size is 100 KB.
        +--------------------+------+---------+------------+---------+------------+----------+
        |                body|offset|seqNumber|enqueuedTime|publisher|partitionKey|properties|
        +--------------------+------+---------+------------+---------+------------+----------+
        |[7B 22 74 65 6D 7...|     0|        0|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   112|        1|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   224|        2|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   336|        3|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   448|        4|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   560|        5|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   672|        6|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   784|        7|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   896|        8|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1008|        9|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1120|       10|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1232|       11|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1344|       12|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1456|       13|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1568|       14|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1680|       15|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1792|       16|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1904|       17|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2016|       18|  1497734889|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2128|       19|  1497734889|     null|        null|     Map()|
        +--------------------+------+---------+------------+---------+------------+----------+
        only showing top 20 rows

15. À mesure que de nouveaux événements arrivent du producteur d’événements, ils sont traités par cette requête de flux structuré.
16. Veillez à supprimer votre cluster HDInsight lorsque vous avez terminé l’exécution de cet exemple.



## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble de Spark Streaming](apache-spark-streaming-overview.md)













