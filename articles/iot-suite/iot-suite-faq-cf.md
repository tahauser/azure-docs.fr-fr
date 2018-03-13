---
title: "Questions fréquentes sur la solution d’usine connectée - Azure | Microsoft Docs"
description: "Questions fréquentes sur l’usine connectée IoT Suite"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: ab72152fc937e3c4552147fce29c95ea0efcadf4
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="frequently-asked-questions-for-iot-suite-connected-factory-preconfigured-solution"></a>Questions fréquentes sur la solution préconfigurée d’usine connectée IoT Suite

Voir aussi les [questions fréquentes (FAQ)](iot-suite-faq.md) d’ordre général sur IoT Suite.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solution"></a>Où trouver le code source de la solution préconfigurée ?

Le code source est stocké dans le dépôt GitHub suivant :

* [Solution préconfigurée d’usine connectée](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Qu’est-ce que l’UA OPC ?

OPC UA (Unified Architecture) est un standard d’interopérabilité indépendant de la plateforme et orienté services, qui date de 2008. OPC UA est utilisé par différents systèmes et dispositifs industriels tels que les PC industriels, les automates programmables industriels et les capteurs. OPC UA intègre les fonctionnalités des spécifications OPC Classic dans un seul et même framework extensible avec un dispositif de sécurité intégré. La fondation OPC Foundation est à l’origine de cette norme. [OPC Foundation](http://opcfoundation.org/) est une organisation à but non lucratif qui compte plus de 440 membres. L’objectif de cette organisation est d’utiliser les spécifications OPC de façon à favoriser une interopérabilité fiable et sécurisée entre les fabricants et les plateformes par les moyens suivants :

* Infrastructure
* Spécifications
* Technology
* Processus

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-preconfigured-solution"></a>Pourquoi le choix de Microsoft s’est-il porté sur l’UA OPC en matière de solution préconfigurée d’usine connectée ?

Microsoft a choisi l’UA OPC, car il s’agit d’une norme ouverte, non propriétaire, ne dépendant pas d’une plateforme, reconnue par le secteur et éprouvée. Elle est exigée pour les solutions d’architecture de référence Industrie 4.0 (RAMI4.0) assurant l’interopérabilité entre un large ensemble de processus de fabrication et les équipements. Microsoft constate une demande de solutions Industrie 4.0 chez ses clients. La prise en charge de l’UA OPC permet de supprimer la barrière empêchant les clients d’atteindre leurs objectifs et leur apporte une valeur commerciale instantanée.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Comment ajouter une adresse IP publique à la machine virtuelle de simulation ?

Vous pouvez ajouter l’adresse IP de deux manières :

* Utilisez le script PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` dans le [dépôt](https://github.com/Azure/azure-iot-connected-factory). Passez le nom de votre déploiement en tant que paramètre. Pour un déploiement local, utilisez `<your username>ConnFactoryLocal`. Le script imprime l’adresse IP de la machine virtuelle.

* Dans le portail Azure, recherchez le groupe de ressources de votre déploiement. À moins qu’il s’agisse d’un déploiement local, le groupe de ressources porte le nom que vous avez spécifié comme nom de solution ou de déploiement. Pour un déploiement local utilisant le script build, le nom du groupe de ressources est `<your username>ConnFactoryLocal`. À présent, ajoutez une nouvelle ressource **Adresse IP publique** au groupe de ressources.

> [!NOTE]
> Dans les deux cas, veillez à installer les derniers correctifs en suivant les instructions sur le [site web Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Tenez à jour l’installation aussi longtemps que votre machine virtuelle est accessible via une adresse IP publique.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Comment supprimer l’adresse IP publique de la machine virtuelle de simulation ?

Vous pouvez supprimer l’adresse IP de deux manières :

* Utilisez le script PowerShell Simulation/Factory/Remove-SimulationPublicIp.ps1 du [dépôt](https://github.com/Azure/azure-iot-connected-factory). Passez le nom de votre déploiement en tant que paramètre. Pour un déploiement local, utilisez `<your username>ConnFactoryLocal`. Le script imprime l’adresse IP de la machine virtuelle.

* Dans le portail Azure, recherchez le groupe de ressources de votre déploiement. À moins qu’il s’agisse d’un déploiement local, le groupe de ressources porte le nom que vous avez spécifié comme nom de solution ou de déploiement. Pour un déploiement local utilisant le script build, le nom du groupe de ressources est `<your username>ConnFactoryLocal`. À présent, supprimez la ressource **Adresse IP publique** du groupe de ressources.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Comment se connecter à la machine virtuelle de simulation ?

La connexion à la machine virtuelle de simulation n’est prise en charge que si vous avez déployé votre solution à l’aide du script PowerShell `build.ps1` du [dépôt](https://github.com/Azure/azure-iot-connected-factory).

Si vous avez déployé la solution à partir de www.azureiotsuite.com, vous ne pouvez pas vous connecter à la machine virtuelle. En effet, le mot de passe est généré de façon aléatoire, donc vous ne pouvez pas le réinitialiser.

1. Ajoutez une adresse IP publique à la machine virtuelle. Consultez [Comment ajouter une adresse IP publique à la machine virtuelle de simulation ?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm).
1. Créez une session SSH sur votre machine virtuelle en utilisant l’adresse IP de la machine virtuelle.
1. Le nom d’utilisateur à utiliser est `docker`.
1. Le mot de passe à utiliser dépend de la version que vous avez utilisée pour le déploiement :
    * Pour les solutions déployées à l’aide du script build.ps1 avant le 1er juin 2017, le mot de passe est `Passw0rd`.
    * Pour les solutions déployées à l’aide du script build.ps1 après le 1er juin 2017, le mot de passe est `<name of your deployment>.config.user`. Le mot de passe est stocké dans le paramètre **VmAdminPassword**. Le mot de passe est généré de façon aléatoire au moment du déploiement, sauf si vous le spécifiez à l’aide du paramètre `-VmAdminPassword` du script `build.ps1`.

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Comment arrêter et démarrer tous les processus Docker dans la machine virtuelle de simulation ?

1. Connectez-vous à la machine virtuelle de simulation. Consultez [Comment se connecter à la machine virtuelle de simulation ?](#how-do-i-sign-in-to-the-simulation-vm).
1. Pour identifier les conteneurs actifs, exécutez `docker ps`.
1. Pour arrêter tous les conteneurs de simulation, exécutez `./stopsimulation`.
1. Pour démarrer tous les conteneurs de simulation :
    * Exportez une variable shell sous le nom **IOTHUB_CONNECTIONSTRING**. Utilisez la valeur du paramètre **IotHubOwnerConnectionString** du fichier `<name of your deployment>.config.user`. Par exemple : 

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Exécutez `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Comment mettre à jour la simulation dans la machine virtuelle ?

Si vous avez apporté des modifications à la simulation, vous pouvez utiliser le script PowerShell `build.ps1` du [dépôt](https://github.com/Azure/azure-iot-connected-factory) en utilisant la commande `updatedimulation`. Ce script génère tous les composants de simulation, arrête la simulation dans la machine virtuelle, puis les charge, les installe et les démarre.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Comment identifier la chaîne de connexion du hub IoT utilisé par ma solution ?

Si vous avez déployé votre solution avec le script `build.ps1` du [dépôt](https://github.com/Azure/azure-iot-connected-factory), la chaîne de connexion est la valeur de **IotHubOwnerConnectionString** du fichier `<name of your deployment>.config.user`.

Vous pouvez aussi trouver la chaîne de connexion dans le portail Azure. Dans la ressource IoT Hub du groupe de ressources de votre déploiement, recherchez les paramètres de chaîne de connexion.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Quels appareils IoT Hub la simulation d’usine connectée utilise ?

La simulation inscrit elle-même les appareils suivants :

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

À l’aide de l’outil [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou [l’extension IoT pour Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension), vous pouvez identifier les appareils inscrits auprès du hub IoT utilisé par votre solution. Pour utiliser l’explorateur d’appareils, vous avez besoin de la chaîne de connexion du hub IoT de votre déploiement. Pour utiliser l’extension IoT pour Azure CLI 2.0, vous avez besoin de votre nom d’IoT Hub.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Comment obtenir les données de journal des composants de simulation ?

Tous les composants de la simulation enregistrent des informations dans des fichiers journaux. Ces fichiers se trouvent dans la machine virtuelle dans le dossier `home/docker/Logs`. Pour récupérer les journaux, vous pouvez utiliser le script PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` du [dépôt](https://github.com/Azure/azure-iot-connected-factory).

Ce script doit se connecter à la machine virtuelle. Vous serez peut-être amené à fournir des informations d’identification pour vous connecter. Pour savoir comment trouver les informations d’identification, consultez [Comment se connecter à la machine virtuelle de simulation ?](#how-do-i-sign-in-to-the-simulation-vm).

Le script ajoute une adresse IP publique à la machine virtuelle, si elle n’en dispose pas déjà d’une, puis la supprime. Le script place tous les fichiers journaux dans une archive et télécharge celle-ci sur votre station de travail de développement.

Vous pouvez aussi vous connecter à la machine virtuelle via SSH et inspecter les fichiers journaux au moment de l’exécution.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Comment vérifier si la simulation envoie des données vers le cloud ?

Avec l’outil [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou [iothub-explorer](https://github.com/azure/iothub-explorer), vous pouvez inspecter les données envoyées à IoT Hub à partir de certains appareils. Pour utiliser ces outils, vous devez connaître la chaîne de connexion du hub IoT de votre déploiement. Consultez [Comment identifier la chaîne de connexion du hub IoT utilisé par ma solution ?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution).

Inspecter les données envoyées par l’un des appareils serveur de publication :

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Si vous constatez qu’aucune donnée n’a été envoyée à IoT Hub, c’est qu’il y a un problème au niveau de la simulation. La première étape d’analyse que vous devez effectuer consiste à analyser les fichiers journaux des composants de simulation. Consultez [Comment obtenir les données de journal des composants de simulation ?](#how-can-i-get-log-data-from-the-simulation-components). Ensuite, essayez d’arrêter et de démarrer la simulation et si aucune donnée n’est toujours envoyée, mettez entièrement à jour la simulation. Consultez [Comment mettre à jour la simulation dans la machine virtuelle ?](#how-do-i-update-the-simulation-in-the-vm).

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Comment activer une carte interactive dans une solution Connected Factory ?

Pour activer une carte interactive dans une solution Connected Factory, vous devez disposer d’un plan API Bing Cartes pour les entreprises.

Lors d’un déploiement à partir de [www.azureiotsuite.com](http://www.azureiotsuite.com), le processus de déploiement vérifie que votre abonnement a une API Bing Maps activée pour le plan Entreprise et déploie automatiquement une carte interactive sur la fabrique connectée. Si ce n’est pas le cas, vous pouvez toujours activer une carte interactive dans votre déploiement comme suit :

Lorsque vous déployez à l’aide du script `build.ps1` dans le référentiel GitHub de la fabrique connectée et que vous avez une API Bing Maps pour le plan Entreprise, définissez la variable d’environnement `$env:MapApiQueryKey` dans la fenêtre de création sur la clé de requête de votre plan. La carte interactive est ensuite automatiquement activée.

Si vous ne disposez pas d’un plan API Bing Maps pour le plan Entreprise, déployez la solution Connected Factory à partir de [www.azureiotsuite.com](http://www.azureiotsuite.com), ou à l’aide du script `build.ps1`. Ajoutez ensuite une API Bing Maps pour le plan Entreprise à votre abonnement, comme expliqué dans [Comment créer un compte API Bing Maps pour les entreprises ?](#how-do-i-create-a-bing-maps-api-for-enterprise-account). Recherchez la clé de requête de ce compte comme expliqué dans [Comment obtenir une clé de requête API Bing Maps pour les entreprises ?](#how-to-obtain-your-bing-maps-api-for-enterprise-querykey) et enregistrez-la. Accédez au portail Azure et accédez à la ressource App Service dans votre déploiement de fabrique connectée. Accédez aux **Paramètres de l’application**, où vous trouverez une section **Paramètres de l’application**. Définissez **MapApiQueryKey** sur la clé de requête que vous avez obtenue. Enregistrez les paramètres, puis accédez à la **Vue d’ensemble** et redémarrez App Service.

### <a name="how-do-i-create-a-bing-maps-api-for-enterprise-account"></a>Comment créer un compte API Bing Cartes pour les entreprises

Vous pouvez obtenir gratuitement un plan *Bing Cartes pour les entreprises Transactions internes de niveau 1*. Toutefois, vous ne pouvez ajouter que deux plans de ce type à un abonnement Azure. Si vous ne disposez pas d’un compte API Bing Cartes pour les entreprises, créez-en-un sur le Portail Azure en cliquant sur **+ Créer une ressource**. Ensuite, recherchez **API Bing Cartes pour les entreprises** et suivez les invites pour le créer.

![Clé Bing](media/iot-suite-faq-cf/bing.png)

### <a name="how-to-obtain-your-bing-maps-api-for-enterprise-querykey"></a>Comment obtenir une clé de requête API Bing Cartes pour les entreprises ?

Une fois que vous avez créé votre plan API Bing Cartes pour les entreprises, ajoutez une ressource Bing Cartes pour les entreprises au groupe de ressources de votre solution Connected Factory sur le Portail Azure.

1. Sur le Portail Azure, accédez au groupe de ressources contenant votre plan API Bing Cartes pour les entreprises sur le Portail Azure.

1. Cliquez sur **Tous les paramètres**, puis sur **Gestion des clés**.

1. Vous y trouverez deux clés : **MasterKey** et **QueryKey**. Copiez la valeur de **QueryKey**.

1. Pour que la clé soit récupérée par le script `build.ps1`, affectez la **QueryKey** de votre plan à la variable `$env:MapApiQueryKey` de votre environnement PowerShell. Le script de génération ajoute alors automatiquement la valeur aux paramètres d’App Service.

1. Lancez un déploiement local ou cloud avec le script `build.ps1`.

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Comment activer la carte interactive pendant un débogage local ?

Pour activer la carte interactive pendant un débogage local, affectez la valeur de la **QueryKey** copiée précédemment au paramètre `MapApiQueryKey` dans les fichiers `local.user.config` et `<yourdeploymentname>.user.config` à la racine de votre déploiement.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Comment utiliser une autre image sur la page d’accueil du tableau de bord ?

Pour modifier l’image statique qui s’affiche sur la page d’accueil du tableau de bord, remplacez l’image `WebApp\Content\img\world.jpg`. Ensuite, régénérez et redéployez l’application WebApp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Comment utiliser les appareils d’un autre type qu’OPC UA avec Connected Factory ?

Pour envoyer les données de télémétrie à Connected Factory à partir d’appareils d’un autre type qu’OPC UA :

1. [Configurez une nouvelle station dans la topologie Connected Factory](iot-suite-connected-factory-configure.md) dans le fichier `ContosoTopologyDescription.json`.

1. Ingérez les données de télémétrie dans un format JSON compatible avec Connected Factory :

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. Le format de `<timestamp>` est `2017-12-08T19:24:51.886753Z`.

1. Redémarrez le service Connected Factory App Service.

### <a name="next-steps"></a>Étapes suivantes

Vous pouvez également explorer certaines des autres fonctionnalités et capacités des solutions préconfigurées IoT Suite :

* [Présentation de la solution préconfigurée de maintenance prédictive](iot-suite-predictive-overview.md)
* [Présentation de la solution préconfigurée d’usine connectée](iot-suite-connected-factory-overview.md)
* [Sécurisation de l’Internet des objets de bout en bout](securing-iot-ground-up.md)