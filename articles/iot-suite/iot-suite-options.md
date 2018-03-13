---
title: Options IoT Microsoft Azure | Microsoft Docs
description: "Choisissez la mise en œuvre de votre solution IoT utilisant Azure IoT Suite, Microsoft IoT Central ou Azure IoT Hub."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1a979bde37d247da5c630547924cadbd79c4a6a4
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="compare-azure-iot-options"></a>Comparer les options d’Azure IoT

L’article [Azure et l’Internet des objets](iot-suite-what-is-azure-iot.md) décrit une architecture de solution IoT standard avec les couches suivantes :

* Connectivité et gestion des appareils
* Traitement et analyse des données
* Présentation et intégration d’entreprise

Pour implémenter cette architecture, Azure IoT offre plusieurs options, adaptées à différents ensembles d’exigences du client :

* [Azure IoT Suite](index.md) est une collection de niveau entreprise de [solutions pré-configurées](iot-suite-what-are-preconfigured-solutions.md) basées sur la plateforme en tant que service (PaaS) Azure qui vous permettent d’accélérer le développement de solutions IoT personnalisées.

* [Microsoft IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions) est une solution SaaS (logiciel en tant que service) qui utilise une approche basée sur les modèles pour vous permettre de créer des solutions IoT de niveau entreprise sans nécessiter de compétences en développement de solutions cloud.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub est la plateforme en tant que service (PaaS) de base utilisée par Microsoft IoT Central et Azure IoT Suite. IoT Hub permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et une solution cloud. IoT Hub vous aide à répondre aux défis de mise en œuvre de l’IoT telles que :

* Connectivité et gestion de haut volume d’appareils.
* Ingestion de télémétrie de haut volume.
* Commande et contrôle des appareils.
* Application de la sécurité des appareils.

## <a name="compare-azure-iot-suite-and-microsoft-iot-central"></a>Comparaison d’Azure IoT Suite et Microsoft IoT Central

Le choix de votre produit Azure IoT est une étape critique de la planification de votre solution IoT. IoT Hub est un service Azure individuel qui seul n’offre pas une solution IoT de bout en bout. IoT Hub peut être utilisé comme point de départ pour une solution IoT, et vous n’avez pas besoin d’utiliser Azure IoT Suite ou Microsoft IoT Central pour l’utiliser. Azure IoT Suite et Microsoft IoT Central, ainsi que d’autres services Azure utilisent IoT Hub. Le tableau suivant résume les principales différences entre Azure IoT Suite et Microsoft IoT Central pour vous aider à choisir celui qui convient le mieux à vos besoins :

|                        | Azure IoT Suite | Microsoft IoT Central |
| ---------------------- | --------- | ----------- |
| Utilisation principale | Accélérer le développement d’une solution IoT personnalisée nécessitant une flexibilité maximale. | Accélérer la mise sur le marché de solutions IoT simples qui ne nécessitent pas une personnalisation de service complète. |
| Accès aux services PaaS sous-jacents          | Vous avez accès aux services Azure sous-jacents en vue de les gérer ou de les remplacer en fonction des besoins. | SaaS. Solution entièrement gérée, les services sous-jacents ne sont pas exposés. |
| Flexibilité            | Élevée. Le code des microservices est open source, et vous pouvez le modifier comme vous le souhaitez. En outre, vous pouvez personnaliser l’infrastructure de déploiement.| Moyenne. Vous pouvez utiliser l’expérience utilisateur intégrée basée sur un navigateur pour personnaliser le modèle de la solution et les aspects de l’interface utilisateur. L’infrastructure n’est pas personnalisable, car les différents composants ne sont pas exposés.|
| Niveau de compétence                 | Moyenne-élevée. Des compétences en Java ou en .NET sont nécessaires pour personnaliser le back end de la solution. Des compétences en JavaScript sont nécessaires pour personnaliser la visualisation. | Faible. Des compétences en modélisation sont nécessaires pour personnaliser la solution. Aucune compétence en codage n’est requise. |
| Expérience de démarrage | Des solutions pré-configurées mettent en œuvre des scénarios IoT courants. Déploiement possible en quelques minutes. | Les modèles d’application et les modèles de périphériques fournissent des modèles prédéfinis. Déploiement possible en quelques minutes. |
| Tarifs                | Vous pouvez affiner les services pour contrôler le coût. | Structure de tarification simple et prévisible. |

Le choix du produit à utiliser pour créer votre solution IoT est finalement déterminé par :

* Vos exigences métier.
* Le type de solution que vous souhaitez créer
* Les compétences de votre organisation pour créer et maintenir la solution à long terme.

## <a name="next-steps"></a>Étapes suivantes

Les étapes suggérées en fonction du produit et de l’approche choisis, sont :

* **Azure IoT Suite** : [Que sont les solutions préconfigurées Azure IoT ?](iot-suite-what-are-preconfigured-solutions.md)
* **Microsoft IoT Central** : [Microsoft IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions).
* **IoT Hub** : [Présentation du service Azure IoT Hub](../iot-hub/iot-hub-what-is-iot-hub.md).
