---
title: Démarrer avec Apache Kafka - Azure HDInsight | Microsoft Docs
description: Découvrez comment créer un cluster Apache Kafka sur Azure HDInsight. Découvrez comment créer des rubriques, des abonnés et des consommateurs.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: 27e6472480dac104de799ebf0e7579a7987f6c4c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>Démarrer avec Apache Kafka sur HDInsight

Découvrez comment créer et utiliser un cluster [Apache Kafka](https://kafka.apache.org) sur Azure HDInsight. Kafka est une plateforme de diffusion en continu distribuée open source, disponible avec HDInsight. Elle est souvent utilisée comme répartiteur de messages, car elle fournit des fonctionnalités similaires à une file d’attente de messages de publication/d’abonnement. Kafka est souvent utilisée avec Apache Spark et Apache Storm pour la messagerie, le suivi des activités, l’agrégation de flux de données ou la transformation des données.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Création d’un cluster Kafka

Pour créer un Kafka sur un cluster HDInsight, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **+ Créer une ressource**, **Données + Analytique**, puis **HDInsight**.
   
    ![Création d'un cluster HDInsight](./media/apache-kafka-get-started/create-hdinsight.png)

2. À partir du panneau **Informations de base**, entrez les informations suivantes :

    * **Nom du cluster** : nom du cluster HDInsight. Ce nom doit être unique.
    * **Abonnement** : sélectionnez l'abonnement souhaité.
    * **Type de cluster** : sélectionnez cette entrée, puis définissez les valeurs suivantes sur le panneau **Configuration du cluster** :

        * **Type de cluster** : Kafka
        * **Version** : Kafka 0.10.0 (HDI 3.6)

        Utilisez le bouton **Sélectionner** pour enregistrer les paramètres de type de cluster.

        ![Sélectionner un type de cluster](./media/apache-kafka-get-started/set-hdinsight-cluster-type.png)

    * **Nom d’utilisateur de connexion du cluster** et **Mot de passe de connexion du cluster** : les informations de connexion lors de l’accès au cluster sur HTTPS. Vous utilisez ces informations d’identification pour accéder aux services tels que l’interface utilisateur Ambari Web ou l’API REST.
    * **Nom d’utilisateur Secure Shell (SSH)** : information de connexion utilisée lors de l’accès au cluster sur SSH. Par défaut, le mot de passe est le même que le mot de passe de connexion de cluster.
    * **Groupe de ressources** : groupe de ressources dans lequel créer le cluster.
    * **Emplacement** : la région Azure dans laquelle créer le cluster.

        > [!IMPORTANT]
        > Pour la haute disponibilité des données, nous vous recommandons de sélectionner un emplacement (région) qui contient __trois domaines d’erreur__. Pour plus d’informations, consultez la section [Haute disponibilité des données](#data-high-availability).
   
 ![Sélectionnez un abonnement](./media/apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Utilisez le bouton __Suivant__ pour terminer la configuration de base.

4. À partir du panneau **Stockage**, sélectionnez ou créez un compte de stockage. Concernant les étapes de ce document, laissez les autres champs sur leurs valeurs par défaut. Utilisez le bouton __Suivant__ pour enregistrer la configuration de stockage.

    ![Définir les paramètres de compte de stockage pour HDInsight](./media/apache-kafka-get-started/set-hdinsight-storage-account.png)

5. Dans le panneau __Applications (facultatif)__, sélectionnez __Suivant__ pour continuer. Cet exemple ne requiert aucune application.

6. Dans le panneau __Taille du cluster__, sélectionnez __Suivant__ pour continuer.

    > [!WARNING]
    > Pour garantir la disponibilité de Kafka sur HDInsight, votre cluster doit contenir au moins trois nœuds Worker. Pour plus d’informations, consultez la section [Haute disponibilité des données](#data-high-availability).

    ![Définir la taille du cluster Kafka](./media/apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > L’entrée relative aux **disques par nœud Worker** configure l’extensibilité de Kafka sur HDInsight. Kafka sur HDInsight utilise le disque local des machines virtuelles dans le cluster. Kafka fait une utilisation intensive des E/S, c’est pourquoi le service [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) est utilisé pour fournir un haut débit et davantage de stockage à chaque nœud. Le type de disque géré peut être soit __Standard__ (HDD), soit __Premium__ (SSD). Les disques Premium sont utilisés avec les machines virtuelles séries DS et GS. Tous les autres types de machines virtuelles utilisent des disques Standard.

7. Dans le panneau __Paramètres avancés__, sélectionnez __Suivant__ pour continuer.

8. Dans le panneau **Résumé**, passez en revue la configuration du cluster. Utilisez les liens __Modifier__ pour modifier les éventuels paramètres incorrects. Pour finir, cliquez sur le bouton __Créer__ pour créer le cluster.
   
    ![Résumé de la configuration du cluster](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > La création du cluster peut prendre jusqu’à 20 minutes.

## <a name="connect-to-the-cluster"></a>Connexion au cluster

> [!IMPORTANT]
> Lorsque vous effectuez les étapes suivantes, vous devez utiliser un client SSH. Pour plus d’informations, consultez le document [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Pour vous connecter au cluster à l’aide de SSH, vous devez fournir le nom du compte d’utilisateur SSH et le nom de votre cluster. Dans l’exemple suivant, remplacez `sshuser` et `clustername` par le nom de votre compte et le nom du cluster :

```ssh sshuser@clustername-ssh.azurehdinsight.net```

Lorsque vous y êtes invité, entrez le mot de passe utilisé pour le compte SSH.

Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="getkafkainfo"></a>Obtention des informations sur Zookeeper et l’hôte du répartiteur

Lorsque vous utilisez Kafka, vous devez connaître les hôtes *Zookeeper* et les hôtes du *répartiteur*. Ces hôtes sont utilisés avec l’API Kafka et la plupart des utilitaires fournis avec Kafka.

Pour créer des variables d’environnement contenant des informations sur l’hôte, procédez comme suit :

1. À partir d’une connexion SSH avec le cluster, utilisez la commande suivante pour installer l’utilitaire `jq`. Cet utilitaire est utilisé pour analyser des documents JSON et est utile lors de la récupération des informations sur l’hôte du répartiteur :
   
    ```bash
    sudo apt -y install jq
    ```

2. Pour définir une variable d’environnement associée au nom du cluster, utilisez la commande suivante :

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

3. Pour définir une variable d’environnement avec les informations d’hôte Zookeeper, utilisez la commande suivante :

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion au cluster (admin).

4. Pour vérifier que la variable d’environnement est correctement définie, utilisez la commande suivante :

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Cette commande renvoie des informations semblables au texte suivant :

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Pour définir une variable d’environnement avec les informations d’hôte de répartiteur Kafka, utilisez la commande suivante :

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion au cluster (admin).

6. Pour vérifier que la variable d’environnement est correctement définie, utilisez la commande suivante :

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Cette commande renvoie des informations semblables au texte suivant :
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
> [!WARNING]
> Les informations renvoyées par cette session ne sont pas toujours précises. Lorsque vous redimensionnez le cluster, de nouveaux répartiteurs sont ajoutés ou supprimés. Si une défaillance se produit et si un nœud est remplacé, le nom d’hôte du nœud peut changer.
>
> Vous devez récupérer les informations des hôtes Zookeeper et du répartiteur peu de temps avant de les utiliser pour vous assurer de leur validité.

## <a name="create-a-topic"></a>Création d'une rubrique

Kafka stocke les flux de données dans des catégories appelées *rubriques*. À partir d’une connexion SSH à un nœud principal de cluster, utilisez un script fourni avec Kafka pour créer une rubrique :

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Cette commande se connecte à Zookeeper par le biais des informations d’hôte stockées dans `$KAFKAZKHOSTS`. Elle crée ensuite une rubrique Kafka nommée **test**. Vous pouvez vérifier que la rubrique a été créée à l’aide du script suivant pour répertorier les rubriques :

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

La sortie de cette commande répertorie les rubriques Kafka sur le cluster.

## <a name="produce-and-consume-records"></a>Produire et consommer des enregistrements

Kafka stocke les *enregistrements* dans des rubriques. Les enregistrements sont produits par des *producteurs*, et utilisés par des *consommateurs*. Les producteurs créent des enregistrements dans les *répartiteurs* Kafka. Chaque nœud Worker dans votre cluster HDInsight est un répartiteur Kafka.

Pour stocker les enregistrements dans la rubrique test créée précédemment, puis les lire à l’aide d’un consommateur, procédez comme suit :

1. À partir de la session SSH, utilisez un script fourni avec Kafka pour écrire des enregistrements dans la rubrique :
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Une fois cette commande exécutée, vous accédez à une ligne vide.

2. Saisissez un message texte sur la ligne vide et appuyez sur Entrée. Entrez quelques messages de cette manière, puis utilisez **Ctrl + C** pour revenir à l’invite de commandes normale. Chaque ligne est envoyée en tant qu’enregistrement distinct vers la rubrique Kafka.

3. Utilisez un script fourni avec Kafka pour lire les enregistrements à partir de la rubrique :
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Cette commande permet de récupérer les enregistrements à partir de la rubrique et de les afficher. L’utilisation de `--from-beginning` indique au consommateur de démarrer à partir du début du flux, de manière à récupérer tous les enregistrements.

    > [!NOTE]
    > Si vous utilisez une version antérieure de Kafka, remplacez `--bootstrap-server $KAFKABROKERS` par `--zookeeper $KAFKAZKHOSTS`.

4. Utilisez la combinaison __Ctrl + C__ pour arrêter le consommateur.

Vous pouvez également créer les producteurs et consommateurs par programme. Pour obtenir un exemple d’utilisation de cette API, consultez le document [Kafka Producer and Consumer API with HDInsight (API Kafka Producer and Consumer avec HDInsight)](apache-kafka-producer-consumer-api.md).

## <a name="data-high-availability"></a>Haute disponibilité des données

Chaque région Azure (emplacement) fournit des _domaines d’erreur_. Un domaine d’erreur est un regroupement logique de matériel sous-jacent dans un datacenter Azure. Chaque domaine d’erreur partage une source d’alimentation et un commutateur réseau communs. Les ordinateurs virtuels et les disques gérés mettant en œuvre les nœuds au sein d’un cluster HDInsight sont répartis dans ces domaines d’erreur. Cette architecture limite l’impact potentiel des défaillances de matériel physique.

Pour plus d’informations sur le nombre de domaines d’erreur dans une région, consultez le document [Disponibilité des machines virtuelles Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

> [!IMPORTANT]
> Si possible, utilisez une région Azure qui contient les trois domaines d’erreur, et créez des rubriques avec un facteur de réplication de 3.

Si vous utilisez une région qui contient uniquement deux domaines d’erreur, utilisez un facteur de réplication de 4 afin de répartir uniformément les réplicas sur les deux domaines d’erreur.

### <a name="kafka-and-fault-domains"></a>Kafka et les domaines d’erreur

Kafka n’est pas informé des domaines d’erreur. Lors de la création de réplicas de partitions pour les rubriques, il ne peut pas distribuer les réplicas correctement pour la haute disponibilité. Pour garantir une haute disponibilité, utilisez l’[outil de rééquilibrage de partitions de Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Cet outil doit être exécuté à partir d’une session SSH pour le nœud principal de votre cluster Kafka.

Pour garantir la haute disponibilité de vos données Kafka, vous devez rééquilibrer les réplicas de partition de votre rubrique lorsque :

* Vous créez une rubrique ou une partition

* Vous mettez à l’échelle un cluster

## <a name="delete-the-cluster"></a>Suppression du cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris les bases de l’utilisation d’Apache Kafka sur HDInsight. Consultez les articles suivants pour en savoir plus sur l’utilisation de Kafka :

* [Analyser les journaux Kafka](apache-kafka-log-analytics-operations-management.md)
* [Répliquer des données entre des clusters Kafka](apache-kafka-mirroring.md)
* [API de producteur et de consommateur Kafka avec HDInsight](apache-kafka-producer-consumer-api.md)
* [API de flux Kafka avec HDInsight](apache-kafka-streams-api.md)
* [Utiliser le streaming Apache Spark (DStream) avec Kafka sur HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Utiliser Apache Spark Structured Streaming avec Kafka sur HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Utiliser Apache Spark Structured Streaming pour déplacer des données de Kafka sur HDInsight vers Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Use Apache Storm with Kafka on HDInsight](../hdinsight-apache-storm-with-kafka.md) (Utilisation d’Apache Storm avec Kafka sur HDInsight)
* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Se connecter à Kafka via un réseau virtuel Azure)
* [Utiliser Kafka avec Azure Container Service](apache-kafka-azure-container-services.md)
* [Utiliser Kafka avec les applications de fonction Azure](apache-kafka-azure-functions.md)
