---
title: Tarification et facturation - Azure Logic Apps | Microsoft Docs
description: "Découvrez comment fonctionnement la tarification et la facturation avec Azure Logic Apps."
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: LADocs; klam
ms.openlocfilehash: f2a92e45b8a759d2d8193ac188efdcfc694a3e6d
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2017
---
# <a name="logic-apps-pricing-model"></a>Modèle de tarification de Logic Apps
Vous pouvez créer et exécuter des flux de travail d’intégration scalables et automatisés dans le cloud avec Azure Logic Apps. Voici les détails du fonctionnement de la facturation et de la tarification de Logic Apps.
## <a name="consumption-pricing-model"></a>Modèle de tarification de la consommation
Avec les applications logiques qui viennent d’être créées, vous payez uniquement ce que vous utilisez. Les nouvelles applications logiques utilisent un plan de consommation et un modèle de tarification, ce qui signifie que toutes les exécutions effectuées par une instance d’application logique sont mesurées et facturées à l’usage.
### <a name="what-are-action-executions"></a>Que sont les exécutions d’action ?
Chaque étape dans une définition d’application logique est une action, ce qui inclut des déclencheurs, des étapes de flux de contrôle, et des appels aux actions intégrées et aux connecteurs.
### <a name="triggers"></a>Déclencheurs
Les déclencheurs sont des actions particulières qui créent une instance d’application logique, quand un événement spécifique se produit. Les déclencheurs ont différents comportements qui affectent la façon dont l’application logique est mesurée.
* **Déclencheur d’interrogation** : ce déclencheur vérifie constamment un point de terminaison jusqu’à obtenir un message répondant aux critères de création d’une instance d’application logique pour démarrer le flux de travail. Vous pouvez configurer l’intervalle d’interrogation dans le déclencheur par le biais du Concepteur d’application logique. Chaque requête d’interrogation compte comme une exécution, même quand aucune instance d’application logique n’est créée.
* **Déclencheur webhook** : ce déclencheur attend qu’un client envoie une requête à un point de terminaison spécifique. Chaque requête envoyée au point de terminaison webhook est considérée comme une exécution d’action. Par exemple, les déclencheurs Requête et Webhook HTTP sont tous deux des déclencheurs webhook.
* **Déclencheur de périodicité** : ce déclencheur crée une instance d’application logique en fonction de l’intervalle de périodicité que vous avez configuré dans le déclencheur. Par exemple, vous pouvez configurer un déclencheur de périodicité qui s’exécute tous les trois jours ou d’après un calendrier plus complexe.

Vous trouverez les exécutions de déclencheur dans le volet Vue d’ensemble de votre application logique, dans la section Historique du déclencheur.

### <a name="actions"></a>Actions
Les actions intégrées, par exemple les actions qui appellent HTTP, Azure Functions ou Gestion des API, ainsi que des étapes de flux de contrôle, sont mesurées en tant qu’actions natives et ont leurs types respectifs. Les actions qui appellent des [connecteurs](https://docs.microsoft.com/connectors) ont le type « ApiConnection ». Les connecteurs sont classés comme des connecteurs standard ou d’entreprise, et ils sont mesurés et facturés à l’usage en fonction de leur [tarification][pricing] respective.
Toutes les actions d’exécution ayant réussi ou non sont considérées et mesurées comme des exécutions d’action. Toutefois, les actions qui sont ignorées en raison d’une condition non remplie ou celles qui n’ont pas été exécutées (car l’application logique s’est arrêtée avant son achèvement) ne sont pas considérées comme des exécutions d’action. Les applications logiques désactivées ne peuvent pas instancier d’instances. Elles ne sont donc pas facturées pendant qu’elles sont désactivées.

> [!NOTE]
> Une fois que vous avez désactivé une application logique, l’arrêt complet de ses instances en cours d’exécution peut prendre un peu de temps.

Les actions qui s’exécutent dans des boucles sont comptées à chaque cycle de la boucle. Par exemple, une action unique dans une boucle « for each » qui traite une liste de 10 éléments est comptée en multipliant le nombre d’éléments de liste (10) par le nombre d’actions dans la boucle (1) plus un pour le démarrage de la boucle. Pour cet exemple, le calcul est donc (10 * 1) + 1, ce qui donne 11 exécutions d’action.

### <a name="integration-account-usage"></a>Utilisation d’un compte d’intégration
La consommation en fonction de l’utilisation inclut un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) à des fins d’exploration, de développement et de test des fonctionnalités de [traitement XML](logic-apps-enterprise-integration-xml.md) et [B2B/EDI](logic-apps-enterprise-integration-b2b.md) de Logic Apps sans coût supplémentaire. Vous pouvez avoir l’un de ces comptes d’intégration par région et stocker jusqu’à 10 contrats et 25 cartes. Vous pouvez avoir et charger une quantité illimitée de certificats, de schémas et de partenaires.

Logic Apps offre également des comptes d’intégration de base et standard avec le contrat SLA Logic Apps pris en charge. Vous pouvez utiliser des comptes d’intégration de base quand vous souhaitez uniquement utiliser la gestion des messages ou agir en tant que petite entreprise partenaire ayant une relation de partenariat commercial avec une entité professionnelle plus importante. Les comptes d’intégration standard prennent en charge les relations B2B plus complexes et augmentent le nombre d’entités que vous pouvez gérer. Pour plus d’informations, consultez la page [Tarification Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="pricing"></a>Tarification
Pour plus d’informations, consultez [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Étapes suivantes
* [Vue d’ensemble de Logic Apps][whatis]
* [Créer votre première application logique][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

