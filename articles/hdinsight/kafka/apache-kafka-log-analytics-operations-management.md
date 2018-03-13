---
title: Analyse des journaux pour Apache Kafka - Azure HDInsight | Microsoft Docs
description: "Découvrez comment utiliser Operations Management Suite pour analyser les journaux du cluster Apache Kafka sur Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: larryfr
ms.openlocfilehash: 6fcb925829e33704c94c96209a61346b0404e13b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analyser les journaux pour Apache Kafka sur HDInsight

Découvrez comment utiliser Microsoft Operations Management Suite pour analyser les journaux générés par Apache Kafka sur HDInsight.

## <a name="enable-oms-for-kafka"></a>Activer OMS pour Kafka

Les étapes permettant d’activer Log Analytics pour HDInsight sont les mêmes pour tous les clusters HDInsight. Utilisez les liens suivants pour apprendre à créer et à configurer les services requis :

1. Créez un espace de travail Log Analytics. Pour plus d’informations, consultez le document [Prise en main d’un espace de travail Log Analytics](https://docs.microsoft.com/azure/log-analytics).

2. Créez un cluster Kafka dans HDInsight. Pour plus d’informations, consultez le document [Démarrer avec Apache Kafka dans HDInsight](apache-kafka-get-started.md).

3. Configurez le cluster Kafka pour utiliser Log Analytics. Pour plus d’informations, consultez le document [Utiliser Log Analytics pour surveiller HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md).

    > [!NOTE]
    > Vous pouvez également configurer le cluster pour utiliser Log Analytics à l’aide de l’applet de commande `Enable-AzureRmHDInsightOperationsManagementSuite`. Cette applet de commande requiert les informations suivantes :
    >
    > * Le nom du cluster HDInsight.
    > * L’ID de l’espace de travail pour Log Analytics. Vous trouverez l’ID de l’espace de travail dans l’espace de travail OMS de votre espace de travail Log Analytics.
    > * La clé primaire de la connexion OMS. Pour trouver la clé primaire, sélectionnez votre instance Log Analytics, puis __Portail OMS__. Dans le portail OMS, sélectionnez __Paramètres__, __Sources connectées__, puis __Serveurs Linux__.


> [!IMPORTANT]
> Un délai de 20 minutes peut s’écouler avant que les données soient disponibles pour l’analyse des journaux.

## <a name="query-logs"></a>Journaux des requêtes

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail Log Analytics.

2. Sélectionnez __Recherche de journaux__. À ce stade, vous pouvez rechercher les données collectées par Kafka. Voici quelques exemples de recherches :

    * Utilisation du disque : `Type=Perf ObjectName="Logical Disk" (CounterName="Free Megabytes")  InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by   Computer interval 1HOUR`
    * Utilisation du processeur : `Type:Perf CounterName="% Processor Time" InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by Computer interval 1HOUR`
    * Messages entrants par seconde : `Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s interval 1HOUR`
    * Octets entrants par seconde : `Type=metrics_kafka_CL HostName_s="wn0-kafka" InstanceName_s="kafka-BrokerTopicMetrics-BytesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) interval 1HOUR`
    * Octets sortants par seconde : `Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-BytesOutPerSec-Count" |  measure avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) interval 1HOUR`

    > [!IMPORTANT]
    > Remplacez les valeurs de la requête par les informations spécifiques à votre cluster. Par exemple, `ClusterName_s` doit être définie sur le nom de votre cluster. La valeur `HostName_s` doit être définie sur le nom de domaine d’un nœud de travail dans le cluster.

    Vous pouvez également entrer `*` pour rechercher tous les types consignés. Actuellement, les fichiers journaux suivants sont disponibles pour les requêtes :

    | Type de journal | Description |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Mesures JMX Kafka |

    ![Image de la recherche de l’utilisation du processeur](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>étapes suivantes

Pour plus d’informations sur Log Analytics, consultez le document [Prise en main d’un espace de travail Log Analytics](../../log-analytics/log-analytics-get-started.md).

Pour plus d’informations sur l’utilisation de Kafka, consultez les documents suivants :

 * [Mettre en miroir de Kafka entre des clusters HDInsight](apache-kafka-mirroring.md)
 * [Augmenter l’évolutivité de Kafka sur HDInsight](apache-kafka-scalability.md)
 * [Utiliser la diffusion en continu Spark (DStream) avec Kafka](../hdinsight-apache-spark-with-kafka.md)
 * [Utiliser Spark Structured Streaming avec Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)