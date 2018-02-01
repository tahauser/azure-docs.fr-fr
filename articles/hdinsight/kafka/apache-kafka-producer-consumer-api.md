---
title: Utiliser les API de producteur et de consommateur Apache Kafka - Azure HDInsight | Microsoft Docs
description: "Découvrez comment utiliser les API de consommateur et de producteur Apache Kafka avec Kafka sur HDInsight. Ces API permettent de développer des applications qui écrivent et lisent à partir d’Apache Kafka."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: larryfr
ms.openlocfilehash: b57745d6bd993a993e923c964327d9071e745413
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>API de producteur et de consommateur Apache Kafka

Découvrez comment créer une application qui utilise les API de consommateur et de producteur Kafka avec Kafka sur HDInsight.

Pour plus d’informations sur les API, consultez [API producteur](https://kafka.apache.org/documentation/#producerapi) et [API consommateur](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Configuration de l'environnement de développement

Les composants suivants doivent être installés dans votre environnement de développement :

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html), ou un équivalent, par exemple, OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Un client SSH et la commande `scp`. Pour plus d’informations, consultez le document [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Configurer votre environnement de déploiement

Cet exemple nécessite Kafka sur HDInsight 3.6. Pour découvrir comment créer un cluster Kafka sur HDInsight, consultez le document [Démarrer avec Apache Kafka sur HDInsight](apache-kafka-get-started.md).

## <a name="build-and-deploy-the-example"></a>Générer et déployer l’exemple

1. Téléchargez les exemples à partir de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Accédez à l’emplacement du répertoire `Producer-Consumer` et utilisez la commande suivante :

    ```
    mvn clean package
    ```

    Cette commande crée un répertoire nommé `target`, qui contient un fichier nommé `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Utilisez les commandes suivantes pour copier le fichier `kafka-producer-consumer-1.0-SNAPSHOT.jar` dans votre cluster HDInsight :
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Remplacez **SSHUSER** par l’utilisateur SSH pour votre cluster, et remplacez **CLUSTERNAME** par le nom de votre cluster. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.

## <a id="run"></a> Exécuter l’exemple

1. Pour ouvrir une connexion SSH au cluster, utilisez la commande suivante :

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Remplacez **SSHUSER** par l’utilisateur SSH pour votre cluster, et remplacez **CLUSTERNAME** par le nom de votre cluster. Si vous y êtes invité, entrez le mot de passe du compte d’utilisateur SSH. Pour en savoir plus sur l’utilisation de `scp` avec HDInsight, voir [Utiliser SSH avec Hadoop - Azure HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pour créer les rubriques Kafka utilisées par cet exemple, exécutez les commandes suivantes :

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Remplacez __your cluster name__ par le nom de votre cluster HDInsight. À l’invite, entrez le mot de passe du compte de connexion au cluster HDInsight.

    > [!NOTE]
    > Si votre connexion de cluster est différente de la valeur par défaut `admin`, remplacez la valeur `admin` dans les commandes précédentes par le nom de connexion de votre cluster.

3. Pour exécuter le producteur et écrire des données dans la rubrique, utilisez la commande suivante :

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. Une fois que le producteur a terminé, utilisez la commande suivante pour lire à partir de la rubrique :
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    Les enregistrements lus et le nombre d’enregistrements s’affichent.

5. Utilisez __Ctrl + C__ pour quitter le consommateur.

### <a name="multiple-consumers"></a>Consommateurs multiples

Les consommateurs Kafka utilisent un groupe de consommateurs lors de la lecture des enregistrements. L’utilisation du même groupe avec plusieurs consommateurs permet des lectures à charge équilibrée à partir d’une rubrique. Chaque consommateur dans le groupe reçoit une partie des enregistrements.

L’application consommatrice accepte un paramètre utilisé comme ID de groupe. Par exemple, la commande suivante démarre un consommateur à l’aide de l’ID de groupe `mygroup` :
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

Pour voir ce processus en action, effectuez les étapes suivantes :

1. Répétez les étapes 1 et 2 de la section [Exécuter l’exemple](#run) pour ouvrir une nouvelle session SSH.

2. Dans les deux sessions SSH, entrez la commande suivante :

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > Entrez les deux commandes simultanément, afin qu’elles s’exécutent en même temps.

    Notez que le nombre de messages lus n’est pas le même qu’à la section précédente, où vous n’aviez qu’un seul consommateur. Ici, les messages lus sont répartis entre les instances.

La consommation par les clients au sein du même groupe est gérée par le biais des partitions de la rubrique. Pour la rubrique `test` créée précédemment, il y a huit partitions. Si vous ouvrez huit sessions SSH et lancez un consommateur dans toutes les sessions, chaque consommateur lit les enregistrements à partir d’une seule partition de la rubrique.

> [!IMPORTANT]
> Il ne peut pas y avoir plus d’instances de consommateurs dans un groupe de consommateurs que de partitions. Dans cet exemple, un groupe de consommateurs peut contenir jusqu’à huit consommateurs puisque c’est le nombre de partitions de la rubrique. Vous pouvez également disposer de plusieurs groupes de consommateurs, chacun ne dépassant pas huit consommateurs.

Les enregistrements stockés dans Kafka sont stockés dans l’ordre de réception dans une partition. Pour obtenir la livraison chronologique des enregistrements *dans une partition*, créez un groupe de consommateurs où le nombre d’instances de consommateurs correspond au nombre de partitions. Pour obtenir la livraison chronologique des enregistrements *dans la rubrique*, créez un groupe de consommateurs avec une seule instance de consommateur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert comment utiliser les API de consommateur et de producteur Apache Kafka avec Kafka sur HDInsight. Consultez les articles suivants pour en savoir plus sur l’utilisation de Kafka :

* [Analyser les journaux Kafka](apache-kafka-log-analytics-operations-management.md)
* [Répliquer des données entre des clusters Kafka](apache-kafka-mirroring.md)
* [API de flux Kafka avec HDInsight](apache-kafka-streams-api.md)
* [Utiliser le streaming Apache Spark (DStream) avec Kafka sur HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Utiliser Apache Spark Structured Streaming avec Kafka sur HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Utiliser Apache Spark Structured Streaming pour déplacer des données de Kafka sur HDInsight vers Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Utiliser Apache Storm avec Kafka sur HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Se connecter à Kafka via un réseau virtuel Azure](apache-kafka-connect-vpn-gateway.md)