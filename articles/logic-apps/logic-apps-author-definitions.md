---
title: Créer, modifier ou étendre JSON pour des définitions d’application logique - Azure Logic Apps | Microsoft Docs
description: Créer et personnaliser des définitions d’application logique en JSON
author: ecfan
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2018
ms.author: estfan; LADocs
ms.openlocfilehash: bde275eb75c97da2a99109484b46b599a5b2f871
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-edit-or-customize-json-for-logic-app-definitions"></a>Créer, modifier ou personnaliser JSON pour des définitions d’application logique

Lorsque vous créez des solutions d’intégration d’entreprise avec des flux de travail automatisés dans [Azure Logic Apps](../logic-apps/logic-apps-overview.md), les définitions d’application logique sous-jacentes utilisent le JavaScript Object Notation (JSON) avec le [schéma Langue de la définition du flux de travail (WDL)](../logic-apps/logic-apps-workflow-definition-language.md) pour leur description et validation. Ces formats simplifient la lecture et la compréhension des définitions d’application sans en savoir beaucoup sur le code. Lorsque vous désirez automatiser la création et le déploiement des applications logiques, vous pouvez inclure des définitions d’application logique en tant que [Ressources Azure](../azure-resource-manager/resource-group-overview.md) dans des [modèles Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment). Pour créer, gérer et déployer des applications logiques, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp), [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) ou les [API REST Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/).

Pour utiliser des définitions d’application logique dans JSON, ouvrez l’éditeur en mode Code lorsque vous travaillez dans le portail Azure ou dans Visual Studio ou copiez la définition dans tout éditeur de votre choix. Si vous débutez avec les applications logiques, consultez [procédure de création de votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Certaines fonctionnalités Azure Logic Apps, telle que la définition des paramètres et de plusieurs déclencheurs dans les définitions d’application logique, sont uniquement disponibles dans JSON, et pas le Concepteur d’applications logiques. Par conséquent, pour ces tâches, vous devez travailler en mode Code ou sur un autre éditeur.

## <a name="edit-json---azure-portal"></a>Modifier JSON - portail Azure

1. Connectez-vous au <a href="https://portal.azure.com" target="_blank">Portail Azure</a>.

2. Dans le menu de gauche, choisissez **Tous les services**. Dans la zone de recherche, rechercher « applications logiques », puis à partir des résultats, sélectionnez votre application logique.

3. Dans le menu de l’application logique, sous **Outils de développement**, choisissez **Affichage du code de l’application logique**.

   L’éditeur Mode Code s’ouvre et affiche la définition de votre application logique au format JSON.

## <a name="edit-json---visual-studio"></a>Modifier JSON - Visual Studio

Avant de pouvoir travailler sur la définition de votre application logique dans Visual Studio, assurez-vous d’avoir [installé les outils requis](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Pour créer une application logique avec Visual Studio, consultez [Démarrage rapide : automatiser des tâches et des processus avec Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

Dans Visual Studio, vous pouvez ouvrir des applications logiques précédemment créées et déployées directement depuis le portail Azure ou en tant que projets Azure Resource Manager à partir de Visual Studio.

1. Ouvrez la solution Visual Studio ou le projet [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md), qui contient votre application logique.

2. Recherchez et ouvrez la définition de votre application logique, qui par défaut s’affiche dans un [modèle Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), nommé **LogicApp.json**. Vous pouvez utiliser et personnaliser ce modèle pour le déploiement vers différents environnements.

3. Ouvrez le menu contextuel de la définition et du modèle de votre application logique. Sélectionnez **Ouvrir avec le Concepteur d’application logique**.

   ![Ouvrir une application logique dans une solution Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. En bas du concepteur, choisissez **Mode Code**. 

   L’éditeur Mode Code s’ouvre et affiche la définition de votre application logique au format JSON.

5. Pour revenir au mode concepteur, en bas de l’éditeur Mode Code, choisissez **Conception**.

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


## <a name="next-steps"></a>Étapes suivantes

* [Instructions conditionnelles : Exécuter des étapes en fonction d’une condition dans des applications logiques](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Switch statements: Run different steps based on specific values in logic apps](../logic-apps/logic-apps-control-flow-switch-statement.md) (Instructions switch : Exécuter différentes étapes en fonction de valeurs spécifiques dans des applications logiques)
* [Loops: Process arrays or repeat actions until a condition is met](../logic-apps/logic-apps-control-flow-loops.md) (Boucles : Traiter des tableaux ou répéter des actions jusqu’à ce qu’une condition soit remplie)
* [Create or join parallel branches in your logic app](../logic-apps/logic-apps-control-flow-branches.md) (Créer ou joindre des branches parallèles dans votre application logique)
* [Scopes: Run steps based on group status in logic apps](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) (Étendues : Exécuter des étapes en fonction de l’état d’un groupe dans des applications logiques)
* En savoir plus sur le [schéma du langage de définition du flux de travail pour Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* En savoir plus sur les [actions et déclencheurs de flux de travail pour Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)