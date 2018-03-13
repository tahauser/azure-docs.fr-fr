---
title: "Ajouter des conditions pour exécuter les workflows - Azure Logic Apps | Microsoft Docs"
description: "Contrôlez le mode d’exécution des workflows dans Azure Logic Apps en ajoutant une logique conditionnelle, des déclencheurs, des actions et des paramètres."
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: estfan; LADocs
ms.openlocfilehash: cca13db916dc2560ff78d477a0faa241e032ab27
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2018
---
# <a name="use-logic-apps-features"></a>Utiliser les fonctionnalités des applications logiques

Dans une [rubrique précédente](../logic-apps/quickstart-create-first-logic-app-workflow.md), vous avez créé votre première application logique. Pour contrôler le workflow de votre application logique, vous pouvez définir des chemins d’accès différents pour l’exécution de votre application logique et spécifier comment traiter les données dans des tableaux, des collections et des lots. Vous pouvez inclure les éléments suivants dans le workflow de votre application logique :

* Les conditions et les [instructions switch](../logic-apps/logic-apps-switch-case.md) permettent à votre application logique d’exécuter différentes actions si certaines conditions sont remplies ou non.

* Les [boucles](../logic-apps/logic-apps-loops-and-scopes.md) permettent à votre application logique d’exécuter des étapes à plusieurs reprises. Par exemple, vous pouvez répéter des actions sur un tableau en utilisant une boucle **For_each**. Vous pouvez également répéter des actions jusqu’à ce qu’une condition soit remplie à l’aide d’une boucle **Until**.

* Les [étendues](../logic-apps/logic-apps-loops-and-scopes.md) permettent de regrouper des séries d’actions, par exemple pour mettre en œuvre la gestion des exceptions.

* La [décomposition](../logic-apps/logic-apps-loops-and-scopes.md) permet à votre application logique de démarrer des workflows distincts pour les éléments d’un tableau lorsque vous utilisez la commande **SplitOn**.

Cette rubrique présente d’autres concepts utiles pour la création de votre application logique :

* mode code pour modifier une application logique existante ;
* options de démarrage d’un flux de travail.

## <a name="conditions-run-steps-only-after-meeting-a-condition"></a>Conditions : exécuter des étapes uniquement lorsqu’une condition est remplie

Pour que votre application logique exécute des étapes uniquement lorsque les données remplissent des critères spécifiques, vous pouvez ajouter une condition qui compare les données du workflow à des champs ou des valeurs spécifiques.

Par exemple, supposons que vous disposez d’une application logique qui vous envoie un trop grand nombre d’e-mails pour des publications sur le flux RSS d’un site web. Vous pouvez ajouter une condition afin que votre application logique envoie un e-mail uniquement lorsque la nouvelle publication appartient à une catégorie spécifique.

1. Dans le [portail Azure](https://portal.azure.com), recherchez et ouvrez votre application logique dans le concepteur d’application logique.

2. Ajoutez une condition dans le workflow à l’emplacement souhaité. 

   Pour ajouter la condition entre des étapes existantes du workflow de l’application logique, déplacez le pointeur sur la flèche où vous voulez ajouter la condition. 
   Cliquez sur le **signe plus** (**+**), puis choisissez **Ajouter une condition**. Par exemple : 

   ![Ajouter une condition à l’application logique](./media/logic-apps-use-logic-app-features/add-condition.png)

   > [!NOTE]
   > Si vous souhaitez ajouter une condition à la fin de votre workflow actuel, accédez au bas de votre application logique et sélectionnez **+ Nouvelle étape**.

3. Maintenant, définissez la condition. Spécifiez le champ source à évaluer, l’opération à effectuer et la valeur ou le champ cible. Pour ajouter des champs existants à votre condition, utilisez la liste **Ajouter du contenu dynamique**.

   Par exemple : 

   ![Modifier la condition en mode de base](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode.png)

   Voici la condition complète :

   ![Condition complète](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Pour définir la condition dans le code, choisissez **Modifier en mode Avancé**. Par exemple : 
   > 
   > ![Modifier la condition dans le code](./media/logic-apps-use-logic-app-features/edit-condition-advanced-mode.png)

4. Sous **SI OUI** et **SI NON**, ajoutez les étapes à effectuer si la condition est remplie ou non.

   Par exemple : 

   ![Condition avec les chemins SI OUI et SI NON](./media/logic-apps-use-logic-app-features/condition-yes-no-path.png)

   > [!TIP]
   > Vous pouvez faire glisser des actions existantes dans les chemins **SI OUI** et **SI NON**.

5. Lorsque vous avez terminé, enregistrez votre application logique.

À présent, vous ne recevrez des e-mails que lorsque les publications remplissent votre condition.

## <a name="repeat-actions-over-a-list-with-foreach"></a>Répétition des actions sur une liste à l’aide de forEach

La boucle forEach spécifie un tableau sur lequel répéter une action. Le flux échouera s’il ne se présente pas sous la forme d’un tableau. Par exemple, si action1 génère un tableau de messages et que vous souhaitez envoyer chaque message, vous pouvez inclure cette instruction forEach dans les propriétés de votre action : `forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>Modification de la définition du code pour une application logique

Même si vous avez le concepteur d’applications logiques, vous pouvez modifier directement le code qui définit une application logique.

1. Dans la barre de commandes, choisissez **Mode Code**.

    Cela ouvre un éditeur complet qui affiche la définition que vous venez de modifier.

    ![Mode code](media/logic-apps-use-logic-app-features/codeview.png)

    Dans l'éditeur de texte, vous pouvez copier et coller les actions de votre choix dans la même application logique ou d'une application logique vers une autre. 
    Vous pouvez aussi ajouter ou supprimer facilement des sections entières de la définition et partager des définitions avec d'autres utilisateurs.

2. Pour enregistrer vos modifications, cliquez sur **Enregistrer**.

## <a name="parameters"></a>parameters

Certaines fonctionnalités de Logic Apps sont disponibles uniquement en mode code, les paramètres par exemple. Les paramètres simplifient la réutilisation des valeurs dans votre application logique. Par exemple, si vous avez une adresse de messagerie que vous souhaitez utiliser dans plusieurs actions, vous devez la définir en tant que paramètre.

Les paramètres constituent un bon moyen d'extraire des valeurs que vous êtes susceptible de modifier souvent. Ils sont particulièrement utiles quand vous devez substituer des paramètres dans différents environnements. Pour découvrir comment substituer des paramètres en fonction de l’environnement, consultez [Créer des définitions d’application logique](../logic-apps/logic-apps-author-definitions.md) et la [documentation de l’API REST](https://docs.microsoft.com/rest/api/logic).

L’exemple montre comment mettre à jour votre application logique existante pour utiliser des paramètres pour le terme de requête.

1. En mode code, recherchez l’objet `parameters : {}` et ajoutez un objet `currentFeedUrl` :

        "currentFeedUrl" : {
            "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
        }

2. Naviguez jusqu’à l’action `When_a_feed-item_is_published`, recherchez la section `queries` et remplacez la valeur de la requête par `"feedUrl": "#@{parameters('currentFeedUrl')}"`. 

    Pour joindre deux chaînes ou plus, vous pouvez également utiliser la fonction `concat`. 
    Par exemple, `"@concat('#',parameters('currentFeedUrl'))"` fonctionne de la même façon que ci-dessus.

3.  Une fois ces opérations effectuées, sélectionnez **Enregistrer**. 

    Vous pouvez maintenant modifier le flux RSS du site web en transmettant une autre URL via l’objet `currentFeedURL`.

En savoir plus sur la [création de définitions d’application logique](../logic-apps/logic-apps-author-definitions.md).

## <a name="start-logic-app-workflows"></a>Démarrage de workflows d’application logique

Vous avez plusieurs options pour démarrer le flux de travail défini dans votre application logique. Vous pouvez toujours démarrer un flux de travail à la demande dans le [portail Azure].

### <a name="recurrence-triggers"></a>Déclencheurs de périodicité

Un déclencheur de périodicité s'exécute selon un intervalle que vous spécifiez. Quand le déclencheur a une logique conditionnelle, il détermine si le flux de travail doit s'exécuter ou non. Un déclencheur indique que le workflow doit s’exécuter en retournant un code d’état `200` . Lorsque le workflow n’a pas besoin de s’exécuter, le déclencheur renvoie un code d’état `202`.

### <a name="callback-using-rest-apis"></a>Rappel à l'aide des API REST

Pour démarrer un workflow, les services peuvent appeler un point de terminaison d'application logique. Pour démarrer ce type d’application logique à la demande, choisissez **Exécuter maintenant** dans la barre de commandes. Consultez la page [Démarrage des flux de travail en appelant les points de terminaison en tant que déclencheurs](../logic-apps/logic-apps-http-endpoint.md). 

<!-- Shared links -->
[portail Azure]: https://portal.azure.com

## <a name="next-steps"></a>Étapes suivantes

* [Instructions switch](../logic-apps/logic-apps-switch-case.md) 
* [Boucles, étendues et décomposition](../logic-apps/logic-apps-loops-and-scopes.md)
* [Créer des définitions d’application logique](../logic-apps/logic-apps-author-definitions.md)