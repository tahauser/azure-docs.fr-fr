---
title: Gestion des erreurs et des exceptions pour Logic Apps dans Azure | Microsoft Docs
description: Modèles de gestion des erreurs et des exceptions dans Logic Apps.
services: logic-apps
documentationcenter: ''
author: dereklee
manager: anneta
editor: ''
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/2018
ms.author: deli; LADocs
ms.openlocfilehash: 2ae4f0ae9782ada23089d364e8a1700144ef5ff7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Gérer les erreurs et les exceptions dans Logic Apps

Une gestion de manière appropriée des temps d’arrêt ou des problèmes de systèmes dépendants peut s’avérer être un défi pour toute architecture d’intégration. Pour créer des intégrations fiables qui sont résilientes aux problèmes et aux échecs, Logic Apps offre une expérience de qualité pour la gestion des erreurs et des exceptions. 

## <a name="retry-policies"></a>Stratégies de nouvelle tentative

Pour le type de gestion des erreurs et des exceptions le plus simple, vous pouvez utiliser la stratégie de nouvelle tentative. Cette stratégie définit si et comment l’action fait l’objet d’une nouvelle tentative en cas d’expiration ou d’échec de la requête initiale, à savoir toute requête ayant entraîné une réponse 429 ou 5xx. 

Il existe quatre types de stratégies de nouvelle tentative : par défaut, aucune, intervalle fixe et intervalle exponentiel. Si votre définition de flux de travail ne comporte pas de stratégie de nouvelle tentative, la stratégie par défaut telle que définie par le service est utilisée à la place.

Pour configurer des stratégies de nouvelle tentative, le cas échéant, ouvrez le Concepteur d’application logique pour votre application logique, puis accédez à **Paramètres** pour une action spécifique dans votre application logique. Vous pouvez également définir des stratégies de nouvelle tentative dans la section **inputs** d’une action spécifique ou d’un déclencheur, si elle est peut faire l’objet d’une nouvelle tentative, dans votre définition de flux de travail. Voici la syntaxe générale :

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Pour plus d’informations sur la syntaxe et la section **inputs**, consultez la [section relative aux stratégies de nouvelle tentative sous Déclencheurs et actions pour les flux de travail][retryPolicyMSDN]. Pour plus d’informations sur les limitations de la stratégie de nouvelle tentative, consultez [Limites et configuration de Logic Apps](../logic-apps/logic-apps-limits-and-config.md). 

### <a name="default"></a>Default

Lorsque vous ne définissez pas de stratégie de nouvelle tentative dans la section **retryPolicy**, votre application logique utilise la stratégie par défaut, qui est une [stratégie d’intervalle exponentielle](#exponential-interval) qui envoie jusqu’à quatre nouvelles tentatives à des intervalles à croissance exponentielle mis à l’échelle tous les 7,5 secondes. L’intervalle est limité entre 5 et 45 secondes. Cette stratégie est équivalente à la stratégie dans cet exemple de définition de flux de travail HTTP :

```json
"HTTP": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7.5S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT45S"
        }
    },
    "runAfter": {}
}
```

### <a name="none"></a>Aucun

Si vous définissez **retryPolicy** sur **none**, cette stratégie ne réessaie pas les requêtes ayant échoué.

| Nom de l'élément | Obligatoire | type | Description | 
| ------------ | -------- | ---- | ----------- | 
| Type | OUI | Chaîne | **Aucune** | 
||||| 

### <a name="fixed-interval"></a>Intervalle fixe

Si vous définissez **retryPolicy** sur **fixed**, cette stratégie réessaie une requête ayant échoué en attendant l’intervalle de temps spécifié avant d’envoyer la requête suivante.

| Nom de l'élément | Obligatoire | type | Description |
| ------------ | -------- | ---- | ----------- |
| Type | OUI | Chaîne | **fixed** |
| count | OUI | Entier  | Nombre de nouvelles tentatives, qui doit être compris entre 1 et 90 | 
| interval | OUI | Chaîne | Intervalle avant nouvelle tentative au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), qui doit être compris entre PT5S et PT1D | 
||||| 

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Intervalle exponentiel

Si vous définissez **retryPolicy** sur **exponential**, cette stratégie réessaie une requête ayant échoué après un intervalle de temps aléatoire à partir d’une plage à croissance exponentielle. La stratégie garantit également l’envoi de chaque nouvelle tentative à un intervalle aléatoire supérieur à **minimumInterval** et inférieur à **maximumInterval**. Les stratégies exponentielles nécessitent des valeurs **count** et **interval**, tandis que les valeurs **minimumInterval** and **maximumInterval** sont facultatives. Si vous souhaitez remplacer les valeurs par défaut PT5S et PT1D respectivement, vous pouvez ajouter ces valeurs.

| Nom de l'élément | Obligatoire | type | Description |
| ------------ | -------- | ---- | ----------- |
| Type | OUI | Chaîne | **exponential** |
| count | OUI | Entier  | Nombre de nouvelles tentatives, qui doit être compris entre 1 et 90  |
| interval | OUI | Chaîne | Intervalle avant nouvelle tentative au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), qui doit être compris entre PT5S et PT1D. |
| minimumInterval | Non  | Chaîne | Intervalle minimal avant nouvelle tentative au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), qui doit être compris entre PT5S et **interval** |
| maximumInterval | Non  | Chaîne | Intervalle minimal avant nouvelle tentative au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), qui doit être compris entre **interval** et PT1D | 
||||| 

Ce tableau montre comment une variable aléatoire uniforme dans la plage indiquée est générée pour chaque nouvelle tentative jusqu’à **count** compris :

**Plage des variables aléatoires**

| Nombre de nouvelles tentatives | Intervalle minimal | Intervalle maximal |
| ------------ | ---------------- | ---------------- |
| 1 | Max(0, **minimumInterval**) | Min(interval, **maximumInterval**) |
| 2 | Max(interval, **minimumInterval**) | Min(2 * interval, **maximumInterval**) |
| 3 | Max(2 * interval, **minimumInterval**) | Min(4 * interval, **maximumInterval**) |
| 4 | Max(4 * interval, **minimumInterval**) | Min(8 * interval, **maximumInterval**) |
| .... | | | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Intercepter et gérer les échecs avec la propriété RunAfter

Chaque action d’application logique déclare les actions qui doivent être terminées avant le début de cette action, semblable à la façon dont vous spécifiez l’ordre des étapes dans votre flux de travail. Dans une définition d’action, la propriété **runAfter** définit l’ordre et est un objet qui décrit les actions et les états d’action qui exécutent l’action.

Par défaut, toutes les actions que vous ajoutez dans le Concepteur d’application logique sont définies pour s’exécuter après l’étape précédente, lorsque le résultat de l’étape précédente est **Succeeded**. Vous pouvez toutefois personnaliser la propriété **runAfter** de sorte que les actions soient déclenchées lorsque le résultat des actions précédentes est **Failed**, **Skipped**, ou une combinaison de ces valeurs. Par exemple, pour ajouter un élément à une rubrique Service Bus spécifique après l’échec d’une action **Insert_Row** donnée, vous pouvez utiliser cet exemple de définition **runAfter** :

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

La propriété **runAfter** est définie pour exécuter l’action lorsque l’état de l’action **Insert_Row** est **Failed**. Pour exécuter l’action si l’état de l’action est **Succeeded**, **Failed** ou **Skipped**, utilisez cette syntaxe :

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Les actions qui s’exécutent et se terminent correctement suite à l’échec d’une action précédente sont marquées comme **Succeeded**. Ce comportement signifie que si vous avez correctement intercepté tous les échecs dans un flux de travail, l’exécution elle-même est marquée comme **Succeeded**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Évaluer les actions avec des étendues et leurs résultats

Comme pour l’exécution d’étapes après des actions individuelles avec la propriété **runAfter**, vous pouvez regrouper des actions dans une [étendue](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Vous pouvez utiliser des étendues lorsque vous souhaitez regrouper des actions de manière logique, évaluer l’état d’agrégation de l’étendue et effectuer des actions en fonction de cet état. Une fois que toutes les actions d’une étendue ont été exécutées, l’étendue récupère son propre état. 

Pour vérifier l’état d’une étendue, vous pouvez utiliser les mêmes critères que ceux utilisés pour vérifier l’état d’exécution d’une application logique, tels que **Succeeded**, **Failed**, etc. 

Par défaut, lorsque toutes les actions de l’étendue réussissent, l’état de l’étendue est défini sur **Succeeded**. Si l’état de la dernière action d’une étendue est **Failed** ou **Aborted**, l’état de l’étendue est défini sur **Failed**. 

Pour intercepter des exceptions dans une étendue **Failed** et exécuter des actions qui gèrent ces erreurs, vous pouvez utiliser la propriété **runAfter** de cette étendue **Failed**. Ainsi, si *des* actions figurant dans l’étendue échouent, et que vous utilisez la propriété **runAfter** pour cette étendue, vous pouvez créer une seule action pour intercepter des échecs.

Pour les limites sur les étendues, consultez [Limites et configuration](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Obtenir le contexte et les résultats des échecs

Bien que l’interception des échecs d’une étendue soit très utile, vous aurez peut-être également besoin du contexte pour identifier précisément les actions qui ont échoué, ainsi que les codes d’erreur ou d’état renvoyés. La fonction de workflow **@result()** fournit le contexte dans le résultat de toutes les actions au sein d’une étendue.

La fonction **@result()** accepte un paramètre unique (le nom de l’étendue), et retourne un tableau de tous les résultats d’action dans cette étendue. Ces objets d’action incluent les mêmes attributs que l’objet  **@actions()**, tels que l’heure de début, l’heure de fin, l’état, les entrées, les ID de corrélation et les sorties de l’action. Vous pouvez facilement associer une fonction **@result()** à une propriété **runAfter** pour envoyer le contexte de toutes les actions qui ont échoué dans une étendue.

Pour exécuter une action *pour chaque* action dans une étendue marquée comme **Failed** et filtrer les résultats sur les actions ayant échoué, vous pouvez associer **@result()** à une action **[Filter Array](../connectors/connectors-native-query.md)** et une boucle **[ForEach](../logic-apps/logic-apps-control-flow-loops.md)**. Vous pouvez prendre le tableau des résultats filtrés et effectuer une action pour chaque échec à l’aide de la boucle **ForEach** . 

Cet exemple, suivi d’une explication détaillée, envoie une requête HTTP POST avec le corps de réponse de toutes les actions qui ont échoué dans l’étendue « My_Scope » :

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Voici la procédure détaillée pour décrire ce qui se produit dans cet exemple :

1. Pour obtenir le résultat de toutes les actions au sein de « My_Scope », l’action **Filter Array** permet de filtrer **@result('My_Scope')**.

2. La condition de l’action **Filter Array** est tout élément **@result()** dont l’état est égal à **Failed**. Cette condition filtre le tableau de tous les résultats d’action de « My_Scope » selon un tableau contenant uniquement les résultats d’action ayant échoué.

3. Exécution d’une action en boucle **For Each** sur les résultats du *tableau filtré*. Cette étape exécute une action *pour chaque* résultat d’action ayant échoué filtré précédemment.

   Si une action unique dans l’étendue a échoué, les actions de **foreach** s’exécutent une seule fois. 
   Plusieurs actions ayant échoué peuvent provoquer une action par échec.

4. Envoi d’une requête HTTP POST sur le corps de réponse de l’élément **foreach**, qui est **@item()['outputs']['body']**. La forme de l’élément **@result()** est identique à la forme **@actions()** et peut être analysée de la même façon.

5. Deux en-têtes personnalisés avec le nom de l’action qui a échoué **@item()['name']** sont également inclus, ainsi que l’ID de suivi du client d’exécution qui a échoué **@item()['clientTrackingId']**.

Pour référence, voici un exemple d’un seul élément **@result()**, montrant les propriétés **name**, **body** et **clientTrackingId** analysées dans l’exemple précédent. En dehors d’une action **foreach**, **@result()** retourne un tableau de ces objets.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Vous pouvez utiliser les expressions décrites plus haut dans cet article pour exécuter différents modèles de gestion des exceptions. Vous pouvez choisir d’exécuter une seule action de gestion en dehors de l’étendue qui accepte l’intégralité du tableau filtré d’échecs et de supprimer l’action **foreach**. Vous pouvez également inclure d’autres propriétés utiles à partir de la réponse **@result()** comme décrit précédemment.

## <a name="azure-diagnostics-and-telemetry"></a>Azure Diagnostics et télémétrie

Les précédents modèles sont très utiles pour gérer les erreurs et les exceptions d’une exécution, mais vous pouvez également identifier les erreurs et y répondre indépendamment de l’exécution elle-même. 
[Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) fournit un moyen simple d’envoyer tous les événements de flux de travail, y compris tous les états d’exécution et d’action, à un compte de stockage Azure ou un hub d’événements créé avec Azure Event Hubs. 

Vous pouvez surveiller les journaux et les mesures ou les publier dans n’importe quel outil de surveillance de votre choix pour évaluer les états d’exécution. Vous avez également la possibilité de transmettre tous les événements via Event Hubs à [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). Dans Stream Analytics, vous pouvez écrire des requêtes actives basées sur des anomalies, moyennes ou échecs dans les journaux de diagnostic. Vous pouvez utiliser Stream Analytics pour envoyer des informations à d’autres sources de données, telles que des files d’attente, des rubriques, SQL, Azure Cosmos DB ou Power BI.

## <a name="next-steps"></a>Étapes suivantes

* [Voir comment un client conçoit la gestion des erreurs avec Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Consultez d’autres exemples et scénarios Logic Apps](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9