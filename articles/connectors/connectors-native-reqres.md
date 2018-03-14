---
title: "Utilisation d’actions de requête et de réponse | Microsoft Docs"
description: "Présentation des déclencheurs et des actions de requête et de réponse dans une application logique Azure"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 58210db585befd7ce915d4579d4d0303eb15bff3
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-request-and-response-components"></a>Familiarisation avec les composants de requête et de réponse
Avec les composants de requête et réponse dans une application logique, vous pouvez répondre en temps réel aux événements.

Vous pouvez par exemple :

* répondre à une requête HTTP avec des données d’une base de données locale par le biais d’une application logique ;
* déclencher une application logique à partir d’un événement webhook externe ;
* appeler une application logique avec une action de requête et de réponse depuis une autre application logique.

Pour commencer à utiliser les actions de requête et de réponse dans une application logique, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-request-trigger"></a>Utilisation d’un déclencheur de requête HTTP
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](connectors-overview.md).

Voici un exemple de séquence de configuration d’une requête HTTP dans le concepteur d’application logique.

1. Ajoutez le déclencheur **Requête quand une demande HTTP est reçue** à votre application logique. Vous pouvez éventuellement fournir un schéma JSON (à l’aide d’un outil tel que [JSONSchema.net](http://jsonschema.net)) pour le corps de texte de la requête. Ainsi, le concepteur peut générer des jetons pour les propriétés dans la requête HTTP.
2. Ajoutez une autre action afin d’enregistrer l’application logique.
3. Après l’enregistrement de l'application logique, vous pouvez obtenir l’URL de la requête HTTP à partir de la carte de requête.
4. Une requête HTTP POST (vous pouvez utiliser un outil tel que [Postman](https://www.getpostman.com/)) à l’URL déclenche l’application logique.

> [!NOTE]
> Si vous ne définissez pas une action de réponse, une réponse `202 ACCEPTED` est immédiatement renvoyée à l’appelant. Vous pouvez utiliser l’action de réponse pour personnaliser une réponse.
> 
> 

![Déclencheur de réponse](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Utilisation d’une action Réponse HTTP
L’action Réponse HTTP est uniquement valide lorsque vous l'utilisez dans un workflow déclenché par une requête HTTP. Si vous ne définissez pas une action de réponse, une réponse `202 ACCEPTED` est immédiatement renvoyée à l’appelant.  Vous pouvez ajouter une action de réponse à tout moment dans le workflow. L’application logique conserve la requête entrante ouverte pendant une minute seulement pour attendre une réponse.  Si aucune réponse n’a été envoyée après une minute depuis le workflow (et qu’une action de réponse existe dans la définition) un `504 GATEWAY TIMEOUT` est envoyé à l’appelant.

Voici comment ajouter une action Réponse HTTP :

1. Sélectionnez le bouton **Nouvelle étape** .
2. Choisissez **Ajouter une action**.
3. Dans la zone de recherche Action, tapez **response** pour répertorier l’action de réponse.
   
    ![Sélectionner l'action de réponse](./media/connectors-native-reqres/using-action-1.png)
4. Ajoutez tout paramètre nécessaire au message de réponse HTTP.
   
    ![Exécuter l’action de réponse](./media/connectors-native-reqres/using-action-2.png)
5. Cliquez dans le coin supérieur gauche de la barre d’outils pour enregistrer, et votre application logique est à la fois enregistrée et publiée (activation).

## <a name="request-trigger"></a>Déclencheur de requête
Voici les détails du déclencheur que ce connecteur prend en charge. Il existe un seul déclencheur de requête.

| Déclencheur | Description |
| --- | --- |
| Requête |Se produit quand une requête HTTP est reçue |

## <a name="response-action"></a>Action de réponse
Voici les détails de l'action que ce connecteur prend en charge. Il existe une action de réponse unique qui est utilisable uniquement lorsqu’elle est accompagnée d’un déclencheur de requête.

| Action | Description |
| --- | --- |
| response |Renvoie une réponse à requête HTTP corrélée |

### <a name="trigger-and-action-details"></a>Détail des déclencheurs et des actions
Les tableaux suivants décrivent les champs d’entrée du déclencheur et de l’action, les détails de sortie correspondants.

#### <a name="request-trigger"></a>Déclencheur de requête
Voici un champ d’entrée pour le déclencheur provenant d’une requête HTTP entrante.

| Nom complet | Nom de la propriété | Description |
| --- | --- | --- |
| JSON Schema (Schéma JSON) |schema |Le schéma JSON du corps de texte de la requête HTTP |

<br>

**Détails des résultats**

Vous trouverez ci-dessous les détails de sortie pour la requête.

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| headers |objet |En-têtes de requête |
| body |objet |Objet Requête |

#### <a name="response-action"></a>Action de réponse
Les éléments suivants sont des champs d’entrée pour l’action Réponse HTTP. Le symbole * désigne est un champ obligatoire.

| Nom complet | Nom de la propriété | Description |
| --- | --- | --- |
| Status Code (Code d’état)* |statusCode |Le code d’état HTTP |
| headers |headers |Un objet JSON de tout en-tête de réponse à inclure |
| body |body |Le corps de texte de la réponse |

## <a name="next-steps"></a>Étapes suivantes
Essayez maintenant la plateforme et [créez une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md). Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).

