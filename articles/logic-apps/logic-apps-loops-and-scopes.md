---
title: "Créer des boucles et des étendues, ou décomposer des données de workflow - Azure Logic Apps | Microsoft Docs"
description: "Créez des boucles pour itérer au sein de données, regroupez les actions au sein d’étendues ou décomposez les données pour démarrer plusieurs workflows dans Azure Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: a17de187f67c075147ea8ff7f69434014eea3fdb
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Boucles, étendues et décomposition dans Logic Apps
  
Logic Apps permet d’utiliser les tableaux, collections, lots et boucles au sein d’un workflow de plusieurs façons.
  
## <a name="foreach-loop-and-arrays"></a>Boucle ForEach et tableaux
  
Logic Apps vous permet de parcourir un jeu de données au moyen d’une boucle et d’effectuer une action pour chaque élément.  Cette opération est possible grâce à l’action `foreach` .  Dans le concepteur, vous pouvez choisir d’ajouter une boucle foreach.  Après avoir sélectionné le tableau à parcourir, vous pouvez commencer à ajouter des actions.  Vous pouvez ajouter plusieurs actions par boucle foreach.  Une fois dans la boucle, vous pouvez commencer à spécifier ce qui doit se produire au niveau de chaque valeur du tableau.

Si vous utilisez le mode code, vous pouvez spécifier une boucle foreach comme ci-dessous.  Dans cet exemple, une boucle foreach envoie un e-mail à chaque adresse contenant « microsoft.com » :

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
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
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  Une action `foreach` peut parcourir des tableaux comportant jusqu’à 5 000 lignes.  Chaque itération s’exécute en parallèle par défaut.  

### <a name="sequential-foreach-loops"></a>Boucles ForEach séquentielles

Pour activer une boucle ForEach permettant une exécution séquentielle, il convient d’ajouter l’option d’opération `Sequential`.

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Boucle Until
  
  Vous pouvez effectuer une action ou une série d’actions jusqu’à ce qu’une condition soit remplie.  Le scénario le plus courant consiste à appeler un point de terminaison jusqu’à obtention de la réponse recherchée.  Dans le concepteur, vous pouvez choisir d’ajouter une boucle until.  Après avoir ajouté des actions à l’intérieur de la boucle, vous pouvez définir la condition de sortie et les limites de la boucle.  Il existe un délai de 1 minute entre les cycles de la boucle.
  
  Si vous utilisez le mode code, vous pouvez spécifier une boucle until comme ci-dessous.  Dans cet exemple, un point de terminaison HTTP est appelé jusqu’à ce que le corps de la réponse ait la valeur « Completed ».  La procédure se termine quand l’une des conditions suivantes est remplie : 
  
  * L’état de la réponse HTTP est « Completed ».
  * La procédure a duré 1 heure.
  * Elle a effectué 100 cycles de boucle.
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn et décomposition

Parfois, un déclencheur peut recevoir un tableau d’éléments à décomposer et à partir de chacun desquels vous souhaitez démarrer un flux de travail.  Vous pouvez effectuer ces opérations à l’aide de la commande `spliton` .  Par défaut, si votre swagger de déclencheur spécifie une charge utile qui est un tableau, un `spliton` est ajouté et lance une exécution par élément.  SplitOn peut uniquement être ajouté à un déclencheur.  Ce paramétrage peut être configuré ou remplacé manuellement dans la définition en mode code.  Actuellement, SplitOn peut décomposer des tableaux qui comportent jusqu’à 5 000 éléments.  Vous ne pouvez pas avoir un `spliton` et également implémenter le modèle de réponse synchrone.  Tout flux de travail appelé qui a une action `response` en plus de `spliton` s’exécute de façon asynchrone et envoie une réponse `202 Accepted` immédiate.  

SplitOn peut être spécifié en mode code, comme dans l’exemple suivant.  Dans cet exemple, un tableau d’éléments est reçu et chacune de ses lignes fait l’objet d’une décomposition.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Étendues

Vous pouvez grouper une série d’actions à l’aide d’une étendue.  Cette opération est particulièrement utile pour implémenter la gestion des exceptions.  Dans le concepteur, vous pouvez ajouter une nouvelle étendue et commencer à y ajouter des actions.  Vous pouvez définir des étendues en mode code comme suit :


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
