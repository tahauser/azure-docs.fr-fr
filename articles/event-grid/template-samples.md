---
title: 'Exemples de modèles Azure Resource Manager : Event Grid | Microsoft Docs'
description: Exemples de modèles Azure Resource Manager pour Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modèles Azure Resource Manager pour Event Grid

Le tableau suivant inclut des liens vers des modèles Azure Resource Manager pour Event Grid.

| | |
|-|-|
|**Abonnements Event Grid**||
| [Rubrique personnalisée et abonnement avec un point de terminaison WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Déploie une rubrique personnalisée Event Grid. Crée un abonnement à cette rubrique personnalisée, qui utilise un point de terminaison WebHook. |
| [Abonnement à une rubrique personnalisée avec un point de terminaison EventHub](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| Crée un abonnement Event Grid à une rubrique personnalisée qui existe déjà. L’abonnement utilise un Hub d’événements pour le point de terminaison. |
| [Abonnement au groupe de ressources](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| S’abonne aux événements d’un groupe de ressources. Le groupe de ressources que vous spécifiez comme cible au cours du déploiement constitue la source d’événements. L’abonnement utilise un Hub d’événements pour le point de terminaison. |
| [Compte de stockage blob et abonnement](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| Déploie un compte de stockage blob Azure et s’abonne aux événements pour ce compte de stockage. |
| | |
