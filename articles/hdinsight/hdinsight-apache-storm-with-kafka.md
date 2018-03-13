---
title: "Utilisation d’Apache Kafka avec Storm sur HDInsight - Azure | Microsoft Docs"
description: "Apache Kafka est installé avec Apache Storm sur HDInsight. Apprenez à écrire sur Kafka et lire à partir de celui-ci, et utiliser les composants KafkaBolt et KafkaSpout fournis avec Storm. Découvrez également comment utiliser l’infrastructure Flux pour définir et envoyer des topologies Storm."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/26/2018
ms.author: larryfr
ms.openlocfilehash: eca3f95b672a7334d77ac027b4774addf4efed2c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="use-apache-kafka-with-storm-on-hdinsight"></a>Utiliser Apache Kafka avec Storm sur HDInsight

Découvrez comment utiliser Apache Storm pour lire et écrire dans Apache Kafka. Cet exemple montre également comment enregistrer des données d’une topologie Storm dans un système de fichiers compatible HDFS utilisé par HDInsight.

> [!NOTE]
> Les étapes décrites dans ce document créent un groupe de ressources Azure qui contient à la fois un Storm sur HDInsight et un Kafka sur un cluster HDInsight. Ces clusters sont tous deux situés dans un réseau virtuel Azure, ce qui permet au cluster Storm de communiquer directement avec le cluster Kafka.
> 
> Lorsque vous avez terminé les étapes décrites dans ce document, n’oubliez pas de supprimer les clusters pour éviter les frais supplémentaires.

## <a name="get-the-code"></a>Obtenir le code

Le code de l’exemple utilisé dans ce document est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Pour compiler ce projet, la configuration de votre environnement de développement doit être la suivante :

* [Java JDK 1.8](http://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) ou version ultérieure. HDInsight 3.5 ou les versions ultérieures requièrent Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Un client SSH (vous avez besoin des commandes `ssh` et `scp`) : pour plus d’informations, voir [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Un éditeur de texte ou IDE.

Les variables d’environnement suivantes peuvent être définies lors de l’installation de Java et du Kit de développeur Java (JDK) sur votre station de travail de développement. Toutefois, vous devez vérifier qu’elles existent et qu’elles contiennent les valeurs correctes pour votre système.

* `JAVA_HOME` : doit pointer vers le répertoire d’installation du Kit de développeur Java (JDK).
* `PATH` : doit contenir les chemins d’accès suivants :
  
    * `JAVA_HOME` (ou le chemin équivalent).
    * `JAVA_HOME\bin` (ou le chemin équivalent).
    * Répertoire d’installation de Maven.

## <a name="create-the-clusters"></a>Création des clusters

Apache Kafka sur HDInsight ne donne pas accès aux répartiteurs Kafka via l’internet public. Tout ce qui communique avec Kafka doit se trouver sur le même réseau virtuel Azure que les nœuds du cluster Kafka. Pour cet exemple, les clusters Kafka et Storm sont situés dans un réseau virtuel Azure. Le diagramme suivant illustre le flux de communication entre les clusters :

![Diagramme des clusters Storm et Kafka dans un réseau virtuel Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Les autres services sur le cluster, tels que SSH et Ambari, sont accessibles via Internet. Pour plus d’informations sur les ports publics disponibles avec HDInsight, consultez [Ports et URI utilisés par HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Même si vous pouvez créer un réseau virtuel Azure, et des clusters Kafka et Storm manuellement, il est plus facile d’utiliser un modèle Azure Resource Manager. Utilisez les étapes suivantes pour déployer un réseau virtuel Azure et des clusters Kafka et Storm sur votre abonnement Azure.

1. Utilisez le bouton suivant pour vous connecter à Azure et ouvrir le modèle dans le portail Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet-v2.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Le modèle Azure Resource Manager se trouve à l’adresse **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet-v2.json**. Il crée les ressources suivantes :
    
    * Groupe de ressources Azure
    * Réseau virtuel Azure
    * Compte de Stockage Azure
    * Kafka sur HDInsight version 3.6 (trois nœuds worker)
    * Storm sur HDInsight version 3.6 (trois nœuds worker)

  > [!WARNING]
  > Pour garantir la disponibilité de Kafka sur HDInsight, votre cluster doit contenir au moins trois nœuds Worker. Ce modèle crée un cluster Kafka qui contient trois nœuds Worker.

2. Utilisez les instructions suivantes pour remplir les entrées de la section **Déploiement personnalisé** :
   
    ![Déploiement HDInsight personnalisé](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Groupe de ressources** : créez un groupe de ressources ou sélectionnez-en un. Ce groupe contient le cluster HDInsight.
   
    * **Emplacement** : choisissez un emplacement proche de vous géographiquement.

    * **Nom de cluster de base** : cette valeur sera utilisée comme nom de base pour les clusters Storm et HBase. Par exemple, si vous entrez **hdi**, cela crée un cluster Storm nommé **storm-hdi** et un cluster Kafka nommé **kafka-hdi**.
   
    * **Nom d’utilisateur du cluster** : nom utilisateur Admin pour les clusters Storm et Kafka.
   
    * **Mot de passe du cluster** : mot de passe utilisateur Admin pour les clusters Storm et Kafka.
    
    * **Nom d’utilisateur SSH** : utilisateur SSH permettant de créer des clusters Storm et Kafka.
    
    * **Mot de passe SSH** : mot de passe de l’utilisateur SSH pour les clusters Storm et Kafka.

3. Passez en revue les **termes et conditions**, puis cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**.

4. Pour finir, cochez **Épingler au tableau de bord**, puis sélectionnez **Acheter**. La création des clusters prend environ 20 minutes.

Une fois les ressources créées, la section du groupe de ressources s’affiche.

![Section du groupe de ressources pour le réseau virtuel et les clusters](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Les noms des clusters HDInsight sont **storm-BASENAME** et **kafka-BASENAME**, où BASENAME est le nom que vous avez fourni pour le modèle. Vous utilisez ces noms dans les étapes ultérieures, lors de la connexion aux clusters.

## <a name="understanding-the-code"></a>Présentation du code

Ce projet contient deux topologies :

* **KafkaWriter** : définie par le fichier **writer.yaml**, cette topologie écrit des phrases aléatoires dans Kafka en utilisant le KafkaBolt fourni avec Apache Storm.

    Cette topologie utilise un composant **SentenceSpout** personnalisé pour générer des phrases aléatoires.

* **KafkaReader** : définie par le fichier **reader.yaml**, cette topologie lit les données à partir de Kafka à l’aide du KafkaSpout fourni avec Apache Storm, puis écrit les données dans stdout.

    Cette topologie utilise le Storm HdfsBolt pour écrire des données dans le stockage par défaut pour le cluster Storm.
### <a name="flux"></a>Flux

Les topologies sont définies à l’aide de [Flux](https://storm.apache.org/releases/1.1.2/flux.html). Flux a été introduit dans Storm 0.10.x et vous permet de séparer la configuration de topologie du code. Pour les topologies qui utilisent l’infrastructure Flux, la topologie est définie dans un fichier YAML. Le fichier YAML peut être inclus dans le cadre de la topologie. Il peut également s’agir d’un fichier autonome utilisé lorsque vous envoyez la topologie. Flux prend également en charge la substitution des variables au moment de l’exécution, ce qui est utilisé dans cet exemple.

Les paramètres suivants sont définis au moment de l’exécution pour les topologies suivantes :

* `${kafka.topic}` : nom de la rubrique Kafka dans laquelle les topologies lisent et écrivent.

* `${kafka.broker.hosts}` : hôtes sur lesquels les répartiteurs Kafka s’exécutent. Les informations sur les répartiteurs sont utilisées par le KafkaBolt lors de l’écriture sur Kafka.

* `${kafka.zookeeper.hosts}`: hôtes sur lesquels Zookeeper s’exécute dans le cluster Kafka.

Pour plus d’informations sur les topologies Flux, voir la page [https://storm.apache.org/releases/1.1.2/flux.html](https://storm.apache.org/releases/1.1.2/flux.html).

## <a name="download-and-compile-the-project"></a>Téléchargez et compilez le projet

1. Dans votre environnement de développement, téléchargez le projet sur [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), ouvrez une interface de ligne de commande et accédez à l’emplacement auquel vous avez téléchargé le projet.

2. À partir du répertoire **hdinsight-storm-java-kafka**, utilisez la commande suivante pour compiler le projet et créer un package de déploiement :

  ```bash
  mvn clean package
  ```

    Le processus de création de package crée un fichier nommé `KafkaTopology-1.0-SNAPSHOT.jar` dans le répertoire `target`.

3. Utilisez la commande suivante pour copier le package sur votre Storm dans votre cluster HDInsight. Remplacez **USERNAME** par le nom d’utilisateur SSH pour le cluster. Remplacez **BASENAME** par le nom de base que vous avez utilisé lors de la création du cluster.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    À l'invite, saisissez le mot de passe que vous avez utilisé lors de la création du cluster.

## <a name="configure-the-topology"></a>Configurer la topologie

1. Pour découvrir les hôtes du répartiteur Kafka, utilisez l’une des méthodes suivantes :

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > L’exemple de Bash suppose que `$CLUSTERNAME` contient le nom du cluster HDInsight. Il suppose également que la version 1.5 de [jq](https://stedolan.github.io/jq/) (ou une version ultérieure) est installée. Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion au cluster.

    Les informations renvoyées sont similaire au texte suivant :

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > Votre cluster pouvant disposer de plus de deux hôtes de répartiteur, il n’est pas nécessaire de fournir une liste complète des hôtes aux clients. Un ou deux sont suffisants.

2. Pour découvrir les hôtes du Zookeeper Kafka, utilisez l’une des méthodes suivantes :

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > L’exemple de Bash suppose que `$CLUSTERNAME` contient le nom du cluster HDInsight. Il suppose également que [jq](https://stedolan.github.io/jq/) est installé. Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion au cluster.

    Les informations renvoyées sont similaire au texte suivant :

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > Puisqu’il existe plus de deux nœuds Zookeeper, il n’est pas nécessaire de fournir une liste complète des hôtes aux clients. Un ou deux sont suffisants.

    Enregistrez cette valeur, car vous l’utiliserez plus tard.

3. Modifiez le fichier `dev.properties` à la racine du projet. Ajoutez les informations des hôtes Répartiteur et Zookeeper aux lignes correspondantes dans ce fichier. L’exemple suivant est configuré à l’aide des exemples de valeurs de l’étape précédente :

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. Enregistrez le fichier `dev.properties`, puis utilisez la commande suivante pour le charger sur le cluster Storm :

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Remplacez **USERNAME** par le nom d’utilisateur SSH pour le cluster. Remplacez **BASENAME** par le nom de base que vous avez utilisé lors de la création du cluster.

## <a name="start-the-writer"></a>Démarrer l’enregistreur

1. Utilisez ce qui suit pour vous connecter au cluster Storm à l’aide de SSH. Remplacez **USERNAME** par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez **BASENAME** par le nom de base que vous avez utilisé lors de la création du cluster.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    À l'invite, saisissez le mot de passe que vous avez utilisé lors de la création du cluster.
   
    Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. À partir de la connexion SSH, utilisez la commande suivante pour créer la rubrique Kafka utilisée par la topologie :

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Remplacez `$KAFKAZKHOSTS` par les informations de l’hôte Zookeeper que vous avez récupérées dans la section précédente.

2. Dans la connexion SSH sur le cluster Storm, utilisez la commande suivante pour démarrer la topologie d’enregistreur :

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Les paramètres utilisés avec cette commande sont les suivants :

    * `org.apache.storm.flux.Flux` : utilisez Flux pour configurer et exécuter cette topologie.

    * `--remote` : envoyez la topologie à Nimbus. La topologie est distribuée sur les nœuds de travail dans le cluster.

    * `-R /writer.yaml` : utilisez le fichier `writer.yaml` pour configurer la topologie. `-R` indique que cette ressource est incluse dans le fichier jar. Il se trouve à la racine du fichier jar, donc `/writer.yaml` est le chemin d’accès à celui-ci.

    * `--filter` : complétez les entrées de la topologie `writer.yaml` à l’aide des valeurs figurant dans le fichier `dev.properties`. Par exemple, la valeur de l’entrée `kafka.topic` dans le fichier est utilisée pour remplacer l’entrée `${kafka.topic}` dans la définition de la topologie.

5. Après démarrage de la topologie, utilisez la commande suivante pour vérifier qu’elle écrit les données dans la rubrique Kafka :

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
  ```

    Remplacez `$KAFKAZKHOSTS` par les informations de l’hôte Zookeeper que vous avez récupérées dans la section précédente.

    Cette commande utilise un script fourni avec Kafka pour surveiller la rubrique. Après quelques instants, elle doit commencer à retourner des phrases aléatoires qui ont été écrites dans la rubrique. Le résultat ressemble à l’exemple suivant :

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Pour arrêter le script, utilisez les touches Ctrl + C.

## <a name="start-the-reader"></a>Démarrer le lecteur

> [!NOTE]
> Lorsque vous affichez le lecteur dans l’interface utilisateur Storm, vous pouvez voir une section __Erreur de retard des spouts de topologie__. Pour cet exemple, vous pouvez ignorer cette erreur.

1. Dans la session SSH sur le cluster Storm, utilisez la commande suivante pour démarrer la topologie de lecteur :

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. Une fois que la topologie commence, ouvrez l’interface utilisateur Storm. Cette interface utilisateur de site web se trouve à l’adresse `https://storm-BASENAME.azurehdinsight.net/stormui`. Remplacez __BASENAME__ par le nom de base que vous avez utilisé lors de la création du cluster. 

    Lorsque vous y êtes invité, utilisez le nom d’utilisateur administrateur (par défaut `admin`) et le mot de passe utilisés lors de la création du cluster. Vous voyez s’afficher une page web similaire à l’image suivante :

    ![Interface utilisateur de Storm](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. Dans l’interface utilisateur de Storm, sélectionnez le lien __kafka-lecteur__ dans la section __Résumé de la topologie__ pour afficher des informations sur la topologie __kafka-reader__.

    ![Section Résumé de topologie l'interface utilisateur web de Storm](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Pour afficher des informations sur les instances du composant logger-bolt, sélectionnez le lien __logger-bolt__ dans la section __Bolts (All time)__.

    ![Lien logger-bolt dans la section bolts](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. Dans la section __Executors__, sélectionnez un lien dans la colonne __Port__ pour afficher des informations de journalisation sur cette instance du composant.

    ![Exécuteurs de lien](./media/hdinsight-apache-storm-with-kafka/executors.png)

    Le journal contient un journal des données lues à partir de la rubrique Kafka. Les informations dans le journal sont similaires à ce qui suit :

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Arrêt des topologies

Dans une session SSH sur le cluster Storm, utilisez la commande suivante pour arrêter les topologies Storm :

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>Suppression du cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Étant donné que les étapes décrites dans ce document créent deux clusters dans le même groupe de ressources Azure, vous pouvez supprimer le groupe de ressources du portail Azure. La suppression du groupe de ressources a pour effet de supprimer toutes les ressources créées en suivant les étapes décrites dans ce document.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’exemples de topologies qui peuvent être utilisées avec Storm sur HDInsight, consultez [Exemples de topologies et composants Storm](storm/apache-storm-example-topology.md).

Pour plus d’informations sur le déploiement et la surveillance des topologies sur HDInsight Linux, consultez [Déploiement et gestion des topologies Apache Storm sur HDInsight Linux](storm/apache-storm-deploy-monitor-topology-linux.md)