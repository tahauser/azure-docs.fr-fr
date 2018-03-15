---
title: "Actions et déclencheurs de workflow - Azure Logic Apps | Microsoft Docs"
description: "En savoir plus sur les déclencheurs et les actions pour la création de workflows automatisés et de processus avec des applications logiques"
services: logic-apps
author: MandiOhlinger
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/13/2017
ms.author: klam; LADocs
ms.openlocfilehash: af30fd30f389cdc2070c45ae3b6e2cb1165239e7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>Déclencheurs et actions pour les workflows d’application logique

Toutes les applications logiques commencent par un déclencheur suivi d’actions. Cet article décrit les types de déclencheurs et d’actions que vous pouvez utiliser pour créer des intégrations de système et automatiser les flux de travail ou processus métier en générant des applications logiques. 
  
## <a name="triggers-overview"></a>Vue d’ensemble des déclencheurs 

Toutes les applications logiques commencent par un déclencheur, qui spécifie les appels qui peuvent démarrer l’exécution d’une application logique. Voici les types de déclencheurs que vous pouvez utiliser :

* Un déclencheur d’*interrogation*, qui vérifie le point de terminaison HTTP d’un service à intervalles réguliers
* Un déclencheur d’*émission* (Push), qui appelle l’[API REST du service de workflow](https://docs.microsoft.com/rest/api/logic/workflows)
  
Tous les déclencheurs contiennent ces éléments de niveau supérieur :  
  
```json
"<myTriggerName>": {
    "type": "<triggerType>",
    "inputs": { <callSettings> },
    "recurrence": {  
        "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
        "interval": "<recurrence-interval-based-on-frequency>"
    },
    "conditions": [ <array-with-required-conditions> ],
    "splitOn": "<property-used-for-creating-runs>",
    "operationOptions": "<options-for-operations-on-the-trigger>"
}
```

## <a name="trigger-types-and-inputs"></a>Types et entrées relatifs aux déclencheurs  

Chaque type de déclencheur a une interface différente et des *entrées* différentes qui définissent son comportement. 

| Type de déclencheur | DESCRIPTION | 
| ------------ | ----------- | 
| **Périodicité** | Se déclenche selon une planification définie. Vous pouvez définir une date et une heure ultérieures pour déclencher ce déclencheur. Selon la fréquence, vous pouvez également spécifier des heures et des jours d’exécution du workflow. | 
| **Requête**  | Transforme votre application logique en un point de terminaison que vous pouvez appeler, également connu en tant que déclencheur « manuel ». | 
| **HTTP** | Vérifie ou *interroge* un point de terminaison web HTTP. Le point de terminaison HTTP doit être conforme à un contrat de déclenchement spécifique, soit en utilisant un modèle asynchrone « 202 », soit en renvoyant un tableau. | 
| **ApiConnection** | Interroge comme un déclencheur HTTP, mais utilise des [API gérées par Microsoft](../connectors/apis-list.md). | 
| **HTTPWebhook** | Transforme votre application logique en un point de terminaison pouvant être appelé, comme le déclencheur de **requête**, mais appelle une URL spécifiée pour l’inscription et la désinscription. |
| **ApiConnectionWebhook** | Fonctionne comme le déclencheur **HTTPWebhook**, mais utilise des API gérées par Microsoft. | 
||| 

Pour plus d’informations, consultez [Langage de définition de flux de travail](../logic-apps/logic-apps-workflow-definition-language.md). 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>Déclencheur recurrence  

Ce déclencheur s’exécute en fonction de la périodicité et de la planification que vous spécifiez et offre un moyen simple d’exécuter régulièrement un workflow. 

Voici un exemple de déclencheur de récurrence de base qui s’exécute quotidiennement :

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

Vous pouvez également planifier une date et une heure de début pour déclencher le déclencheur. Par exemple, pour démarrer un rapport hebdomadaire tous les lundis, vous pouvez planifier le démarrage de l’application logique un lundi spécifique, comme dans cet exemple : 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime": "2017-09-18T00:00:00Z"
    }
}
```

Voici la définition de ce déclencheur :

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": <recurrence-interval-based-on-frequency>,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ <one-or-more-hour-marks> ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ <one-or-more-minute-marks> ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
        "timeZone": "<specify-time-zone>"
    }
}
```

| Nom de l'élément | Obligatoire | type | DESCRIPTION | 
| ------------ | -------- | ---- | ----------- | 
| frequency | OUI | Chaîne | L’unité de temps pour la fréquence à laquelle le déclencheur se déclenche. Utilisez une seule de ces valeurs : « second » (seconde), « minute », « hour » (heure), « day » (jour), « week » (semaine) ou « month » (mois). | 
| interval | OUI | Entier  | Nombre entier positif qui décrit la fréquence à laquelle le flux de travail s’exécute en fonction de la fréquence. <p>Voici les intervalles minimum et maximum : <p>- Mois : 1-16 mois </br>- Jour : 1-500 jours </br>- Heure : 1-12 000 heures </br>- Minute : 1-72 000 minutes </br>- Seconde : 1-9 999 999 secondes<p>Par exemple, si l’intervalle est défini sur 6 et la fréquence sur « mois », la périodicité est alors tous les 6 mois. | 
| timeZone | Non  | Chaîne | S’applique uniquement quand vous spécifiez une heure de début, car ce déclencheur n’accepte pas le [décalage UTC](https://en.wikipedia.org/wiki/UTC_offset). Spécifiez le fuseau horaire à appliquer. | 
| startTime | Non  | Chaîne | Spécifiez la date et l’heure de début au format suivant : <p>AAAA-MM-JJThh:mm:ss si vous spécifiez un fuseau horaire <p>-ou- <p>AAAA-MM-JJThh:mm:ssZ si vous ne spécifiez pas de fuseau horaire <p>Par exemple, si vous choisissez le 18 septembre 2017 à 14h, alors spécifiez « 2017-09-18T14:00:00 » et spécifiez un fuseau horaire tel que « Pacific Standard Time » (Heure standard du Pacifique). Ou, spécifiez « 2017-09-18T14:00:00Z » sans fuseau horaire. <p>**Remarque :** cette heure de début doit être conforme à la [spécification date/heure ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) au [format date/heure UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mais sans [décalage UTC](https://en.wikipedia.org/wiki/UTC_offset). Si vous ne spécifiez pas de fuseau horaire, vous devez ajouter la lettre « Z » à la fin, sans espace. Ce « Z » fait référence au [temps nautique](https://en.wikipedia.org/wiki/Nautical_time) équivalent. <p>Pour les planifications simples, l’heure de début est la première occurrence, tandis que pour les planifications complexes, le déclencheur ne se déclenche pas avant l’heure de début. Pour plus d’informations sur les dates et heures de début, consultez [Create and schedule regularly running tasks](../connectors/connectors-native-recurrence.md) (Créer et planifier des tâches à exécution régulière). | 
| weekDays | Non  | Chaîne ou tableau de chaînes | Si vous spécifiez « Semaine » pour `frequency`, vous pouvez spécifier un ou plusieurs jours, séparés par des virgules, pour exécuter le workflow : « Lundi », « Mardi », « Mercredi », « Jeudi », « Vendredi », « Samedi » et « Dimanche » | 
| hours | Non  | Entier ou tableau d’entiers | Si vous spécifiez « Jour » ou « Semaine » pour `frequency`, vous pouvez spécifier un ou plusieurs entiers compris entre 0 et 23, séparés par des virgules, pour les heures de la journée durant lesquelles exécuter le workflow. <p>Par exemple, si vous spécifiez « 10 », « 12 » et « 14 », vous obtenez 10h00, 12h00 et 14h00 comme marques horaires. | 
| minutes | Non  | Entier ou tableau d’entiers | Si vous spécifiez « Jour » ou « Semaine » pour `frequency`, vous pouvez spécifier un ou plusieurs entiers compris entre 0 et 59, séparés par des virgules, pour les minutes de l’heure durant lesquelles exécuter le workflow. <p>Par exemple, vous pouvez spécifier « 30 » comme marque de minute et à l’aide de l’exemple précédent des heures de la journée, vous obtenez 10h30, 12h30 et 14h30. | 
||||| 

Par exemple, ce déclencheur de périodicité spécifie que votre application logique s’exécute toutes les semaines chaque lundi à 10h30, 12h30 et 14h30 heure standard du Pacifique, et il ne commence pas avant le 9 septembre 2017 à 14h :

``` json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": 1,
        "schedule": {
            "hours": [
                10,
                12,
                14
            ],
            "minutes": [
                30
            ],
            "weekDays": [
                "Monday"
            ]
        },
       "startTime": "2017-09-07T14:00:00",
       "timeZone": "Pacific Standard Time"
    }
}
```

Pour plus d’informations et pour obtenir des exemples sur la périodicité et l’heure de début pour ce déclencheur, consultez [Create and schedule regularly running tasks](../connectors/connectors-native-recurrence.md) (Créer et planifier des tâches à exécution régulière).

## <a name="request-trigger"></a>Déclencheur de requête

Ce déclencheur joue un rôle de point de terminaison que vous pouvez utiliser pour appeler votre application logique via une requête HTTP. Un déclencheur request se présente comme dans cet exemple :  
  
```json
"myRequestTrigger": {
    "type": "Request",
    "kind": "Http",
    "inputs": {
        "schema": {
            "type": "Object",
            "properties": {
                "myInputProperty1": { "type" : "string" },
                "myInputProperty2": { "type" : "number" }
            },
            "required": [ "myInputProperty1" ]
        }
    }
} 
```

Ce déclencheur dispose d’une propriété facultative appelée `schema` :
  
| Nom de l'élément | Obligatoire | type | DESCRIPTION |
| ------------ | -------- | ---- | ----------- |
| schema | Non  | Object | Schéma JSON qui valide la requête entrante. Utile pour aider les étapes de workflow suivantes à déterminer les propriétés auxquelles faire référence. | 
||||| 

Pour appeler ce déclencheur en tant que point de terminaison, vous devez appeler l’API `listCallbackUrl`. Consultez [API REST du service de workflow](https://docs.microsoft.com/rest/api/logic/workflows).

## <a name="http-trigger"></a>Déclencheur HTTP  

Le déclencheur interroge un point de terminaison spécifique et vérifie la réponse pour déterminer si le workflow doit être exécuté. Ici, l’objet `inputs` prend ces paramètres requis pour la construction d’un appel HTTP : 

| Nom de l'élément | Obligatoire | type | DESCRIPTION | 
| ------------ | -------- | ---- | ----------- | 
| method | OUI | Chaîne | Utilise l’une de ces méthodes HTTP : « GET », « POST », « PUT », « DELETE », « PATCH » ou « HEAD » | 
| URI | OUI| Chaîne | Point de terminaison HTTP ou HTTPs vérifié par le déclencheur. Taille de chaîne maximale : 2 Ko | 
| queries | Non  | Object | Représente les paramètres de requête que vous souhaitez inclure dans l’URL. <p>Par exemple, `"queries": { "api-version": "2015-02-01" }` ajoute `?api-version=2015-02-01` à l’URL. | 
| headers | Non  | Object | Représente chaque en-tête envoyé dans la requête. <p>Par exemple, pour définir la langue et le type d’une requête : <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Non  | Object | Représente la charge utile envoyée au point de terminaison. | 
| retryPolicy | Non  | Object | Utilisez cet objet pour personnaliser le comportement de nouvelle tentative pour les erreurs 4xx ou 5xx. Pour plus d’informations, consultez [Stratégies de relance](../logic-apps/logic-apps-exception-handling.md). | 
| Authentification | Non  | Object | Représente la méthode à utiliser pour authentifier la requête. Pour plus d’informations, consultez [Authentification sortante de Scheduler](../scheduler/scheduler-outbound-authentication.md). <p>En plus de Scheduler, une autre propriété est prise en charge : `authority`. Par défaut, cette valeur est définie sur `https://login.windows.net` lorsqu’aucune valeur n’est spécifiée, mais vous pouvez utiliser une autre valeur comme `https://login.windows\-ppe.net`. | 
||||| 

Une *stratégie de nouvelle tentative* s’applique aux échecs temporaires, caractérisés par les codes d’état HTTP 408, 429 et 5xx, en plus de toutes les exceptions de connectivité. Vous pouvez définir cette stratégie avec l’objet `retryPolicy` comme indiqué ici :
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Le déclencheur HTTP nécessite que l’API HTTP se conforme à un modèle spécifique pour fonctionner correctement avec votre application logique. Le déclencheur reconnaît ces propriétés :  
  
| response | Obligatoire | DESCRIPTION | 
| -------- | -------- | ----------- |  
| Code d’état | OUI | Le code d’état 200 (« OK ») entraîne une exécution. Les autres codes d’état ne déclenchent pas d’exécution. | 
| En-tête Retry-after | Non  | Nombre de secondes au bout duquel l’application logique interroge à nouveau le point de terminaison. | 
| En-tête Location | Non  | URL à appeler lors du prochain intervalle d’interrogation. Si aucune valeur n’est spécifiée, l’URL d’origine est utilisée. | 
|||| 

Voici quelques exemples de comportements pour les différents types de requêtes :
  
| Response code | Retry after (Réessayer après) | Comportement | 
| ------------- | ----------- | -------- | 
| 200 | {aucune} | Exécutez le workflow et vérifiez de nouveau pour obtenir plus de données après la périodicité définie. | 
| 200 | 10 secondes | Exécutez le workflow et vérifiez de nouveau pour obtenir plus de données après 10 secondes. |  
| 202 | 60 secondes | Ne pas déclencher le workflow. La prochaine tentative se produit dans une minute conformément à la périodicité définie. Si la périodicité définie est inférieure à une minute, l’en-tête retry-after est prioritaire. Dans le cas contraire, la périodicité définie est utilisée. | 
| 400 | {aucune} | Requête incorrecte, ne pas exécuter le workflow. Si aucune valeur `retryPolicy` n’est définie, la stratégie par défaut est utilisée. Une fois que le nombre de nouvelles tentatives a été atteint, le déclencheur vérifie de nouveau pour obtenir plus de données après la périodicité définie. | 
| 500 | {aucune}| Erreur de serveur, ne pas exécuter le workflow. Si aucune valeur `retryPolicy` n’est définie, la stratégie par défaut est utilisée. Une fois que le nombre de nouvelles tentatives a été atteint, le déclencheur vérifie de nouveau pour obtenir plus de données après la périodicité définie. | 
|||| 

Voici les sorties de déclencheur HTTP : 
  
| Nom de l'élément | type | DESCRIPTION |
| ------------ | ---- | ----------- |
| headers | Object | En-têtes de la réponse HTTP | 
| body | Object | Corps de la réponse HTTP | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>Déclencheur APIConnection  

Dans les fonctionnalités de base, ce déclencheur fonctionne comme le déclencheur HTTP. Cependant, les paramètres d’identification de l’action sont différents. Voici un exemple :    
  
```json
"myDailyReportTrigger": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            }
        },
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "myCategory"
    }
}
```

| Nom de l'élément | Obligatoire | type | DESCRIPTION | 
| ------------ | -------- | ---- | ----------- | 
| host | OUI | Object | Passerelle hébergée et ID de l’application d’API | 
| method | OUI | Chaîne | Utilise l’une de ces méthodes HTTP : « GET », « POST », « PUT », « DELETE », « PATCH » ou « HEAD » | 
| queries | Non  | Object | Représente les paramètres de requête que vous souhaitez inclure dans l’URL. <p>Par exemple, `"queries": { "api-version": "2015-02-01" }` ajoute `?api-version=2015-02-01` à l’URL. | 
| headers | Non  | Object | Représente chaque en-tête envoyé dans la requête. <p>Par exemple, pour définir la langue et le type d’une requête : <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Non  | Object | Représente la charge utile envoyée au point de terminaison. | 
| retryPolicy | Non  | Object | Utilisez cet objet pour personnaliser le comportement de nouvelle tentative pour les erreurs 4xx ou 5xx. Pour plus d’informations, consultez [Stratégies de relance](../logic-apps/logic-apps-exception-handling.md). | 
| Authentification | Non  | Object | Représente la méthode à utiliser pour authentifier la requête. Pour plus d’informations, consultez [Authentification sortante de Scheduler](../scheduler/scheduler-outbound-authentication.md). | 
||||| 

Pour l’objet `host`, voici les propriétés :  
  
| Nom de l'élément | Obligatoire | DESCRIPTION | 
| ------------ | -------- | ----------- | 
| api runtimeUrl | OUI | Point de terminaison de l’API gérée | 
| connection name |  | Nom de la connexion API gérée utilisée par le workflow. Doit faire référence à un paramètre nommé `$connection`. |
|||| 

Une *stratégie de nouvelle tentative* s’applique aux échecs temporaires, caractérisés par les codes d’état HTTP 408, 429 et 5xx, en plus de toutes les exceptions de connectivité. Vous pouvez définir cette stratégie avec l’objet `retryPolicy` comme indiqué ici :
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Voici les sorties d’un déclencheur APIConnection :
  
| Nom de l'élément | type | DESCRIPTION |
| ------------ | ---- | ----------- |
| headers | Object | En-têtes de la réponse HTTP | 
| body | Object | Corps de la réponse HTTP | 
|||| 

Apprenez-en davantage sur la [tarification des déclencheurs de connexion d’API](../logic-apps/logic-apps-pricing.md#triggers).

## <a name="httpwebhook-trigger"></a>Déclencheur HTTPWebhook  

Ce déclencheur fournit un point de terminaison de la même manière que le déclencheur `Request`, mais le déclencheur HTTPWebhook appelle également une URL spécifique à des fins d’inscription et de désinscription. Un déclencheur HTTPWebhook peut se présenter comme suit :

```json
"myAppsSpotTrigger": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": {},
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {}
        }
    },
    "conditions": []
}
```

La plupart de ces sections sont facultatives, et le comportement du déclencheur HTTPWebhook varie selon les sections que vous renseignez ou omettez. Voici les propriétés du déclencheur HTTPWebhook :
  
| Nom de l'élément | Obligatoire | DESCRIPTION | 
| ------------ | -------- | ----------- |  
| subscribe | Non  | Spécifie la requête sortante à appeler lorsque le déclencheur est créé et effectue l’inscription initiale. | 
| unsubscribe | Non  | Spécifie la requête sortante à appeler lorsque le déclencheur est supprimé. | 
|||| 

Vous pouvez spécifier les limites d’un déclencheur Webhook de la même manière que les [limites asynchrones HTTP](#asynchronous-limits). Voici plus d’informations sur les actions `subscribe` et `unsubscribe` :

* `subscribe` est appelée afin que le déclencheur puisse commencer à écouter les événements. Cet appel sortant démarre avec les mêmes paramètres que les actions HTTP standard. Cet appel est effectué lorsque le workflow change de quelque façon, par exemple lorsque les informations d’identification sont remplacées ou les paramètres d’entrée du déclencheur modifiés. 
  
  Pour prendre cet appel en charge, la fonction `@listCallbackUrl()` retourne une URL unique pour ce déclencheur spécifique dans le workflow. Cette URL représente l’identificateur unique des points de terminaison qui utilisent l’API REST du service.
  
* `unsubscribe` est automatiquement appelée lorsqu’une opération rend ce déclencheur non valide, notamment dans les opérations suivantes :

  * Suppression ou désactivation du déclencheur. 
  * Suppression ou désactivation du workflow. 
  * Suppression ou désactivation de l’inscription. 
  
  Les paramètres de cette fonction sont les mêmes que ceux du déclencheur HTTP.

Voici les sorties du déclencheur HTTPWebhook et le contenu de la requête entrante :
  
| Nom de l'élément | type | DESCRIPTION |
| ------------ | ---- | ----------- |
| headers | Object | En-têtes de la réponse HTTP | 
| body | Object | Corps de la réponse HTTP | 
|||| 

## <a name="triggers-conditions"></a>Déclencheurs : conditions

Pour n’importe quel déclencheur, vous pouvez utiliser une ou plusieurs conditions pour déterminer si le workflow doit s’exécuter ou non. Dans cet exemple, le rapport se déclenche uniquement lorsque le paramètre `sendReports` du workflow est défini sur true. 

```json
"myDailyReportTrigger": {
    "type": "Recurrence",
    "conditions": [ 
        {
            "expression": "@parameters('sendReports')"
        } 
    ],
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

Enfin, les conditions peuvent faire référence au code d’état du déclencheur. Par exemple, vous pouvez lancer un workflow uniquement lorsque votre site web renvoie un code d’état 500 :
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  

> [!NOTE]
> Par défaut, un déclencheur est activé uniquement lors de la réception une réponse « 200 OK ». Lorsqu’une expression fait référence au code d’état du déclencheur de quelque façon, le comportement par défaut du déclencheur est remplacé. Par conséquent, si vous souhaitez que le déclencheur s’active avec plusieurs codes d’état (par exemple, le code d’état 200 et code d’état 201), vous devez inclure cette instruction en tant que condition : 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-process-an-array-with-multiple-runs"></a>Déclencheurs : traiter un tableau avec plusieurs exécutions

Si votre déclencheur renvoie un tableau à traiter pour votre application logique, il arrive qu’une boucle « for each » prenne trop de temps pour traiter chaque élément du tableau. Au lieu de cela, vous pouvez utiliser la propriété **SplitOn** dans votre déclencheur pour *décomposer* le tableau. 

La décomposition sépare les éléments de tableau et démarre une nouvelle instance d’application logique qui s’exécute pour chaque élément du tableau. Par exemple, cette approche est utile lorsque vous souhaitez interroger un point de terminaison qui peut renvoyer plusieurs nouveaux éléments entre les intervalles d’interrogation.
Pour connaître le nombre maximal d’éléments de tableau que **SplitOn** peut traiter en une seule exécution d’application logique, consultez [Limites et configurations](../logic-apps/logic-apps-limits-and-config.md). 

> [!NOTE]
> Vous pouvez ajouter **SplitOn** uniquement aux déclencheurs en définissant ou en remplaçant manuellement en mode code la définition JSON de votre application logique. Vous ne pouvez pas utiliser **SplitOn** lorsque vous souhaitez implémenter un modèle de réponse synchrone. Les workflows qui utilisent **SplitOn** et incluent une réponse action s’exécutent de façon asynchrone et envoient immédiatement une réponse `202 ACCEPTED`.

Si le fichier Swagger de votre déclencheur décrit une charge utile sous forme de tableau, la propriété **SplitOn** est automatiquement ajoutée à votre déclencheur. Sinon, ajoutez cette propriété à l’intérieur de la charge utile de la réponse qui contient le tableau à décomposer. 

Par exemple, supposons que vous ayez une API qui renvoie la réponse suivante : 
  
```json
{
    "Status": "Succeeded",
    "Rows": [ 
        { 
            "id": 938109380,
            "name": "customer-name-one"
        },
        {
            "id": 938109381,
            "name": "customer-name-two"
        }
    ]
}
```
  
Comme votre application logique a uniquement besoin du contenu de `Rows`, vous pouvez créer votre déclencheur comme dans cet exemple.

``` json
"myDebatchTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "Second",
        "interval": "1"
    },
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Si vous utilisez la commande `SplitOn`, vous ne pouvez pas obtenir les propriétés qui ne se trouvent pas dans le tableau. Pour cet exemple, vous ne pouvez donc pas obtenir la propriété `status` dans la réponse renvoyée par l’API.
> 
> Dans cet exemple, nous utilisons l’opérateur `?` afin d’éviter un échec si la propriété `Rows` n’existe pas.

Votre définition de workflow peut désormais utiliser `@triggerBody().name` pour obtenir `customer-name-one` à partir de la première exécution et `customer-name-two` à partir de la deuxième exécution. Par conséquent, les sorties du déclencheur se présentent comme dans les exemples suivants :

```json
{
    "body": {
        "id": 938109380,
        "name": "customer-name-one"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "customer-name-two"
    }
}
```
  
## <a name="triggers-fire-only-after-all-active-runs-finish"></a>Déclencheurs : activer uniquement au terme de toutes les exécutions actives

Vous pouvez configurer les déclencheurs de périodicité afin qu’ils se déclenchent uniquement lorsque toutes les exécutions actives sont terminées. Pour configurer ce paramètre, définissez la propriété `operationOptions` sur `singleInstance` :

```json
"myTrigger": {
    "type": "Http",
    "inputs": { },
    "recurrence": { },
    "operationOptions": "singleInstance"
}
```

Si une périodicité planifiée a lieu alors qu’une exécution d’instance du workflow est encore en cours, le déclencheur attend l’intervalle de périodicité planifié suivant pour effectuer une nouvelle vérification.

## <a name="actions-overview"></a>Vue d’ensemble des actions

Il existe de nombreux types d’actions, chacune présentant un comportement unique. Chaque type d’action présente des entrées différentes qui définissent le comportement d’une action. Les actions de collection peuvent contenir de nombreuses autres actions. 

### <a name="standard-actions"></a>Actions standard  

| Type d’action | DESCRIPTION | 
| ----------- | ----------- | 
| **HTTP** | Appelle un point de terminaison web HTTP. | 
| **ApiConnection**  | Fonctionne comme l’action HTTP, mais utilise des [API gérées par Microsoft](https://docs.microsoft.com/azure/connectors/apis-list). | 
| **ApiConnectionWebhook** | Fonctionne comme HTTPWebhook, mais utilise des API gérées par Microsoft. | 
| **Réponse** | Définit la réponse pour un appel entrant. | 
| **Composer** | Construit un objet arbitraire à partir des entrées de l’action. | 
| **Fonction** | Représente une fonction Azure. | 
| **Wait** | Attend pendant une durée définie ou jusqu’à une heure spécifique. | 
| **Workflow** | Représente un workflow imbriqué. | 
| **Composer** | Construit un objet arbitraire à partir des entrées de l’action. | 
| **Requête** | Filtre un tableau en fonction d’une condition. | 
| **Sélection** | Projette chaque élément d’un tableau dans une nouvelle valeur. Par exemple, vous pouvez convertir un tableau de nombres en tableau d’objets. | 
| **Table** | Convertit un tableau d’éléments en table CSV ou HTML. | 
| **Arrêter** | Arrête l’exécution d’un workflow. | 
| **Wait** | Attend pendant une durée définie ou jusqu’à une heure spécifique. | 
| **Workflow** | Représente un workflow imbriqué. | 
||| 

### <a name="collection-actions"></a>Actions de collection

| Type d’action | DESCRIPTION | 
| ----------- | ----------- | 
| **If** | Évalue une expression et en fonction du résultat, exécute la branche correspondante. | 
| **Switch** | Effectue différentes actions en fonction des valeurs spécifiques d’un objet. | 
| **ForEach** | Cette action en boucle effectue une itération dans un tableau et exécute des actions internes pour chaque élément. | 
| **Until** | Cette action en boucle exécute des actions internes jusqu’à ce qu’une condition soit vérifiée. | 
| **Portée** | À utiliser pour le regroupement logique des autres actions. | 
|||  

## <a name="http-action"></a>Action HTTP  

Une action HHTP appelle un point de terminaison spécifique et vérifie la réponse pour déterminer si le workflow doit être exécuté. Par exemple : 
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

Ici, l’objet `inputs` prend ces paramètres requis pour la construction d’un appel HTTP : 

| Nom de l'élément | Obligatoire | type | DESCRIPTION | 
| ------------ | -------- | ---- | ----------- | 
| method | OUI | Chaîne | Utilise l’une de ces méthodes HTTP : « GET », « POST », « PUT », « DELETE », « PATCH » ou « HEAD » | 
| URI | OUI| Chaîne | Point de terminaison HTTP ou HTTPs vérifié par le déclencheur. Taille de chaîne maximale : 2 Ko | 
| queries | Non  | Object | Représente les paramètres de requête que vous souhaitez inclure dans l’URL. <p>Par exemple, `"queries": { "api-version": "2015-02-01" }` ajoute `?api-version=2015-02-01` à l’URL. | 
| headers | Non  | Object | Représente chaque en-tête envoyé dans la requête. <p>Par exemple, pour définir la langue et le type d’une requête : <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Non  | Object | Représente la charge utile envoyée au point de terminaison. | 
| retryPolicy | Non  | Object | Utilisez cet objet pour personnaliser le comportement de nouvelle tentative pour les erreurs 4xx ou 5xx. Pour plus d’informations, consultez [Stratégies de relance](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Non  | Chaîne | Définit l’ensemble de comportements spéciaux à substituer. | 
| Authentification | Non  | Object | Représente la méthode à utiliser pour authentifier la requête. Pour plus d’informations, consultez [Authentification sortante de Scheduler](../scheduler/scheduler-outbound-authentication.md). <p>En plus de Scheduler, une autre propriété est prise en charge : `authority`. Par défaut, cette valeur est définie sur `https://login.windows.net` lorsqu’aucune valeur n’est spécifiée, mais vous pouvez utiliser une autre valeur comme `https://login.windows\-ppe.net`. | 
||||| 

Les actions HTTP et ApiConnection prennent en charge les *stratégies de nouvelle tentative*. Une stratégie de nouvelle tentative s’applique aux échecs temporaires, caractérisés par les codes d’état HTTP 408, 429 et 5xx, en plus de toutes les exceptions de connectivité. Vous pouvez définir cette stratégie avec l’objet `retryPolicy` comme indiqué ici :
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Cet exemple d’action HTTP tente à nouveau de récupérer les dernières actualités deux fois en cas d’échec temporaire, pour un total de 3 exécutions, avec un délai de 30 secondes entre chaque tentative :
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy": {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

L’intervalle avant nouvelle tentative est spécifié au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). L’intervalle présente une valeur par défaut (et minimale) de 20 secondes, tandis que la valeur maximale est d’une heure. Le nombre de nouvelles tentatives par défaut (et maximal) est de 4. Si vous ne spécifiez pas une définition de stratégie de nouvelle tentative, une stratégie `fixed` est utilisée avec les valeurs par défaut d’intervalle et de nombre de nouvelles tentatives. Pour désactiver la stratégie de nouvelle tentative, définissez son type sur `None`.

### <a name="asynchronous-patterns"></a>Modèles asynchrones

Par défaut, toutes les actions basées sur HTTP prennent en charge le modèle d’opération asynchrone standard. Par conséquent, si le serveur distant indique que la requête est acceptée pour traitement avec une réponse « 202 ACCEPTED », le moteur Logic Apps continue à interroger l’URL spécifiée dans l’en-tête Location de la réponse jusqu’à atteindre un état terminal c’est-à-dire une réponse autre que 202.
  
Pour désactiver le comportement asynchrone décrit précédemment, définissez `operationOptions` et `DisableAsyncPattern` dans les entrées de l’action. Dans ce cas, la sortie de l’action est basée sur la réponse 202 initiale du serveur. Par exemple : 
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>Limites asynchrones

Vous pouvez limiter la durée d’un modèle asynchrone à un intervalle de temps spécifique. Si l’intervalle de temps s’écoule sans qu’un état terminal ait été atteint, l’état de l’action sera marqué comme `Cancelled` avec un code `ActionTimedOut`. Le délai d’expiration de la limite est spécifié au format ISO 8601. Cet exemple montre comment vous pouvez spécifier des limites :


``` json
"<action-name>": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>Action APIConnection

Cette action référence un connecteur géré par Microsoft, ce qui requiert une référence à une connexion valide, ainsi que des informations sur l’API et les paramètres. Voici un exemple d’action APIConnection :

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| Nom de l'élément | Obligatoire | type | DESCRIPTION | 
| ------------ | -------- | ---- | ----------- | 
| host | OUI | Object | Représente les informations sur le connecteur, telles que `runtimeUrl` et la référence à l’objet de connexion. | 
| method | OUI | Chaîne | Utilise l’une de ces méthodes HTTP : « GET », « POST », « PUT », « DELETE », « PATCH » ou « HEAD » | 
| chemin d’accès | OUI | Chaîne | Chemin de l’opération d’API | 
| queries | Non  | Object | Représente les paramètres de requête que vous souhaitez inclure dans l’URL. <p>Par exemple, `"queries": { "api-version": "2015-02-01" }` ajoute `?api-version=2015-02-01` à l’URL. | 
| headers | Non  | Object | Représente chaque en-tête envoyé dans la requête. <p>Par exemple, pour définir la langue et le type d’une requête : <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Non  | Object | Représente la charge utile envoyée au point de terminaison. | 
| retryPolicy | Non  | Object | Utilisez cet objet pour personnaliser le comportement de nouvelle tentative pour les erreurs 4xx ou 5xx. Pour plus d’informations, consultez [Stratégies de relance](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Non  | Chaîne | Définit l’ensemble de comportements spéciaux à substituer. | 
| Authentification | Non  | Object | Représente la méthode à utiliser pour authentifier la requête. Pour plus d’informations, consultez [Authentification sortante de Scheduler](../scheduler/scheduler-outbound-authentication.md). |
||||| 

Une stratégie de nouvelle tentative s’applique aux échecs temporaires, caractérisés par les codes d’état HTTP 408, 429 et 5xx, en plus de toutes les exceptions de connectivité. Vous pouvez définir cette stratégie avec l’objet `retryPolicy` comme indiqué ici :

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

## <a name="apiconnection-webhook-action"></a>Action APIConnection webhook

L’action APIConnectionWebhook fait référence à un connecteur géré par Microsoft. Cette action requiert une référence à une connexion valide, ainsi que des informations sur l’API et les paramètres. Vous pouvez spécifier les limites d’une action webhook de la même manière que les [limites asynchrones HTTP](#asynchronous-limits).

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

| Nom de l'élément | Obligatoire | type | DESCRIPTION | 
| ------------ | -------- | ---- | ----------- | 
| host | OUI | Object | Représente les informations sur le connecteur, telles que `runtimeUrl` et la référence à l’objet de connexion. | 
| chemin d’accès | OUI | Chaîne | Chemin de l’opération d’API | 
| queries | Non  | Object | Représente les paramètres de requête que vous souhaitez inclure dans l’URL. <p>Par exemple, `"queries": { "api-version": "2015-02-01" }` ajoute `?api-version=2015-02-01` à l’URL. | 
| headers | Non  | Object | Représente chaque en-tête envoyé dans la requête. <p>Par exemple, pour définir la langue et le type d’une requête : <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Non  | Object | Représente la charge utile envoyée au point de terminaison. | 
| retryPolicy | Non  | Object | Utilisez cet objet pour personnaliser le comportement de nouvelle tentative pour les erreurs 4xx ou 5xx. Pour plus d’informations, consultez [Stratégies de relance](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Non  | Chaîne | Définit l’ensemble de comportements spéciaux à substituer. | 
| Authentification | Non  | Object | Représente la méthode à utiliser pour authentifier la requête. Pour plus d’informations, consultez [Authentification sortante de Scheduler](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="response-action"></a>Action de réponse  

Cette action contient la charge utile de réponse entière d’une requête HTTP et inclut `statusCode`, `body` et `headers` :
  
```json
"myResponseAction": {
    "type": "Response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

L’action response présente des restrictions spéciales qui ne s’appliquent pas aux autres actions, en particulier :  
  
* Vous ne pouvez pas avoir des actions response dans des branches parallèles au sein de la définition d’une application logique, car la requête entrante requiert une réponse déterministe.
  
* Si le workflow atteint une action response après que la requête entrante a reçu une réponse, l’action response est considérée comme ayant échoué ou comme étant en conflit. Par conséquent, l’exécution de l’application logique est marquée comme `Failed`.
  
* Un workflow avec des actions response ne peut pas utiliser la commande `splitOn` dans la définition du déclencheur car l’appel crée plusieurs exécutions. Par conséquent, vérifiez dans ce cas quand l’opération de workflow est indiquée PUT et retournez une réponse de « requête incorrecte ».

## <a name="compose-action"></a>Action compose

Cette action vous permet de construire un objet arbitraire, et la sortie est le résultat de l’évaluation des entrées de l’action. 

> [!NOTE]
> Vous pouvez utiliser l’action `Compose` pour construire n’importe quelle sortie, notamment des objets, des tableaux et tout autre type pris en charge de façon native par les applications logiques, tel que XML et binaire.

Par exemple, vous pouvez utiliser l’action `Compose` pour fusionner les sorties de plusieurs actions :

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="function-action"></a>Action function

Cette action vous permet de représenter et d’appeler une [fonction Azure](../azure-functions/functions-overview.md), par exemple :

```json
"<my-Azure-Function-name>": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/sites/<your-Azure-function-app-name>/functions/<your-Azure-function-name>"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Nom de l'élément | Obligatoire | type | DESCRIPTION | 
| ------------ | -------- | ---- | ----------- |  
| function id | OUI | Chaîne | ID de ressource de la fonction Azure que vous souhaitez appeler. | 
| method | Non  | Chaîne | Méthode HTTP utilisée pour appeler la fonction. Si elle n’est pas spécifiée, « POST » est la méthode par défaut. | 
| queries | Non  | Object | Représente les paramètres de requête que vous souhaitez inclure dans l’URL. <p>Par exemple, `"queries": { "api-version": "2015-02-01" }` ajoute `?api-version=2015-02-01` à l’URL. | 
| headers | Non  | Object | Représente chaque en-tête envoyé dans la requête. <p>Par exemple, pour définir la langue et le type d’une requête : <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Non  | Object | Représente la charge utile envoyée au point de terminaison. | 
|||||

Lorsque vous enregistrez votre application logique, le moteur Logic Apps effectue des contrôles sur la fonction référencée :

* Vous devez avoir accès à la fonction.
* Seul un déclencheur HTTP standard ou un déclencheur Webhook JSON générique sont autorisés.
* La fonction ne doit pas avoir d’itinéraire défini.
* Seuls les niveaux d’autorisation « fonction » et « anonyme » sont autorisés.

> [!NOTE]
> Le moteur Logic Apps récupère et met en cache de l’URL du déclencheur, qui est utilisée lors de l’exécution. Par conséquent, si une opération invalide l’URL en cache, l’action échoue au runtime. Pour contourner ce problème, enregistrez de nouveau l’application logique pour que celle-ci puisse de nouveau récupérer et mettre en cache l’URL du déclencheur.

## <a name="select-action"></a>Action select

Cette action vous permet de projeter chaque élément d’un tableau dans une nouvelle valeur. Cet exemple convertit un tableau de nombres en tableau d’objets :

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| NOM | Obligatoire | type | DESCRIPTION | 
| ---- | -------- | ---- | ----------- | 
| from | OUI | Tableau | Tableau source |
| select | OUI | Quelconque | Projection appliquée à chaque élément du tableau source |
||||| 

La sortie de l’action `select` est un tableau qui a la même cardinalité que le tableau d’entrée. Chaque élément est transformé comme défini par la propriété `select`. Si l’entrée est un tableau vide, la sortie est également un tableau vide.

## <a name="terminate-action"></a>Action terminate

Cette action arrête une exécution de workflow : elle annule toutes les actions en cours d’exécution et ignore les actions restantes. L’action terminate n’a pas d’effet sur les actions terminées.

Par exemple, pour arrêter une exécution avec l’état `Failed`, procédez comme suit :

```json
"HandleUnexpectedResponse": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response",
        }
    }
}
```

| NOM | Obligatoire | type | DESCRIPTION | 
| ---- | -------- | ---- | ----------- | 
| runStatus | OUI | Chaîne | État de l’exécution cible, `Failed` ou `Cancelled` |
| runError | Non  | Object | Détails de l’erreur. Pris en charge uniquement lorsque `runStatus` a la valeur `Failed`. |
| runError code | Non  | Chaîne | Code d’erreur de l’exécution |
| runError message | Non  | Chaîne | Message d’erreur de l’exécution | 
||||| 

## <a name="query-action"></a>Action de requête

Cette action vous permet de filtrer un tableau en fonction d’une condition. 

> [!NOTE]
> Vous ne pouvez pas utiliser l’action Compose pour construire une sortie, y compris des objets, des tableaux et tout autre type pris en charge de façon native par les applications logiques, tel que XML et binaire.

Par exemple, pour sélectionner des nombres supérieurs à 2, procédez comme suit :

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

| NOM | Obligatoire | type | DESCRIPTION | 
| ---- | -------- | ---- | ----------- | 
| from | OUI | Tableau | Tableau source |
| where | OUI | Chaîne | Condition appliquée à chaque élément du tableau source. Si aucune valeur ne remplit la condition `where`, le résultat est un tableau vide. |
||||| 

La sortie de l’action `query` est un tableau qui contient les éléments du tableau d’entrée qui remplissent la condition.

## <a name="table-action"></a>Action table

Cette action vous permet de convertir un tableau en table CSV ou HTML. 

```json
"ConvertToTable": {
    "type": "Table",
    "inputs": {
        "from": "<source-array>",
        "format": "CSV | HTML"
    }
}
```

| NOM | Obligatoire | type | DESCRIPTION | 
| ---- | -------- | ---- | ----------- | 
| from | OUI | Tableau | Tableau source. Si la valeur de propriété `from` est un tableau vide, la sortie est un tableau vide. | 
| format | OUI | Chaîne | Format de table souhaité, CSV ou HTML. | 
| colonnes | Non  | Tableau | Les colonnes de table que vous souhaitez. À utiliser pour remplacer la forme du tableau par défaut. | 
| column header | Non  | Chaîne | En-tête de colonne | 
| column value | OUI | Chaîne | Valeur de colonne | 
||||| 

Supposons que vous définissiez une action de table comme dans l’exemple suivant :

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "HTML"
    }
}
```

Et que vous utilisiez ce tableau pour `@triggerBody()` :

```json
[ {"ID": 0, "Name": "apples"},{"ID": 1, "Name": "oranges"} ]
```

Voici la sortie correspondant à cet exemple :

<table><thead><tr><th>ID</th><th>NOM</th></tr></thead><tbody><tr><td>0</td><td>pommes</td></tr><tr><td>1</td><td>oranges</td></tr></tbody></table>

Afin de personnaliser ce tableau, vous pouvez spécifier explicitement les colonnes, par exemple :

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

Voici la sortie correspondant à cet exemple :

<table><thead><tr><th>ID produit</th><th>DESCRIPTION</th></tr></thead><tbody><tr><td>0</td><td>pommes fraîches</td></tr><tr><td>1</td><td>oranges fraîches</td></tr></tbody></table>

## <a name="wait-action"></a>Action wait  

Cette action interrompt l’exécution du workflow pendant l’intervalle spécifié. Cet exemple montre que le workflow attend 15 minutes :
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
Pour attendre jusqu’à un moment spécifique, vous pouvez utiliser cet exemple :
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> Vous pouvez spécifier la durée d’attente avec l’objet `interval` ou `until`, mais pas les deux.

| Nom de l'élément | Obligatoire | type | DESCRIPTION | 
| ------------ | -------- | ---- | ----------- | 
| until | Non  | Object | Durée d’attente basée sur un point dans le temps | 
| until timestamp | OUI | Chaîne | Point dans le temps au [format date/heure UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) où l’attente expire | 
| interval | Non  | Object | Durée d’attente basée sur l’unité et le nombre d’intervalles | 
| interval unit | OUI | Chaîne | Unité de temps. Utilisez une seule de ces valeurs : « second » (seconde), « minute », « hour » (heure), « day » (jour), « week » (semaine) ou « month » (mois). | 
| interval count | OUI | Entier  | Nombre entier positif représentant le nombre d’unités d’intervalle utilisées pour la durée d’attente | 
||||| 

## <a name="workflow-action"></a>Action workflow

Cette action vous permet d’imbriquer un workflow. Le moteur Logic Apps effectue une vérification d’accès sur le workflow enfant, ou plus précisément, le déclencheur. Vous devriez donc avoir accès au workflow enfant. Par exemple : 

```json
"<my-nested-workflow-action-name>": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/<my-subscription-ID>/resourceGroups/<my-resource-group-name>/providers/Microsoft.Logic/<my-nested-workflow-action-name>",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Nom de l'élément | Obligatoire | type | DESCRIPTION | 
| ------------ | -------- | ---- | ----------- |  
| host id | OUI | Chaîne| ID de ressource du workflow que vous souhaitez appeler | 
| host triggerName | OUI | Chaîne | Nom du déclencheur que vous souhaitez appeler | 
| queries | Non  | Object | Représente les paramètres de requête que vous souhaitez inclure dans l’URL. <p>Par exemple, `"queries": { "api-version": "2015-02-01" }` ajoute `?api-version=2015-02-01` à l’URL. | 
| headers | Non  | Object | Représente chaque en-tête envoyé dans la requête. <p>Par exemple, pour définir la langue et le type d’une requête : <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Non  | Object | Représente la charge utile envoyée au point de terminaison. | 
||||| 

Les sorties de cette action sont basées sur ce que vous avez défini pour l’action `Response` pour le workflow enfant. Si le workflow enfant ne définit pas une action `Response`, les sorties sont vides.

## <a name="collection-actions-overview"></a>Vue d’ensemble des actions de collection

Pour faciliter le contrôle de l’exécution de workflow, les actions de collection peuvent inclure d’autres actions. Vous pouvez faire référence aux actions d’une collection directement en dehors de la collection. Par exemple, si vous définissez une action `Http` dans une portée, `@body('http')` est toujours valide, n’importe où dans un workflow. De plus, les actions d’une collection peuvent uniquement être exécutées après les autres actions de la même collection.

## <a name="if-action"></a>Action If

En tant qu’instruction conditionnelle, cette action vous permet d’évaluer une condition et d’exécuter une branche spécifique si l’expression évaluée présente la valeur true. Si la condition est vérifiée (true), elle est marquée comme Réussite. Les actions qui se trouvent dans les objets `actions` ou `else` renvoient les valeurs suivantes :

* Réussite, lorsqu’elles sont exécutées et qu’elles réussissent
* Échec, lorsqu’elles sont exécutées et qu’elles échouent
* Ignoré, lorsque la branche respective ne s’exécute pas

En savoir plus sur les [instructions conditionnelles dans les applications logiques](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"<my-condition-name>": {
  "type": "If",
  "expression": "<condition>",
  "actions": {
    "if-true-run-this-action": {
      "type": <action-type>,
      "inputs": {},
      "runAfter": {}
    }
  },
  "else": {
    "actions": {
        "if-false-run-this-action": {
            "type": <action-type>,
            "inputs": {},
            "runAfter": {}
        }
    }
  },
  "runAfter": {}
}
```

| NOM | Obligatoire | type | DESCRIPTION | 
| ---- | -------- | ---- | ----------- | 
| actions | OUI | Object | Actions internes à exécuter quand `expression` prend la valeur `true` | 
| expression | OUI | Chaîne | Expression à évaluer. |
| else | Non  | Object | Actions internes à exécuter quand `expression` prend la valeur `false` |
||||| 

Par exemple : 

```json
"myCondition": {
    "type": "If",
    "actions": {
        "if-true-check-this-website": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://this-url"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if-false-check-this-other-website": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://this-other-url"
                },
                "runAfter": {}
            }
        }
    }
}
```  

### <a name="how-conditions-can-use-expressions-in-actions"></a>Utilisation des expressions dans les actions pour les conditions

Voici des exemples illustrant comment utiliser des expressions dans des conditions :
  
| Expression JSON | Résultat | 
| --------------- | ------ | 
| `"expression": "@parameters('hasSpecialAction')"` | Cette condition est remplie dès lors que l’expression évaluée présente la valeur true. Seules les expressions booléennes sont prises en charge. Pour convertir d’autres types en valeurs booléennes, utilisez les fonctions `empty` ou `equals`. | 
| `"expression": "@greater(actions('action1').output.value, parameters('threshold'))"` | Prend en charge les fonctions de comparaison. Pour cet exemple, l’action s’exécute uniquement lorsque la sortie de l’action 1 est supérieure au seuil. | 
| `"expression": "@or(greater(actions('action1').output.value, parameters('threshold')), less(actions('action1').output.value, 100))"` | Prend en charge les fonctions logiques pour la création d’expressions booléennes imbriquées. Pour cet exemple, l’action s’exécute lorsque la sortie de l’action 1 est supérieure au seuil ou inférieure à 100. | 
| `"expression": "@equals(length(actions('action1').outputs.errors), 0))"` | Vous pouvez utiliser les fonctions de tableau pour vérifier si un tableau contient des éléments. Pour cet exemple, l’action s’exécute lorsque le tableau des erreurs est vide. | 
| `"expression": "parameters('hasSpecialAction')"` | Cette expression provoque une erreur et n’est pas une condition valide. Les conditions doivent utiliser le symbole « @ ». | 
||| 

## <a name="switch-action"></a>Action Switch

En tant qu’instruction de basculement, cette action effectue différentes actions en fonction des valeurs spécifiques d’un objet,d’ une expression ou d’un jeton. Cette action évalue l’objet, l’expression ou le jeton, choisit le cas qui correspond au résultat et exécute les actions pour ce cas seulement. Si aucun cas correspond au résultat, l’action par défaut s’exécute. Lors de l’exécution de l’instruction de basculement; un seul cas doit correspondre au résultat. En savoir plus sur les [instructions de basculement dans les applications logiques](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"<my-switch-statement-name>": {
   "type": "Switch",
   "expression": "<evaluate-this-object-expression-token>",
   "cases": {
      "myCase1" : {
         "actions" : {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions" : {
           "myAction2": {}
         },
         "case": "<result2>"
      }
   },
   "default": {
      "actions": {
          "myDefaultAction": {}
      }
   },
   "runAfter": {}
}
```

| NOM | Obligatoire | type | DESCRIPTION | 
| ---- | -------- | ---- | ----------- | 
| expression | OUI | Chaîne | Objet, expression ou jeton à évaluer | 
| cas | OUI | Object | Contient les ensembles d’actions internes qui s’exécutent en fonction du résultat de l’expression. | 
| cas | OUI | Chaîne | Valeur de correspondance avec le résultat | 
| actions | OUI | Object | Actions internes qui s’exécutent pour le cas qui correspond au résultat de l’expression | 
| default | Non  | Object | Actions internes qui s’exécutent si aucun cas ne correspond au résultat | 
||||| 

Par exemple : 

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions" : {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions" : {
           "Send_an_email_2": {...}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

## <a name="foreach-action"></a>Action Foreach

Cette action en boucle effectue une itération dans un tableau et exécute des actions internes pour chaque élément. Par défaut, la boucle Foreach s’exécute en parallèle. Pour connaître le nombre maximal de cycles parallèles que peut exécuter chaque boucle « Foreach », consultez [Limites et configurations](../logic-apps/logic-apps-limits-and-config.md). Pour exécuter chaque cycle de manière séquentielle, définissez le paramètre `operationOptions` sur `Sequential`. En savoir plus sur les [boucles Foreach dans les applications logiques](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"<my-forEach-loop-name>": {
    "type": "Foreach",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    },
    "foreach": "<array>",
    "runAfter": {}
}
```

| NOM | Obligatoire | type | DESCRIPTION | 
| ---- | -------- | ---- | ----------- | 
| actions | OUI | Object | Actions internes à exécuter dans la boucle | 
| foreach | OUI | Chaîne | Tableau dans lequel effectuer l’itération | 
| operationOptions | Non  | Chaîne | Spécifie les options de l’opération pour personnaliser le comportement. Prend actuellement en charge uniquement `Sequential` pour les itérations exécutées de manière séquentielle où le comportement par défaut est parallèle. |
||||| 

Par exemple : 

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "foreach": "@body('email_filter')",
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Action until

Cette action en boucle exécute des actions internes jusqu’à ce qu’une condition soit vérifiée (true). En savoir plus sur les [boucles Until dans les applications logiques](../logic-apps/logic-apps-control-flow-loops.md#until-loop).

```json
 "<my-Until-loop-name>": {
    "type": "Until",
    "actions": {
        "myActionName": {
            "type": "<action-type>",
            "inputs": {},
            "runAfter": {}
        }
    },
    "expression": "<myCondition>",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| NOM | Obligatoire | type | DESCRIPTION | 
| ---- | -------- | ---- | ----------- | 
| actions | OUI | Object | Actions internes à exécuter dans la boucle | 
| expression | OUI | Chaîne | Expression à évaluer après chaque itération. | 
| limit | OUI | Object | Les limites de la boucle. Doit définir au moins une limite. | 
| count | Non  | Entier  | La limite du nombre d’itérations à effectuer | 
| timeout | Non  | Chaîne | La limite de délai d’expiration au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) qui spécifie la durée pendant laquelle la boucle doit s’exécuter |
||||| 

Par exemple : 

```json
 "runUntilSucceededAction": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 100,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

## <a name="scope-action"></a>Action scope

Cette action vous permet de logiquement regrouper les actions d’un workflow. Une fois toutes les actions d’une portée ont été exécutées, la portée récupère également son propre état. En savoir plus sur les [portées](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"<my-scope-action-name>": {
    "type": "Scope",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    }
}
```

| NOM | Obligatoire | type | DESCRIPTION | 
| ---- | -------- | ---- | ----------- |  
| actions | OUI | Object | Actions internes à exécuter dans l’étendue |
||||| 

## <a name="next-steps"></a>étapes suivantes

* En savoir plus sur le [langage de définition de workflow](../logic-apps/logic-apps-workflow-definition-language.md)
* En savoir plus sur l’[API REST de workflow](https://docs.microsoft.com/rest/api/logic/workflows)