---
title: "Glossaire Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur : glossaire des termes courants relatifs à Azure IoT Hub."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 16ef29ea-a185-48c3-ba13-329325dc6716
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 7e6590b97470bb00c5967f232e3ae134ce20a347
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="glossary-of-iot-hub-terms"></a>Glossaire des termes d’IoT Hub
Cet article répertorie certains termes courants utilisés dans les articles relatifs à IoT Hub.

## <a name="advanced-message-queueing-protocol"></a>Advanced Message Queuing Protocol
[Advanced Message Queuing Protocol (AMQP)](https://www.amqp.org/) est l’un des protocoles de messagerie que [IoT Hub](#iot-hub) prend en charge pour communiquer avec des appareils. Pour plus d’informations sur les protocoles de messagerie pris en charge par IoT Hub, consultez [Envoyer et recevoir des messages avec IoT Hub](iot-hub-devguide-messaging.md).

## <a name="azure-cli"></a>Azure CLI
[Azure CLI](../cli-install-nodejs.md) est un outil de commande multiplateforme, open source, basé sur un interpréteur de commandes, servant à créer et à gérer des ressources dans Microsoft Azure. Cette version de l’interface CLI est implémentée à l’aide de Node.js.

## <a name="azure-cli-20"></a>Azure CLI 2.0
[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) est un outil de commande multiplateforme, open source, basé sur un interpréteur de commandes, servant à créer et à gérer des ressources dans Microsoft Azure. Cette version préliminaire de l’interface CLI est implémentée à l’aide de Python.


## <a name="azure-iot-device-sdks"></a>Kits Azure IoT device SDK
Des kits _device SDK_ sont disponibles pour plusieurs langages et permettent de créer des [applications pour appareils](#device-app) qui interagissent avec un hub IoT. Les didacticiels sur IoT Hub expliquent comment utiliser ces kits device SDK. Le code source et des informations supplémentaires sur les kits device SDK sont disponibles dans ce [dépôt](https://github.com/Azure/azure-iot-sdks) GitHub.

## <a name="azure-iot-service-sdks"></a>Kits Azure IoT service SDK
Des kits _service SDK_ sont disponibles pour plusieurs langages et permettent de créer des [applications principales](#back-end-app) qui interagissent avec un hub IoT. Les didacticiels sur IoT Hub expliquent comment utiliser ces kits service SDK. Le code source et des informations supplémentaires sur les Kits de développement logiciel de services sont disponibles dans ce [dépôt](https://github.com/Azure/azure-iot-sdks) GitHub.

## <a name="azure-portal"></a>Portail Azure
Le [portail Microsoft Azure](https://portal.azure.com) permet de configurer et de gérer l’ensemble de vos ressources Azure. Son contenu est organisé à l’aide de _panneaux_.

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) est une collection d’applets de commande permettant de gérer Azure avec Windows PowerShell. Ces applets de commande permettent de créer, de tester, de déployer et de gérer des solutions et des services fournis via la plateforme Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) vous permet de travailler avec les ressources de votre solution en tant que groupe. Vous pouvez déployer, mettre à jour ou supprimer les ressources de votre solution dans le cadre d’une seule opération coordonnée.

## <a name="azure-service-bus"></a>Azure Service Bus
[Service Bus](../service-bus/index.md) offre une communication cloud avec une messagerie d’entreprise et une communication relayée qui vous aide à connecter des solutions locales au cloud. Certains didacticiels concernant IoT Hub utilisent des [files d’attente](../service-bus-messaging/service-bus-messaging-overview.md) Service Bus.

## <a name="azure-storage"></a>Stockage Azure
Le service [Stockage Azure](../storage/common/storage-introduction.md) est une solution de stockage cloud. Il inclut le service Stockage Blob qui permet de stocker des données d’objet non structurées. Certains didacticiels concernant IoT Hub utilisent le service Stockage Blob.

## <a name="back-end-app"></a>Application principale
Dans le contexte de [IoT Hub](#iot-hub), une application principale est une application qui se connecte à l’un des points de terminaison visible par le service sur un hub IoT. Par exemple, une application principale peut récupérer des messages [appareil-à-cloud](#device-to-cloud) ou gérer le [registre des identités](#identity-registry). En règle générale, une application principale s’exécute dans le cloud mais, dans de nombreux didacticiels, les applications principales sont des applications console qui s’exécutent sur votre ordinateur de développement local.

## <a name="built-in-endpoints"></a>Points de terminaison intégrés
Chaque hub IoT inclut un [point de terminaison](iot-hub-devguide-endpoints.md) intégré compatible avec Event Hub. Vous pouvez utiliser n’importe quel mécanisme compatible avec Event Hubs pour lire les messages appareil-à-cloud à partir de ce point de terminaison.

## <a name="cloud-gateway"></a>Passerelle cloud
Une passerelle cloud assure la connectivité d’appareils qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub). Une passerelle cloud est hébergée dans le cloud, contrairement à une [passerelle de champ](#field-gateway) qui s’exécute localement sur vos appareils. Un cas d’utilisation classique d’une passerelle cloud est l’implémentation d’une traduction de protocole pour vos appareils.

## <a name="cloud-to-device"></a>Cloud-à-appareil
Fait référence aux messages envoyés à partir d’un hub IoT à un appareil connecté. Souvent, ces messages sont des commandes qui donnent pour instruction à l’appareil d’effectuer une action. Pour plus d’informations, consultez [Envoyer et recevoir des messages avec IoT Hub](iot-hub-devguide-messaging.md).

## <a name="connection-string"></a>Chaîne de connexion
Vous utilisez des chaînes de connexion dans votre code d’application afin d’encapsuler les informations requises pour se connecter à un point de terminaison. Une chaîne de connexion inclut généralement l’adresse du point de terminaison et des informations de sécurité, mais les formats de chaîne de connexion varient selon les services. Il existe deux types de chaîne de connexion associés au service IoT Hub :
- Les *Chaînes de connexion d’appareil* permettent aux appareils de se connecter aux points de terminaison visibles par l’appareil sur un hub IoT.
- Les *Chaînes de connexion IoT Hub* activent des applications principales pour se connecter aux points de terminaison côté service sur un hub IoT.

## <a name="custom-endpoints"></a>Points de terminaison personnalisés
Vous pouvez créer des [points de terminaison](iot-hub-devguide-endpoints.md) personnalisés sur un hub IoT pour transmettre les messages envoyés à l’aide d’une [règle de routage](#routing-rules). Les points de terminaison personnalisés se connectent directement à un hub d’événements, à une file d’attente Service Bus ou à une rubrique Service Bus.

## <a name="custom-gateway"></a>Passerelle personnalisée
Une passerelle assure la connectivité d’appareils qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub). [Azure IoT Edge](#azure-iot-edge) permet de construire des passerelles personnalisées qui implémentent une logique personnalisée pour gérer des messages, des conversions de protocole personnalisées et d’autres opérations.

## <a name="data-point-message"></a>Message de point de données
Un message de point de données est un message [appareil-à-cloud](#device-to-cloud) qui contient des données de [télémétrie](#telemetry) telles que la vitesse du vent ou la température.

## <a name="desired-configuration"></a>Configuration souhaitée
Dans le contexte d’un [jumeau d’appareil](iot-hub-devguide-device-twins.md), une configuration souhaitée fait référence à l’ensemble complet des propriétés et métadonnées dans le jumeau d’appareil, qui doivent être synchronisées avec l’appareil.

## <a name="desired-properties"></a>Propriétés souhaitées
Dans le contexte d’un [jumeau d’appareil](iot-hub-devguide-device-twins.md), les propriétés souhaitées sont une sous-section du jumeau d’appareil qui est utilisée avec des [propriétés signalées](#reported-properties) pour synchroniser une configuration ou une condition d’appareil. Les propriétés souhaitées peuvent être définies uniquement par une [application principale](#back-end-app) et observées par l’[application pour appareil](#device-app).

## <a name="device-to-cloud"></a>Appareil-à-cloud
Qualifie des messages envoyés à partir d’un appareil connecté vers [IoT Hub](#iot-hub). Ces messages peuvent être des messages de [point de données](#data-point-message) ou [interactifs](#interactive-message). Pour plus d’informations, consultez [Envoyer et recevoir des messages avec IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Appareil
Dans le contexte d’IoT, un appareil est généralement un petit dispositif informatique autonome qui peut collecter des données ou contrôler d’autres appareils. Par exemple, un appareil peut être un dispositif de surveillance de l’environnement ou un contrôleur pour les systèmes de ventilation et d’arrosage dans une serre. Le [catalogue d’appareils](https://catalog.azureiotsuite.com/) fournit la liste des appareils certifiés pour fonctionner avec [IoT Hub](#iot-hub).

## <a name="device-app"></a>Application pour appareil
Une application pour appareil s’exécute sur votre [appareil](#device) et gère la communication avec votre [hub IoT](#iot-hub). En règle générale, vous utilisez l’un des kits [Azure IoT device SDK](#azure-iot-device-sdks) lorsque vous implémentez une application pour appareil. Dans de nombreux didacticiels sur IoT, vous utilisez un [appareil simulé](#simulated-device) pour des raisons pratiques.

## <a name="device-condition"></a>Condition d’appareil
Fait référence aux informations d’état d’un appareil, telles que la méthode de connectivité utilisée, signalée par une [application pour appareil](#device-app). Des [applications pour appareil](#device-app) peuvent également signaler leurs capacités. Vous pouvez interroger des informations de condition et de capacité en utilisant des jumeaux d’appareil.

## <a name="device-data"></a>Données d’appareil
Les données d’appareil font référence aux données stockées pour chaque appareil dans le [registre des identités](#identity-registry) d’IoT Hub. Il est possible d’importer et d’exporter ces données.

## <a name="device-explorer"></a>Explorateur d’appareils
[L’Explorateur d’appareils](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) est un outil s’exécutant sur Windows, qui vous permet de gérer vos appareils dans le [registre des identités](#identity-registry). Cet outil peut également échanger des messages avec vos appareils.

## <a name="device-identities-rest-api"></a>API REST des identités des appareils
L’[API REST des identités des appareils](https://docs.microsoft.com/rest/api/iothub/iothubresource) vous permet de gérer vos appareils inscrits dans le [registre des identités](#identity-registry) à l’aide d’une API REST. En règle générale, vous devez utiliser l’un des kits [service SDK](#azure-iot-service-sdks) comme indiqué dans les didacticiels concernant IoT Hub.

## <a name="device-identity"></a>Identité d’appareil
L’identité d’appareil est l’identificateur unique attribué à chaque appareil inscrit dans le [registre des identités](#identity-registry).

## <a name="device-management"></a>Gestion des appareils
La gestion des appareils couvre le cycle de vie complet associé à la gestion des appareils dans votre solution IoT, comprenant la planification, l’approvisionnement, la configuration, la surveillance et la mise hors service.

## <a name="device-management-patterns"></a>Modèle de gestion des appareils
[Hub IoT](#iot-hub) permet d’effectuer les opérations courantes de gestion des appareils, dont le redémarrage, les réinitialisations aux paramètres d’usine et les mises à jour de microprogramme.

## <a name="device-messaging-rest-api"></a>API REST de messagerie des appareils
Vous pouvez utiliser l’[API REST de messagerie des appareils](https://docs.microsoft.com/rest/api/iothub/httpruntime) d’un appareil pour envoyer des messages appareil-à-cloud à un IoT Hub et recevoir des messages [cloud-à-appareil](#cloud-to-device) d’un hub IoT. En règle générale, vous devez utiliser l’un des kits [device SDK](#azure-iot-device-sdks) comme indiqué dans les didacticiels sur IoT Hub.

## <a name="device-provisioning"></a>Approvisionnement des appareils
L’approvisionnement des appareils est le processus d’ajout des [données d’appareil](#device-data) initiales aux magasins dans votre solution. Pour permettre à un nouvel appareil de se connecter à votre hub, vous devez ajouter un ID et des clés d’appareil au [registre des identités](#identity-registry) d’IoT Hub. Dans le cadre du processus d’approvisionnement, vous devrez peut-être initialiser les données spécifiques à l’appareil dans d’autres magasins de la solution.

## <a name="device-twin"></a>Jumeau d’appareil
Un [jumeau d’appareil](iot-hub-devguide-device-twins.md) est un document JSON contenant des informations d’état d’appareil telles que des métadonnées, des configurations et des conditions. [IoT Hub](#iot-hub) conserve une représentation d’appareil pour chaque appareil que vous configurez dans votre IoT Hub. Les jumeaux d’appareil vous permettent de synchroniser des [conditions d’appareil](#device-condition) et des configurations entre l’appareil et le back-end de la solution. Vous pouvez interroger des jumeaux d’appareil pour localiser des appareils spécifiques et déterminer l’état d’opérations longues.

## <a name="device-twin-queries"></a>Requêtes de jumeaux d’appareil
Les [requêtes de jumeaux d’appareil](iot-hub-devguide-query-language.md) utilisent le langage de requête similaire à SQL d’IoT Hub pour extraire des informations de vos jumeaux d’appareil. Vous pouvez utiliser ce même langage de requête IoT Hub pour extraire des informations sur des [travaux](#job) en cours d’exécution dans votre hub IoT.

## <a name="device-twin-rest-api"></a>API REST Jumeau d’appareil
Vous pouvez utiliser [l’API REST Jumeau d’appareil](https://docs.microsoft.com/rest/api/iothub/devicetwinapi) à partir du back-end de la solution pour gérer vos jumeaux d’appareil. L’API vous permet de récupérer et de mettre à jour les propriétés de [jumeau d’appareil](#device-twin), et d’appeler des [méthodes directes](#direct-method). En règle générale, vous devez utiliser l’un des kits [service SDK](#azure-iot-service-sdks) comme indiqué dans les didacticiels sur IoT Hub.

## <a name="device-twin-synchronization"></a>Synchronisation de jumeau d’appareil
Une synchronisation de jumeau d’appareil utilise les [propriétés souhaitées](#desired-properties) de vos jumeaux d’appareil pour configurer vos appareils et extraire de ceux-ci des [propriétés signalées](#reported-properties) à stocker dans le jumeau d’appareil.

## <a name="direct-method"></a>Méthode directe
Une [méthode directe](iot-hub-devguide-direct-methods.md) est un moyen de déclencher une méthode à exécuter sur un appareil en appelant une API sur votre hub IoT.

## <a name="endpoint"></a>Point de terminaison
Un hub IoT expose plusieurs [points de terminaison](iot-hub-devguide-endpoints.md) qui permettent à vos applications de s’y connecter. Des points de terminaison visibles par l’appareil permettent aux appareils d’effectuer des opérations telles que l’envoi de messages [appareil-à-cloud](#device-to-cloud) et la réception de messages [cloud-à-appareil](#cloud-to-device). Des points de terminaison de gestion visibles par le service permettent aux [applications principales](#back-end-app) d’effectuer des opérations telles que la gestion [d’identité d’appareil](#device-identity) et des jumeaux d’appareil. Des [points de terminaison intégrés](#built-in-endpoints) visibles par le service permettent de lire des messages appareil-à-cloud. Vous pouvez créer des [points de terminaison personnalisés](#custom-endpoints) pour recevoir des messages appareil-à-cloud envoyés à l’aide d’une [règle de routage](#routing-rules).

## <a name="event-hubs-service"></a>Service Event Hubs
[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) est un service d’entrée de données hautement extensible, capable d’ingérer des millions d’événements par seconde. Il vous permet de traiter et d’analyser des volumes conséquents de données produites par vos applications et appareils connectés. Pour une comparaison avec le service IoT Hub, voir [Comparaison entre Azure IoT Hub et Azure Event Hub](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Point de terminaison compatible Event Hub
Pour lire des messages [appareil-à-cloud](#device-to-cloud) envoyés à votre hub IoT, vous pouvez vous connecter à un point de terminaison sur votre hub et utiliser n’importe quelle méthode compatible Event Hub pour lire ces messages. Les méthodes compatibles Event Hub comprennent l’utilisation des kits [Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) et d’[Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Passerelle de champ
Une passerelle de champ assure la connectivité d’appareils qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub), et est généralement déployée localement avec vos appareils. Pour plus d’informations, voir [Qu’est-ce qu’Azure IoT Hub ?](iot-hub-what-is-iot-hub.md).

## <a name="free-account"></a>Compte gratuit
Vous pouvez créer un [compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/) pour suivre les didacticiels concernant IoT Hub et tester le service IoT Hub (ainsi que d’autres services Azure).

## <a name="gateway"></a>Passerelle
Une passerelle assure la connectivité d’appareils qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub). Voir aussi [Passerelle de champ](#field-gateway), [Passerelle cloud](#cloud-gateway) et [Passerelle personnalisée](#custom-gateway).

## <a name="identity-registry"></a>Registre des identités
Le [registre des identités](iot-hub-devguide-identity-registry.md) est le composant intégré d’un IoT Hub, qui stocke des informations sur les appareils autorisés à se connecter à un IoT Hub.

## <a name="interactive-message"></a>Message interactif
Un message interactif est un message [cloud-à-appareil](#cloud-to-device) qui déclenche une action immédiate dans le serveur principal de solution. Par exemple, un appareil peut envoyer une alarme concernant une défaillance qui devrait être journalisée automatiquement dans un système CRM.

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge permet d’effectuer un déploiement basé sur le cloud de services Azure et de code spécifique de solution sur des appareils locaux. Les appareils IoT Edge peuvent agréger des données d’autres appareils afin d’effectuer un calcul et une analyse avant d’envoyer les données vers le cloud. Pour plus d’informations, consultez [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agent IoT Edge
Partie du runtime IoT Edge responsable des modules de déploiement et de surveillance.

## <a name="iot-edge-device"></a>Appareil IoT Edge
Un runtime IoT Edge doit être installé sur les appareils IoT Edge et ces derniers doivent être marqués comme « Appareil IoT Edge » dans les détails de l’appareil. En savoir plus sur [Déployer Azure IoT Edge sur un appareil simulé dans Linux - préversion](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-deployment"></a>Déploiement IoT Edge
Un déploiement IoT Edge configure un ensemble cible d’appareils IoT Edge pour exécuter un ensemble de modules IoT Edge. Chaque déploiement s’assure en permanence que tous les appareils qui correspondent à la condition cible exécutent l’ensemble spécifié de modules, même lorsque de nouveaux appareils sont créés ou modifiées pour correspondre à la condition cible. Chaque appareil IoT Edge ne reçoit que le déploiement de priorité la plus élevée pour lequel il répond à la condition cible. Pour en savoir plus sur le [Déploiement IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifeste de déploiement IoT Edge
Document JSON contenant les informations à copier dans une ou plusieurs représentations de modules d’appareils IoT Edge pour déployer un ensemble de modules, itinéraires et propriétés souhaitées de module associé.

## <a name="iot-edge-gateway-device"></a>Appareil de passerelle IoT Edge
Appareil IoT Edge avec appareil en aval. L’appareil en aval peut être un appareil IoT Edge ou non IoT Edge.

## <a name="iot-edge-hub"></a>Hub IoT Edge
Partie du runtime IoT Edge responsable des communications entre modules, des communications en amont (vers IoT Hub) et en aval (depuis IoT Hub). 

## <a name="iot-edge-leaf-device"></a>Appareil de nœud terminal IoT Edge
Appareil IoT Edge avec aucun appareil en aval. 

## <a name="iot-edge-module"></a>Module IoT Edge
Un module IoT Edge est un conteneur Docker que vous pouvez déployer sur des appareils IoT Edge. Il effectue une tâche spécifique, comme l’ingestion de messages provenant d’appareils, la transformation de messages ou l’envoi de messages à un IoT Hub. Il communique avec d’autres modules et envoie des données au runtime IoT Edge. [Comprendre les exigences et outils de développement de modules IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Identité de module IoT Edge
Enregistrement dans le registre d’identités de module IoT Hub détaillant les informations d’identification d’existence et de sécurité utilisées par un module pour s’authentifier à Edge Hub ou un IoT Hub.

## <a name="iot-edge-module-image"></a>Image de module IoT Edge
Image Docker utilisée par le runtime IoT Edge pour instancier des instances de module.

## <a name="iot-edge-module-twin"></a>Représentation de module IoT Edge
Document JSON permanent dans l’IoT Hub qui stocke les informations d’état d’une instance de module. 

## <a name="iot-edge-runtime"></a>Runtime IoT Edge
Le runtime IoT Edge inclut tout ce que Microsoft distribue pour l’installation sur un appareil IoT Edge. Il inclut l’agent Edge, Edge Hub et l’outil Edge CTL.

## <a name="iot-edge-set-modules-to-a-single-device"></a>Modules d’ensemble IoT Edge sur un seul appareil
Opération qui copie le contenu d’un manifeste IoT Edge sur une représentation de module d’appareil. L’API sous-jacente est de type « appliquer la configuration » générique, qui utilise simplement un manifeste IoT Edge en tant qu’entrée.
s

## <a name="iot-hub"></a>IoT Hub
IoT Hub est un service Azure entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un back-end de solution. Pour plus d’informations, voir [Qu’est-ce qu’Azure IoT Hub ?](iot-hub-what-is-iot-hub.md). Votre [abonnement Azure](#subscription) vous permet de créer des hubs IoT pour gérer vos charges de travail de messagerie IoT.

## <a name="iot-hub-metrics"></a>Métriques IoT Hub
Les [métriques IoT Hub](iot-hub-metrics.md) fournissent des données sur l’état des hubs IoT dans votre [abonnement Azure](#subscription). Grâce aux métriques IoT Hub, vous pouvez évaluer l’intégrité globale du service et des appareils connectés à ce dernier. Les métriques IoT Hub peuvent vous aider à voir l’état de votre hub IoT et à examiner des problèmes constituant des causes premières sans devoir contacter le support Azure.

## <a name="iot-hub-query-language"></a>Langage de requête IoT Hub
Le [langage de requête IoT Hub](iot-hub-devguide-query-language.md) est un langage similaire à SQL, qui vous permet d’interroger vos [travaux](#job) et jumeaux d’appareil.

## <a name="iot-hub-resource-provider-rest-api"></a>API REST de fournisseur de ressources IoT Hub
L’[API REST de fournisseur de ressources IoT Hub](https://docs.microsoft.com/rest/api/iothub/resourceprovider/iot-hub-resource-provider-rest) vous permet de gérer les hubs IoT dans votre [abonnement Azure](#subscription) en effectuant des opérations telles que la création, la mise à jour et la suppression de hubs.

## <a name="iot-suite"></a>IoT Suite
Azure IoT Suite inclut plusieurs services Azure et des solutions préconfigurées. Ces solutions préconfigurées vous permettent de démarrer rapidement avec des implémentations de bout en bout de scénarios IoT courants. Pour plus d’informations, voir [Qu’est-ce qu’Azure IoT Suite ?](../iot-suite/iot-suite-overview.md).

## <a name="iothub-explorer"></a>iothub-explorer
L’[iothub-explorer](https://github.com/azure/iothub-explorer) est un outil en ligne de commande multiplateforme. Cet outil vous permet de gérer vos appareils dans le [registre des identités](#identity-registry), d’envoyer et de recevoir des messages et des fichiers sur vos appareils, et de surveiller les opérations de votre IoT Hub.

## <a name="job"></a>Travail
Votre serveur principal de solution peut utiliser des [travaux](iot-hub-devguide-jobs.md) pour planifier et suivre des activités sur un ensemble d’appareils inscrits auprès de votre IoT Hub. Ces activités comprennent la mise à jour des [propriétés souhaitées](#desired-properties) de l’appareil, la mise à jour des [balises](#tags) de jumeau d’appareil et l’appel de [méthodes directes](#direct-method). [IoT Hub](#iot-hub) utilise également des travaux pour [importer dans et exporter](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) à partir du [registre des identités](#identity-registry).

## <a name="jobs-rest-api"></a>API REST Travaux
L’[API REST Travaux](https://docs.microsoft.com/rest/api/iothub/jobapi) vous permet de gérer les [travaux](#job) en cours d’exécution dans votre hub IoT.

## <a name="mqtt"></a>MQTT
[MQTT](http://mqtt.org/) est l’un des protocoles de messagerie qu’[IoT Hub](#iot-hub) prend en charge pour communiquer avec des appareils. Pour plus d’informations sur les protocoles de messagerie pris en charge par IoT Hub, consultez [Envoyer et recevoir des messages avec IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Surveillance des opérations
La surveillance des opérations d’[IoT Hub](iot-hub-operations-monitoring.md) vous permet de surveiller l’état des opérations sur votre IoT Hub en temps réel. [IoT Hub](#iot-hub) suit les événements liés à différentes catégories d’opérations. Vous pouvez opter pour l’envoi des événements d’une ou plusieurs catégories à un point de terminaison d’IoT Hub en vue de leur traitement. Vous pouvez surveiller les données des erreurs ou configurer un traitement plus complexe basé sur des modèles de données.

## <a name="physical-device"></a>Appareil physique
Un appareil physique est un appareil réel, par exemple un Raspberry Pi, qui se connecte à un hub IoT. Pour des raisons pratiques, bon nombre des didacticiels sur IoT Hub utilisent des [appareils simulés](#simulated-device) pour vous permettre d’exécuter les exemples sur votre ordinateur local.

## <a name="primary-and-secondary-keys"></a>Clés primaires et secondaires
Lorsque vous vous connectez à un point de terminaison visible par l’appareil ou par le service sur un hub IoT, votre [chaîne de connexion](#connection-string) inclut la clé vous permettant d’accéder. Lorsque vous ajoutez un appareil au [registre des identités](#identity-registry) ou une [stratégie d’accès partagé](#shared-access-policy) à votre hub, le service génère une clé primaire et une clé secondaire. Le fait de disposer de deux clés vous permet de passer d’une clé à un autre lorsque vous mettez à jour une clé sans perdre l’accès au hub IoT.

## <a name="priority"></a>Priorité
Lorsque deux déploiements IoT Edge ciblent le même appareil, le déploiement avec la priorité plus élevée est appliqué. Si les deux déploiements ont la même priorité, le déploiement avec la date de création la plus tardive est appliqué. En savoir plus sur la [priorité](#https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="protocol-gateway"></a>Passerelle de protocole
Une passerelle de protocole est généralement déployée dans le cloud. Elle fournit des services de traduction de protocole pour les appareils qui se connectent à [IoT Hub](#iot-hub). Pour plus d’informations, voir [Qu’est-ce qu’Azure IoT Hub ?](iot-hub-what-is-iot-hub.md).

## <a name="quotas-and-throttling"></a>Quotas et limitation
Divers [quotas](iot-hub-devguide-quotas-throttling.md) s’appliquent à votre utilisation d’[IoT Hub](#iot-hub). Bon nombre d’entre eux varient en fonction du niveau de l’IoT Hub. [IoT Hub](#iot-hub) applique également des [limitations](iot-hub-devguide-quotas-throttling.md) à votre utilisation du service au moment de l’exécution.

## <a name="reported-configuration"></a>Configuration signalée
Dans le contexte d’un [jumeau d’appareil](iot-hub-devguide-device-twins.md), une configuration souhaitée fait référence à l’ensemble complet des propriétés et métadonnées dans le jumeau d’appareil, qui doivent être signalées au serveur principal de solution.

## <a name="reported-properties"></a>Propriétés signalées
Dans le contexte d’un [jumeau d’appareil](iot-hub-devguide-device-twins.md), les propriétés signalées sont une sous-section du jumeau d’appareil qui est utilisé avec des [propriétés souhaitées](#desired-properties) pour synchroniser une configuration ou une condition d’appareil. Les propriétés signalées peuvent être définies uniquement par l’[application d’appareil](#device-app), et peuvent être lues et interrogées par une [application principale](#back-end-app).

## <a name="resource-group"></a>Groupe de ressources
[Azure Resource Manager](#azure-resource-manager) utilise des groupes de ressources pour regrouper des ressources associées. Vous pouvez utiliser un groupe de ressources pour effectuer des opérations simultanément sur toutes les ressources de ce groupe.

## <a name="retry-policy"></a>Stratégie de nouvelle tentative
Vous utilisez une stratégie de nouvelle tentative pour gérer des [erreurs temporaires](https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx) lorsque vous vous connectez à un service cloud.

## <a name="routing-rules"></a>Règles de routage
Vous pouvez configurer des [règles de routage](iot-hub-devguide-messages-read-custom.md) dans votre hub IoT pour acheminer les messages appareil-à-cloud vers un [point de terminaison intégré](#built-in-endpoints) ou vers des [points de terminaison personnalisés](#custom-endpoints) afin qu’ils soient traités par votre back-end de solution.

## <a name="sasl-plain"></a>SAPL PLAIN
SAPL PLAIN est un protocole que le protocole [AMQP](#advanced-message-queue-protocol) utilise pour transférer des jetons de sécurité.

## <a name="shared-access-signature"></a>Signature d’accès partagé
Les signatures d’accès partagé (SAP) sont des mécanismes d’authentification basés sur des hachages sécurisés SHA-256 ou des URI. Une authentification par SAP comporte deux composants : une _stratégie d’accès partagé_ et une _signature d’accès partagé_ (souvent appelée jeton). Un appareil utilise une SAP pour s’authentifier auprès d’un hub IoT. Les [applications principales](#back-end-app) utilisent également une SAP pour s’authentifier auprès des points de terminaison visibles par le service sur un hub IoT. En règle générale, vous incluez le jeton SAP dans la [chaîne de connexion](#connection-string) qu’une application utilise pour établir une connexion à un hub IoT.

## <a name="shared-access-policy"></a>Stratégie d’accès partagé
Une stratégie d’accès partagé définit les autorisations accordées à toute personne disposant d’une [clé primaire ou secondaire](#primary-and-secondary-keys) valide associée à cette stratégie. Vous pouvez gérer les stratégies d’accès partagé et les clés de votre hub via le [portail](#azure-portal).

## <a name="simulated-device"></a>Appareil simulé
Pour des raisons pratiques, bon nombre des didacticiels sur IoT Hub utilisent des appareils simulés pour vous permettre d’exécuter les exemples sur votre ordinateur local. À l’inverse, un [appareil physique](#physical-device) est un appareil réel, comme un Raspberry Pi, qui se connecte à un hub IoT.

## <a name="solution"></a>Solution
Une _solution_ peut être une solution Visual Studio incluant un ou plusieurs projets. Une _solution_ peut également être une solution IoT incluant des éléments tels que des appareils, [des applications pour appareil](#device-app), un hub IoT, d’autres services Azure et des [applications principales](#back-end-app).

## <a name="subscription"></a>Abonnement
L’abonnement est le mode de facturation d’Azure. Chaque ressource ou service Azure que vous créez ou utilisez est associé à un abonnement unique. De nombreux quotas s’appliquent également au niveau d’un abonnement.

## <a name="system-properties"></a>Propriétés système
Dans le contexte d’un [jumeau d’appareil](iot-hub-devguide-device-twins.md), les propriétés système sont en lecture seule et incluent des informations sur l’utilisation de l’appareil, telles que l’heure et l’état de connexion de la dernière activité.

## <a name="tags"></a>Balises
Dans le contexte d’un [jumeau d’appareil](iot-hub-devguide-device-twins.md), les balises sont des métadonnées d’appareil stockées et récupérées par le back-end de solution sous la forme d’un document JSON. Les balises ne sont pas visibles pour les applications sur un appareil.

## <a name="target-condition"></a>Condition cible
Dans un déploiement IoT Edge, la condition cible est une condition booléenne sur les balises des représentations d’appareil afin de sélectionner les appareils cibles du déploiement, par exemple, « tag.environment = prod ». La condition cible est évaluée en permanence pour inclure les nouveaux appareils qui répondent aux exigences ou pour supprimer les appareils qui n’y répondent plus. En savoir plus sur la [condition cible](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)

## <a name="telemetry"></a>Télémétrie
Les appareils collectent des données de télémétrie, telles que la vitesse du vent ou la température, et utilisent des [messages de point de données](#data-point-messages) pour envoyer les données de télémétrie à un hub IoT.

## <a name="token-service"></a>Service d’émission de jeton
Vous pouvez utiliser un service d’émission de jeton pour implémenter un mécanisme d’authentification pour vos appareils. Il utilise une [stratégie d’accès partagé](#shared-access-policy) IoT Hub avec des autorisations **DeviceConnect** pour créer des jetons *device-scoped*. Ces jetons permettent à un appareil de se connecter à votre hub IoT. Un appareil utilise un mécanisme d’authentification personnalisé pour s’authentifier auprès du service d’émission de jeton. Si l’appareil est correctement authentifié, le service d’émission de jeton lui fournit un jeton SAP avec lequel il peut accéder à votre hub IoT.

## <a name="x509-client-certificate"></a>Certificat client X.509
Un appareil peut utiliser un certificat X.509 pour s’authentifier auprès de [IoT Hub](#iot-hub). L’utilisation d’un certificat X.509 est une alternative à l’utilisation d’un [jeton SAP](#shared-access-signature).
