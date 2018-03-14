---
title: Utiliser Azure Container Service avec Kafka sur HDInsight | Microsoft Docs
description: "Découvrez comment utiliser Kafka sur HDInsight à partir d’images de conteneur hébergées dans Azure Container Service (AKS)."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2018
ms.author: larryfr
ms.openlocfilehash: 53342e11476a307bb6af356eb40fe51928041822
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="use-azure-container-services-with-kafka-on-hdinsight"></a>Utiliser Azure Container Service avec Kafka sur HDInsight

Découvrez comment utiliser Azure Container Service (AKS) avec Kafka sur un cluster HDInsight. Les étapes de ce document utilisent une application Node.js hébergée dans AKS pour vérifier la connectivité avec Kafka. Cette application utilise le package [kafka-node](https://www.npmjs.com/package/kafka-node) pour communiquer avec Kafka. Elle utilise [Socket.io](https://socket.io/) pour les échanges entre le navigateur client et le serveur principal hébergé dans AKS liés aux événements.

[Apache Kafka](https://kafka.apache.org) est une plateforme de diffusion en continu distribuée open source qui permet de générer des pipelines de données et des applications de diffusion en continu en temps réel. Azure Container Service gère votre environnement Kubernetes hébergé et facilite et accélère le déploiement des applications en conteneur. À l’aide d’un réseau virtuel Azure, vous pouvez connecter les deux services.

> [!NOTE]
> Ce document se concentre sur les étapes à suivre pour permettre à Azure Container Service de communiquer avec Kafka sur HDInsight. Nous prenons pour exemple un simple client Kafka pour montrer que la configuration fonctionne.

## <a name="prerequisites"></a>Prérequis

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Abonnement Azure

Ce document suppose que vous êtes familiarisé avec la création et l’utilisation des services Azure suivants :

* Kafka sur HDInsight
* Azure Container Service
* Réseaux virtuels Azure

Ce document suppose également que vous avez parcouru le [didacticiel Azure Container Service](../../aks/tutorial-kubernetes-prepare-app.md). Ce didacticiel crée un service de conteneur, un cluster Kubernetes, un registre de conteneur et configure l’utilitaire `kubectl`.

## <a name="architecture"></a>Architecture

### <a name="network-topology"></a>Topologie du réseau

HDInsight et AKS utilisent un réseau virtuel Azure comme conteneur pour les ressources de calcul. Pour activer la communication entre HDInsight et AKS, vous devez activer la communication entre leurs réseaux. Les étapes décrites dans ce document utilisent l’homologation de réseau virtuel pour les réseaux. D’autres connexions, comme les VPN, doivent également fonctionner. Pour plus d’informations sur l’homologation, consultez le document [Homologation de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md).


Le diagramme suivant illustre la topologie de réseau utilisée dans ce document :

![HDInsight dans un réseau virtuel, AKS dans un autre et les réseaux connectés à l’aide de l’homologation](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]
> La résolution de noms n’est pas activée entre les réseaux homologués, l’adressage IP est donc utilisé. Par défaut, Kafka sur HDInsight est configuré pour retourner les noms d’hôte à la place des adresses IP lorsque les clients se connectent. Les étapes décrites dans ce document modifient Kafka pour utiliser la publication IP à la place.

## <a name="create-an-azure-container-service-aks"></a>Créer un service Azure Container Service (AKS)

Si vous n’avez pas déjà un cluster AKS, utilisez un des documents suivants pour apprendre à créer un :

* [Déployer un cluster Azure Container Service (AKS) - Portail](../../aks/kubernetes-walkthrough-portal.md)
* [Déployer un cluster Azure Container Service (AKS) - CLI](../../aks/kubernetes-walkthrough.md)

> [!NOTE]
> AKS crée un réseau virtuel lors de l’installation. Ce réseau est homologué avec celui créé pour HDInsight dans la section suivante.

## <a name="configure-virtual-network-peering"></a>Configurer l’homologation de réseaux virtuels

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez __Groupes de ressources__, puis recherchez le groupe de ressources qui contient le réseau virtuel pour votre cluster AKS. Le nom du groupe de ressources est `MC_<resourcegroup>_<akscluster>_<location>`. Les entrées `resourcegroup` et `akscluster` sont le nom du groupe de ressources dans lequel vous avez créé le cluster et le nom du cluster. `location` est l’emplacement dans lequel le cluster a été créé.

2. Dans le groupe de ressources, sélectionnez la ressource __Réseau virtuel__.

3. Sélectionnez __Espace d’adressage__. Notez l’espace d’adressage indiqué.

4. Pour créer un réseau virtuel pour HDInsight, sélectionnez __+ Créer une ressource__, __Mise en réseau__, puis __Réseau virtuel__.

    > [!IMPORTANT]
    > Lorsque vous entrez les valeurs pour le nouveau réseau virtuel, vous devez utiliser un espace d’adressage qui ne chevauche pas celui utilisé par le réseau de cluster AKS.

    Utilisez le même __emplacement__ pour le réseau virtuel que celui que vous avez utilisé pour le cluster AKS.

    Attendez que le réseau virtuel ait été créé avant de passer à l’étape suivante.

5. Pour configurer l’homologation entre le réseau HDInsight et le réseau de cluster AKS, sélectionnez le réseau virtuel, puis sélectionnez __Homologations__. Sélectionnez __+ Ajouter__ et utilisez les valeurs suivantes pour remplir le formulaire :

    * __Nom__ : entrez un nom unique pour cette configuration d’homologation.
    * __Réseau virtuel__ : ce champ permet de sélectionner le réseau virtuel pour le **cluster AKS**.

    Laissez tous les autres champs sur la valeur par défaut, puis sélectionnez __OK__ pour configurer l’homologation.

6. Pour configurer l’homologation entre le réseau de cluster AKS et le réseau HDInsight, sélectionnez le __réseau virtuel du cluster AKS__, puis sélectionnez __Homologations__. Sélectionnez __+ Ajouter__ et utilisez les valeurs suivantes pour remplir le formulaire :

    * __Nom__ : entrez un nom unique pour cette configuration d’homologation.
    * __Réseau virtuel__ : ce champ permet de sélectionner le réseau virtuel pour le __cluster HDInsight__.

    Laissez tous les autres champs sur la valeur par défaut, puis sélectionnez __OK__ pour configurer l’homologation.

## <a name="install-kafka-on-hdinsight"></a>Installer Kafka sur HDInsight

Lorsque vous créez le cluster Kafka sur le cluster HDInsight, vous devez joindre le réseau virtuel créé précédemment pour HDInsight. Pour plus d’informations sur la création d’un cluster Kafka, consultez le document [Créer un cluster Kafka](apache-kafka-get-started.md).

> [!IMPORTANT]
> Lorsque vous créez le cluster, vous devez utiliser les __paramètres avancés__ pour joindre le réseau virtuel que vous avez créé pour HDInsight.

## <a name="configure-kafka-ip-advertising"></a>Configuration de la publication d’adresses IP Kafka

Suivez les étapes ci-dessous pour configurer Kafka afin qu’il publie des adresses IP plutôt que des noms de domaine :

1. Accédez à https://CLUSTERNAME.azurehdinsight.net via votre navigateur web. Remplacez __CLUSTERNAME__ par le nom du cluster Kafka sur HDInsight.

    Lorsque vous y êtes invité, utilisez le nom et le mot de passe utilisateur HTTPS correspondant au cluster. L’interface utilisateur web d’Ambari pour le cluster s’affiche.

2. Pour afficher plus d’informations sur Kafka, sélectionnez __Kafka__ dans la liste sur la gauche.

    ![Liste des services avec l’option Kafka en surbrillance](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Pour afficher la configuration Kafka, sélectionnez __Configurations__ en haut et au centre de la page.

    ![Liens vers les configurations Kafka](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Pour trouver la configuration __kafka-env__, entrez `kafka-env` dans le champ __Filtre__ situé en haut à droite.

    ![Configuration Kafka, pour kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Pour configurer Kafka afin qu’il publie des adresses IP, ajoutez le texte suivant au bas du champ __kafka-env-template__ :

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Pour configurer l’interface écoutée par Kafka, entrez `listeners` dans le champ __Filtre__ situé en haut à droite.

7. Pour configurer Kafka afin qu’il écoute toutes les interfaces réseau, remplacez la valeur du champ __écouteurs__ par `PLAINTEXT://0.0.0.0:9092`.

8. Utilisez le bouton __Enregistrer__ pour enregistrer les modifications apportées à la configuration. Entrez un message texte décrivant les modifications. Sélectionnez __OK__ une fois les modifications apportées.

    ![Bouton pour enregistrer la configuration](./media/apache-kafka-azure-container-services/save-button.png)

9. Pour éviter des erreurs lors du redémarrage de Kafka, utilisez le bouton __Service Actions__ (Actions du service) et sélectionnez __Activer le mode de maintenance__. Sélectionnez OK pour terminer cette opération.

    ![Actions de service, avec l’option Activer le mode de maintenance en surbrillance](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Pour redémarrer Kafka, utilisez le bouton __Redémarrer__ et sélectionnez __Restart All Affected__ (Redémarrer tous les éléments affectés). Confirmez le redémarrage, puis utilisez le bouton __OK__ une fois l’opération terminée.

    ![Bouton Redémarrer avec l’option Restart All Affected (Redémarrer tous les éléments affectés) en surbrillance](./media/apache-kafka-azure-container-services/restart-button.png)

11. Pour désactiver le mode de maintenance, utilisez le bouton __Service Actions__ (Actions du service) et sélectionnez __Désactiver le mode de maintenance__. Sélectionnez **OK** pour terminer cette opération.

## <a name="test-the-configuration"></a>Tester la configuration

À ce stade, Kafka et Azure Container Service sont en communication via les réseaux virtuels homologués. Pour tester cette connexion, procédez comme suit :

1. Créez une rubrique Kafka qui est utilisée par l’application de test. Pour plus d’informations sur la création de rubriques Kafka, consultez le document [Créer un cluster Kafka](apache-kafka-get-started.md).

2. Téléchargez l’exemple d’application à partir de [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test). 

3. Modifiez le fichier `index.js` et modifiez les lignes suivantes :

    * `var topic = 'mytopic'`: Remplacez `mytopic` par le nom de la rubrique Kafka utilisé par cette application.
    * `var brokerHost = '176.16.0.13:9092`: Remplacez `176.16.0.13` par l’adresse IP interne de l’un des hôtes du répartiteur pour votre cluster.

        Pour trouver l’adresse IP interne des hôtes du répartiteur (workernodes) dans le cluster, consultez le document [API Ambari REST](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes). Choisissez l’adresse IP de l’une des entrées dont le nom de domaine commence par `wn`.

4. À partir d’une ligne de commande dans le répertoire `src`, installez des dépendances et utilisez Docker pour générer une image pour le déploiement :

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]
    > Les packages requis par cette application sont archivés dans le référentiel, vous n’avez donc pas besoin d’utiliser l’utilitaire `npm` pour les installer.

5. Connectez-vous à votre service Azure Container Registry (ACR) et recherchez le nom de loginServer :

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]
    > Si vous ne connaissez pas le nom de votre service Azure Container Registry ou si vous ne savez pas comment utiliser l’interface de ligne de commande Azure pour fonctionner avec Azure Container Service, consultez les [didacticiels AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Marquez l’image `kafka-aks-test` locale avec le loginServer de votre ACR. Ajoutez également `:v1` à la fin pour indiquer la version de l’image :

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Envoyez l’image vers le registre :

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Cette opération prend plusieurs minutes.

8. Modifiez le fichier manifeste Kubernetes (`kafka-aks-test.yaml`) et remplacez `microsoft` par le nom loginServer d’ACR récupéré à l’étape 4.

9. Utilisez la commande suivante pour déployer les paramètres d’application à partir du manifeste :

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Utilisez la commande suivante pour surveiller le `EXTERNAL-IP` de l’application :

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Une fois qu’une adresse IP externe est affectée, utilisez __CTRL + C__ pour quitter la surveillance

11. Ouvrez un navigateur web et entrez l’adresse IP externe pour le service. Vous accédez à une page similaire à l’image ci-dessous :

    ![Image de la page web](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Entrez le texte dans le champ, puis sélectionnez le bouton __Envoyer__. Les données sont envoyées à Kafka. Ensuite, le consommateur Kafka dans l’application lit le message et l’ajoute à la section __Messages à partir de Kafka__.

    > [!WARNING]
    > Vous pouvez recevoir plusieurs copies d’un message. Ce problème se produit généralement lorsque vous actualisez votre navigateur une fois connecté, ou lorsque vous ouvrez plusieurs connexions de navigateur à l’application.

## <a name="next-steps"></a>Étapes suivantes

Cliquez sur les liens suivants pour apprendre à utiliser Apache Kafka sur HDInsight :

* [Prise en main de Kafka sur HDInsight](apache-kafka-get-started.md)

* [Utilisation de MirrorMaker pour créer un réplica de Kafka sur HDInsight](apache-kafka-mirroring.md)

* [Use Apache Storm with Kafka on HDInsight](../hdinsight-apache-storm-with-kafka.md) (Utilisation d’Apache Storm avec Kafka sur HDInsight)

* [Use Apache Spark with Kafka on HDInsight](../hdinsight-apache-spark-with-kafka.md) (Utilisation d’Apache Spark avec Kafka sur HDInsight)

* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Se connecter à Kafka via un réseau virtuel Azure)