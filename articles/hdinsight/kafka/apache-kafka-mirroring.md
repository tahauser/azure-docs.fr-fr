---
title: Mettre en miroir les rubriques Apache Kafka - Azure HDInsight | Documents Microsoft
description: "Découvrez comment utiliser la fonctionnalité de mise en miroir d’Apache Kafka afin de conserver un réplica Kafka sur un cluster HDInsight par la mise en miroir de rubriques sur un cluster secondaire."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/31/2018
ms.author: larryfr
ms.openlocfilehash: 87b5912e7f9244dc1be74ac357200122b194dbdc
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>MirrorMaker permet de répliquer des rubriques Apache Kafka avec Kafka sur HDInsight

Découvrez comment utiliser la fonctionnalité de mise en miroir d’Apache Kafka pour répliquer des rubriques sur un cluster secondaire. La mise en miroir peut être exécutée sous la forme d’un processus continu ou être utilisée par intermittence comme une méthode de migration des données d’un cluster à l’autre.

Dans cet exemple, la mise en miroir est utilisée pour répliquer des rubriques entre deux clusters HDInsight. Les deux clusters se trouvent dans un réseau Azure Virtual Network situé dans la même région.

> [!WARNING]
> La mise en miroir ne doit pas être considérée comme un moyen de bénéficier d’une tolérance de pannes. Le décalage aux éléments d’un sujet diffère entre le cluster source et le cluster de destination : les clients ne peuvent donc pas utiliser indifféremment les deux.
>
> Si la tolérance de pannes vous préoccupe, définissez la réplication pour les sujets au sein de votre cluster. Pour plus d’informations, consultez [Prise en main de Kafka sur HDInsight](apache-kafka-get-started.md).

## <a name="how-kafka-mirroring-works"></a>Fonctionne de la mise en miroir Kafka

La mise en miroir se fait à l’aide de l’outil MirrorMaker (qui fait partie d’Apache Kafka) : cet outil permet d’utiliser les enregistrements de sujets du cluster source et de créer une copie locale sur le cluster de destination. MirrorMaker utilise un (ou plusieurs) *consommateurs* qui lisent le cluster source, ainsi qu’un *producteur* qui écrit dans le cluster local (de destination).

Le diagramme suivant illustre le processus de mise en miroir :

![Diagramme du processus de mise en miroir](./media/apache-kafka-mirroring/kafka-mirroring.png)

Apache Kafka sur HDInsight ne donne pas accès au service Kafka par le biais de l’Internet public. Les producteurs ou consommateurs de Kafka doivent se trouver dans le même réseau Azure Virtual Network que les nœuds du cluster Kafka. Pour cet exemple, le cluster source et le cluster de destination Kafka sont situés dans un réseau virtuel Azure. Le diagramme suivant illustre le flux de communication entre les clusters :

![Diagramme du cluster source et du cluster de destination Kafka dans un réseau virtuel Azure](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Le cluster source et le cluster de destination peuvent comporter un nombre différent de nœuds et de partitions, et les décalages dans les sujets ne sont pas forcément les mêmes. La mise en miroir conserve la valeur clé utilisée pour le partitionnement afin de préserver l’ordre d’enregistrement en fonction de la clé.

### <a name="mirroring-across-network-boundaries"></a>Mise en miroir au-delà des limites réseau

Si vous avez besoin d’effectuer une mise en miroir entre des clusters Kafka sur des réseaux différents, vous devez tenir compte des points suivants :

* **Passerelles** : les réseaux doivent être capables de communiquer au niveau TCPIP.

* **Résolution de noms** : les clusters Kafka de chaque réseau doivent être capables de se connecter entre eux à l’aide de noms d’hôte. Cela peut nécessiter la configuration d’un serveur DNS (Domain Name System) dans chaque réseau pour l’acheminement des demandes vers les autres réseaux.

    Lorsque vous créez un réseau virtuel Azure, au lieu d’utiliser le DNS automatique fourni avec le réseau, vous devez spécifier un serveur DNS personnalisé et l’adresse IP du serveur. Après avoir créé le réseau virtuel, vous devez ensuite créer une machine virtuelle Azure qui utilise cette adresse IP, puis y installer et configurer le logiciel DNS.

    > [!WARNING]
    > Créez et configurez le serveur DNS personnalisé avant d’installer HDInsight sur le réseau virtuel. Aucune configuration supplémentaire n’est requise pour HDInsight dans le cadre de l’utilisation du serveur DNS configuré pour le réseau virtuel.

Pour plus d’informations sur la connexion de deux réseaux virtuels Azure, consultez [Configurer une connexion de réseau virtuel à réseau virtuel](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-kafka-clusters"></a>Création de clusters Kafka

Même si vous pouvez créer un réseau virtuel Azure et des clusters Kafka manuellement, il est plus facile d’utiliser un modèle Azure Resource Manager. Utilisez les étapes suivantes pour déployer un réseau virtuel Azure et des clusters Kafka sur votre abonnement Azure.

1. Utilisez le bouton suivant pour vous connecter à Azure et ouvrir le modèle dans le portail Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Le modèle Azure Resource Manager se trouve à l’adresse **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]
    > Pour garantir la disponibilité de Kafka sur HDInsight, votre cluster doit contenir au moins trois nœuds Worker. Ce modèle crée un cluster Kafka qui contient trois nœuds Worker.

2. Utilisez les informations suivantes pour remplir les entrées sur le panneau **déploiement personnalisé** :
    
    ![Déploiement HDInsight personnalisé](./media/apache-kafka-mirroring/parameters.png)
    
    * **Groupe de ressources** : créez un groupe de ressources ou sélectionnez-en un. Ce groupe contient le cluster HDInsight.

    * **Emplacement** : choisissez un emplacement proche de vous géographiquement.
     
    * **Nom de cluster de base** : cette valeur sera utilisée comme nom de base pour les clusters Kafka. Par exemple, l’entrée de **hdi** crée des clusters nommés **source-hdi** et **dest-hdi**.

    * **Nom d’utilisateur du cluster** : nom utilisateur Admin pour le cluster source et le cluster de destination Kafka.

    * **Mot de passe du cluster** : mot de passe Admin pour le cluster source et le cluster de destination Kafka.

    * **Nom d’utilisateur SSH** : utilisateur SSH permettant de créer des clusters sources et des clusters de destination Kafka.

    * **Mot de passe SSH** : mot de passe de l’utilisateur SSH pour les clusters sources et les clusters de destination Kafka.

3. Passez en revue les **termes et conditions**, puis cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**.

4. Pour finir, cochez **Épingler au tableau de bord**, puis sélectionnez **Acheter**. La création des clusters prend environ 20 minutes.

> [!IMPORTANT]
> Les noms des clusters HDInsight sont **source-BASENAME** et **dest-BASENAME**, où BASENAME est le nom que vous avez fourni pour le modèle. Vous utilisez ces noms dans les étapes ultérieures, lors de la connexion aux clusters.

## <a name="create-topics"></a>Création de sujets

1. Connectez-vous au cluster **source** à l’aide de SSH :

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Remplacez **sshuser** par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez **BASENAME** par le nom de base que vous avez utilisé lors de la création du cluster.

    Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilisez les commandes suivantes pour rechercher les hôtes Zookeeper pour le cluster source :

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Remplacez `$CLUSTERNAME` par le nom du cluster source. Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion (admin) au cluster.

3. Pour créer une rubrique nommée `testtopic`, utilisez la commande suivante :

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. Exécutez la commande suivante pour vérifier la création du sujet :

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    La réponse contient `testtopic`.

4. Utilisez la commande suivante afin d’afficher les informations sur l’hôte Zookeeper pour ce cluster (**source**) :

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    Cette commande renvoie des informations semblables au texte suivant :

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Enregistrez ces informations. Elles seront utilisées dans la section suivante.

## <a name="configure-mirroring"></a>Configuration de la mise en miroir

1. Connectez-vous au cluster **de destination** à l’aide d’une autre session SSH :

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Remplacez **sshuser** par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez **BASENAME** par le nom de base que vous avez utilisé lors de la création du cluster.

    Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Un fichier `consumer.properties` est utilisé pour configurer la communication avec le cluster **source**. Pour créer le fichier, utilisez la commande suivante :

    ```bash
    nano consumer.properties
    ```

    Utilisez les données suivantes comme contenu du fichier `consumer.properties` :

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Remplacez **SOURCE_ZKHOSTS** par les informations sur l’hôte Zookeeper du cluster **source**.

    Ce fichier détaille les informations de consommateur à utiliser lors de la lecture du cluster source Kafka. Pour plus d’informations sur la configuration du consommateur, consultez [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) (Configurations de consommateur) sur kafka.apache.org.

    Pour enregistrer le fichier, appuyez sur **Ctrl+X**, sur **O**, puis sur **Entrée**.

3. Avant de configurer le producteur qui communique avec le cluster de destination, vous devez trouver les hôtes du répartiteur pour le cluster **de destination**. Pour récupérer ces informations, utilisez les commandes suivantes :

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Remplacez `$CLUSTERNAME` par le nom du cluster de destination. Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion (admin) au cluster.

    La commande `echo` renvoie des informations semblables au texte suivant :

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. Un fichier `producer.properties` est utilisé pour communiquer avec le cluster __de destination__. Pour créer le fichier, utilisez la commande suivante :

    ```bash
    nano producer.properties
    ```

    Utilisez les données suivantes comme contenu du fichier `producer.properties` :

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Remplacez **DEST_BROKERS** par les informations sur le répartiteur de l’étape précédente.

    Pour plus d’informations sur la configuration du producteur, consultez [Producer Configs](https://kafka.apache.org/documentation#producerconfigs) (Configurations de producteur) sur kafka.apache.org.

5. Utilisez les commandes suivantes pour rechercher les hôtes Zookeeper pour le cluster de destination :

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Remplacez `$CLUSTERNAME` par le nom du cluster de destination. Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion (admin) au cluster.

7. La configuration par défaut pour Kafka sur HDInsight n’autorise pas la création automatique de rubriques. Vous devez utiliser une des options suivantes avant de commencer le processus de mise en miroir :

    * **Create the topics on the destination cluster** (Créer les rubriques sur le cluster de destination) : cette option vous permet également de définir le nombre de partitions et le facteur de réplication.

        Vous pouvez créer à l’avance les rubriques avec la commande suivante :

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        Remplacez `testtopic` par le nom de la rubrique à créer.

    * **Configure the cluster for automatic topic creation** (Configurer le cluster pour la création automatique des rubriques) : cette option permet à MirrorMaker de créer automatiquement des rubriques, mais en créant un nombre différent de partitions ou un autre facteur de réplication que la rubrique source.

        Pour configurer le cluster de destination afin de créer automatiquement des rubriques, procédez comme suit :

        1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le cluster Kafka de destination.
        2. Dans la vue d’ensemble du cluster, sélectionnez __Tableau de bord du cluster__. Puis choisissez __Tableau de bord du cluster HDInsight__. Lorsque vous y êtes invité, authentifiez-vous à l’aide des informations d’identification de connexion (admin) pour le cluster.
        3. Sélectionnez le service __Kafka__ dans la liste à gauche de la page.
        4. Sélectionnez __Configs__ au milieu de la page.
        5. Dans le champ __Filter__ (Filtrer), entrez la valeur `auto.create`. Cette option filtre la liste des propriétés et affiche le paramètre `auto.create.topics.enable`.
        6. Définissez la valeur `auto.create.topics.enable` sur true, puis sélectionnez __Save__ (Enregistrer). Ajoutez une note, puis sélectionnez à nouveau __Save__ (Enregistrer).
        7. Sélectionnez le service __Kafka__, choisissez __Restart__ (Redémarrer), puis __Restart all affected__ (Redémarrer tous les éléments affectés). Lorsque vous y êtes invité, sélectionnez __Confirm Restart All__ (Confirmer le redémarrage).

## <a name="start-mirrormaker"></a>Lancement de MirrorMaker

1. Dans la connexion SSH sur le cluster **de destination**, utilisez la commande suivante pour lancer le processus MirrorMaker :

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Les paramètres utilisés dans cet exemple sont :

    * **--consumer.config** : spécifie le fichier qui contient les propriétés du consommateur. Ces propriétés permettent de créer un consommateur qui lit le cluster *source* Kafka.

    * **--producer.config** : spécifie le fichier qui contient les propriétés du producteur. Ces propriétés permettent de créer un producteur qui écrit dans le cluster *de destination* Kafka.

    * **--whitelist** : liste de sujets que MirrorMaker réplique du cluster source vers le cluster de destination.

    * **--num.streams** : nombre de threads de consommateur à créer.

 Au démarrage, MirrorMaker renvoie des informations semblables au texte suivant :

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. À partir de la connexion SSH au cluster **source**, utilisez la commande suivante pour démarrer un producteur et envoyer des messages sur la rubrique :

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Remplacez `$CLUSTERNAME` par le nom du cluster source. Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion (admin) au cluster.

     Lorsque vous arrivez sur une ligne vide avec un curseur, tapez quelques messages texte. Les messages sont envoyés à la rubrique sur le cluster **source**. Lorsque vous avez terminé, utilisez **Ctrl + C** pour terminer le processus de production.

3. Dans la connexion SSH au cluster **de destination**, utilisez **Ctrl + C** pour mettre fin au processus MirrorMaker. Plusieurs secondes peuvent être nécessaires pour terminer le processus. Pour vérifier que les messages ont été répliqués vers la destination, utilisez la commande suivante :

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Remplacez `$CLUSTERNAME` par le nom du cluster de destination. Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion (admin) au cluster.

    La liste des rubriques inclut désormais `testtopic`, qui est créé lorsque MirrorMaster reflète la rubrique du cluster source vers la destination. Les messages récupérés de la rubrique sont les mêmes que ceux entrés dans le cluster source.

## <a name="delete-the-cluster"></a>Suppression du cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Étant donné que les étapes décrites dans ce document créent deux clusters dans le même groupe de ressources Azure, vous pouvez supprimer le groupe de ressources du portail Azure. La suppression du groupe source supprime toutes les ressources créées par la suite dans ce document, le réseau virtuel Azure et le compte de stockage utilisé par les clusters.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à utiliser MirrorMaker pour créer un réplica d’un cluster Kafka. Utilisez les liens suivants pour découvrir d’autres façons de travailler avec Kafka :

* [Documentation d’Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) sur cwiki.apache.org.
* [Prise en main d’Apache Kafka sur HDInsight](apache-kafka-get-started.md)
* [Use Apache Spark with Kafka on HDInsight](../hdinsight-apache-spark-with-kafka.md) (Utilisation d’Apache Spark avec Kafka sur HDInsight)
* [Use Apache Storm with Kafka on HDInsight](../hdinsight-apache-storm-with-kafka.md) (Utilisation d’Apache Storm avec Kafka sur HDInsight)
* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Se connecter à Kafka via un réseau virtuel Azure)
