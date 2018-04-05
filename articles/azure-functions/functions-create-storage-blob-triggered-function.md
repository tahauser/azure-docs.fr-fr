---
title: Créer une fonction dans Azure déclenchée par le stockage Blob | Microsoft Docs
description: Utilisez Azure Functions pour créer une fonction sans serveur appelée par les éléments ajoutés au stockage Blob Azure.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/27/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 928ea1dbb68206e128f0593ba15cb48935ab1ccf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Créer une fonction déclenchée par un stockage Blob Azure

Apprenez à créer une fonction déclenchée lorsque des fichiers sont chargés dans ou mis à jour dans le stockage Blob Azure.

![Affichage du message dans les journaux.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Prérequis


+ Télécharger et installer l’[Explorateur de Stockage Microsoft Azure](http://storageexplorer.com/).
+ Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-an-azure-function-app"></a>Création d’une application Azure Function

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App créée avec succès.](./media/functions-create-first-azure-function/function-app-create-success.png)

Créez ensuite une fonction dans la nouvelle Function App.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Créer une fonction déclenchée par le stockage Blob

1. Développez votre Function App, puis cliquez sur le bouton **+** en regard de **Fonctions**. S’il s’agit de la première fonction de votre Function App, sélectionnez **Fonction personnalisée**. Cela affiche l’ensemble complet des modèles de fonction.

    ![Page de démarrage rapide des fonctions sur le portail Azure](./media/functions-create-storage-blob-triggered-function/add-first-function.png)

2. Dans la zone de recherche, saisissez `blob`, puis sélectionnez la langue souhaitée pour le modèle déclencheur de stockage d’objets blob.

    ![Sélectionnez le modèle déclencheur de stockage d’objets blob.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)
 
3. Utilisez les paramètres spécifiés dans le tableau sous l’image.

    ![Création de la fonction déclenchée par le stockage Blob.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png)

    | Paramètre | Valeur suggérée | Description |
    |---|---|---|
    | **Name** | Unique dans votre Function App | Nom de cette fonction déclenchée par l’objet Blob. |
    | **Chemin d’accès**   | samples-workitems/{name}    | Emplacement du stockage Blob analysé. Le nom de fichier de l’objet Blob est transmis dans la liaison en tant que paramètre _name_.  |
    | **Connexion au compte de stockage** | AzureWebJobsStorage | Vous pouvez utiliser la connexion de compte de stockage qui est déjà utilisée par votre application de fonction, ou créez-en une.  |

3. Cliquez sur **Créer** pour créer votre fonction.

Ensuite, vous vous connectez à votre compte Stockage Azure et créez le conteneur **samples-workitems**.

## <a name="create-the-container"></a>Créer le conteneur

1. Dans votre fonction, cliquez sur **Intégrer**, développez **Documentation** et copiez le **Nom du compte** et la **Clé du compte**. Vous utilisez ces informations d’identification pour vous connecter au compte de stockage. Si vous avez déjà connecté votre compte de stockage, passez à l’étape 4.

    ![Obtention des informations d’identification de connexion du compte de stockage.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. Exécutez [l’Explorateur de stockage Microsoft Azure](http://storageexplorer.com/), cliquez sur l’icône de connexion situé sur la gauche, choisissez **Utiliser un nom et une clé de compte de stockage**, puis cliquez sur **Suivant**.

    ![Exécutez l’outil Explorateur de compte de stockage.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. Saisissez le **Nom du compte** et la **Clé du compte** récupérés à l’étape 1, puis cliquez sur **Suivant** et sur **Connexion**. 

    ![Saisie des informations d’identification de stockage et connexion.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Développez le compte de stockage attaché, cliquez avec le bouton droit sur **Conteneurs d’objets Blob**, puis sur **Créer un conteneur d’objets blob**, tapez `samples-workitems` et appuyez sur Entrée.

    ![Création d’une file d’attente de stockage.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Une fois que vous avez un conteneur d’objets blob, vous pouvez tester la fonction en chargeant un fichier dans le conteneur.

## <a name="test-the-function"></a>Tester la fonction

1. Dans le portail Azure, accédez à votre fonction, développez les **Journaux** en bas de la page et vérifiez que la diffusion de journaux n’est pas suspendue.

1. Dans l’explorateur Stockage, développez votre compte de stockage, **Conteneurs d’objets Blob** et **samples-workitems**. Cliquez sur **Charger**, puis sur **Charger des fichiers...**.

    ![Chargement d’un fichier dans le conteneur d’objets blob.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. Dans la boîte de dialogue **Charger des fichiers**, cliquez sur le champ **Fichiers**. Accédez à un fichier sur votre ordinateur local, par exemple un fichier image, sélectionnez-le, puis cliquez sur **Ouvrir** et sur **Charger**.

1. Revenez à vos journaux de fonction et vérifiez que l’objet blob a été lu.

   ![Affichage du message dans les journaux.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > Lorsque votre Function App s’exécute dans le plan de consommation par défaut, il peut s’écouler un délai de plusieurs minutes entre l’ajout ou la mise à jour de l’objet blob et le déclenchement de la fonction. Si vous exigez une faible latence pour vos fonctions déclenchées par des objets Blob, exécutez plutôt votre Function App dans un plan App Service.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une fonction qui s’exécute lorsqu’un objet blob est ajouté ou mis à jour dans le stockage Blob. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Pour plus d’informations sur les déclencheurs de stockage Blob, consultez [Liaisons de stockage Blob Azure Functions](functions-bindings-storage-blob.md).
