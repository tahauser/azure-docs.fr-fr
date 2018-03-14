---
title: "Branches parallèles - Azure Logic Apps | Microsoft Docs"
description: "Créer ou joindre des branches parallèles dans des applications logiques"
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
ms.openlocfilehash: 9a836b707a576b9a938f43397ef35c00aeb476bf
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="create-or-join-parallel-branches-in-your-logic-app"></a>Créer ou joindre des branches parallèles dans votre application logique

Par défaut, les actions d’une application logique s’exécutent de manière séquentielle. Pour effectuer des actions indépendantes en même temps, vous pouvez créer des [branches parallèles](#parallel-branches), puis [joindre ces branches](#join-branches) plus tard dans votre flux. 

> [!TIP] 
> Si vous disposez d’un déclencheur qui reçoit un tableau et souhaitez exécuter un flux de travail pour chaque élément du tableau, vous pouvez *dégrouper* ce tableau avec la [propriété de déclencheur **SplitOn**](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>configuration requise

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/). 

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-a-parallel-branch"></a>Ajouter une branche parallèle

Pour exécuter des étapes indépendantes en même temps, vous pouvez ajouter des branches parallèles en regard d’une étape existante. 

![Exécuter des étapes en parallèle](media/logic-apps-control-flow-branches/parallel.png)

Votre application logique attend que toutes les branches se terminent avant de poursuivre le flux de travail.
Les branches parallèles s’exécutent uniquement si leurs valeurs de propriété `runAfter` correspondent à l’état de l’étape parente terminée. Par exemple, les deux branches `branchAction1` et `branchAction2` sont définies pour s’exécuter uniquement lorsque l’étape `parentAction` est terminée avec l’état `Succeded`.

> [!NOTE]
> Avant que vous ne commenciez, votre application logique doit déjà disposer d’une étape dans laquelle vous pouvez ajouter des branches parallèles.

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, ouvrez votre application logique dans le Concepteur d’applications logiques.

2. Placez la souris sur la flèche au-dessus de l’étape où vous souhaitez ajouter des branches parallèles.

3. Cliquez sur le signe **plus** (**+**), choisissez **Ajouter une branche parallèle**, puis sélectionnez l’élément que vous souhaitez ajouter.

   ![Ajouter une branche parallèle](media/logic-apps-control-flow-branches/add-parallel-branch.png)

   L’élément que vous avez sélectionné apparaît désormais dans une branche parallèle.

4. Pour chaque branche parallèle, ajoutez les étapes de votre choix. Pour ajouter une action séquentielle à une branche parallèle, déplacez la souris sous l’action où vous souhaitez ajouter l’action séquentielle. Cliquez sur le signe **plus** (**+**) et sur l’étape que vous souhaitez ajouter.

   ![Ajouter une étape séquentielle à une branche parallèle](media/logic-apps-control-flow-branches/add-sequential-action-parallel-branch.png)

5. Pour fusionner à nouveau des branches, [joignez vos branches parallèles](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Définition de branche parallèle (JSON)

Si vous travaillez en mode Code, vous pouvez définir la structure parallèle dans la définition JSON de votre application logique à la place. Exemple :

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Joindre des branches parallèles

Pour fusionner des branches parallèles, ajoutez simplement une étape en bas, sous toutes les branches. Cette étape s’exécute à l’issue de l’exécution de toutes les branches parallèles.

![Joindre des branches parallèles](media/logic-apps-control-flow-branches/join.png)

1. Dans le [portail Azure](https://portal.azure.com), recherchez et ouvrez votre application logique dans le concepteur d’application logique. 

2. Sous les branches parallèles que vous souhaitez joindre, ajoutez l’étape que vous souhaitez effectuer.

   ![Ajouter une étape qui joint des branches parallèles](media/logic-apps-control-flow-branches/join-steps.png)

   À présent, les branches parallèles sont fusionnées.

<a name="join-json"></a>

## <a name="join-definition-json"></a>Définition de jointure (JSON)

Si vous travaillez en mode Code, vous pouvez définir la structure de jointure dans la définition JSON de votre application logique à la place. Exemple :

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des fonctionnalités et suggestions ou pour en soumettre, visitez le [site de commentaires des utilisateurs Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>étapes suivantes

* [Conditional statements: Run steps based on a condition in logic apps](../logic-apps/logic-apps-control-flow-conditional-statement.md) (Instructions conditionnelles : Exécuter des étapes en fonction d’une condition dans des applications logiques)
* [Switch statements: Run different steps based on specific values in logic apps](../logic-apps/logic-apps-control-flow-switch-statement.md) (Instructions switch : Exécuter différentes étapes en fonction de valeurs spécifiques dans des applications logiques)
* [Loops: Process arrays or repeat actions until a condition is met](../logic-apps/logic-apps-control-flow-loops.md) (Boucles : Traiter des tableaux ou répéter des actions jusqu’à ce qu’une condition soit remplie)
* [Scopes: Run steps based on group status in logic apps](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) (Étendues : Exécuter des étapes en fonction de l’état d’un groupe dans des applications logiques)