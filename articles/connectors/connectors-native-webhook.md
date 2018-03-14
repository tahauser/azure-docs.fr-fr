---
title: Connecteur Webhook pour Azure Logic Apps | Microsoft Docs
description: "Utilisation d’actions et de déclencheurs webhook pour effectuer des actions comme Filtrer le tableau à partir d’applications logiques"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: ec407d72f90cf3616214d27c378dee0ff42f4c6f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-webhook-connector"></a>Prise en main du connecteur webhook

Avec le déclencheur et l’action webhook, vous pouvez déclencher, suspendre et reprendre les flux afin d’effectuer les tâches suivantes :

* Créer un déclenchement à partir d’[Azure Event Hub](https://github.com/logicappsio/EventHubAPI) lorsqu’un élément est reçu
* Attendre une approbation avant de poursuivre un flux de travail.

En savoir plus sur la [création d’API personnalisées qui prennent en charge un webhook](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Utilisation du déclencheur webhook

Un [*déclencheur*](connectors-overview.md) est un événement qui démarre un flux de travail d’application logique. Un déclencheur webhook est basé sur un événement et ne repose pas sur l’interrogation de nouveaux éléments. Comme le [déclencheur de requête](connectors-native-reqres.md), l’application logique se déclenche dès qu’un événement se produit. Le déclencheur webhook enregistre une *URL de rappel* vers un service et utilise cette URL pour déclencher l’application logique si nécessaire.

Voici un exemple de configuration d’un déclencheur HTTP dans le concepteur d’application logique. Ces étapes supposent que vous avez déjà déployé ou que vous accédez à une API qui suit [le modèle d’abonnement et de résiliation d’abonnement au webhook dans les applications logiques](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). L’appel d’abonnement est effectué à chaque fois qu’une application logique est enregistrée avec un nouveau webhook, ou basculée de l’état désactivé à l’état activé. L’appel de résiliation d’abonnement est effectué à chaque fois qu’un déclencheur webhook d’application logique est supprimé et enregistré, ou basculé de l’état activé à l’état désactivé.

**Pour ajouter le déclencheur webhook**

1. Ajoutez le déclencheur **HTTP Webhook** en tant que première étape dans une application logique.
2. Renseignez les paramètres pour les appels d’abonnement et de résiliation d’abonnement au webhook.

   Cette étape suit le même modèle que le format de [l’action HTTP](connectors-native-http.md).

     ![Déclencheur HTTP](./media/connectors-native-webhook/using-trigger.png)

3. Ajoutez au moins une action.
4. Cliquez sur **Enregistrer** pour publier l’application logique. Cela permet d’appeler le point de terminaison d’abonnement avec l’URL de rappel nécessaire pour déclencher cette application logique.
5. À chaque fois que le service effectue un `HTTP POST` à l’URL de rappel, l’application logique se déclenche et inclut toutes les données transmises dans la requête.

## <a name="use-the-webhook-action"></a>Utilisation de l’action webhook

Une [*action*](connectors-overview.md) est une opération effectuée par le flux de travail défini dans une application logique. Une action webhook enregistre une *URL de rappel* avec un service et attend jusqu'à ce que l’URL soit appelée avant de se déclencher à nouveau. [« Envoyer un message électronique d’approbation »](connectors-create-api-office365-outlook.md) est un exemple de connecteur qui suit ce modèle. Vous pouvez étendre ce modèle à n’importe quel service à l’aide de l’action webhook. 

Voici un exemple de configuration d’une action webhook dans le concepteur d’application logique. Ces étapes supposent que vous avez déjà déployé ou que vous accédez à une API qui suit [le modèle d’abonnement et de résiliation d’abonnement au webhook utilisé dans les applications logiques](../logic-apps/logic-apps-create-api-app.md#webhook-actions). L’appel d’abonnement est effectué à chaque fois qu’une application logique exécute l’action webhook. L’appel de résiliation d’abonnement est effectué à chaque fois qu’une exécution est annulée en attendant une réponse, ou avant que l’exécution de l’application logique n’expire.

**Pour ajouter une action webhook**

1. Choisissez **Nouvelle étape** > **Ajouter une action**.

2. Dans la zone de recherche, tapez « webhook » pour rechercher l’action **HTTP Webhook**.

    ![Sélectionner l’action de requête](./media/connectors-native-webhook/using-action-1.png)

3. Renseignez les paramètres pour les appels d’abonnement et de résiliation d’abonnement au webhook

   Cette étape suit le même modèle que le format de [l’action HTTP](connectors-native-http.md).

     ![Terminer l’action de requête](./media/connectors-native-webhook/using-action-2.png)
   
   Lors de l’exécution, l’application logique appelle le point de terminaison d’abonnement après avoir atteint cette étape.

4. Cliquez sur **Enregistrer** pour publier l’application logique.

## <a name="technical-details"></a>Détails techniques

Voici plus de détails sur les déclencheurs et les actions que webhook prend en charge.

## <a name="webhook-triggers"></a>Déclencheurs Webhook

| Action | Description |
| --- | --- |
| HTTP Webhook |Permet d’abonner une URL de rappel à un service qui peut appeler l’URL pour déclencher l’application logique lorsque nécessaire. |

### <a name="trigger-details"></a>Détails du déclencheur

#### <a name="http-webhook"></a>HTTP Webhook

Permet d’abonner une URL de rappel à un service qui peut appeler l’URL pour déclencher l’application logique lorsque nécessaire.
Une * signifie que le champ est obligatoire.

| Nom d’affichage | Nom de la propriété | Description |
| --- | --- | --- |
| Méthode d’abonnement* |method |Méthode HTTP à utiliser pour la demande d’abonnement |
| URI d’abonnement* |URI |URI HTTP à utiliser pour la demande d’abonnement |
| Méthode de résiliation d’abonnement* |method |Méthode HTTP à utiliser pour la demande de résiliation d’abonnement |
| URI de résiliation d’abonnement* |URI |URI HTTP à utiliser pour la demande de résiliation d’abonnement |
| Corps d’abonnement |body |Corps de la demande HTTP pour s’abonner |
| En-têtes de l’abonnement |headers |En-têtes de la demande HTTP pour s’abonner |
| Authentification de l’abonnement |Authentification |Authentification HTTP à utiliser pour s’abonner. Voir [Connecteur HTTP](connectors-native-http.md#authentication) pour plus d’informations |
| Corps de résiliation d’abonnement |body |Corps de la demande HTTP de résiliation d’abonnement |
| En-têtes de résiliation d’abonnement |headers |En-têtes de la demande HTTP de résiliation d’abonnement |
| Authentification de la résiliation d’abonnement |authentication |Authentification HTTP à utiliser pour la résiliation d’abonnement. Voir [Connecteur HTTP](connectors-native-http.md#authentication) pour plus d’informations |

**Détails des résultats**

Requête Webhook

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| headers |objet |En-têtes de requête Webhook |
| body |objet |Objet de la requête Webhook |
| Code d’état |int |Code d’état de la requête Webhook |

## <a name="webhook-actions"></a>Actions de webhook

| Action | Description |
| --- | --- |
| HTTP Webhook |Permet d’abonner une URL de rappel à un service qui peut appeler l’URL redémarrer une étape du flux de travail lorsque nécessaire. |

### <a name="action-details"></a>Détails de l’action

#### <a name="http-webhook"></a>HTTP Webhook

Permet d’abonner une URL de rappel à un service qui peut appeler l’URL redémarrer une étape du flux de travail lorsque nécessaire.
Une * signifie que le champ est obligatoire.

| Nom d’affichage | Nom de la propriété | Description |
| --- | --- | --- |
| Méthode d’abonnement* |method |Méthode HTTP à utiliser pour la demande d’abonnement |
| URI d’abonnement* |URI |URI HTTP à utiliser pour la demande d’abonnement |
| Méthode de résiliation d’abonnement* |method |Méthode HTTP à utiliser pour la demande de résiliation d’abonnement |
| URI de résiliation d’abonnement* |URI |URI HTTP à utiliser pour la demande de résiliation d’abonnement |
| Corps d’abonnement |body |Corps de la demande HTTP pour s’abonner |
| En-têtes de l’abonnement |headers |En-têtes de la demande HTTP pour s’abonner |
| Authentification de l’abonnement |Authentification |Authentification HTTP à utiliser pour s’abonner. Voir [Connecteur HTTP](connectors-native-http.md#authentication) pour plus d’informations |
| Corps de résiliation d’abonnement |body |Corps de la demande HTTP de résiliation d’abonnement |
| En-têtes de résiliation d’abonnement |headers |En-têtes de la demande HTTP de résiliation d’abonnement |
| Authentification de la résiliation d’abonnement |authentication |Authentification HTTP à utiliser pour la résiliation d’abonnement. Voir [Connecteur HTTP](connectors-native-http.md#authentication) pour plus d’informations |

**Détails des résultats**

Requête Webhook

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| headers |objet |En-têtes de requête Webhook |
| body |objet |Objet de la requête Webhook |
| Code d’état |int |Code d’état de la requête Webhook |

## <a name="next-steps"></a>étapes suivantes

* [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Rechercher d’autres connecteurs](apis-list.md)