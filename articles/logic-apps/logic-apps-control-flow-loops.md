---
title: "Boucles - Tableaux de traitement ou actions répétées - Azure Logic Apps | Microsoft Docs"
description: "Tableaux de traitement avec des boucles « Foreach » ou des actions répétées jusqu’à remplir des conditions spécifiques dans des applications logiques"
services: logic-apps
keywords: "Boucles « Foreach »"
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: f634b1004fef2eb65c6b8134088ceead47c91890
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="loops-process-arrays-or-repeat-actions-until-a-condition-is-met"></a>Boucles : tableaux de traitement ou actions répétées jusqu’à remplir une condition

Pour itérer sur des tableaux dans votre application logique, vous pouvez utiliser une [boucle « Foreach »](#foreach-loop) ou une [boucle « Foreach » séquentielle](#sequential-foreach-loop). Les cycles d’une bouche « Foreach » standard s’exécutent en parallèle, tandis que des cycles d’une bouche « Foreach » séquentielle s’exécutent un à la fois. Pour connaître le nombre maximal d’éléments de tableau que des boucles « Foreach » peuvent traiter en une seule exécution d’application logique, consultez [Limites et configurations](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP] 
> Si vous disposez d’un déclencheur qui reçoit un tableau et souhaite exécuter un workflow pour chaque élément du tableau, vous pouvez *dégrouper* ce tableau avec le déclencheur de propriété](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) [**SplitOn**. 
  
Pour répéter des actions jusqu’à ce qu’une condition soit remplie ou qu’un statut ait changé, utilisez une [boucle « Until »](#until-loop). Votre application logique réalise toutes les actions dans la boucle puis vérifie la condition à la dernière étape. Si la condition est remplie, la boucle s’arrête. Dans le cas contraire, la boucle se répète. Pour connaître le nombre maximal de boucles « Until » dans une seule exécution d’application logique, consultez [Limites et configurations](../logic-apps/logic-apps-limits-and-config.md). 

## <a name="prerequisites"></a>configuration requise

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/). 

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Boucle « Foreach »

Pour répéter des actions pour chaque élément d’un tableau, utilisez une boucle « Foreach » dans le workflow de votre application logique. Vous pouvez inclure plusieurs actions dans une boucle « Foreach », et vous pouvez imbriquer des boucles « Foreach » l’une dans l’autre. Par défaut, les cycles dans une boucle « Foreach » standard s’exécutent en parallèle. Pour connaître le nombre maximal de cycles parallèles que peuvent exécuter des boucles « Foreach », consultez [Limites et configurations](../logic-apps/logic-apps-limits-and-config.md).

> [!NOTE] 
> Une boucle « Foreach » ne fonctionne qu’avec un tableau, et les actions dans cette boucle utilisent la référence `@item()` pour traiter chaque élément du tableau. Si vous spécifiez des données qui ne sont pas présentes dans le tableau, le workflow de l’application logique échoue. 

Par exemple, cette application logique vous envoie un résumé quotidien depuis le flux RSS d’un site web. L’application utilise une boucle « Foreach » qui envoie un courrier électronique pour chaque nouvel élément trouvé.

1. [Créez cet exemple d’application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md) avec un compte Outlook.com ou Office 365 Outlook.

2. Entre le déclencheur RSS et l’action Envoyer un courrier électronique, ajoutez une bouche « Foreach ». 

   Pour ajouter une boucle entre des étapes, placez le curseur sur la flèche où vous souhaitez ajouter la boucle. 
   Cliquez sur le **signe plus** (**+**) qui s’affiche, puis choisissez **Ajouter un pour chaque**.

   ![Ajouter une boucle « Foreach » entre des étapes](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. Générez maintenant la boucle. Sous **Sélectionner une sortie des étapes précédentes**, après que la liste **Ajouter contenu dynamique** s’affiche, sélectionnez le tableau **Liens du flux**, qui sort du déclencheur RSS. 

   ![Sélectionner depuis la liste de contenu dynamique](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Vous pouvez sélectionner *seulement* des sorties de tableau de l’étape précédente.

   Le tableau sélectionné s’affiche maintenant ici :

   ![Sélectionner le tableau](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Pour effectuer une action sur chaque élément du tableau, faites glisser l’action **Envoyer un courrier électronique** dans la boucle **For each**. 

   Votre application logique peut ressembler à cet exemple :

   ![Ajouter des étapes à la boucle « Foreach »](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Enregistrez votre application logique. Pour tester manuellement votre application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Définition de la boucle « Foreach » (JSON)

Si vous travaillez en mode code pour votre application logique, vous pouvez définir la boucle `Foreach` dans la définition JSON de votre application logique à la place, par exemple :

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {
                "type": "ApiConnection",
                "inputs": {
                    "body": {
                        "Body": "@{item()}",
                        "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                        "To": "me@contoso.com"
                    },
                    "host": {
                        "api": {
                            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                        },
                        "connection": {
                            "name": "@parameters('$connections')['office365']['connectionId']"
                        }
                    },
                    "method": "post",
                    "path": "/Mail"
                },
                "runAfter": {}
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
    }
},
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>Boucle « Foreach » : séquentielle

Par défaut, chaque cycle d’une boucle « Foreach » s’exécute en parallèle pour chaque élément du tableau. Pour exécuter chaque cycle de façon séquentielle, définissez l’option **Séquentielle** dans votre boucle « Foreach ».

1. En haut à droite de la boucle, choisissez **Ellipses** (**...**) > **Paramètres**.

   ![Dans la boucle « Foreach », choisissez « ... » > « Paramètres »](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. Activez le paramètre **Séquentielle**, puis choisissez **Terminé**.

   ![Activer le paramètre Séquentielle pour la boucle « Foreach »](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Vous pouvez aussi régler le paramètre **operationOptions** sur `Sequential` dans la définition JSON de votre application logique. Par exemple : 

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {               
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
        "operationOptions": "Sequential"
    }
},
```

<a name="until-loop"></a>

## <a name="until-loop"></a>Boucle « Until »
  
Pour répéter des actions jusqu’à ce qu’une condition soit remplie ou qu’un statut ait changé, utilisez une boucle « Until ». Voici quelques cas d’utilisation courants dans lesquels vous pouvez utiliser une boucle « Until » :

* Appelez un point de terminaison jusqu’à obtenir la réponse souhaitée.
* Créez un enregistrement dans une base de données, attendez qu’un champ spécifique de cet enregistrement soit approuvé, puis continuez. 

> [!NOTE]
> Les boucles « Until » ne peuvent inclure de boucles « Foreach » ou d’autres boucles « Until ».

Par exemple, à 8 h 00 chaque jour, cette application logique incrémente une variable jusqu’à ce que sa valeur soit égale à 10. Ensuite, l’application logique envoie un message qui confirme la valeur actuelle. Bien que cet exemple utilise Office 365 Outlook, vous pouvez utiliser n’importe quel fournisseur de messagerie électronique pris en charge par Logic Apps ([voir la liste des connecteurs ici](https://docs.microsoft.com/connectors/)). Si vous utilisez un autre compte de messagerie, les étapes générales sont identiques, mais votre interface utilisateur peut-être légèrement différente. 

1. Créez une application logique vide. Dans Logic App Designer, cherchez « Récurrence », et sélectionnez ce déclencheur : **Planification - Récurrence** 

   ![Ajouter le déclencheur « Planification - Récurrence »](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. Spécifiez l’envoi du déclencheur en définissant l’intervalle, la fréquence et l’heure du jour. Pour définir l’heure, choisissez **Afficher les options avancées**.

   ![Ajouter le déclencheur « Planification - Récurrence »](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Propriété | Valeur |
   | -------- | ----- |
   | **Intervalle** | 1 | 
   | **Fréquence** | jour |
   | **Aux heures indiquées** | 8 |
   ||| 

3. Sous le déclencheur, choisissez **Nouvelle étape** > **Ajouter une action**. Rechercher « variables » puis sélectionnez cette action : **Variables : initialiser la variable**

   ![Ajouter l’action « Variables : initialiser la variable »](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. Configurez votre variable avec ces valeurs :

   ![Définir les propriétés de la variable](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Propriété | Valeur | DESCRIPTION |
   | -------- | ----- | ----------- |
   | **Name** | Limite | Nom de votre variable | 
   | **Type** | Entier  | Type de données de votre variable | 
   | **Valeur** | 0 | Valeur de départ de votre variable | 
   |||| 

5. Sous l’action **Initialiser la variable**, choisissez **Nouvelle étape** > **Plus**. Sélectionner la boucle : **Ajouter Do Until**

   ![Ajouter une boucle « Do Until »](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. Générez la condition de sortie de la boucle en sélectionnant la variable **Limite** et l’opérateur **Est égal à**. Entrez **10** comme valeur de comparaison.

   ![Générer la connexion de sortie pour arrêter la boucle](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. Dans la boucle, choisissez **Ajouter une action**. Cherchez « variables » puis ajoutez cette action : **Variables - Incrémenter la variable**

   ![Ajouter une action pour incrémenter une variable](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. Comme **Nom**, sélectionnez la variable **Limite**. Comme **Valeur**, entrez « 1 ». 

   ![Incrémenter la variable « Limite » de 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. Sous la boucle, mais hors de cette dernière, ajoutez une action d’envoi de message électronique. Si vous y êtes invité, connectez-vous à votre compte e-mail.

   ![Ajouter une action d’envoi de message électronique](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. Définissez les propriétés du message électronique. Ajoutez la variable **Limite** au sujet. De cette façon, vous pouvez confirmer que la valeur actuelle de la variable corresponde aux conditions que vous avez spécifiées. Par exemple :

    ![Configurer les propriétés du message électronique](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | Propriété | Valeur | DESCRIPTION |
    | -------- | ----- | ----------- | 
    | **To** | *<email-address@domain>* | Adresse e-mail du destinataire. Pour effectuer le test, utilisez votre propre adresse e-mail. | 
    | **Objet** | La valeur actuelle de la variable « Limite » est **Limite** | Spécifiez l’objet du message électronique. Pour cet exemple, assurez-vous d’inclure la variable **Limite**. | 
    | **Corps** | <*email-content*> | Spécifiez le contenu du message électronique à envoyer. Pour cet exemple, écrivez ce que vous voulez. | 
    |||| 

11. Enregistrez votre application logique. Pour tester manuellement votre application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur.

    Lorsque votre application logique s’exécute, vous recevez un message électronique avec le contenu spécifié :

    ![Message électronique reçu](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Empêcher les boucles infinies

Une boucle « Until » dispose de limites par défaut qui arrêtent l’exécution si l’une de ces conditions est remplie :

| Propriété | Valeur par défaut | DESCRIPTION | 
| -------- | ------------- | ----------- | 
| **Count** | 60 | Le nombre maximum de boucles qui s’exécutent avant que la boucle ne sorte. La valeur par défaut est 60 cycles. | 
| **Délai d'expiration** | PT1H | La durée maximale d’exécution d’une boucle avant que la boucle ne sorte. La valeur par défaut est d’une heure et est spécifiée au format ISO 8601. <p>La valeur du délai d’attente est évaluée pour chaque cycle de boucle. Si une action dans la boucle dure plus longtemps que la limite du délai d’attente, le cycle actuel ne s’arrête pas, mais le prochain ne démarre pas car la condition de limite n’est pas remplie. | 
|||| 

Pour modifier ces limites par défaut, choisissez **Afficher les options avancées** dans la forme d’action de la boucle.

<a name="until-json"></a>

## <a name="until-definition-json"></a>Définition (JSON) de la boucle « Until »

Si vous travaillez en mode code pour votre application logique, vous pouvez définir une boucle `Until` dans la définition JSON de votre application logique à la place, par exemple :

``` json
"actions": {
    "Initialize_variable": {
        // Definition for initialize variable action
    },
    "Send_an_email": {
        // Definition for send email action
    },
    "Until": {
        "type": "Until",
        "actions": {
            "Increment_variable": {
                "type": "IncrementVariable",
                "inputs": {
                    "name": "Limit",
                    "value": 1
                },
                "runAfter": {}
            }
        },
        "expression": "@equals(variables('Limit'), 10)",
        // To prevent endless loops, an "Until" loop 
        // includes these default limits that stop the loop. 
        "limit": { 
            "count": 60,
            "timeout": "PT1H"
        },
        "runAfter": {
            "Initialize_variable": [
                "Succeeded"
            ]
        },
    }
},
```

Dans un autre exemple, cette boucle « Until » appelle un point de terminaison HTTP qui crée une ressource et s’arrête lorsque le corps de la réponse HTTP renvoie le statut « Terminé ». Pour empêcher les boucles infinies, la boucle s’arrête aussi si l’un de ces conditions est remplie :

* La boucle s’est exécutée 10 fois comme spécifié par l’attribut `count`. La valeur par défaut est de 60 fois. 
* La boucle a essayé de s’exécuter pendant deux heures comme spécifié par l’attribut `timeout` au format ISO 8601. La valeur par défaut est d’une heure.
  
``` json
"actions": {
    "myUntilLoopName": {
        "type": "Until",
        "actions": {
            "Create_new_resource": {
                "type": "Http",
                "inputs": {
                    "body": {
                        "resourceId": "@triggerBody()"
                    },
                    "url": "https://domain.com/provisionResource/create-resource",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                },
                "runAfter": {},
                "type": "ApiConnection"
            }
        },
        "expression": "@equals(triggerBody(), 'Completed')",
        "limit": {
            "count": 10,
            "timeout": "PT2H"
        },
        "runAfter": {}
    }
},
```

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées et suggestions de fonctionnalités ou pour en soumettre, rendez-vous sur le [site de commentaires des utilisateurs Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>étapes suivantes

* [Instructions conditionnelles : Exécuter des étapes en fonction d’une condition](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Instructions switch : Exécuter différentes étapes en fonction de valeurs spécifiques](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Exécuter ou joindre des étapes (branches) parallèles](../logic-apps/logic-apps-control-flow-branches.md)
* [Étendues : Exécuter des étapes en fonction de l’état d’un groupe](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)