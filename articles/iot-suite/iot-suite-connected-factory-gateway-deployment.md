---
title: "Déployer votre passerelle Usine connectée - Azure | Microsoft Docs"
description: "Comment déployer une passerelle sur Windows ou Linux pour activer la connectivité à la solution préconfigurée Usine connectée."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: 4606cb676c3ab7c8c8511579f43d251ff7d2ae8a
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-preconfigured-solution-on-windows-or-linux"></a>Déployer une passerelle de périmètre pour la solution préconfigurée Usine connectée sur Windows ou Linux

Vous avez besoin de deux composants logiciels pour déployer une passerelle de périmètre pour la solution préconfigurée *Usine connectée* :

- Le *Proxy OPC* établit une connexion à Usine connectée. Le Proxy OPC établit une connexion à IoT Hub et attend les messages de contrôle et de commande du navigateur OPC intégré qui s’exécute sur le portail de la solution Usine connectée.

- *L’Éditeur d’OPC* se connecte aux serveurs OPC UA locaux existants et transfère leurs messages de télémétrie à Usine connectée. Vous pouvez connecter un appareil classique OPC à l’aide de [l’adaptateur classique OPC pour OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Les deux composants sont open source et disponibles en tant que sources sur GitHub et en tant que conteneurs Docker sur DockerHub :

| GitHub | DockerHub |
| ------ | --------- |
| [Éditeur d’OPC](https://github.com/Azure/iot-edge-opc-publisher) | [Éditeur d’OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [Proxy OPC](https://github.com/Azure/iot-edge-opc-proxy)         | [Proxy OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Aucune adresse IP publique ni aucun port entrant ouvert dans le pare-feu de la passerelle n’est nécessaire pour l’un ou l’autre des deux composants. Les composants Proxy OPC et Éditeur d’OPC utilisent uniquement le port sortant 443.

Les étapes décrites dans cet article montrent comment déployer une passerelle de périmètre à l’aide de Docker sur Windows ou Linux. La passerelle permet de se connecter à la solution préconfigurée Usine connectée. Vous pouvez également utiliser les composants sans Usine connectée.

> [!NOTE]
> Les deux composants peuvent être utilisés en tant que modules dans [Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Choisir un appareil de passerelle

Si vous ne possédez pas encore d’appareil de passerelle, Microsoft vous recommande d’acheter une passerelle disponible dans le commerce auprès de l’un de nos partenaires. Pour obtenir la liste des passerelles compatibles avec la solution Usine connectée, consultez le [catalogue d’appareils Azure IoT](https://catalog.azureiotsuite.com/?q=opc). Suivez les instructions fournies avec la passerelle pour la configurer.

Vous pouvez aussi utiliser les instructions suivantes pour configurer manuellement un appareil de passerelle existant.

## <a name="install-and-configure-docker"></a>Installer et configurer Docker

Installez [Docker pour Windows](https://www.docker.com/docker-windows) sur votre appareil de passerelle basé sur Windows ou utilisez un gestionnaire de package pour installer Docker sur votre appareil de passerelle basé sur Linux.

Pendant l’installation de Docker pour Windows, sélectionnez sur l’ordinateur hôte un lecteur à partager avec Docker. La capture d’écran suivante montre comment partager le lecteur **D** sur votre système Windows pour autoriser l’accès au lecteur hôte à partir d’un conteneur Docker :

![Installer Docker pour Windows](./media/iot-suite-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Vous pouvez également effectuer cette étape après l’installation de Docker, à partir de la boîte de dialogue **Paramètres**. Cliquez avec le bouton droit sur l’icône **Docker** dans la barre d’état du système Windows, puis choisissez **Paramètres**. Si des mises à jour Windows importantes ont été déployées sur le système, telles que la mise à jour de Windows Fall Creators, annulez le partage des lecteurs, puis partagez-les de nouveau pour actualiser les droits d’accès.

Si vous utilisez Linux, aucune configuration supplémentaire n’est requise pour activer l’accès au système de fichiers.

Sur Windows, créez un dossier sur le lecteur que vous avez partagé avec Docker ; sur Linux, créez un dossier sous le système de fichiers racine. Cette procédure pas à pas fait référence à ce dossier sous la forme `<SharedFolder>`.

Quand vous faites référence à `<SharedFolder>` dans une commande Docker, veillez à utiliser la syntaxe correcte pour votre système d’exploitation. Voici deux exemples, l’un pour Windows et l’autre pour Linux :

- Si vous utilisez le dossier `D:\shared` sur Windows en tant que `<SharedFolder>`, la syntaxe de la commande Docker est `d:/shared`.

- Si vous utilisez le dossier `/shared` sur Linux en tant que `<SharedFolder>`, la syntaxe de la commande Docker est `/shared`.

Pour plus d’informations, consultez le guide de référence sur le moteur Docker [Use volumes](https://docs.docker.com/engine/admin/volumes/volumes/) (Utiliser des volumes).

## <a name="configure-the-opc-components"></a>Configurer les composants OPC

Avant d’installer les composants OPC, effectuez les étapes suivantes pour préparer votre environnement :

1. Pour procéder au déploiement de passerelle, vous avez besoin de la chaîne de connexion **iothubowner** du hub IoT dans votre déploiement de la solution Usine connectée. Dans le [portail Azure](http://portal.azure.com/), accédez à votre hub IoT dans le groupe de ressources créé durant le déploiement de la solution Usine connectée. Cliquez sur **Stratégies d’accès partagé** pour accéder à la chaîne de connexion **iothubowner** :

    ![Recherche de la chaîne de connexion de l’IoT Hub](./media/iot-suite-connected-factory-gateway-deployment/image2.png)

    Copiez la valeur **Clé primaire de la chaîne de connexion**.

1. Pour permettre la communication entre les conteneurs Docker, vous avez besoin d’un réseau de pont défini par l’utilisateur. Pour créer un réseau de pont pour vos conteneurs, exécutez les commandes suivantes depuis une invite de commandes :

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Pour vérifier que le réseau de pont **iot_edge** a été créé, exécutez la commande suivante :

    ```cmd/sh
    docker network ls
    ```

    Votre réseau de pont **iot_edge** est inclus dans la liste des réseaux.

Pour exécuter l’Éditeur d’OPC, exécutez la commande suivante depuis une invite de commandes :

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.3 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- Le [dépôt GitHub de l’Éditeur d’OPC](https://github.com/Azure/iot-edge-opc-publisher) et le [guide de référence sur la commande docker run](https://docs.docker.com/engine/reference/run/) fournissent plus d’informations sur les éléments suivants :

  - Les options de ligne de commande Docker spécifiées avant le nom du conteneur (`microsoft/iot-edge-opc-publisher:2.1.3`)
  - La signification des paramètres de ligne de commande de l’Éditeur d’OPC spécifiés après le nom du conteneur (`microsoft/iot-edge-opc-publisher:2.1.3`)

- `<IoTHubOwnerConnectionString>` est la chaîne de connexion **iothubowner** de stratégie d’accès partagé à partir du portail Azure. Vous avez copié cette chaîne de connexion à l’étape précédente. Vous n’avez besoin de cette chaîne de connexion que pour la première exécution de l’Éditeur d’OPC. Pour les exécutions suivantes, vous devez l’omettre, car elle présente un risque de sécurité.

- Le `<SharedFolder>` que vous utilisez et sa syntaxe sont décrits dans la section [Installer et configurer Docker](#install-and-configure-docker). L’Éditeur d’OPC utilise le `<SharedFolder>` pour lire et écrire son fichier de configuration, écrire dans le fichier journal et rendre ces deux fichiers disponibles en dehors du conteneur.

- L’Éditeur d’OPC lit sa configuration à partir du fichier **publishednodes.json**, qui est lu à partir de et écrit dans le dossier `<SharedFolder>/docker`. Ce fichier de configuration définit les données de nœud OPC UA sur un serveur OPC UA spécifique auxquelles l’Éditeur d’OPC doit s’abonner. La syntaxe complète du fichier **publishednodes.json** est décrite dans la page GitHub consacrée à [l’Éditeur d’OPC](https://github.com/Azure/iot-edge-opc-publisher). Quand vous ajoutez une passerelle, placez un fichier **publishednodes.json** vide dans le dossier :

    ```json
    [
    ]
    ```

- Chaque fois que le serveur OPC UA notifie l’Éditeur d’OPC d’une modification de données, la nouvelle valeur est envoyée à IoT Hub. Selon les paramètres de traitement par lot, l’Éditeur d’OPC peut accumuler les données avant de les envoyer à IoT Hub dans un lot.

- Docker ne prend pas en charge la résolution de noms NetBIOS, mais uniquement la résolution de noms DNS. Si vous n’avez pas de serveur DNS sur le réseau, vous pouvez utiliser la solution de contournement indiquée dans l’exemple de ligne de commande précédent. L’exemple de ligne de commande précédent utilise le paramètre `--add-host` pour ajouter une entrée dans le fichier d’hôtes de conteneurs. Cette entrée permet d’effectuer une recherche de nom d’hôte pour le `<OpcServerHostname>` donné et de le résoudre en l’adresse IP `<IpAddressOfOpcServerHostname>` donnée.

- OPC UA utilise des certificats X.509 pour l’authentification et le chiffrement. Vous devez placer le certificat de l’Éditeur d’OPC sur le serveur OPC UA auquel vous vous connectez, afin qu’il approuve l’Éditeur d’OPC. Le magasin du certificat de l’Éditeur d’OPC se trouve dans le dossier `<SharedFolder>/CertificateStores`. Le certificat de l’Éditeur d’OPC se trouve dans le dossier `trusted/certs` du dossier `CertificateStores`.

  Les étapes pour configurer le serveur OPC UA dépendent de l’appareil que vous utilisez. Ces étapes sont généralement documentées dans le manuel de l’utilisateur du serveur OPC UA.

Pour exécuter le Proxy OPC, exécutez la commande suivante depuis une invite de commandes :

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Vous ne devez exécuter l’installation qu’une seule fois sur un système.

Utilisez la commande suivante pour exécuter le Proxy OPC :

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -D /mapped/cs.db
```

Le Proxy OPC enregistre la chaîne de connexion pendant l’installation. Pour les exécutions suivantes, vous devez omettre la chaîne de connexion, car elle présente un risque de sécurité.

## <a name="enable-your-gateway"></a>Activer votre passerelle

Effectuez les étapes suivantes pour activer votre passerelle dans la solution préconfigurée Usine connectée :

1. Quand les deux composants sont en cours d’exécution, accédez à la page **Connectez votre propre serveur OPC UA**du portail de solution Usine connectée. Cette page est uniquement disponible pour les administrateurs dans la solution. Entrez l’URL du point de terminaison du serveur de publication (opc.tcp://publisher:62222) et cliquez sur **Connecter**.

1. Établissez une relation d’approbation entre le portail Usine connectée et l’Éditeur d’OPC. Quand vous voyez un avertissement de certificat, cliquez sur **Continuer**. Un message d’erreur apparaît alors, indiquant que l’Éditeur d’OPC n’approuve pas le client web UA. Pour résoudre cette erreur, copiez le certificat du **client web UA** depuis le dossier `<SharedFolder>/CertificateStores/rejected/certs` vers le dossier `<SharedFolder>/CertificateStores/trusted/certs` sur la passerelle. Il est inutile de redémarrer la passerelle.

Vous pouvez désormais vous connecter à la passerelle à partir du cloud et ajouter des serveurs OPC UA à la solution.

## <a name="add-your-own-opc-ua-servers"></a>Ajouter vos propres serveurs OPC UA

Pour ajouter vos propres serveurs OPC UA à la solution préconfigurée Usine connectée :

1. Accédez à la page **Connectez votre propre serveur OPC UA** du portail de solution Usine connectée.

    1. Démarrez le serveur OPC UA auquel vous souhaitez vous connecter. Assurez-vous que votre serveur OPC UA est accessible à partir de l’Éditeur OPC et du proxy OPC exécutés dans le conteneur (reportez-vous aux commentaires précédents sur la résolution des noms).
    1. Entrez l’URL du point de terminaison de votre serveur OPC UA (`opc.tcp://<host>:<port>`) et cliquez sur **Connexion**.
    1. Durant la configuration de la connexion, une relation d’approbation entre le portail Usine connectée (client OPC UA) et le serveur OPC UA auquel vous essayez de vous connecter est établie. Dans le tableau de bord Usine connectée, un avertissement du type **Impossible de vérifier le certificat du serveur auquel vous souhaitez vous connecter** s’affiche. Quand vous voyez un avertissement de certificat, cliquez sur **Continuer**.
    1. La configuration du certificat du serveur OPC UA auquel vous essayez de vous connecter est plus difficile à effectuer. Pour les serveurs OPC UA de type PC, il vous suffit généralement de confirmer une boîte de dialogue d’avertissement qui s’affiche dans le tableau de bord. Pour les systèmes de serveur OPC UA incorporés, consultez la documentation de votre serveur OPC UA pour connaître la procédure à suivre. Pour effectuer cette tâche, vous aurez peut-être besoin du certificat du client OPC UA du portail Usine connectée. Les administrateurs peuvent télécharger ce certificat à partir de la page **Connectez votre propre serveur OPC UA** :

        ![Portail de la solution](./media/iot-suite-connected-factory-gateway-deployment/image4.png)

1. Naviguez dans l’arborescence de nœuds OPC UA de votre serveur OPC UA, cliquez avec le bouton droit sur les nœuds OPC pour envoyer les valeurs à Usine connectée, puis sélectionnez **Publier**.

1. Les données de télémétrie transitent maintenant à partir de la passerelle. Vous pouvez afficher les données de télémétrie dans la vue **Emplacements des usines** du portail de solution Usine connectée, sous **Nouvelle fabrique**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’architecture de la solution préconfigurée Usine connectée, consultez la [présentation de la solution préconfigurée Usine connectée](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-sample-walkthrough).

Découvrez [l’implémentation de référence de l’éditeur OPC](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).