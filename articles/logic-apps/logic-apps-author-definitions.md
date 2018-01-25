---
title: "Définir des workflows avec JSON - Azure Logic Apps | Microsoft Docs"
description: "Procédure d’écriture de définitions de workflow au format JSON pour les applications logiques"
author: jeffhollan
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
ms.date: 03/29/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 7dde5bc4733af1aba34199f332379d2faf566725
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="create-workflow-definitions-for-logic-apps-using-json"></a>Créer des définitions de workflow pour les applications logiques à l’aide de JSON

Vous pouvez créer des définitions de workflow pour [Azure Logic Apps](logic-apps-overview.md) à l’aide d’un langage JSON déclaratif simple. Si vous ne l’avez pas encore fait, commencez par consulter l’article décrivant la [procédure de création de votre première application logique avec le concepteur d’application logique](quickstart-create-first-logic-app-workflow.md). Vous pouvez également lire la [référence complète du langage de définition de workflow](http://aka.ms/logicappsdocs).

## <a name="repeat-steps-over-a-list"></a>Répéter des étapes dans une liste

Pour itérer au sein d’un tableau comportant plus de 10 000 éléments et exécuter une action sur chaque élément, utilisez le [type foreach](logic-apps-loops-and-scopes.md).

## <a name="handle-failures-if-something-goes-wrong"></a>Gérer les échecs en cas de problème

Vous souhaitez généralement inclure une *étape de correction*, c’est-à-dire une logique qui s’exécute *si et seulement si* un ou plusieurs de vos appels échouent. Cet exemple obtient des données provenant de différents emplacements, mais si l’appel échoue, nous voulons PUBLIER un message à un endroit quelconque de façon à pouvoir repérer cet échec par la suite :  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "postToErrorMessageQueue": {
      "type": "ApiConnection",
      "inputs": "...",
      "runAfter": {
        "readData": [
          "Failed"
        ]
      }
    }
  },
  "outputs": {}
}
```

Pour indiquer que l’action `postToErrorMessageQueue` s’exécute uniquement si l’action `readData` présente l’état `Failed`, utilisez la propriété `runAfter`, par exemple pour spécifier une liste de valeurs possibles, de sorte que `runAfter` peut présenter les valeurs `["Succeeded", "Failed"]`.

Enfin, étant donné que cet exemple gère désormais l’erreur, nous n’identifions plus l’exécution avec l’état `Failed`. Puisque nous avons ajouté l’étape de gestion de cet échec dans cet exemple, l’exécution est indiquée comme `Succeeded`, même si une étape a présenté l’état `Failed`.

## <a name="execute-two-or-more-steps-in-parallel"></a>Exécuter au moins deux étapes en parallèle

Pour exécuter plusieurs actions en parallèle, la propriété `runAfter` doit être équivalente au moment de l’exécution. 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "kind": "http",
      "type": "Request"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

Dans cet exemple, les éléments `branch1` et `branch2` sont tous deux définis comme devant s’exécuter après l’action `readData`. Par conséquent, les deux branches s’exécutent en parallèle. Ces deux branches présentent le même horodateur.

![Parallèle](media/logic-apps-author-definitions/parallel.png)

## <a name="join-two-parallel-branches"></a>Joindre deux branches parallèles

Vous pouvez joindre deux actions définies comme devant s’exécuter en parallèle en ajoutant des éléments à la propriété `runAfter`, comme dans l’exemple précédent.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {}
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "join": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "branch1": [
          "Succeeded"
        ],
        "branch2": [
          "Succeeded"
        ]
      }
    }
  },
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "Http",
      "inputs": {
        "schema": {}
      }
    }
  },
  "contentVersion": "1.0.0.0",
  "outputs": {}
}
```

![Parallèle](media/logic-apps-author-definitions/join.png)

## <a name="map-list-items-to-a-different-configuration"></a>Mapper les éléments d’une liste sur une autre configuration

À présent, supposons que nous voulions obtenir un contenu différent selon la valeur d’une propriété. Nous pouvons créer un mappage des valeurs aux destinations en tant que paramètre :  

```
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

Dans ce cas, nous commençons par obtenir une liste d’articles. Selon la catégorie définie en tant que paramètre, la deuxième étape utilise un mappage pour rechercher l’URL à partir de laquelle obtenir le contenu.

Tenez compte des points suivants : 

*   La fonction [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) vérifie si la catégorie correspond ou non à l’une des catégories définies connues.

*   Une fois que nous avons obtenu la catégorie, nous pouvons extraire l’élément du mappage à l’aide de crochets : `parameters[...]`

## <a name="process-strings"></a>Traiter des chaînes

Différentes fonctions vous permettent de manipuler les chaînes. Par exemple, supposons que nous souhaitions transmettre une chaîne à un système, mais que nous ne soyons pas certains de l’encodage de caractères à utiliser. Une option consiste à encoder cette chaîne en base64. Toutefois, pour éviter la séquence d’échappement dans une URL, nous allons remplacer plusieurs caractères. 

Nous voulons également disposer d’une sous-chaîne du nom de la commande, car les cinq premiers caractères ne sont pas utilisés.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "orderer": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Voici le déroulement des opérations de l’intérieur vers l’extérieur :

1. Nous obtenons l’élément [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) pour le nom de la commande afin de récupérer le nombre total de caractères.

2. Nous soustrayons la valeur 5, car nous voulons une chaîne plus courte.

3. Nous utilisons l’élément [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring). Nous commençons à l'index `5` et suivons le reste de la chaîne.

4. Nous convertissons cette sous-chaîne en une chaîne [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64).

5. Nous exécutons l’action [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) pour remplacer tous les caractères `+` par des caractères `-`.

6. Nous exécutons l’action [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) pour remplacer tous les caractères `/` par des caractères `_`.

## <a name="work-with-date-times"></a>Utiliser des dates

Les dates peuvent vous être utiles, particulièrement lorsque vous tentez d’extraire des données à partir d’une source de données qui ne prend pas naturellement en charge les *déclencheurs*. Vous pouvez également utiliser les dates pour déterminer la durée d’exécution des différentes étapes.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
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

Dans cet exemple, nous extrayons l’élément `startTime` de l’étape précédente. Puis nous obtenons l’heure actuelle, et nous lui soustrayons une seconde :

[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) 

Vous pouvez utiliser d’autres unités de temps, par exemple `minutes` ou `hours`. Enfin, nous pouvons comparer ces deux valeurs. Si la première valeur est inférieure à la seconde, plus d’une seconde s’est écoulée depuis que la commande a été passée.

Pour formater les dates, nous pouvons utiliser des formateurs de chaîne. Par exemple, pour obtenir RFC1123, nous utilisons [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). Pour plus d’informations sur le formatage des dates, consultez l’article [Workflow Definition Language](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) (Langage de définition de workflow).

## <a name="deployment-parameters-for-different-environments"></a>Paramètres de déploiement pour différents environnements

Généralement, les cycles de vie de déploiement comprennent un environnement de développement, un environnement intermédiaire et un environnement de production. Par exemple, vous pouvez mettre en œuvre la même définition dans tous ces environnements, tout en utilisant des bases de données distinctes. De même, vous pouvez faire en sorte d’utiliser la même définition dans plusieurs régions à des fins de haute disponibilité, tout en souhaitant que chaque instance de l’application logique communique avec la base de données d’une région spécifique.
Ce scénario diffère de l’utilisation de paramètres au moment de *l’exécution* dans lequel vous devez plutôt utiliser la fonction `trigger()`, comme indiqué dans l’exemple précédent.

Vous pouvez commencer par une définition de base comme celle-ci :

```
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

Puis, dans la requête `PUT` réelle pour les applications logiques, vous pouvez fournir le paramètre `uri`. Étant donné qu’il n’existe plus de valeur par défaut, la charge utile d’application logique nécessite le paramètre suivant :

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
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

Vous pouvez fournir une valeur différente pour le paramètre `connection` dans chaque environnement. 

Pour connaître toutes les options dont vous disposez pour créer et gérer des applications logiques, consultez la [documentation de l’API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx). 
