---
title: "Exécuter des étapes en fonction de l’état de l’action groupée Azure Logic Apps | Microsoft Docs"
description: "Grouper des actions dans des étendues et exécuter des étapes en fonction de l’état du groupe"
services: logic-apps
keywords: "branches, traitement en parallèle"
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 052af45962f442e96ca28f05ffaa1b9814b2588b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="scopes-run-steps-based-on-group-status-in-logic-apps"></a>Étendues : Exécuter des étapes en fonction de l’état du groupe dans des applications logiques

Pour exécuter des étapes uniquement après l’échec ou la réussite d’un groupe d’actions, placez ce groupe dans une *étendue*. Cette structure est utile lorsque vous souhaitez organiser les actions en tant que groupe logique, évaluer l’état de ce groupe et effectuer des actions qui sont basées sur l’état de l’étendue. Une fois que toutes les actions d’une étendue ont été exécutées, l’étendue récupère également son propre état. Par exemple, vous pouvez utiliser des étendues lorsque vous souhaitez implémenter la [gestion des erreurs et des exceptions](../logic-apps/logic-apps-exception-handling.md#scopes). 

Pour vérifier l’état d’une étendue, vous pouvez utiliser les mêmes critères que ceux utilisés pour déterminer l’état d’exécution des applications logiques, tels que « Réussi », « Échec », « Annulé », etc. Par défaut, lorsque toutes les actions de l’étendue réussissent, l’état de l’étendue est défini sur « Réussi ». Mais lorsqu’une action dans l’étendue échoue ou est annulée, l’état de l’étendue est défini sur « Échec ». Pour les limites sur les étendues, consultez [Limites et configuration de Logic Apps](../logic-apps/logic-apps-limits-and-config.md). 

Par exemple, voici une application logique de haut niveau qui utilise une étendue pour exécuter des actions spécifiques et une condition pour vérifier l’état de l’étendue. Si des actions de l’étendue échouent ou se ferment de façon inattendue, l’étendue est définie sur « Échec » ou « Abandonné », respectivement ; l’application logique envoie un message « Scope failed » (Échec de l’étendue). Si toutes les actions de l’étendue réussissent, l’application logique envoie un message « Scope succeeded » (Réussite de l’étendue).

![Configurer le déclencheur « Planification - Périodicité »](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>configuration requise

Pour suivre l’exemple de cet article, vous avez besoin de ces éléments :

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/). 

* Un compte de messagerie de n’importe quel fournisseur de messagerie pris en charge par Logic Apps. Cet exemple utilise Outlook.com. Si vous utilisez un autre fournisseur, le flux général reste le même, mais votre interface utilisateur peut s’afficher différemment.

* Une clé Bing Maps. Pour obtenir cette clé, consultez <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Getting a Bing Maps Key</a> (Obtention d’une clé Bing Maps).

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Créer un exemple d’application logique

Commencez par créer cet exemple d’application logique pour que vous puissiez ajouter une étendue plus tard :

![Créer un exemple d’application logique](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Un déclencheur **Planification - Périodicité** qui vérifie le service Bing Maps à un intervalle que vous spécifiez
* Une action **Bing Maps - Obtenir un itinéraire** qui vérifie le temps de trajet entre deux emplacements
* Une instruction conditionnelle qui vérifie si le temps de trajet dépasse votre temps de trajet spécifié
* Une action qui vous envoie un e-mail indiquant que ce temps de trajet actuel dépasse celui que vous avez spécifié

Vous pouvez enregistrer votre application logique à tout moment, par conséquent, enregistrez votre travail souvent.

1. Si ce n’est pas déjà fait, connectez-vous au <a href="https://portal.azure.com" target="_blank">portail Azure</a>. Créez une application logique vide.

2. Ajouter le déclencheur **Planification - Périodicité** avec ces paramètres : **Intervalle** = « 1 » et **Fréquence** = « Minute »

   ![Configurer le déclencheur « Planification - Périodicité »](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Pour simplifier l’affichage et masquer les détails de chaque action dans le concepteur, réduisez la forme de chaque action au cours de ces étapes.

3. Ajoutez l’action **Bing Maps - Obtenir un itinéraire**. 

   1. Si vous ne disposez pas d’une connexion à Bing Maps, vous êtes invité à en créer une.

      | Paramètre | Valeur | DESCRIPTION |
      | ------- | ----- | ----------- |
      | **Nom de connexion** | BingMapsConnection | Donnez un nom à votre connexion. | 
      | **Clé API** | <*your-Bing-Maps-key*> | Entrez la clé Bing Cartes que vous avez reçue précédemment. | 
      ||||  

   2. Configurez votre action **Obtenir un itinéraire**, comme le montre le tableau sous cette image :

      ![Configurer l’action « Bing Maps - Obtenir un itinéraire »](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Pour plus d’informations sur ces paramètres, voir [Calculate a route (Calculer un itinéraire)](https://msdn.microsoft.com/library/ff701717.aspx).

      | Paramètre | Valeur | DESCRIPTION |
      | ------- | ----- | ----------- |
      | **Étape 1** | <*start*> | Entrez l’origine de votre itinéraire. | 
      | **Étape 2** | <*end*> | Entrez la destination de votre itinéraire. | 
      | **Avoid** | Aucun | Entrez les éléments à éviter sur votre itinéraire, par exemple les autoroutes, les péages, etc. Pour les valeurs possibles, consultez [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Calculer un itinéraire). | 
      | **Optimize** | timeWithTraffic | Sélectionnez un paramètre permettant d’optimiser votre itinéraire, par exemple la distance, la durée du trajet avec les informations de circulation actuelle, etc. Cet exemple utilise cette valeur : « timeWithTraffic » | 
      | **Unité de distance** | <*your-preference*> | Entrez l’unité de distance pour calculer votre itinéraire. Cet exemple utilise cette valeur : « Mile » | 
      | **Mode de déplacement** | Conduite | Entrez le mode de déplacement pour votre itinéraire. Cet exemple utilise cette valeur : « Driving » | 
      | **Date et heure de transit** | Aucun | S’applique au mode transit uniquement. | 
      | **Type de date et heure de transit** | Aucun | S’applique au mode transit uniquement. | 
      ||||  

4. Ajoutez une condition pour vérifier si le temps de trajet actuel dépasse une durée spécifiée. Pour cet exemple, suivez les étapes sous cette image :

   ![Créer une condition](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Renommez la condition avec cette description : **Si le temps de trajet est supérieur au temps spécifié**

   2. Dans la liste des paramètres, sélectionnez le champ **Travel Duration Traffic** (Trafic correspondant à la durée du trajet), qui est exprimé en secondes. 

   3. Sélectionnez cet opérateur de comparaison : **est supérieur à**

   4. Pour la valeur de comparaison, entrez **600**, qui est exprimé en secondes et correspond à 10 minutes.

5. Dans de la branche **If true** (Si true) de la condition, ajoutez une action « Envoyer e-mail » pour votre fournisseur de messagerie. Configurez cette action avec les détails comme indiqué dans le tableau sous cette image :

   ![Ajouter une action « Envoyer e-mail » à la branche « If true » (Si true)](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Pour le champ **To** (À), entrez votre adresse e-mail à des fins de test.

   2. Pour le champ **Sujet**, entrez ce texte :

      ```Time to leave: Traffic more than 10 minutes```

   3. Pour le champ **Corps**, entrez ce texte avec un espace de fin : 

      ```Travel time: ```

      Alors que le curseur s’affiche dans le champ **Corps**, la liste de contenu dynamique reste ouverte afin que vous puissiez sélectionner tous les paramètres qui sont disponibles à ce stade.

   4. Dans la liste de contenu dynamique, choisissez **Expression**.

   5. Recherchez et sélectionnez la fonction **div( )**.

   6. Alors que votre curseur se trouve dans les parenthèses de la fonction, choisissez **Contenu dynamique** afin d’ajouter ensuite le paramètre **Travel Duration Traffic** (Trafic correspondant à la durée du trajet).

   7. Sous **Obtenir un itinéraire** dans la liste des paramètres dynamiques, sélectionnez le champ **Travel Duration Traffic** (Trafic correspondant à la durée du trajet).

      ![Sélectionner « Travel Duration Traffic » (Trafic correspondant à la durée du trajet)](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   8. Une fois que le champ correspond au format JSON, ajoutez une **virgule** (```,```) suivie du nombre ```60``` afin de convertir la valeur dans **Travel Duration Traffic** (Trafic correspondant à la durée du trajet) exprimée en secondes en minutes. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Votre expression ressemble maintenant à cet exemple :

      ![Expression finale](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   9. Pensez à cliquer sur **OK** lorsque vous avez terminé.

  10. Une fois l’expression résolue, ajoutez ce texte avec un espace de début : ``` minutes```
  
      Votre champ **Corps** ressemble maintenant à cet exemple :

      ![Champ « Corps » final](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)

6. Enregistrez votre application logique.

Ensuite, ajoutez une étendue afin que vous puissiez regrouper des actions spécifiques et évaluer leur état.

## <a name="add-a-scope"></a>Ajouter une étendue

1. Si ce n’est pas déjà le cas, ouvrez votre application logique dans le concepteur d’application logique. 

2. Ajoutez une étendue à l’emplacement du flux de travail que vous souhaitez. Par exemple : 

   * Pour ajouter une étendue entre des étapes existantes du flux de travail de l’application logique, déplacez le pointeur sur la flèche où vous voulez ajouter l’étendue. 
   Choisissez le **signe plus** (**+**) > **Ajouter une étendue**.

     ![Ajouter une étendue](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

     Quand vous souhaitez ajouter une étendue à la fin de votre flux de travail, accédez au bas de votre application logique et sélectionnez **+ Nouvelle étape** > **...Plus** > **Ajouter une étendue**.

3. Ajoutez maintenant les étapes ou faites glisser les étapes que vous souhaitez exécuter dans l’étendue. Pour cet exemple, faites glisser ces actions dans l’étendue :
      
   * **Obtenir un itinéraire**
   * **Si le temps de trajet est supérieur au temps spécifié**, qui comprend les branches **true** et **false**

   Votre application logique ressemble maintenant à cet exemple :

   ![Étendue ajoutée](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

4. Sous l’étendue, ajoutez une condition qui vérifie l’état de l’étendue. Renommez la condition à l’aide de cette description : **Si l’étendue a échoué**

   ![Ajouter une condition pour vérifier l’état de l’étendue](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
5. Créez cette expression qui vérifie si l’état de l’étendue est égal à `Failed` ou `Aborted`.

   ![Ajouter l’expression qui vérifie l’état de l’étendue](./media/logic-apps-control-flow-run-steps-group-scopes/build-expression-check-scope-status.png)

   Ou, pour entrer cette expression sous forme de texte, choisissez **Edit in advanced mode** (Modifier en mode avancé).

   ```@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')```

6. Dans les branches **If true** (Si true) et **If false** (Si false), ajoutez les actions que vous souhaitez effectuer, par exemple, envoyer un e-mail ou un message.

   ![Ajouter l’expression qui vérifie l’état de l’étendue](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

7. Enregistrez votre application logique.

Votre application logique finale ressemble maintenant à cet exemple avec toutes les formes développées :

![Application logique finale avec étendue](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Tester votre travail

Dans la barre d’outils du concepteur, choisissez **Exécuter**. Si toutes les actions de l’étendue réussissent, vous recevez un message « Scope succeeded » (Réussite de l’étendue). Si des actions de l’étendue échouent, vous recevez un message « Scope failed » (Échec de l’étendue). 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Définition JSON

Si vous travaillez en mode code, vous pouvez définir une structure d’étendue dans la définition JSON de votre application logique à la place. Par exemple, voici la définition JSON du déclencheur et des actions dans l’application logique précédente :

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    },
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "None",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": "@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')",
    "runAfter": {
      "Scope": [
        "Succeeded"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'], 60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": "@greater(body('Get_route')?['travelDurationTraffic'], 600)",
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
}
```

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées et suggestions de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>étapes suivantes

* [Instructions conditionnelles : Exécuter des étapes en fonction d’une condition dans des applications logiques](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Switch statements: Run different steps based on specific values in logic apps](../logic-apps/logic-apps-control-flow-switch-statement.md) (Instructions switch : Exécuter différentes étapes en fonction de valeurs spécifiques dans des applications logiques)
* [Loops: Process arrays or repeat actions until a condition is met](../logic-apps/logic-apps-control-flow-loops.md) (Boucles : Traiter des tableaux ou répéter des actions jusqu’à ce qu’une condition soit remplie)
* [Create or join parallel branches in your logic app](../logic-apps/logic-apps-control-flow-branches.md) (Créer ou joindre des branches parallèles dans votre application logique)