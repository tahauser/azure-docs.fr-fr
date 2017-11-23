---
title: "Architecture de la solution de surveillance à distance - Azure | Microsoft Docs"
description: "Présentation détaillée de l’architecture de la solution préconfigurée de surveillance à distance"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: e19ba9c88e4fbe4f065c45ce7029247436f7155c
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="remote-monitoring-preconfigured-solution-architecture"></a>Architecture de la solution préconfigurée de surveillance à distance

La [solution préconfigurée](iot-suite-what-are-preconfigured-solutions.md) de surveillance à distance IoT Suite implémente une solution de surveillance de bout en bout destinée à plusieurs ordinateurs distants. Combinant les principaux services Azure pour fournir une implémentation générique du scénario d’entreprise. Vous pouvez utiliser la solution comme point de départ pour votre propre implémentation et la [personnaliser](iot-suite-remote-monitoring-customize.md) pour répondre à vos propres exigences professionnelles.

Cet article vous familiarise avec les éléments clés de la solution de surveillance à distance pour vous permettre de comprendre son fonctionnement. Ces connaissances vous aident à :

* Résoudre les problèmes dans la solution.
* Adapter la solution à vos besoins professionnels.
* Concevoir votre propre solution IoT utilisant des services Azure.

## <a name="logical-architecture"></a>Architecture logique

Le diagramme suivant décrit l’articulation des composants logiques de la solution préconfigurée de surveillance à distance dans [l’architecture IoT](iot-suite-what-is-azure-iot.md) :

![Architecture logique](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Intérêt des microservices

L’architecture cloud a évolué depuis que Microsoft a publié les premières solutions préconfigurées. Les [microservices](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) ont émergé comme une pratique ayant fait ses preuves pour obtenir une mise à l’échelle et une flexibilité sans sacrifier la vitesse de développement. Plusieurs services de Microsoft utilisent ce modèle d’architecture en interne et obtiennent de très bons résultats en termes de scalabilité et de fiabilité. Les solutions préconfigurées mises à jour intègrent ces apprentissages afin que vous puissiez également en bénéficier.

> [!TIP]
> Pour en savoir plus sur les architectures de microservice, consultez [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Architecture d’application .NET) et [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservices: une révolution des applications par le cloud).

## <a name="device-connectivity"></a>Connectivité des appareils

La solution inclut les composants suivants dans la partie de la connectivité des appareils de l’architecture logique :

### <a name="simulated-devices"></a>Simulations d’appareils

La solution inclut un microservice qui vous permet de gérer un pool d’appareils simulés pour tester le flux de bout en bout dans la solution. Les appareils simulés :

* Génèrent des données de télémétrie appareil-à-cloud.
* Répondent aux appels de méthode cloud-à-appareil provenant d’IoT Hub.

Le microservice fournit un point de terminaison RESTful vous permettant de créer, de démarrer et d’arrêter des simulations. Chaque simulation se compose d’un ensemble d’appareils virtuels de différents types, qui envoient des données de télémétrie et répondent aux appels de méthode.

Vous pouvez provisionner les appareils simulés à partir du tableau de bord dans le portail des solutions.

### <a name="physical-devices"></a>Appareils physiques

Vous pouvez connecter des appareils physiques à la solution. Vous pouvez implémenter le comportement de vos appareils simulés à l’aide des kits Azure IoT device SDK.

Vous pouvez provisionner les appareils physiques à partir du tableau de bord dans le portail des solutions.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>IoT Hub et le microservice iot-manager

[IoT Hub](../iot-hub/index.md) ingère les données envoyées par les appareils au cloud et les met à disposition dans le microservice `telemetry-agent`.

L’instance IoT Hub de la solution effectue également ce qui suit :

* Conserve un registre des identités contenant les identifiants et clés d’authentification de tous les appareils autorisés à se connecter au portail. Vous pouvez activer et désactiver des appareils via le Registre des identités.
* Appelle des méthodes sur vos appareils pour le compte du portail de la solution.
* Gère les représentations d’appareil pour tous les appareils inscrits. Une représentation d’appareil stocke les valeurs des propriétés signalées par un appareil. Une représentation d’appareil stocke également les propriétés souhaitées, définies dans le portail de la solution, que l’appareil récupère lors de sa connexion suivante.
* Planifie des travaux pour définir des propriétés pour plusieurs appareils ou appeler des méthodes sur plusieurs appareils.

La solution inclut le microservice `iot-manager` pour gérer les interactions avec votre hub IoT telles que les suivantes :

* Création et gestion des appareils IoT
* Gestion des jumeaux d’appareil
* Appel de méthodes sur les appareils
* Gestion des informations d’identification IoT

Ce service exécute également des requêtes IoT Hub pour récupérer les appareils appartenant à des groupes définis par l’utilisateur.

Le microservice fournit un point de terminaison RESTful pour gérer les appareils et les jumeaux d’appareil, appeler des méthodes et exécuter des requêtes IoT Hub.

## <a name="data-processing-and-analytics"></a>Traitement et analyse des données

La solution inclut les composants suivants dans la partie de traitement et d’analyse des données de l’architecture logique :

### <a name="device-telemetry"></a>Télémétrie d’appareil

La solution inclut deux microservices pour gérer la télémétrie d’appareil.

Le microservice [telemetry-agent](https://github.com/Azure/telemetry-agent-dotnet) :

* Stocke les données de télémétrie dans Cosmos DB.
* Analyse le flux des données de télémétrie issu des appareils.
* Génère des alarmes en fonction des règles définies.

Les alarmes sont stockées dans Cosmos DB.

Le microservice `telemetry-agent` permet au portail de solutions de lire les données de télémétrie reçues des appareils. Le portail de solutions utilise également ce service pour :

* Définir des règles de surveillance telles que les seuils qui déclenchent des alarmes.
* Récupérer la liste des alarmes passées.

Utilisez le point de terminaison RESTful fourni par ce microservice pour gérer les données de télémétrie, les règles et les alarmes.

### <a name="storage"></a>Storage

Le microservice [storage-adapter](https://github.com/Azure/pcs-storage-adapter-dotnet) est un adaptateur devant le service de stockage principal utilisé pour la solution préconfigurée. Son rôle se résume aux opérations de collecte et de stockage de paires clé/valeur.

Le déploiement standard de la solution préconfigurée utilise Cosmos DB comme service de stockage principal.

La base de données Cosmos DB stocke les données de la solution préconfigurée. Le microservice **storage-adapter** fait office d’adaptateur permettant aux autres microservices dans la solution d’accéder aux services de stockage.

## <a name="presentation"></a>Présentation

La solution inclut les composants suivants dans la partie présentation de l’architecture logique :

[L’interface utilisateur web est une application Javascript React](https://github.com/Azure/pcs-remote-monitoring-webui). L’application :

* Utilise Javascript React uniquement et s’exécute entièrement dans le navigateur.
* Est mise en forme en CSS.
* Interagit avec des microservices publics par le biais d’appels AJAX.

L’interface utilisateur présente toutes les fonctionnalités de la solution préconfigurée et interagit avec d’autres services tels que les suivants :

* Le microservice [authentication](https://github.com/Azure/pcs-auth-dotnet) pour protéger les données utilisateur
* Le microservice [iothub-manager](https://github.com/Azure/iothub-manager-dotnet) pour lister et gérer les appareils IoT

Le microservice [ui-config](https://github.com/Azure/pcs-config-dotnet) permet à l’interface utilisateur de stocker et de récupérer les paramètres de configuration.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez consulter le code source et la documentation de développement, commencez avec l’un des deux principaux dépôts GitHub :

* [Solution préconfigurée de surveillance à distance avec Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/)
* [Solution préconfigurée de surveillance à distance avec Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Solution préconfigurée pour l’architecture de surveillance à distance](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture)

Pour plus d’informations conceptuelles sur la solution préconfigurée de surveillance à distance, consultez [Personnaliser la solution préconfigurée](iot-suite-remote-monitoring-customize.md).
