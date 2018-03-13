---
title: "Vue d’ensembles solutions préconfigurées Azure IoT Suite | Microsoft Docs"
description: "Description des solutions préconfigurées Azure IoT Suite et de leur architecture avec des liens vers des ressources supplémentaires."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 17/01/2018
ms.author: dobett
ms.openlocfilehash: b29c5fe9543c5fe6da9ff52da6549fdcacff868f
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="what-is-azure-iot-suite"></a>Qu’est-ce qu’Azure IoT Suite ?

Azure IoT Suite est un ensemble de *solutions préconfigurées* qui :

* Offre un déploiement rapide
* Vous aide à démarrer rapidement
* Vous pouvez le personnaliser pour satisfaire vos besoins spécifiques

Les solutions préconfigurées de *IoT Suite* sont toutes conçues avec les mêmes principes et objectifs.

La vidéo suivante présente une vue d’ensemble de la solution préconfigurée de surveillance à distance :

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Meet-the-new-Remote-Monitoring-accelerator-for-Azure-IoT/Player]

## <a name="preconfigured-solutions-overview"></a>Vue d’ensemble des solutions préconfigurées

Une solution préconfigurée est une implémentation open source de modèles de solution IoT courants que vous pouvez déployer sur Azure en utilisant votre abonnement. Chaque solution préconfigurée combine un code personnalisé et des services Azure pour implémenter un ou plusieurs scénarios IoT spécifiques. Vous pouvez personnaliser l’un des scénarios pour satisfaire vos besoins spécifiques. Ces scénarios sont les suivants :

* Visualisez les données dans un tableau de bord riche pour obtenir des informations détaillées et l’état de la solution.
* Configurez des règles et des alarmes selon la télémétrie IoT dynamique de l’appareil.
* Planifier des tâches de gestion de périphérique, telles que des mises à jour de logiciels et la configuration.
* Approvisionner vos propres appareils personnalisés, simulés ou physiques.
* Dépannez et corrigez les problèmes au sein de vos groupes d’appareils IoT.

Chaque solution préconfigurée est une implémentation complète de bout en bout pouvant utiliser des appareils simulés ou physiques pour générer la télémétrie. Vous pouvez utiliser les solutions préconfigurées comme des accélérateurs de solution pour :

* Fournir un point de départ de vos propres solutions IoT.
* Pour en savoir plus sur les modèles courants en matière de développement et de conception d’une solution IoT.

Trois solutions préconfigurées sont disponibles dès aujourd'hui :

* [Surveillance à distance](iot-suite-remote-monitoring-explore.md)
* [Maintenance prédictive](iot-suite-predictive-overview.md)
* [Fabrique connectée](iot-suite-connected-factory-overview.md)

Le tableau suivant montre le mappage entre les solutions et des fonctionnalités IoT spécifiques :

| Solution | Ingestion de données | Identité d’appareil | Gestion des appareils | Traitement Edge | Commande et contrôle | Règles et actions | Analyse prédictive |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [Surveillance à distance](iot-suite-remote-monitoring-explore.md)  |OUI |OUI |OUI |-   |OUI |OUI |-   |
| [Maintenance prédictive](iot-suite-predictive-overview.md)   |OUI |OUI |-   |-   |OUI |OUI |OUI |
| [Fabrique connectée](iot-suite-connected-factory-overview.md) |OUI |- |- |OUI |OUI |OUI |-   |

* *Données*: entrer des données à l'échelle dans le cloud.
* *Identité de l’appareil* : gérez les identités d’appareil uniques et contrôlez l’accès de l’appareil à la solution.
* *Gestion des appareils* : gérez les métadonnées de l’appareil et effectuez des opérations comme le redémarrage d’appareils et la mise à jour du microprogramme.
* *Commande et contrôle*: envoyer des messages à un appareil à partir du cloud pour lui demander d’agir.
* *Règles et actions*: le serveur principal utilise des règles pour agir sur des données d’appareil spécifiques vers le cloud.
* *Analyse prédictive*: le serveur principal analyse les données d’appareil vers le cloud afin de prédire le moment où des actions spécifiques devraient se produire. Par exemple, l’analyse de la télémétrie d’un moteur d'avion permet de déterminer le moment où la maintenance doit avoir lieu.

> [!NOTE]
> Pour déployer une solution préconfigurée et en savoir plus sur sa personnalisation, visitez [Microsoft Azure IoT Suite](https://www.azureiotsuite.com/).

## <a name="azure-services"></a>Services Azure

Lorsque vous déployez une solution préconfigurée, le processus de déploiement configure un nombre de services Azure. Le tableau suivant présente les services utilisés dans les solutions préconfigurées :

|                      | Surveillance à distance  | Maintenance prédictive | Fabrique connectée |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| IoT Hub              | OUI                | OUI                    | OUI               |
| Event Hubs           |                    | OUI                    |                   |
| Time Series Insights |                    |                        | OUI               |
| Services de conteneur   | OUI                |                        |                   |
| Stream Analytics     |                    | OUI                    |                   |
| Web Apps             | OUI                | OUI                    | OUI               |
| Cosmos DB            | OUI                | OUI                    |                    |
| Stockage Azure         |                    | OUI                    | OUI               |

> [!NOTE]
> Pour plus d’informations sur les ressources déployées dans la solution préconfigurée de surveillance à distance, consultez cet [article](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments) disponible sur GitHub.

* [Azure IoT Hub](../iot-hub/index.md). Ce service fournit les fonctionnalités de messagerie Appareil vers cloud et Cloud vers appareil, et agit comme la passerelle vers le cloud et les autres services clés IoT Suite. Le service vous permet de recevoir à grande échelle des messages provenant de vos périphériques et d’envoyer des commandes à vos périphériques. Le service vous permet également de [gérer vos appareils](../iot-hub/iot-hub-device-management-overview.md). Par exemple, vous pouvez configurer, redémarrer ou effectuer une réinitialisation aux paramètres d’usine sur un ou plusieurs appareils connectés au hub.
* [Azure Event Hubs](../event-hubs/index.md). Ce service permet l’ingestion d’événements de haut volume vers le cloud. Voir [Comparaison entre Azure IoT Hub et Azure Event Hub](../iot-hub/iot-hub-compare-event-hubs.md).
* [Azure Time Series Insights](../time-series-insights/index.yml). Les solutions préconfigurées utilisent ce service pour analyser et afficher les données de télémétrie de vos périphériques.
* [Azure Container Service](../container-service/index.yml). Ce service héberge et gère les microservices dans les solutions préconfigurées.
* [Azure Cosmos DB](../cosmos-db/index.yml) et [Stockage Azure](../storage/index.yml) pour le stockage de données.
* [Azure Stream Analytics](../stream-analytics/index.md). La solution préconfigurée de maintenance prédictive utilise ce service pour traiter la télémétrie entrante, effectuer l’agrégation et détecter les événements. Cette solution préconfigurée utilise également Stream Analytics pour traiter les messages d’information qui contiennent des données telles que les réponses de métadonnées ou de commandes provenant des appareils.
* [Azure Web Apps](../app-service/index.yml) pour héberger le code d’application personnalisé dans les solutions préconfigurées.

Pour une vue d’ensemble de l’architecture d’une solution IoT standard, consultez [Microsoft Azure et l’Internet des objets (IoT)](iot-suite-what-is-azure-iot.md).

## <a name="whats-new-in-preconfigured-solutions"></a>Quelles sont les nouveautés dans les solutions préconfigurées ?

Microsoft met à jour les solutions préconfigurées pour une nouvelle architecture basée sur les microservices. Le tableau suivant présente l’état actuel des solutions préconfigurées :

| Solutions préconfigurées | Architecture  | Langues     |
| ---------------------- | ------------- | ------------- |
| Surveillance à distance      | Microservices | Java et .NET |
| Maintenance prédictive | MVC           | .NET          |
| Fabrique connectée      | MVC           | .NET          |

Les sections suivantes décrivent les nouveautés dans les solutions préconfigurées basées sur les microservices :

### <a name="microservices"></a>Microservices

La nouvelle version de la solution préconfigurée de surveillance à distance utilise une architecture de microservices. Cette solution préconfigurée est composée de plusieurs microservices comme un *Gestionnaire de Hub IoT*  et un *Gestionnaire de stockage*. Les versions Java et .NET de chaque microservice peuvent être téléchargées, de même que la documentation destinée aux développeurs. Pour plus d’informations sur les microservices, consultez [Architecture de surveillance à distance](iot-suite-remote-monitoring-sample-walkthrough.md).

Cette architecture de microservices est un modèle ayant fait ses preuves pour les solutions de cloud et qui :

* Est évolutif.
* Permet une extensibilité.
* Est facile à comprendre.
* Permet que des services individuels soient remplacés par d’autres solutions.

> [!TIP]
> Pour en savoir plus sur les architectures de microservice, consultez [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Architecture d’application .NET) et [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservices: une révolution des applications par le cloud).

Lorsque vous déployez la nouvelle version de surveillance à distance, vous devez sélectionner une des options de déploiement suivantes :

* **Basic :** version à coût réduit pour une démonstration ou pour tester un déploiement. Tous les microservices se déploient sur une seule machine virtuelle Azure.
* **Standard :** déploiement étendu de l’infrastructure pour le développement d’un déploiement de production. Azure Container Service déploie les microservices vers plusieurs machines virtuelles Azure. Kubernetes orchestre les conteneurs Docker qui hébergent les microservices individuels.

### <a name="language-choices-java-and-net"></a>Options de langage : Java et .NET

Les implémentations de chaque microservice peuvent être effectuées en Java et en .NET. De même que le code .NET, le code source Java est open source et vous pouvez personnaliser selon vos besoins spécifiques :

* [Référentiel GitHub .NET de surveillance à distance](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Référentiel GitHub Java de surveillance à distance](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

Si vous souhaitez voir d’autres langages d’implémentation, faites une requête sur [Azure IoT user voice](https://feedback.azure.com/forums/321918-azure-iot).

### <a name="react-user-interface-framework"></a>Infrastructure d’interface utilisateur React

L’interface utilisateur est générée à l’aide de la bibliothèque Javascript [React](https://facebook.github.io/react/). Le code source est open source, vous pouvez le télécharger et le personnaliser.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une vue d’ensemble des solutions préconfigurées de IoT Suite, voici les étapes suggérées pour chacune d’elles :

* [Explorer le modèle de déploiement du Gestionnaire des ressources de la solution de surveillance à distance de Azure IoT Suite](iot-suite-remote-monitoring-explore.md).
* [Présentation de la solution préconfigurée de maintenance prédictive](iot-suite-predictive-overview.md).
* [Prise en main de la solution préconfigurée d’usine connectée](iot-suite-connected-factory-overview.md).

Pour plus d’informations sur les architectures de solution IoT, consultez le document [Microsoft Azure IoT services: Reference Architecture](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf).
