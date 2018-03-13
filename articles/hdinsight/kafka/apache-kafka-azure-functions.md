---
title: "Utiliser Azure Functions pour envoyer des données vers Kafka sur HDInsight | Microsoft Docs"
description: "Apprenez à utiliser une fonction Azure pour écrire des données dans Kafka sur HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: larryfr
ms.openlocfilehash: c1c03cfcbcb7e0bfdb4a631b9e2ae568f0684069
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>Utiliser Kafka sur HDInsight depuis une application de fonction Azure

Apprenez à utiliser une application de fonction Azure qui envoie des données vers Kafka sur HDInsight.

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/) est un service de calcul sans serveur. Ce service vous permet d’exécuter du code à la demande sans explicitement configurer ou gérer l’infrastructure.

[Apache Kafka](https://kafka.apache.org) est une plateforme de diffusion en continu distribuée open source qui permet de générer des pipelines de données et des applications de diffusion en continu en temps réel. Kafka fournit également des fonctionnalités de courtier de messages semblables à une file d’attente, où vous pouvez publier et vous abonner aux flux de données nommés. Kafka sur HDInsight vous offre un service géré, hautement évolutif et hautement disponible dans le cloud Microsoft Azure.

Kafka sur HDInsight ne fournit pas d’API sur l’Internet public. Pour publier ou utiliser des données depuis Kafka, vous devez vous connecter à un cluster Kafka à l’aide d’un réseau virtuel Azure. Azure Functions offre une façon pratique de créer un point de terminaison public pour envoyer des données vers Kafka sur HDInsight, via une passerelle de réseau virtuel.

> [!NOTE]
> Ce document se concentre sur les étapes à suivre pour permettre à Azure Functions de communiquer avec Kafka sur HDInsight. Nous prenons pour exemple un simple producteur Kafka pour montrer que la configuration fonctionne.

## <a name="prerequisites"></a>Prérequis

* Une application de fonction Azure. Pour en savoir plus, consultez la documentation [Créer votre première fonction](../../azure-functions/functions-create-first-azure-function.md).

* Un réseau virtuel Azure. Pour utiliser Azure Functions, le réseau virtuel doit utiliser une des plages d’adresses IP suivantes :

    * 10.0.0.0-10.255.255.255
    * 172.16.0.0-172.31.255.255
    * 192.168.0.0-192.168.255.255

    Pour en savoir plus, consultez le document [Intégrer votre application à un réseau Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md).

* Un cluster Kafka sur HDInsight. Pour savoir comment créer un cluster Kafka sur HDInsight, consultez le document [Créer un cluster Kafka](apache-kafka-get-started.md).

    > [!IMPORTANT]
    > Lors de la configuration du cluster, vous devez utiliser l’étape __Paramètres avancés__ pour sélectionner un réseau virtuel Azure et un sous-réseau utilisés par HDInsight. Sélectionnez le réseau virtuel et sous-réseau créés dans l’étape précédente.

    Pour en savoir plus sur Kafka et les réseaux virtuels, consultez le document [Se connecter à Kafka via un réseau virtuel](apache-kafka-connect-vpn-gateway.md).

## <a name="architecture"></a>Architecture

Kafka sur HDInsight est contenu dans un réseau virtuel Azure. Azure Functions peut communiquer avec le réseau virtuel en utilisant une passerelle point à site. L’image suivante représente un diagramme de la topologie de ce réseau :

![Architecture du service Azure Functions se connectant à HDInsight](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>Configurer Kafka

Les informations dans cette section préparent le cluster Kafka à accepter les données envoyées par la fonction Azure. Elles regroupent les actions de configurations suivantes :

* Publication d’adresses IP
* Collecte des adresses IP du répartiteur Kafka
* Création d’une rubrique Kafka

### <a name="configure-kafka-for-ip-advertising"></a>Configuration de Kafka pour la publication d’adresses IP

Par défaut, Zookeeper renvoie le nom de domaine des répartiteurs Kafka aux clients. Cette configuration ne fonctionne pas sans serveur DNS, car le client (Azure Functions) ne peut résoudre des noms pour le réseau virtuel. Suivez les étapes ci-dessous pour configurer Kafka afin qu’il publie des adresses IP au lieu des noms de domaine :

1. Accédez à https://CLUSTERNAME.azurehdinsight.net via votre navigateur web. Remplacez __CLUSTERNAME__ par le nom du cluster Kafka sur HDInsight.

    Lorsque vous y êtes invité, utilisez le nom et le mot de passe utilisateur HTTPS correspondant au cluster. L’interface utilisateur web d’Ambari pour le cluster s’affiche.

2. Pour afficher plus d’informations sur Kafka, sélectionnez __Kafka__ dans la liste sur la gauche.

    ![Liste des services avec l’option Kafka en surbrillance](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. Pour afficher la configuration Kafka, sélectionnez __Configurations__ en haut et au centre de la page.

    ![Liens vers les configurations Kafka](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. Pour trouver la configuration __kafka-env__, entrez `kafka-env` dans le champ __Filtre__ situé en haut à droite.

    ![Configuration Kafka, pour kafka-env](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

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

    ![Bouton pour enregistrer la configuration](./media/apache-kafka-azure-functions/save-button.png)

9. Pour éviter des erreurs lors du redémarrage de Kafka, utilisez le bouton __Service Actions__ (Actions du service) et sélectionnez __Activer le mode de maintenance__. Sélectionnez OK pour terminer cette opération.

    ![Actions de service, avec l’option Activer le mode de maintenance en surbrillance](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. Pour redémarrer Kafka, utilisez le bouton __Redémarrer__ et sélectionnez __Restart All Affected__ (Redémarrer tous les éléments affectés). Confirmez le redémarrage, puis utilisez le bouton __OK__ une fois l’opération terminée.

    ![Bouton Redémarrer avec l’option Restart All Affected (Redémarrer tous les éléments affectés) en surbrillance](./media/apache-kafka-azure-functions/restart-button.png)

11. Pour désactiver le mode de maintenance, utilisez le bouton __Service Actions__ (Actions du service) et sélectionnez __Désactiver le mode de maintenance__. Sélectionnez **OK** pour terminer cette opération.

### <a name="get-the-kafka-broker-ip-address"></a>Obtenir l’adresse IP du répartiteur Kafka

Pour récupérer le nom de domaine complet (FQDN) et les adresses IP des nœuds du cluster Kafka, utilisez l’une des méthodes suivantes :

```powershell
$resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

Cette commande suppose que `<resourcegroupname>` est le nom du groupe de ressources Azure qui contient le réseau virtuel Azure.

À partir de la liste des noms obtenus, sélectionnez l’adresse IP du nœud worker. Le nom de domaine complet du nœud commence par les lettres `wn`. Cette adresse IP est utilisée par la fonction pour communiquer avec Kafka.

### <a name="create-a-kafka-topic"></a>Création d’une rubrique Kafka

Kafka stocke des données dans __rubriques__. Avant d’envoyer des données vers Kafka depuis une fonction Azure, vous devez créer la fonction.

Pour créer une rubrique, suivez les étapes du document [Créer un cluster Kafka](apache-kafka-get-started.md).

## <a name="create-a-function-that-sends-to-kafka"></a>Créer une fonction qui envoie des données vers Kafka

> [!NOTE]
> Les étapes de cette section permettent de créer une fonction JavaScript qui utilise le package [kafka-node](https://www.npmjs.com/package/kafka-node) pour publier des données dans Kafka :

1. Créez une fonction __WebHook + API__, et sélectionnez __JavaScript__ comme langage. Pour savoir comment créer des fonctions, consultez la documentation [Créer votre première fonction](../../azure-functions/functions-create-first-azure-function.md#create-function).

2. Utilisez le code suivant comme corps de la fonction :

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    Remplacez `'test'` par le nom de la rubrique Kafka que vous avez créée sur votre cluster HDInsight.

    Remplacez `10.1.0.7` par l’adresse IP que vous avez récupérée plus tôt. Ne touchez pas à la valeur `:9092`. 9092 correspond au port qu’écoute Kafka.

    Utilisez le bouton __Enregistrer__ pour enregistrer les modifications.

    ![Éditeur avec le bouton Enregistrer](./media/apache-kafka-azure-functions/function-editor.png)

3. À droite de l’éditeur de fonction, sélectionnez __Afficher fichiers__. Sélectionnez __+ Ajouter__ et ajoutez un nouveau fichier nommé `package.json`. Ce fichier sert à spécifier la dépendance du package `kafka-node`.

    ![Ajouter un fichier](./media/apache-kafka-azure-functions/add-files.png)

4. Pour modifier le nouveau fichier, sélectionnez `package.json` depuis la liste __Afficher les fichiers__. Utilisez les données suivantes comme contenu du fichier :

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    Utilisez le bouton __Enregistrer__ pour enregistrer les modifications.

5. Pour installer le package `kafka-node`, utilisez la section _Version du nœud et gestion de package_ du [guide du développeur JavaScript Azure Functions](../../azure-functions/functions-reference-node.md#node-version-and-package-management).

    > [!NOTE]
    > Vous pouvez recevoir plusieurs erreurs car le package kafka-node est installé. Vous pouvez ignorer ces erreurs.

## <a name="run-the-function"></a>Exécuter la fonction

À droite de l’éditeur de fonction, sélectionnez __Test__. Conservez les paramètres par défaut pour le test, puis sélectionnez __Exécuter__. Tandis qu’il s’exécute, le test transmet un paramètre `name` à la fonction. Ce paramètre contient une valeur de `Azure`, que la fonction insère dans Kafka.

![Capture d’écran de la boîte de dialogue test](./media/apache-kafka-azure-functions/function-test-dialog.png)

Pour afficher les informations consignées par la fonction pendant l’exécution du test, sélectionnez __Journaux__ en bas de la page. Exécutez le test à nouveau pour générer les informations du journal.

![Exemple de résultat de la fonction Log](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>Vérifier que les données sont dans Kafka

Pour vérifier que les données sont arrivées dans la rubrique Kafka, utilisez les informations dans la section _Produire et utiliser des enregistrements_ du document [Créer un cluster Kafka](apache-kafka-get-started.md#produce-and-consume-records). L’élément `kafka-console-consumer` lit les données de la rubrique et affiche une liste des messages qui y sont stockés.

## <a name="next-steps"></a>étapes suivantes

Cliquez sur les liens suivants pour apprendre à utiliser Apache Kafka sur HDInsight :

* [Prise en main de Kafka sur HDInsight](apache-kafka-get-started.md)

* [Utilisation de MirrorMaker pour créer un réplica de Kafka sur HDInsight](apache-kafka-mirroring.md)

* [Use Apache Storm with Kafka on HDInsight](../hdinsight-apache-storm-with-kafka.md) (Utilisation d’Apache Storm avec Kafka sur HDInsight)

* [Use Apache Spark with Kafka on HDInsight](../hdinsight-apache-spark-with-kafka.md) (Utilisation d’Apache Spark avec Kafka sur HDInsight)

* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Se connecter à Kafka via un réseau virtuel Azure)