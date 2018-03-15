---
title: "Instructions switch - Exécuter des étapes en fonction de valeurs spécifiques - Azure Logic Apps | Microsoft Docs"
description: "Exécuter des étapes différentes en fonction des valeurs d’objets, d’expressions ou de jetons dans des applications logiques"
services: logic-apps
keywords: instruction switch
author: ecfan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: e1f515189be8a5659af0f6c29b3fac0550abc9f9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="switch-statements-run-different-steps-based-on-specific-values-in-logic-apps"></a>Instructions switch : exécuter des étapes différentes en fonction de valeurs spécifiques dans des applications logiques

Pour effectuer des étapes différentes en fonction des valeurs d’un objet, d’une expression ou d’un jeton, utilisez une instruction *switch*. Cette structure évalue l’objet, l’expression ou le jeton, choisit le cas qui correspond au résultat et exécute les étapes pour ce cas seulement. Lors de l’exécution de l’instruction switch, un seul cas doit correspondre au résultat.

Par exemple, supposez que vous souhaitiez une application logique qui prenne des étapes différentes selon l’option sélectionnée dans l’e-mail. Dans cet exemple, l’application logique vérifie le flux RSS d’un site web pour y rechercher un nouveau contenu. Si un nouvel élément s’affiche dans le flux RSS, l’application logique envoie un e-mail à un approbateur. Selon que l’approbateur sélectionne « Approuver » ou « Rejeter », l’application logique suit des étapes différentes.

> [!TIP]
> Comme dans tous les langages de programmation, les instructions switch ne prennent en charge que les opérateurs d’égalité. Si vous avez besoin d’autres opérateurs de relation, par exemple « supérieur à », utilisez une [instruction de condition](#conditions).
> Pour garantir un comportement d’exécution déterministe, les cas doivent contenir une valeur statique unique et non des jetons ou des expressions dynamiques.

## <a name="prerequisites"></a>configuration requise

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/).

* Pour suivre l’exemple présenté dans cet article, [créez cet exemple d’application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md) avec un compte Outlook.com ou Office 365 Outlook.

  1. Si vous ajoutez l’action dans l’e-mail d’envoi, sélectionnez plutôt **Envoyer un e-mail d’approbation**.

     ![Sélectionner « Envoyer un e-mail d’approbation »](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  2. Renseignez les champs obligatoires, tels que l’adresse e-mail de la personne qui reçoit l’e-mail d’approbation. 
  Sous **Options utilisateur**, entrez « Approuver, Rejeter ».

     ![Entrer les détails de l’e-mail](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-a-switch-statement"></a>Ajouter une instruction switch

1. À la fin de l’exemple de flux de travail, choisissez **+Nouvelle étape** > **... Plus** > **Ajouter un cas switch**. 

   ![Ajouter une instruction switch](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Une instruction switch s’affiche avec un cas et un cas par défaut. 
   Une instruction switch requiert au moins un cas plus le cas par défaut. 

   Si vous souhaitez ajouter une instruction switch entre les étapes, placez le pointeur sur la flèche là où vous souhaitez l’ajouter. 
   Cliquez sur le **signe plus** (**+**) qui s’affiche, puis choisissez **Ajouter un cas switch**.

4. Dans la zone **Activé**, sélectionnez le champ **SelectedOption** dont la sortie détermine l’action à effectuer. 
   
   Dans la liste **Ajouter du contenu dynamique** qui s’affiche, vous pouvez sélectionner le champ.

5. Pour gérer les cas dans lesquels l’approbateur sélectionne `Approve` ou `Reject`, ajoutez un autre cas entre **Cas** et **Par défaut**. 
   
6. Ajoutez ces actions aux cas correspondants :

   | N° de cas | **SelectedOption** | Action |
   |:------ |:-------------------|:------ |
   | Cas 1 | **Approuver** | Ajoutez l’action **Envoyer un e-mail** Outlook pour envoyer des détails sur l’élément RSS uniquement si l’approbateur a sélectionné **Approuver**. |
   | Cas 2 | **Rejeter** | Ajoutez l’action **Envoyer un e-mail** Outlook pour informer les autres approbateurs que l’élément RSS a été rejeté. |
   | Default | \<Aucune\> | Aucune action requise. Dans cet exemple, le cas **Par défaut** n’est pas renseigné, car **SelectedOption** possède uniquement deux options. |
   |         |          |

   ![Instruction switch](./media/logic-apps-control-flow-switch-statement/switch.png)

7. Enregistrez votre application logique. 

   Pour tester manuellement cet exemple, choisissez **Exécuter** jusqu’à ce que l’application logique trouve un nouvel élément RSS et envoie un e-mail d’approbation. 
   Sélectionnez **Approuver** pour observer les résultats.

## <a name="json-definition"></a>Définition JSON

Maintenant que vous avez créé une application logique avec une instruction switch, examinons la définition de code générale derrière l’instruction switch.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case" : {
         "actions" : {
           "Send_an_email": { }
         },
         "case" : "Approve"
      },
      "Case_2" : {
         "actions" : {
           "Send_an_email_2": { }
         },
         "case" : "Reject"
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

| Étiquette              | DESCRIPTION |
| :----------------- | :---------- |
| `"Switch"`         | Nom de l’instruction switch que vous pouvez modifier pour plus de lisibilité. |
| `"type": "Switch"` | Indique que l’action est une instruction switch. |
| `"expression"`     | Dans cet exemple, spécifie l’option sélectionnée par l’approbateur qui est confrontée à chacun des cas déclarés plus loin dans la définition. |
| `"cases"` | Définit un nombre quelconque de cas. Pour chaque cas, `"Case_*"` est le nom par défaut du cas donné, que vous pouvez modifier pour plus de lisibilité. |
| `"case"` | Spécifie la valeur du cas qui doit être une valeur unique et constante utilisée par l’instruction switch pour la comparaison. Si aucun cas ne correspond au résultat de l’expression switch, les actions de la section `"default"` sont exécutées.
|           |         |

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des fonctionnalités ou suggestions ou pour en soumettre, visitez le [site de commentaires des utilisateurs Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>étapes suivantes

* [Conditional statements: Run steps based on a condition in logic apps](../logic-apps/logic-apps-control-flow-conditional-statement.md) (Instructions conditionnelles : Exécuter des étapes en fonction d’une condition dans des applications logiques)
* [Loops: Process arrays or repeat actions until a condition is met](../logic-apps/logic-apps-control-flow-loops.md) (Boucles : Traiter des tableaux ou répéter des actions jusqu’à ce qu’une condition soit remplie)
* [Create or join parallel branches in your logic app](../logic-apps/logic-apps-control-flow-branches.md) (Créer ou joindre des branches parallèles dans votre application logique)
* [Scopes: Run steps based on group status in logic apps](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) (Étendues : Exécuter des étapes en fonction de l’état d’un groupe dans des applications logiques)