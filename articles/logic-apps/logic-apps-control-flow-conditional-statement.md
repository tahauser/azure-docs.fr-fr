---
title: "Instructions conditionnelles - Exécuter des étapes en fonction d’une condition - Azure Logic Apps | Microsoft Docs"
description: "Exécutez des étapes dans votre application logique uniquement lorsqu’une condition est remplie. Créer des arbres de décision qui exécutent des flux de travail basés sur des conditions spécifiées."
services: logic-apps
keywords: "instructions conditionnelles, arbres de décision"
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
ms.openlocfilehash: 486c1053f42ed3becc2c4b60accc993db7f24baa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="conditional-statements-run-steps-based-on-a-condition-in-logic-apps"></a>Instructions conditionnelles : Exécuter des étapes en fonction d’une condition dans des applications logiques

Pour exécuter des étapes uniquement après avoir transmis une condition spécifiée, utilisez une *instruction conditionnelle*. Cette structure compare les données de votre flux de travail à des champs ou des valeurs spécifiques. Vous pouvez ensuite définir différentes étapes à exécuter selon que les données remplissent ou non la condition. Vous pouvez imbriquer les conditions les unes dans les autres.

Par exemple, supposons que vous disposez d’une application logique qui envoie un trop grand nombre d’e-mails lorsque de nouveaux éléments s’affichent dans le flux RSS d’un site web. Vous pouvez ajouter une instruction conditionnelle pour envoyer un e-mail uniquement lorsque le nouvel élément inclut une chaîne spécifique. 

> [!TIP]
> Pour exécuter différentes étapes en fonction de diverses valeurs spécifiques, utilisez plutôt une [*instruction switch*](../logic-apps/logic-apps-control-flow-switch-statement.md).

## <a name="prerequisites"></a>configuration requise

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/).

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Pour suivre l’exemple présenté dans cet article, [créez cet exemple d’application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md) avec un compte Outlook.com ou Office 365 Outlook.

## <a name="add-a-condition"></a>Ajouter une condition

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, ouvrez votre application logique dans le concepteur d’application logique.

2. Ajoutez une condition à l’emplacement souhaité. 

   Pour ajouter une condition entre des étapes existantes, déplacez le pointeur sur la flèche où vous voulez ajouter la condition. Cliquez sur le **signe plus** (**+**) qui s’affiche, puis choisissez **Ajouter une condition**. Par exemple : 

   ![Ajouter une condition entre des étapes](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

   Quand vous souhaitez ajouter une condition à la fin de votre flux de travail, accédez au bas de votre application logique et sélectionnez **+ Nouvelle étape** > **Ajouter une condition**.

3. Sous **Condition**, créez votre condition. 

   1. Dans la zone de gauche, spécifiez les données ou le champ que vous souhaitez comparer.

      Dans la liste **Ajouter du contenu dynamique**, vous pouvez sélectionner des champs existants de votre application logique.

   2. Dans la liste du milieu, sélectionnez l’opération à effectuer. 
   3. Dans la zone de droite, spécifiez une valeur ou un champ comme critères à respecter.

   Par exemple : 

   ![Modifier la condition en mode de base](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode.png)

   Voici la condition complète :

   ![Condition complète](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Pour créer une condition plus avancée ou utiliser des expressions, choisissez **Edit in advanced mode** (Modifier en mode avancé). Vous pouvez utiliser des expressions définies par le [langage de définition de flux de travail](../logic-apps/logic-apps-workflow-definition-language.md).
   > 
   > Par exemple : 
   >
   > ![Modifier la condition dans le code](./media/logic-apps-control-flow-conditional-statement/edit-condition-advanced-mode.png)

5. Sous **SI OUI** et **SI NON**, ajoutez les étapes à effectuer si la condition est remplie ou non. Par exemple : 

   ![Condition avec les chemins SI OUI et SI NON](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Vous pouvez faire glisser des actions existantes dans les chemins **SI OUI** et **SI NON**.

6. Enregistrez votre application logique.

Désormais, cette application logique envoie un e-mail uniquement lorsque les nouveaux éléments du flux RSS remplissent la condition que vous avez définie.

## <a name="json-definition"></a>Définition JSON

Maintenant que vous avez créé une application logique avec une instruction conditionnelle, examinons la définition de code de niveau élevé derrière l’instruction conditionnelle.

``` json
"actions": {
  "myConditionName": {
    "type": "If",
    "expression": "@contains(triggerBody()?['summary'], 'Microsoft')",
    "actions": {
      "Send_an_email": {
        "inputs": { },
        "runAfter": {}
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées et suggestions de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>étapes suivantes

* [Switch statements: Run different steps based on specific values in logic apps](../logic-apps/logic-apps-control-flow-switch-statement.md) (Instructions switch : Exécuter différentes étapes en fonction de valeurs spécifiques dans des applications logiques)
* [Loops: Process arrays or repeat actions until a condition is met](../logic-apps/logic-apps-control-flow-loops.md) (Boucles : Traiter des tableaux ou répéter des actions jusqu’à ce qu’une condition soit remplie)
* [Create or join parallel branches in your logic app](../logic-apps/logic-apps-control-flow-branches.md) (Créer ou joindre des branches parallèles dans votre application logique)
* [Scopes: Run steps based on group status in logic apps](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) (Étendues : Exécuter des étapes en fonction de l’état d’un groupe dans des applications logiques)