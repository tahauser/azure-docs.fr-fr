---
title: Gestion des erreurs et des exceptions pour Logic Apps dans Azure | Microsoft Docs
description: "Modèles de gestion des erreurs et des exceptions dans Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: derek1ee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: a74c7d18306359c9152f139299de1208b5932fe5
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Gérer les erreurs et les exceptions dans Logic Apps

Logic Apps dans Azure propose un ensemble complet d’outils et de modèles afin de garantir la résistance et la fiabilité de vos intégrations contre les défaillances. Toute architecture d’intégration nécessite que les temps d’arrêt et les problèmes soient gérés de manière appropriée. Logic Apps fait de la gestion des erreurs une expérience hors pair. Il met à votre disposition les outils dont vous avez besoin pour agir sur les exceptions et les erreurs dans vos workflows.

## <a name="retry-policies"></a>Stratégies de nouvelle tentative

Le type de gestion des erreurs et des exceptions le plus simple consiste à utiliser une stratégie de nouvelle tentative. Une stratégie de nouvelle tentative définit si et comment l’action doit être réessayée en cas d’expiration ou d’échec de la demande initiale (toute demande ayant entraîné une réponse 429 ou 5xx). 

Il existe quatre types de stratégies de nouvelle tentative : par défaut, aucune, intervalle fixe et intervalle exponentiel. Si aucune stratégie de nouvelle tentative n’est fournie dans la définition de workflow, la stratégie par défaut telle que définie par le service est utilisée. 

Vous pouvez configurer des stratégies de nouvelle tentative dans les *entrées* d’une action ou d’un déclencheur s’ils peuvent faire l’objet d’une nouvelle tentative. De même, vous pouvez configurer des stratégies de nouvelle tentative (le cas échéant) dans le Concepteur d’application logique. Pour configurer une stratégie de nouvelle tentative dans le Concepteur d’application logique, accédez à **Paramètres** pour une action spécifique.

Pour plus d’informations sur les limitations des stratégies de nouvelle tentative, consultez [Limites et configuration de Logic Apps](../logic-apps/logic-apps-limits-and-config.md). Pour plus d’informations sur la syntaxe prise en charge, consultez la [section relative aux stratégies de nouvelle tentative sous Déclencheurs et actions pour les workflows][retryPolicyMSDN].

### <a name="default"></a>Default

Si vous ne définissez pas de stratégie de nouvelle tentative (**retryPolicy** n’est pas défini), la stratégie par défaut est utilisée. La stratégie par défaut est une stratégie d’intervalles exponentiels qui envoie jusqu’à quatre tentatives à intervalles exponentiels définis à 7,5 secondes. L’intervalle est limité entre 5 et 45 secondes. Cette stratégie par défaut est équivalente à la stratégie dans cet exemple de définition de workflow HTTP :

```json
"HTTP":
{
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
    "runAfter": {},
    "type": "Http"
}
```

### <a name="none"></a>Aucun

Si **retryPolicy** a la valeur **none**, une demande ayant échoué n’est pas renouvelée.

| Nom de l'élément | Obligatoire | type | DESCRIPTION |
| ------------ | -------- | ---- | ----------- |
| Type | OUI | Chaîne | **Aucune** |

### <a name="fixed-interval"></a>Intervalle fixe

Si **retryPolicy** est défini sur **fixed**, la stratégie réessaie une demande ayant échoué en attendant l’intervalle de temps spécifié avant d’envoyer la demande suivante.

| Nom de l'élément | Obligatoire | type | DESCRIPTION |
| ------------ | -------- | ---- | ----------- |
| Type | OUI | Chaîne | **fixed** |
| count | OUI | Entier  | Nombre de nouvelles tentatives. Doit être compris entre 1 et 90. |
| interval | OUI | Chaîne | Intervalle de nouvelle tentative au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Doit être compris entre PT5S et PT1D. |

### <a name="exponential-interval"></a>Intervalle exponentiel

Si **retryPolicy** est défini sur **exponential**, la stratégie réessaie une demande ayant échoué après un intervalle de temps aléatoire à partir d’une plage à croissance exponentielle. Grâce à ce type de stratégie, chaque nouvelle tentative est envoyée à un intervalle aléatoire supérieur à **minimumInterval** et inférieur à **maximumInterval**. Une variable aléatoire uniforme dans la plage indiquée dans le tableau suivant est générée pour chaque nouvelle tentative jusqu’à **count** compris :

**Plage des variables aléatoires**

| Nombre de nouvelles tentatives | Intervalle minimal | Intervalle maximal |
| ------------ |  ------------ |  ------------ |
| 1 | Max(0, **minimumInterval**) | Min(interval, **maximumInterval**) |
| 2 | Max(interval, **minimumInterval**) | Min(2 * interval, **maximumInterval**) |
| 3 | Max(2 * interval, **minimumInterval**) | Min(4 * interval, **maximumInterval**) |
| 4 | Max(4 * interval, **minimumInterval**) | Min(8 * interval, **maximumInterval**) |
| ... |

Pour les stratégies de type exponentiel, **count** et **interval** sont requis. Les valeurs pour **minimumInterval** et **maximumInterval** sont facultatives. Vous pouvez les ajouter pour remplacer les valeurs par défaut de PT5S et PT1D, respectivement.

| Nom de l'élément | Obligatoire | type | DESCRIPTION |
| ------------ | -------- | ---- | ----------- |
| Type | OUI | Chaîne | **exponential** |
| count | OUI | Entier  | Nombre de nouvelles tentatives. Doit être compris entre 1 et 90.  |
| interval | OUI | Chaîne | Intervalle de nouvelle tentative au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Doit être compris entre PT5S et PT1D. |
| minimumInterval | Non  | Chaîne | Intervalle minimal de nouvelle tentative au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Doit être compris entre PT5S et **interval**. |
| maximumInterval | Non  | Chaîne | Intervalle maximal de nouvelle tentative au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Doit être compris entre **interval** et PT1D. |

## <a name="catch-failures-with-the-runafter-property"></a>Identification des échecs avec la propriété RunAfter

Chaque action d’application logique déclare quelles actions doivent se terminer avant de démarrer une autre action. Cette opération revient à hiérarchiser les étapes de votre workflow. Ce classement est connu sous le nom de propriété **runAfter** dans la définition de l’action. 

La propriété **runAfter** est un objet qui décrit les actions et les états d’action qui exécutent l’action. Par défaut, toutes les actions que vous avez ajoutées à l’aide du Concepteur d’application logique sont définies pour s’exécuter après l’étape précédente, si le résultat de celle-ci est **Succeeded**. 

Toutefois, vous pouvez personnaliser la valeur **runAfter** pour déclencher des actions quand les actions précédentes ont pour résultat **Failed**, **Skipped** ou un ensemble de ces valeurs possible. Si vous souhaitez ajouter un élément à une rubrique Service Bus désignée suite à l’échec d’une action spécifique **Insert_Row**, vous pouvez utiliser la configuration **runAfter** suivante :

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

Notez que **runAfter** est défini pour se déclencher si le résultat de l’action **Insert_Row** est **Failed**. Pour exécuter l’action si l’état de l’action est **Succeeded**, **Failed** ou **Skipped**, utilisez cette syntaxe :

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Les actions qui s’exécutent et se terminent correctement suite à l’échec d’une action précédente sont marquées comme **Succeeded**. Cela signifie que si vous avez correctement intercepté tous les échecs dans un workflow, l’exécution elle-même est marquée comme **Succeeded**.

## <a name="scopes-and-results-to-evaluate-actions"></a>Étendues et résultats permettant d’évaluer les actions

Vous pouvez regrouper des actions dans une [étendue](../logic-apps/logic-apps-loops-and-scopes.md), à l’image des actions individuelles dont l’issue détermine le déclenchement d’une exécution. Une étendue fait office de regroupement logique d’actions. 

Les étendues sont utiles pour organiser vos actions d’application logique et pour effectuer des évaluations d’agrégation sur l’état d’une étendue. L’étendue elle-même se voit attribuer un état une fois toutes les actions de l’étendue effectuées. L’état de l’étendue est déterminé avec les mêmes critères que pour une exécution. Si la dernière action dans une branche de l’exécution est **Failed** ou **Aborted**, l’état est **Failed**.

Vous pouvez utiliser la propriété **runAfter** sur une étendue qui a été marquée comme **Failed** pour déclencher des actions spécifiques en raison d’échecs survenus dans l’étendue. Si *des* actions figurant dans l’étendue échouent et que vous utilisez **runAfter** pour celle-ci, vous pouvez créer une seule action pour intercepter des échecs.

### <a name="get-the-context-of-failures-with-results"></a>Obtenir le contexte des échecs avec les résultats

Bien que l’interception des échecs d’une étendue soit très utile, vous aurez peut-être également besoin du contexte pour identifier précisément les actions qui ont échoué et pour comprendre les codes d’erreur ou d’état retournés. La fonction de workflow **@result()** fournit le contexte dans le résultat de toutes les actions au sein d’une étendue.

La fonction **@result()** prend un paramètre unique, le nom de l’étendue, et retourne un tableau de tous les résultats d’action dans cette étendue. Ces objets d’action incluent les mêmes attributs que l’objet **@actions()**, y compris l’heure de début de l’action, l’heure de fin de l’action, l’état de l’action, les entrées de l’action, les ID de corrélation d’action, ainsi que ses résultats. 

Vous pouvez facilement associer une fonction **@result()** à une propriété **runAfter** pour envoyer le contexte de toutes les actions qui ont échoué dans une étendue.

Pour exécuter une action *pour chaque* action dans une étendue marquée comme **Failed** et filtrer les résultats sur les actions ayant échoué, associez **@result()** à une action [Filter_array](../connectors/connectors-native-query.md) et une boucle [foreach](../logic-apps/logic-apps-loops-and-scopes.md). Avec le tableau des résultats filtrés, vous pouvez effectuer une action pour chaque échec à l’aide de la boucle **foreach**. 

Voici un exemple qui envoie une demande HTTP POST avec le corps de réponse de toutes les actions qui ont échoué dans l’étendue My_Scope :

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

Voici la procédure détaillée pour décrire ce qui se produit dans l’exemple précédent :

1. Pour obtenir le résultat de toutes les actions au sein de My_Scope, l’action **Filter_array** permet de filtrer **@result('My_Scope')**.

2. La condition de l’action **Filter_array** est tout élément **@result()** dont l’état est égal à **Failed**. Cette condition filtre le tableau de tous les résultats d’action de My_Scope selon un tableau contenant uniquement les résultats d’action ayant échoué.

3. Exécution d’une action **foreach** sur les résultats du *tableau filtré*. Cette étape exécute une action *pour chaque* résultat d’action ayant échoué filtré précédemment.

    Si une action unique dans l’étendue a échoué, les actions de **foreach** s’exécutent une seule fois. Plusieurs actions ayant échoué peuvent provoquer une action par échec.

4. Envoi d’une requête HTTP POST sur le corps de réponse de l’élément **foreach**, ou **@item()['outputs']['body']**. La forme de l’élément **@result()** est la même que celle de **@actions()**. Elle peut être analysée de la même façon.

5. Deux en-têtes personnalisés avec le nom de l’action qui a échoué **@item()['name']** sont également inclus, ainsi que l’ID de suivi du client d’exécution qui a échoué **@item()['clientTrackingId']**.

Pour référence, voici un exemple d’un élément **@result()** unique. Il montre les propriétés **name**, **body** et **clientTrackingId** qui sont analysées dans l’exemple précédent. En dehors d’une action **foreach**, **@result()** retourne un tableau de ces objets.

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

Vous pouvez utiliser les expressions décrites plus haut dans cet article pour découvrir différents modèles de gestion des exceptions. Vous pouvez choisir d’exécuter une seule action de gestion en dehors de l’étendue qui accepte l’intégralité du tableau filtré d’échecs et de supprimer **foreach**. Vous pouvez également inclure d’autres propriétés utiles à partir de la réponse **@result()**, comme décrit précédemment.

## <a name="azure-diagnostics-and-telemetry"></a>Azure Diagnostics et télémétrie

Les modèles décrit dans cet article sont très utiles pour gérer les erreurs et les exceptions dans une exécution, mais vous pouvez également identifier les erreurs et y répondre indépendamment de l’exécution elle-même. [Azure Diagnostics](../logic-apps/logic-apps-monitor-your-logic-apps.md) fournit un moyen simple d’envoyer tous les événements de workflow (y compris tous les états d’exécution et d’action) à un compte de stockage Azure ou un hub d’événements dans Azure Event Hubs. 

Vous pouvez surveiller des journaux et des métriques ou les publier dans n’importe quel outil de surveillance de votre choix pour évaluer les états d’exécution. Vous avez également la possibilité de transmettre tous les événements via Event Hubs à [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). Dans Stream Analytics, vous pouvez écrire des requêtes actives basées sur des anomalies, moyennes ou échecs dans les journaux de diagnostic. Vous pouvez utiliser Stream Analytics pour envoyer des informations à d’autres sources de données, telles que des files d’attente, des rubriques, SQL, Azure Cosmos DB ou Power BI.

## <a name="next-steps"></a>Étapes suivantes

* Voir comment un client [conçoit la gestion des erreurs avec Logic Apps dans Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md).
* Consulter d’autres [exemples et scénarios Logic Apps](../logic-apps/logic-apps-examples-and-scenarios.md).
* Découvrir comment créer des [déploiements automatisés pour des applications logiques](../logic-apps/logic-apps-create-deploy-template.md).
* Découvrir comment [créer et déployer des applications logiques avec Visual Studio](logic-apps-deploy-from-vs.md).

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
