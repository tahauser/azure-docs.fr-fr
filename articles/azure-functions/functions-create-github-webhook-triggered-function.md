---
title: Création d’une fonction dans Azure déclenchée par un webhook GitHub | Microsoft Docs
description: Utilisez Azure Functions pour créer une fonction sans serveur appelée par un webhook GitHub.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 05ad567e407a6506222acdb66ab38c4cfab76e4b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Créer une fonction déclenchée par un webhook GitHub

Apprenez à créer une fonction qui est déclenchée par une demande webhook HTTP avec une charge utile GitHub.

![Fonction webhook GitHub déclenchée dans le Portail Azure](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Prérequis


+ Un compte GitHub avec au moins un projet.
+ Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-an-azure-function-app"></a>Création d’une application Azure Function

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App créée avec succès.](./media/functions-create-first-azure-function/function-app-create-success.png)

Créez ensuite une fonction dans la nouvelle Function App.

<a name="create-function"></a>

## <a name="create-a-github-webhook-triggered-function"></a>Créer une fonction de déclenchement de webhook GitHub

1. Développez votre Function App, puis cliquez sur le bouton **+** en regard de **Fonctions**. S’il s’agit de la première fonction de votre Function App, sélectionnez **Fonction personnalisée**. Cela affiche l’ensemble complet des modèles de fonction.

    ![Page de démarrage rapide des fonctions sur le portail Azure](./media/functions-create-github-webhook-triggered-function/add-first-function.png)

2. Dans la zone de recherche, saisissez `github`, puis sélectionnez la langue souhaitée pour le modèle déclencheur Webhook GitHub 

     ![Choisir le modèle déclencheur Webhook GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Saisissez un **Nom** pour votre fonction, puis sélectionnez **Créer**. 

     ![Configurer la fonction déclenchée de Webhook GitHub dans le portail Azure](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger-2.png) 

3. Dans votre nouvelle fonction, cliquez sur **</> Obtenir l’URL de fonction**, puis copiez et enregistrez les valeurs. Faite la même chose pour **</> Obtenir le secret GitHub**. Vous avez besoin de ces valeurs pour configurer le webhook dans GitHub.

    ![Examiner le code de fonction](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png)

Ensuite, vous créez le webhook dans votre référentiel GitHub.

## <a name="configure-the-webhook"></a>Configurer le webhook

1. Dans GitHub, accédez à un référentiel que vous possédez. Vous pouvez également utiliser l’un des référentiels que vous avez dupliqués. Si vous avez besoin de dupliquer un référentiel, utilisez <https://github.com/Azure-Samples/functions-quickstart>.

2. Choisissez **Paramètres** > **Options** et assurez-vous que l’option **Problèmes** est activée sous **Fonctionnalités**.

   ![Activer l’option Problèmes](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook.png)

1. Dans **Paramètres**, choisissez **Webhooks** > **Ajouter un Webhook**.

    ![Ajouter un webhook GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

1. Utilisez les paramètres spécifiés dans la table suivante, puis cliquez sur **Ajouter un Webhook** :

    ![Définir l’URL et le secret du webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

| Paramètre | Valeur suggérée | Description |
|---|---|---|
| **URL de charge utile** | Valeur copiée | Utilisez la valeur retournée par **<>/ Obtenir une fonction URL**. |
| **Type de contenu** | application/json | La fonction attend une charge utile JSON. |
| **Secret**   | Valeur copiée | Utilisez la valeur retournée par **<>/ Obtenir un secret GitHub**. |
| Déclencheurs d’événement | Je vais sélectionner les événements individuels | Nous voulons seulement déclencher des événements de problème sous forme de commentaire.  |
| | Problème sous forme de commentaire |  |

À ce stade, le webhook est configuré pour déclencher la fonction au moment de l’ajout d’un nouveau problème sous forme de commentaire.

## <a name="test-the-function"></a>Tester la fonction

1. Dans votre référentiel GitHub, ouvrez l’onglet **Problèmes** dans une nouvelle fenêtre de navigateur.

1. Dans la nouvelle fenêtre, cliquez sur **Nouveau problème**, saisissez un titre, puis cliquez sur **Envoyer nouveau problème**.

1. Dans le problème, entrez un commentaire et cliquez sur **Commentaire**.

    ![Ajoutez un problème GitHub sous forme de commentaire.](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png)

1. Revenez au portail et afficher les journaux. Vous devez voir une entrée de suivi avec le nouveau commentaire textuel.

     ![Afficher le commentaire textuel dans les journaux.](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une fonction qui est déclenchée lorsqu’une requête est reçue à partir d’un webhook GitHub.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Pour en savoir plus sur les déclencheurs webhook, consultez la page [Liaisons HTTP et webhook d’Azure Functions](functions-bindings-http-webhook.md).
