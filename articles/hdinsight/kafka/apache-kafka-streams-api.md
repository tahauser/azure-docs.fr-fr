---
title: "Utiliser l’API de flux Apache Kafka - HDInsight Azure | Microsoft Docs"
description: "Découvrez comment utiliser l’API de flux Apache Kafka avec Kafka sur HDInsight. Cette API vous permet d’effectuer un traitement de flux entre des rubriques dans Kafka."
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
ms.date: 01/19/2018
ms.author: larryfr
ms.openlocfilehash: be6ed6d4c0c3a5fa55166b84b128881d434c4ab2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="apache-kafka-streams-api"></a>API de flux Apache Kafka

Découvrez comment créer une application qui utilise l’API de flux Kafka et comment l’exécuter avec Kafka sur HDInsight.

Lors de l’utilisation d’Apache Kafka, le traitement des flux est souvent effectué à l’aide d’Apache Spark ou Storm. Kafka version 0.10.0 (dans HDInsight 3.5 et 3.6) a introduit l’API de flux Kafka. Cette API permet de transformer des flux de données entre des rubriques d’entrée et de sortie, à l’aide d’une application qui s’exécute sur Kafka. Dans certains cas, cela peut être une alternative à la création d’une solution de streaming Spark ou Storm. Pour plus d’informations sur les flux Kafka, consultez la documentation [d’introduction aux flux](https://kafka.apache.org/10/documentation/streams/) sur le site Apache.org.

## <a name="set-up-your-development-environment"></a>Configuration de l'environnement de développement

Les composants suivants doivent être installés dans votre environnement de développement :

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html), ou un équivalent, par exemple, OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Un client SSH et la commande `scp`. Pour plus d’informations, consultez le document [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Configurer votre environnement de déploiement

Cet exemple nécessite Kafka sur HDInsight 3.6. Pour découvrir comment créer un cluster Kafka sur HDInsight, consultez le document [Démarrer avec Apache Kafka sur HDInsight](apache-kafka-get-started.md).

## <a name="build-and-deploy-the-example"></a>Générer et déployer l’exemple

Utilisez les étapes suivantes pour générer et déployer le projet sur votre cluster Kafka sur HDInsight.

1. Téléchargez les exemples à partir de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Accédez à l’emplacement du répertoire `Streaming` et utilisez la commande suivante pour créer un package jar :

    ```bash
    mvn clean package
    ```

    Cette commande crée un répertoire nommé `target`, qui contient un fichier nommé `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Utilisez la commande suivante pour copier le fichier `kafka-streaming-1.0-SNAPSHOT.jar` dans votre cluster HDInsight :
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Remplacez **SSHUSER** par l’utilisateur SSH pour votre cluster, et remplacez **CLUSTERNAME** par le nom de votre cluster. Si vous y êtes invité, entrez le mot de passe du compte d’utilisateur SSH. Pour en savoir plus sur l’utilisation de `scp` avec HDInsight, voir [Utiliser SSH avec Hadoop - Azure HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-the-example"></a>Exécuter l’exemple

1. Pour ouvrir une connexion SSH au cluster, utilisez la commande suivante :

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Remplacez **SSHUSER** par l’utilisateur SSH pour votre cluster, et remplacez **CLUSTERNAME** par le nom de votre cluster. Si vous y êtes invité, entrez le mot de passe du compte d’utilisateur SSH. Pour en savoir plus sur l’utilisation de `scp` avec HDInsight, voir [Utiliser SSH avec Hadoop - Azure HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Pour créer les rubriques Kafka utilisées par cet exemple, exécutez les commandes suivantes :

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    Remplacez __your cluster name__ par le nom de votre cluster HDInsight. À l’invite, entrez le mot de passe du compte de connexion au cluster HDInsight.

    > [!NOTE]
    > Si votre connexion de cluster est différente de la valeur par défaut `admin`, remplacez la valeur `admin` dans les commandes précédentes par le nom de connexion de votre cluster.

5. Pour exécuter cet exemple, vous devez effectuer trois opérations :

    * Démarrer la solution Streams contenue dans `kafka-streaming.jar`
    * Démarrer un producteur qui écrit dans la rubrique `test`
    * Démarrer un consommateur pour que vous puissiez voir la sortie écrite dans la rubrique `wordcounts`

    > [!NOTE]
    > Vous devez vérifier que la propriété `auto.create.topics.enable` est définie sur `true` dans le fichier de configuration Kafka Broker. Cette propriété peut être affichée et modifiée dans le fichier de configuration avancé Kafka Broker à l’aide de l’interface utilisateur web Ambari. Sinon, vous devez créer la rubrique intermédiaire `RekeyedIntermediateTopic` manuellement avant d’exécuter cet exemple à l’aide de la commande suivante :
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic  --zookeeper $KAFKAZKHOSTS
    ```
    
    Vous pouvez effectuer ces opérations en ouvrant trois sessions SSH, mais dans ce cas vous devez définir `$KAFKABROKERS` et `$KAFKAZKHOSTS` pour chaque session en exécutant l’étape 4 de cette section dans chaque session SSH. Une solution plus simple consiste à recourir à l’utilitaire `tmux`, qui peut fractionner l’affichage SSH actuel en plusieurs sections. Pour démarrer le flux, le producteur et le consommateur à l’aide de `tmux`, utilisez la commande suivante :

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    Cette commande fractionne l’affichage SSH en trois sections :

    * La section gauche exécute un consommateur de console, qui lit les messages à partir de la rubrique `wordcounts` : `/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > Les paramètres `--property` indiquent au consommateur de console qu’il faut imprimer la clé (mot) et le nombre (valeur). Ce paramètre configure également le désérialiseur à utiliser lors de la lecture de ces valeurs à partir de Kafka.

    * La section en haut à droite exécute la solution d’API de flux : `java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * La section en bas à droite exécute le producteur de console et attend que vous entriez des messages à envoyer à la rubrique `test` : `/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. Une fois que la commande `tmux` a fractionné l’affichage, le curseur se trouve dans la section en bas à droite. Commencez à entrer des phrases. Après chaque phrase, le volet gauche est mis à jour pour afficher le nombre de mots uniques. Le résultat ressemble au texte suivant :
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > Le nombre augmente à chaque fois qu’un mot est rencontré.

7. Utilisez __Ctrl + C__ pour quitter le producteur. Continuez à utiliser __Ctrl + C__ pour quitter l’application et le consommateur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert comment utiliser l’API de flux Kafka avec Kafka sur HDInsight. Consultez les articles suivants pour en savoir plus sur l’utilisation de Kafka :

* [Analyser les journaux Kafka](apache-kafka-log-analytics-operations-management.md)
* [Répliquer des données entre des clusters Kafka](apache-kafka-mirroring.md)
* [API de producteur et de consommateur Kafka avec HDInsight](apache-kafka-producer-consumer-api.md)
* [Utiliser le streaming Apache Spark (DStream) avec Kafka sur HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Utiliser Apache Spark Structured Streaming avec Kafka sur HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Utiliser Apache Spark Structured Streaming pour déplacer des données de Kafka sur HDInsight vers Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Use Apache Storm with Kafka on HDInsight](../hdinsight-apache-storm-with-kafka.md) (Utilisation d’Apache Storm avec Kafka sur HDInsight)
* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Se connecter à Kafka via un réseau virtuel Azure)
