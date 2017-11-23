---
title: "Déploiement de votre passerelle d’usine connectée - Azure | Microsoft Docs"
description: "Comment déployer une passerelle sur Windows ou Linux pour activer la connectivité à la solution préconfigurée d’usine connectée."
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
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: f6a69ecbeb09dc042eff7c1f95ee518e701b0507
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>Déployer une passerelle sur Windows ou Linux pour la solution préconfigurée d’usine connectée

Le logiciel requis afin de déployer une passerelle pour la solution préconfigurée d’usine connectée comporte deux composants :

* Le *Proxy OPC* établit une connexion à IoT Hub. Le *Proxy OPC* établit une connexion à IoT Hub et attend les messages de contrôle et de commande du navigateur OPC intégré qui s’exécute sur le portail de la solution d’usine connectée.
* *L’Éditeur d’OPC* se connecte aux serveurs OPC UA locaux existants et transfère leurs messages de télémétrie à IoT Hub.

Les deux composants sont open source et disponibles en tant que sources sur GitHub et en tant que conteneurs Docker :

| GitHub | DockerHub |
| ------ | --------- |
| [Éditeur d’OPC][lnk-publisher-github] | [Éditeur d’OPC][lnk-publisher-docker] |
| [Proxy OPC][lnk-proxy-github] | [Proxy OPC][lnk-proxy-docker] |

Aucune adresse IP publique ni aucune faille dans le pare-feu de la passerelle n’est requise pour aucun des deux composants. Le Proxy OPC et l’Éditeur d’OPC utilisent uniquement les ports sortants 443, 5671 et 8883.

Les étapes décrites dans cet article montrent comment déployer une passerelle à l’aide de Docker sur [Windows](#windows-deployment) ou [Linux](#linux-deployment). La passerelle permet de se connecter à la solution préconfigurée d’usine connectée.

> [!NOTE]
> Le logiciel de passerelle qui s’exécute dans le conteneur Docker est [Azure IoT Edge].

## <a name="windows-deployment"></a>Déploiement sous Windows

> [!NOTE]
> Si vous ne possédez pas encore d’appareil de passerelle, Microsoft vous recommande d’acheter une passerelle disponible dans le commerce auprès de l’un de nos partenaires. Consultez le [catalogue d’appareils Azure IoT] pour obtenir la liste des passerelles compatibles avec la solution d’usine connectée. Suivez les instructions fournies avec la passerelle pour la configurer. Vois pouvez aussi utiliser les instructions suivantes pour configurer manuellement une de vos passerelles existantes.

### <a name="install-docker"></a>Installation de Docker

Installez [Docker pour Windows] sur votre passerelle basée sur Windows. Pendant l’installation de Windows Docker, sélectionnez sur l’ordinateur hôte un lecteur à partager avec Docker. La capture d’écran suivante illustre le partage du lecteur D sur votre système Windows :

![Installation de Docker][img-install-docker]

Créez ensuite un dossier appelé **docker** à la racine du lecteur partagé.
Vous pouvez également effectuer cette étape après l’installation de Docker, à partir du menu **Paramètres**.

### <a name="configure-the-gateway"></a>Configurer la passerelle

1. Pour procéder au déploiement de passerelle, vous avez besoin de la chaîne de connexion **iothubowner** de votre déploiement d’usine connectée Azure IoT Suite. Dans le [portail Azure], accédez à votre IoT Hub dans le groupe de ressources créé lors du déploiement de la solution d’usine connectée. Cliquez sur **Stratégies d’accès partagé** pour accéder à la chaîne de connexion **iothubowner** :

    ![Recherche de la chaîne de connexion de l’IoT Hub][img-hub-connection]

    Copiez la valeur **Clé primaire de la chaîne de connexion**.

1. Configurez la passerelle pour votre IoT Hub en exécutant **une seule fois** les deux modules de passerelle à partir d’une invite de commandes, avec :

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** est le nom à donner à votre Éditeur OPC UA au format **publisher.&lt;votre nom de domaine complet&gt;**. Par exemple, si votre réseau d’usine se nomme **myfactorynetwork.com**, la valeur **ApplicationName** est **publisher.myfactorynetwork.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;** est la chaîne de connexion **iothubowner** que vous avez copiée à l’étape précédente. Cette chaîne de connexion est utilisée uniquement dans cette étape ; elle vous sera inutile dans les étapes suivantes :

    Utilisez le dossier mappé D :\\docker (l’argument `-v`) ultérieurement pour conserver les deux certificats X.509 utilisés par les modules de passerelle.

### <a name="run-the-gateway"></a>Exécuter la passerelle

1. Redémarrez la passerelle en utilisant les commandes suivantes :

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Pour des raisons de sécurité, les deux certificats X.509 conservés dans le dossier D:\\docker contiennent la clé privée. L’accès à ce dossier doit être limité aux informations d’identification (généralement **administrateurs**) utilisées pour exécuter le conteneur Docker. Cliquez avec le bouton droit sur le dossier D:\\docker, choisissez **Propriétés**, puis **Sécurité**, puis **Modifier**. Donnez aux **Administrateurs** le contrôle total, et supprimez tous les autres utilisateurs :

    ![Accorder des autorisations au partage Docker][img-docker-share]

1. Vérifiez la connectivité du réseau. Dans une invite de commandes, entrez la commande `ping publisher.<your fully qualified domain name>` pour effectuer un test ping sur votre passerelle. Si la destination est inaccessible, ajoutez l’adresse IP et le nom de votre passerelle aux fichiers hosts de votre passerelle. Le fichier hosts se trouve dans le dossier **Windows\\System32\\drivers\\etc**.

1. Essayez ensuite de vous connecter au serveur de publication à l’aide d’un client OPC UA local en cours d’exécution sur la passerelle. L’URL du point de terminaison OPC UA est `opc.tcp://publisher.<your fully qualified domain name>:62222`. Si vous n’avez pas de client OPC UA, vous pouvez télécharger un [client OPC UA open source].

1. Lorsque vous avez terminé ces tests en local, accédez à la page **Connect your own OPC UA Server** (Connecter votre propre serveur OPC UA) du portail de solution d’usine connectée. Entrez l’URL du point de terminaison du serveur de publication (`tcp://publisher.<your fully qualified domain name>:62222`) et cliquez sur **Connecter**. Vous obtenez un avertissement de certificat ; cliquez sur **Continuer.** Vous recevez un message d’erreur indiquant que le serveur de publication n’approuve pas le client web UA. Pour résoudre cette erreur, copiez le certificat **UA Web Client** du dossier **D:\\docker\\Rejected Certificates\\certs** dans le dossier **D:\\docker\\UA Applications\\certs** de la passerelle. Il est inutile de redémarrer la passerelle. Répétez cette étape. Vous pouvez désormais vous connecter à la passerelle à partir du cloud et ajouter des serveurs OPC UA à la solution.

### <a name="add-your-opc-ua-servers"></a>Ajouter vos serveurs OPC UA

1. Accédez à la page **Connect your own OPC UA Server** (Connecter votre propre serveur OPC UA) du portail de solution d’usine connectée. Suivez les mêmes étapes que dans la section précédente pour établir une liaison approuvée entre le portail de la solution d’usine connectée et le serveur OPC UA. Cette étape établit une approbation mutuelle des certificats à partir du portail de solution d’usine connectée et le serveur OPC UA ; elle crée également une connexion.

1. Naviguez dans l’arborescence de nœuds OPC UA de votre serveur OPC UA, cliquez avec le bouton droit sur les nœuds OPC et sélectionnez **Publier**. Pour que la publication réussisse, le serveur OPC UA et le serveur de publication doivent se trouver sur le même réseau. En d’autres termes, si le nom de domaine complet du serveur de publication est **publisher.mondomaine.com**, le nom de domaine complet du serveur OPC UA doit être, par exemple, **myopcuaserver.mondomaine.com**. Si votre installation est différente, vous pouvez ajouter manuellement des nœuds au fichier publishesnodes.json contenu dans le dossier **D:\\docker**. Le fichier publishesnodes.json est généré automatiquement dès la publication du premier nœud OPC.

1. Les données de télémétrie transitent maintenant à partir de la passerelle. Vous pouvez afficher les données de télémétrie dans la vue **Factory Locations** (Emplacements d’usine) du portail de solution d’usine connectée, sous **New Factory** (Nouvelle usine).

## <a name="linux-deployment"></a>Déploiement sous Linux

> [!NOTE]
> Si vous ne possédez pas encore d’appareil de passerelle, Microsoft vous recommande d’acheter une passerelle disponible dans le commerce auprès de l’un de nos partenaires. Consultez le [catalogue d’appareils Azure IoT] pour obtenir la liste des passerelles compatibles avec la solution d’usine connectée. Suivez les instructions fournies avec la passerelle pour la configurer. Vois pouvez aussi utiliser les instructions suivantes pour configurer manuellement une de vos passerelles existantes.

[Installez Docker] sur votre passerelle Linux.

### <a name="configure-the-gateway"></a>Configurer la passerelle

1. Pour procéder au déploiement de passerelle, vous avez besoin de la chaîne de connexion **iothubowner** de votre déploiement d’usine connectée Azure IoT Suite. Dans le [portail Azure], accédez à votre IoT Hub dans le groupe de ressources créé lors du déploiement de la solution d’usine connectée. Cliquez sur **Stratégies d’accès partagé** pour accéder à la chaîne de connexion **iothubowner** :

    ![Recherche de la chaîne de connexion de l’IoT Hub][img-hub-connection]

    Copiez la valeur **Clé primaire de la chaîne de connexion**.

1. Configurez la passerelle pour votre IoT Hub en exécutant **une seule fois** les deux modules de passerelle à partir d’un interpréteur de commandes, avec :

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** est le nom de l’application OPC UA créée par la passerelle au format **publisher.&lt;votre nom de domaine complet&gt;**. Par exemple, **publisher.microsoft.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;** est la chaîne de connexion **iothubowner** que vous avez copiée à l’étape précédente. Cette chaîne de connexion est utilisée uniquement dans cette étape ; elle vous sera inutile dans les étapes suivantes :

    Utilisez le dossier **/shared** (`-v` l’argument) ultérieurement pour conserver les deux certificats X.509 utilisés par les modules de passerelle.

### <a name="run-the-gateway"></a>Exécuter la passerelle

1. Redémarrez la passerelle en utilisant les commandes suivantes :

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 --rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Pour des raisons de sécurité, les deux certificats X.509 conservés dans le dossier **/shared** contiennent la clé privée. Limitez l’accès à ce dossier aux informations d’identification utilisées pour exécuter le conteneur Docker. Pour définir les autorisations en tant que **root** uniquement, utilisez l’interpréteur de commandes `chmod` sur le dossier.

1. Vérifiez la connectivité du réseau. À partir d’un interrupteur de commandes, entrez la commande `ping publisher.<your fully qualified domain name>` pour effectuer un test ping sur votre passerelle. Si la destination est inaccessible, ajoutez l’adresse IP et le nom de votre passerelle au fichier hosts de votre passerelle. Le fichier hosts se trouve dans le dossier **/etc**.

1. Essayez ensuite de vous connecter au serveur de publication à l’aide d’un client OPC UA local en cours d’exécution sur la passerelle. L’URL du point de terminaison OPC UA est `opc.tcp://publisher.<your fully qualified domain name>:62222`. Si vous n’avez pas de client OPC UA, vous pouvez télécharger un [client OPC UA open source].

1. Lorsque vous avez terminé ces tests en local, accédez à la page **Connect your own OPC UA Server** (Connecter votre propre serveur OPC UA) du portail de solution d’usine connectée. Entrez l’URL du point de terminaison du serveur de publication (`tcp://publisher.<your fully qualified domain name>:62222`) et cliquez sur **Connecter**. Vous obtenez un avertissement de certificat ; cliquez sur **Continuer.** Vous recevez un message d’erreur indiquant que le serveur de publication n’approuve pas le client web UA. Pour résoudre cette erreur, copiez le certificat **UA Web Client** du dossier **/shared/Rejected Certificates/certs** dans le dossier **/shared/UA Applications/certs** de la passerelle. Il est inutile de redémarrer la passerelle. Répétez cette étape. Vous pouvez désormais vous connecter à la passerelle à partir du cloud et ajouter des serveurs OPC UA à la solution.

### <a name="add-your-opc-ua-servers"></a>Ajouter vos serveurs OPC UA

1. Accédez à la page **Connect your own OPC UA Server** (Connecter votre propre serveur OPC UA) du portail de solution d’usine connectée. Suivez les mêmes étapes que dans la section précédente pour établir une liaison approuvée entre le portail de la solution d’usine connectée et le serveur OPC UA. Cette étape établit une approbation mutuelle des certificats à partir du portail de solution d’usine connectée et le serveur OPC UA ; elle crée également une connexion.

1. Naviguez dans l’arborescence de nœuds OPC UA de votre serveur OPC UA, cliquez avec le bouton droit sur les nœuds OPC et sélectionnez **Publier**. Pour que la publication réussisse, le serveur OPC UA et le serveur de publication doivent se trouver sur le même réseau. En d’autres termes, si le nom de domaine complet du serveur de publication est **publisher.mondomaine.com**, le nom de domaine complet du serveur OPC UA doit être, par exemple, **myopcuaserver.mondomaine.com**. Si votre installation est différente, vous pouvez ajouter manuellement des nœuds au fichier publishesnodes.json contenu dans le dossier **/shared**. Le fichier publishesnodes.json est généré automatiquement dès la publication du premier nœud OPC.

1. Les données de télémétrie transitent maintenant à partir de la passerelle. Vous pouvez afficher les données de télémétrie dans la vue **Factory Locations** (Emplacements d’usine) du portail de solution d’usine connectée, sous **New Factory** (Nouvelle usine).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’architecture de la solution préconfigurée d’usine connectée, consultez la [présentation de la solution préconfigurée d’usine connectée][lnk-walkthrough].

Découvrez [l’implémentation de référence de l’éditeur OPC](iot-suite-connected-factory-publisher.md).

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[Docker pour Windows]: https://www.docker.com/docker-windows
[catalogue d’appareils Azure IoT]: https://catalog.azureiotsuite.com/?q=opc
[portail Azure]: http://portal.azure.com/
[client OPC UA open source]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[Installez Docker]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[Azure IoT Edge]: https://github.com/Azure/iot-edge

[lnk-publisher-github]: https://github.com/Azure/iot-edge-opc-publisher
[lnk-publisher-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua
[lnk-proxy-github]: https://github.com/Azure/iot-edge-opc-proxy
[lnk-proxy-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua-proxy