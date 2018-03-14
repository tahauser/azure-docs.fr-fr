---
title: "Créer à partir des définitions d’application logique avec JSON - Azure Logic Apps | Microsoft Docs"
description: "Ajouter des paramètres, traiter des chaînes, créer des mappages de paramètres et obtenir des données avec des fonctions de date"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 01/31/2018
ms.author: LADocs; estfan
ms.openlocfilehash: d05f7e34cbe670db6733c199e3420c810c304a84
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="build-on-your-logic-app-definition-with-json"></a>Créer à partir de votre définition d’application logique avec JSON

Pour effectuer des tâches plus avancées avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md), vous pouvez utiliser le mode Code pour modifier votre définition d’application logique, qui utilise le langage JSON simple et déclaratif. Si vous ne l’avez pas encore fait, commencez par passer en revue l’article décrivant la [procédure de création de votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md). Vous pouvez également lire la [référence complète du langage de définition de workflow](http://aka.ms/logicappsdocs).

> [!NOTE]
> Certaines fonctionnalités Azure Logic Apps, comme les paramètres, sont disponibles uniquement lorsque vous travaillez en mode Code pour la définition de votre application logique. Les paramètres permettent de réutiliser des valeurs dans votre application logique. Par exemple, si vous souhaitez utiliser une même adresse e-mail dans plusieurs actions, définissez-la en tant que paramètre.

## <a name="view-and-edit-your-logic-app-definitions-in-json"></a>Afficher et modifier la définition de votre application logique en JSON

1. Connectez-vous au [portail Azure](https://portal.azure.com "portail Azure").

2. Dans le menu de gauche, choisissez **Plus de services**. Sous **Intégration d’entreprise**, choisissez **Applications logiques**. Sélectionnez votre application logique.

3. Dans le menu de l’application logique, sous **Outils de développement**, choisissez **Affichage du code de l’application logique**.

   La fenêtre d’affichage du code contenant la définition de votre application logique s’ouvre.

## <a name="parameters"></a>parameters

Les paramètres vous permettent de réutiliser des valeurs dans l’ensemble de votre application logique. Ils conviennent également au remplacement des valeurs que vous êtes susceptible de modifier souvent. Par exemple, si vous possédez une adresse e-mail que vous souhaitez utiliser dans plusieurs emplacements, vous devez la définir en tant que paramètre. 

Les paramètres sont également utiles si vous devez remplacer des paramètres dans des environnements différents. En savoir plus sur les [paramètres de déploiement](#deployment-parameters) et [la documentation de l’API REST pour Azure Logic Apps](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Les paramètres sont uniquement disponibles en mode Code.

Dans le [premier exemple d’application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md), vous avez créé un flux de travail qui envoie des e-mails lorsque de nouvelles publications s’affichent dans le flux RSS d’un site web. L’URL du flux étant codée en dur, cet exemple montre comment remplacer la valeur de la requête par un paramètre afin que vous puissiez modifier plus facilement l’URL du flux.

1. En mode code, recherchez l’objet `parameters : {}` et ajoutez un objet `currentFeedUrl` :

   ``` json
     "currentFeedUrl" : {
      "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. Dans l’action `When_a_feed-item_is_published`, recherchez la section `queries` et remplacez la valeur de la requête par `"feedUrl": "#@{parameters('currentFeedUrl')}"`. 

   **Avant**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },   
   ```

   **Après**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },   
   ```

   Pour joindre deux chaînes ou plus, vous pouvez également utiliser la fonction `concat`. 
   Par exemple, `"@concat('#',parameters('currentFeedUrl'))"` fonctionne de la même façon que l’exemple précédent.

3.  Une fois ces opérations effectuées, sélectionnez **Enregistrer**. 

Vous pouvez maintenant modifier le flux RSS du site web en transmettant une autre URL via l’objet `currentFeedURL`.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>Paramètres de déploiement pour différents environnements

En règle générale, les cycles de vie de déploiement présentent des environnements de développement, intermédiaires et de production. Par exemple, vous pouvez mettre en œuvre la même définition d’application logique dans tous ces environnements, tout en utilisant des bases de données distinctes. De même, vous pouvez vouloir utiliser la même définition dans plusieurs régions à des fins de haute disponibilité, tout en souhaitant que chaque instance de l’application logique utilise la base de données d’une région spécifique. 

> [!NOTE] 
> Ce scénario diffère de l’utilisation de paramètres au moment de *l’exécution* dans lequel il est conseillé d’utiliser la fonction `trigger()`.

Voici une définition de base :

``` json
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```
Puis, dans la requête `PUT` réelle pour les applications logiques, vous pouvez fournir le paramètre `uri`. Vous pouvez fournir une valeur différente pour le paramètre `connection` dans chaque environnement. Étant donné qu’il n’existe plus de valeur par défaut, la charge utile d’application logique nécessite le paramètre suivant :

``` json
{
    "properties": {},
        "definition": {
          /// Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

Pour plus d’informations, consultez la [documentation de l’API REST pour Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>Traiter des chaînes avec des fonctions

Logic Apps possède différentes fonctions permettant de manipuler des chaînes. Par exemple, supposons que vous souhaitiez transmettre un nom de société d’une commande à un autre système. Toutefois, vous n’êtes pas sûr de la gestion correcte du codage de caractères. Vous pouvez effectuer un codage base64 sur cette chaîne, mais pour éviter les séquences d’échappement dans l’URL, il est conseillé de remplacer plusieurs caractères. De même, vous avez uniquement besoin d’une sous-chaîne du nom de la société, car les cinq premiers caractères ne sont pas utilisés. 

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Les étapes suivantes décrivent la façon dont cet exemple traite cette chaîne, de l’intérieur vers l’extérieur :

``` 
"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Obtenez l’élément [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) pour le nom de la société afin de récupérer le nombre total de caractères.

2. Pour obtenir une chaîne plus courte, soustrayez `5`.

3. Obtenez à présent un élément [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md). Commencez à l’index `5`, puis passez au reste de la chaîne.

4. Nous convertissons cette sous-chaîne en une chaîne [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md).

5. À présent, exécutez l’action [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) pour remplacer tous les caractères `+` par des caractères `-`.

6. Pour finir, exécutez l’action [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) pour remplacer tous les caractères `/` par des caractères `_`.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapper des éléments de liste aux valeurs de propriété, puis utiliser des mappages en tant que paramètres

Pour obtenir des résultats différents en fonction de la valeur d’une propriété, vous pouvez créer un mappage qui associe chaque valeur de propriété à un résultat, puis utiliser ce mappage en tant que paramètre. 

Par exemple, ce flux de travail définit certaines catégories en tant que paramètres et un mappage qui associe ces catégories à une URL spécifique. Tout d’abord, le flux de travail obtient une liste d’articles. Ensuite, le flux de travail utilise le mappage pour rechercher l’URL correspondant à la catégorie de chaque article.

*   La fonction [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) vérifie si la catégorie correspond à une catégorie définie connue.

*   Après avoir obtenu une catégorie correspondante, l’exemple extrait l’élément du mappage à l’aide de crochets : `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Obtenir des données avec des fonctions de date

Pour obtenir des données à partir d’une source de données qui ne prend pas en charge des *déclencheurs* en mode natif, vous pouvez utiliser des fonctions de date pour utiliser plutôt des heures et des dates. Par exemple, cette expression recherche la durée des étapes de ce flux de travail, de l’intérieur vers l’extérieur :

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Dans l’action `order`, extrayez l’élément `startTime`. 
2. Obtenez l’heure en cours avec l’élément `utcNow()`.
3. Soustrayez une seconde :

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Vous pouvez utiliser d’autres unités de temps, par exemple `minutes` ou `hours`. 

3. À présent, vous pouvez comparer ces deux valeurs. 

   Si la première valeur est inférieure à la seconde, plus d’une seconde s’est écoulée depuis que la commande a été passée.

Pour mettre en forme les dates, vous pouvez utiliser des formateurs de chaîne. Par exemple, pour obtenir RFC1123, utilisez [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md). En savoir plus sur [la mise en forme des dates](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```


## <a name="next-steps"></a>étapes suivantes

* [Conditional statements: Run steps based on a condition in logic apps](../logic-apps/logic-apps-control-flow-conditional-statement.md) (Instructions conditionnelles : Exécuter des étapes en fonction d’une condition dans des applications logiques)
* [Switch statements: Run different steps based on specific values in logic apps](../logic-apps/logic-apps-control-flow-switch-statement.md) (Instructions switch : Exécuter différentes étapes en fonction de valeurs spécifiques dans des applications logiques)
* [Loops: Process arrays or repeat actions until a condition is met](../logic-apps/logic-apps-control-flow-loops.md) (Boucles : Traiter des tableaux ou répéter des actions jusqu’à ce qu’une condition soit remplie)
* [Create or join parallel branches in your logic app](../logic-apps/logic-apps-control-flow-branches.md) (Créer ou joindre des branches parallèles dans votre application logique)
* [Scopes: Run steps based on group status in logic apps](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) (Étendues : Exécuter des étapes en fonction de l’état d’un groupe dans des applications logiques)
* En savoir plus sur le [schéma du langage de définition du flux de travail pour Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* En savoir plus sur les [actions et déclencheurs de flux de travail pour Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)