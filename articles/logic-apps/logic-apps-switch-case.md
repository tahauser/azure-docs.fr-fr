---
title: "Instruction switch pour différentes actions dans Azure Logic Apps | Microsoft Docs"
description: "Choisissez les différentes actions à effectuer dans les applications logiques en fonction des valeurs d’une expression avec une instruction switch."
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
ms.date: 11/18/2016
ms.author: LADocs; estfan
ms.openlocfilehash: 8f11d18009d60ea5c74781ccef2ff7d811516750
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="perform-different-actions-in-logic-apps-with-a-switch-statement"></a>Effectuer différentes actions dans les applications logiques avec une instruction switch

Lorsque vous créez un flux de travail, vous êtes souvent amené à exécuter des actions différentes selon la valeur d’un objet ou d’une expression. Par exemple, vous pouvez avoir besoin que votre application logique se comporte différemment selon le code d’état d’une demande HTTP ou l’option sélectionnée dans un e-mail.

Vous pouvez utiliser une instruction switch pour mettre en place ces scénarios. Votre application logique peut évaluer un jeton ou une expression, et choisir le cas correspondant à la même valeur pour exécuter les actions spécifiées. L’instruction switch ne doit correspondre qu’à un seul cas de figure.

> [!TIP]
> Comme dans tous les langages de programmation, les instructions switch ne prennent en charge que les opérateurs d’égalité. Si vous avez besoin d’autres opérateurs relationnels, par exemple « supérieur à », utilisez une instruction de condition.
> Pour garantir un comportement d’exécution constant, les cas de figure doivent contenir une valeur statique unique et non des jetons ou une expression dynamiques.

## <a name="prerequisites"></a>configuration requise

- Un abonnement Azure actif. Si vous n’avez pas d’abonnement Azure actif, créez un [compte gratuit](https://azure.microsoft.com/free/) ou essayez [Logic Apps gratuitement](https://tryappservice.azure.com/).
- [Connaissance de base des applications logiques](logic-apps-overview.md)

## <a name="add-a-switch-statement-to-your-workflow"></a>Ajouter une instruction switch à un flux de travail

Pour illustrer le fonctionnement d’une instruction switch, cet exemple crée une application logique qui surveille les fichiers chargés sur Dropbox. Lorsque les nouveaux fichiers sont chargés, l’application logique envoie un e-mail à un approbateur qui décide s’il faut transférer ces fichiers vers SharePoint. L’application utilise une instruction switch qui effectue des actions différentes selon la valeur sélectionnée de l’approbateur.

1. Créez une application logique, puis sélectionnez ce déclencheur : **Dropbox - Lorsqu’un fichier est créé**.

   ![Utiliser le déclencheur Dropbox - When a file is created (Dropbox - Lorsqu’un fichier est créé)](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. Sous le déclencheur, ajoutez cette action : **Outlook.com - Envoyer un e-mail d’approbation**.

   > [!TIP]
   > Les applications logiques prennent également en charge les scénarios d’envoi d’un e-mail d’approbation à partir d’un compte Outlook Office 365.

   - Si vous ne disposez pas d’une connexion, vous êtes invité à en créer une.
   - Renseignez les champs obligatoires. Par exemple, sous **À**, spécifiez l’adresse e-mail à laquelle l’e-mail de l’approbateur sera envoyé.
   - Sous **Options utilisateur**, entrez `Approve, Reject`.

   ![Configurer la connexion](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Ajoutez une instruction switch.

   - Sélectionnez **+ Nouvelle étape** > **... Plus** > **Ajouter un cas switch**. 
   - Nous allons à présent sélectionner l’action à effectuer en fonction de la sortie `SelectedOptions` de l’action *Envoyer un e-mail d’approbation*. 
   Ce champ se trouve dans le sélecteur **Ajouter du contenu dynamique**.
   - Utilisez *Cas 1* pour gérer la sélection de `Approve` par l’approbateur.
     - En cas d’approbation, copiez le fichier d’origine dans SharePoint Online avec [l’action **SharePoint Online - Créer un fichier**](../connectors/connectors-create-api-sharepointonline.md).
     - Ajoutez une autre action au cas de figure pour notifier les utilisateurs qu’un nouveau fichier est disponible dans SharePoint.
   - Ajoutez un autre cas de figure pour gérer la sélection de `Reject` par l’utilisateur.
     - En cas de refus, envoyez un message électronique de notification informant les autres approbateurs que le fichier a été rejeté et qu’aucune action supplémentaire n’est requise.
   - `SelectedOptions` fournit uniquement deux options ; nous pouvons donc laisser vide le cas **Par défaut**.

   ![Instruction switch](./media/logic-apps-switch-case/switch.jpg)

   > [!NOTE]
   > Une instruction switch requiert au moins un cas de figure en plus du cas par défaut.

4. Après l’instruction switch, supprimez le fichier d’origine chargé sur la Dropbox en ajoutant cette action : **Dropbox - Supprimer le fichier**.

5. Enregistrez votre application logique. Testez votre application en chargeant un fichier sur Dropbox. Vous recevrez sous peu un e-mail d’approbation. Sélectionnez une option et observez le comportement.

   > [!TIP]
   > Découvrez comment [surveiller vos applications logiques](logic-apps-monitor-your-logic-apps.md).

## <a name="understand-the-code-behind-switch-statements"></a>Comprendre le code derrière les instructions switch

Maintenant que vous avez créé une application logique avec une instruction switch, examinons la définition de code derrière l’instruction switch.

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
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

* `"Switch"` est le nom de l’instruction switch, que vous pouvez modifier pour plus de lisibilité. 
* `"type": "Switch"` indique que l’action est une instruction switch. 
* `"expression"` est l’option sélectionnée par l’approbateur dans cet exemple ; elle est confrontée à chacun des cas déclarés plus loin dans la définition. 
* `"cases"` peut contenir une infinité de cas. Pour chaque cas, `"Case *"` est le nom par défaut du cas, que vous pouvez modifier pour plus de lisibilité. 
`"case"` spécifie l’étiquette du cas, utilisée par l’expression switch à des fins de comparaison. Il doit s’agir d’une valeur unique et constante. Si aucun des cas ne correspond à l’expression switch, les actions sous `"default"` sont exécutées.

## <a name="get-help"></a>Obtenir de l’aide

Pour poser des questions, répondre aux questions et voir ce que font les autres utilisateurs d’Azure Logic Apps, visitez le [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Afin d’améliorer Azure Logic Apps ainsi que les connecteurs, votez pour des idées ou soumettez-en sur le [site de commentaires utilisateur Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>étapes suivantes

- Apprendre à [ajouter des conditions](logic-apps-use-logic-app-features.md)
- En savoir plus sur la [gestion des erreurs et des exceptions](logic-apps-exception-handling.md)
- Découvrir d’autres [fonctionnalités en matière de langage de flux de travail](logic-apps-author-definitions.md)