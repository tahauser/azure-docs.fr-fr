---
title: Créer une fonction dans Azure déclenchée par des messages de file d’attente | Microsoft Docs
description: Utilisez Azure Functions pour créer une fonction sans serveur appelée par un message soumis à une file d’attente de stockage Azure.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: d9ab8115ad273101a0a25a094436bc0a24a45e68
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Créer une fonction déclenchée par une file d’attente de stockage Azure

Apprenez à créer une fonction se déclenchant lorsque des messages sont envoyés à une file d’attente de stockage Azure.

![Affichage du message dans les journaux.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Prérequis


- Télécharger et installer l’[Explorateur de Stockage Microsoft Azure](http://storageexplorer.com/).

- Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-an-azure-function-app"></a>Création d’une application Azure Function

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App créée avec succès.](./media/functions-create-first-azure-function/function-app-create-success.png)

Créez ensuite une fonction dans la nouvelle Function App.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Créer une fonction déclenchée par une file d’attente

1. Développez votre Function App, puis cliquez sur le bouton **+** en regard de **Fonctions**. S’il s’agit de la première fonction de votre Function App, sélectionnez **Fonction personnalisée**. Cela affiche l’ensemble complet des modèles de fonction.

    ![Page de démarrage rapide des fonctions sur le portail Azure](./media/functions-create-storage-queue-triggered-function/add-first-function.png)

2. Dans la zone de recherche, saisissez `queue`, puis sélectionnez la langue souhaitée pour le modèle déclencheur de Stockage File d’attente.

    ![Sélectionnez le modèle déclencheur de file d’attente de stockage.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)

3. Utilisez les paramètres spécifiés dans le tableau sous l’image.
    ![Configurez la fonction déclenchée par la file d’attente de stockage.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-2.png)
    
    | Paramètre | Valeur suggérée | Description |
    |---|---|---|
    | **Name** | Unique dans votre Function App | Nom de cette fonction déclenchée par la file d’attente. |
    | **Nom de la file d’attente**   | myqueue-items    | Le nom de la file d’attente à connecter à votre compte de stockage. |
    | **Connexion au compte de stockage** | AzureWebJobStorage | Vous pouvez utiliser la connexion de compte de stockage qui est déjà utilisée par votre application de fonction, ou créez-en une.  |    

3. Cliquez sur **Créer** pour créer votre fonction.

Ensuite, connectez-vous à votre compte de stockage Azure et créez la file d’attente de stockage **myqueue-items**.

## <a name="create-the-queue"></a>Créer la file d’attente

1. Dans votre fonction, cliquez sur **Intégrer**, développez **Documentation** et copiez le **Nom du compte** et la **Clé du compte**. Vous utilisez ces informations d’identification pour vous connecter au compte de stockage dans l’Explorateur Stockage Azure. Si vous avez déjà connecté votre compte de stockage, passez à l’étape 4.

    ![Obtention des informations d’identification de connexion du compte de stockage.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)

1. Exécutez [l’Explorateur de stockage Microsoft Azure](http://storageexplorer.com/), cliquez sur l’icône de connexion situé sur la gauche, choisissez **Utiliser un nom et une clé de compte de stockage**, puis cliquez sur **Suivant**.

    ![Exécutez l’outil Explorateur de compte de stockage.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)

1. Saisissez le **Nom du compte** et la **Clé du compte** récupérés à l’étape 1, puis cliquez sur **Suivant** et sur **Connexion**.

    ![Entrez les informations d’identification de stockage et connectez-vous.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

1. Développez le compte de stockage attaché, cliquez avec le bouton droit sur **Files d’attente**, cliquez sur **Créer une file d’attente**, saisissez `myqueue-items` et appuyez sur Entrée.

    ![Créez une file d’attente de stockage.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Maintenant que vous disposez d’une file d’attente de stockage, vous pouvez tester la fonction en ajoutant un message à la file d’attente.

## <a name="test-the-function"></a>Tester la fonction

1. Dans le portail Azure, accédez à votre fonction, développez les **Journaux** en bas de la page et vérifiez que la diffusion de journaux n’est pas suspendue.

1. Dans l’Explorateur de stockage, développez votre compte de stockage, **Files d’attente**, et **myqueue-items**, puis cliquez sur **Ajouter message**.

    ![Ajoutez un message dans la file d’attente.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

1. Saisissez le message « Hello World ! » dans **Texte du message** et cliquez sur **OK**.

1. Attendez quelques secondes, puis retournez à vos journaux de fonction et vérifiez que le nouveau message a été lu à partir de la file d’attente.

    ![Affichage du message dans les journaux.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

1. Dans l’Explorateur de stockage, cliquez sur **Actualiser** et vérifiez que le message a été traité et qu’il ne se trouve plus dans la file d’attente.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une fonction qui s’exécute lorsqu’un message est ajouté à une file d’attente de stockage.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Pour en savoir plus sur les déclencheurs de stockage en file d’attente, consultez la page [Liaisons de file d’attente de stockage Azure Functions](functions-bindings-storage-queue.md).
