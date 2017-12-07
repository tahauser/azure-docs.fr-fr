---
title: "Utiliser Azure Event Grid pour automatiser le redimensionnement des images chargées | Microsoft Docs"
description: "Azure Event Grid peut être déclenché en cas de chargement d’objets blob dans le stockage Azure. Vous pouvez utiliser cette fonctionnalité pour envoyer des fichiers image chargés dans le stockage Azure vers d’autres services, tels qu’Azure Functions, en vue de les redimensionner ou de leur apporter d’autres améliorations."
services: event-grid
author: ggailey777
manager: cfowler
editor: 
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 22eafca56eb5677c63a833d298799b725c50f768
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>Automatiser le redimensionnement des images chargées à l’aide d’Event Grid

[Azure Event Grid](overview.md) est un service d’événement pour le cloud. Event Grid permet de créer des abonnements aux événements qui sont déclenchés par les services Azure ou des ressources tierces.  

Ce didacticiel constitue la deuxième partie d’une série de didacticiels sur le stockage. Il développe le [didacticiel précédent sur le stockage][previous-tutorial] en y ajoutant la génération automatique de miniatures sans serveur à l’aide d’Azure Event Grid et d’Azure Functions. Event Grid permet à [Azure Functions](..\azure-functions\functions-overview.md) de répondre aux événements de [Stockage Blob Azure](..\storage\blobs\storage-blobs-introduction.md) et de générer les miniatures d’images chargées. Un abonnement est créé pour l’événement de création de Stockage Blob. Lorsqu’un objet blob est ajouté à un conteneur de stockage d’objets blob, un point de terminaison de fonction est appelé. Les données passées à la liaison de fonction à partir d’Event Grid sont utilisées pour accéder à l’objet blob et pour générer l’image miniature. 

Pour ajouter la fonctionnalité de redimensionnement à une application existante de chargement d’images, utilisez Azure CLI et le portail Azure.

![Application web publiée dans le navigateur Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un compte de stockage Azure général
> * Déployer du code sans serveur à l’aide d’Azure Functions
> * Créer un abonnement d’événement Stockage Blob dans Event Grid

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel :

+ Vous devez avoir suivi entièrement le didacticiel précédent sur Stockage Blob intitulé [Charger des données d’image dans le cloud avec Stockage Azure][previous-tutorial]. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser localement l’interface de ligne de commande, vous devez exécuter Azure CLI version 2.0.14 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Si vous n’utilisez pas Cloud Shell, vous devez d’abord vous connecter à l’aide de `az login`.

## <a name="create-an-azure-storage-account"></a>Création d'un compte Azure Storage

Azure Functions nécessite un compte de stockage général. Créez un compte de stockage général distinct dans le groupe de ressources à l’aide de la commande [az storage account create](/cli/azure/storage/account#create).

Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. 

Dans la commande suivante, indiquez le nom global unique de votre compte de stockage général dans l’espace réservé `<general_storage_account>`. 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>Créer une Function App  

Vous devez disposer d’une application de fonction pour héberger l’exécution de votre fonction. La Function App fournit un environnement d’exécution sans serveur de votre code de fonction. Créez une Function App à l’aide de la commande [az functionapp create](/cli/azure/functionapp#create). 

Dans la commande suivante, indiquez le nom unique de votre application de fonction dans l’espace réservé `<function_app>`. La valeur `<function_app>` est utilisée en tant que domaine DNS par défaut pour la Function App. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Dans ce cas, `<general_storage_account>` est le nom du compte de stockage général que vous avez créé.  

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

Maintenant, vous devez configurer l’application de fonction pour la connecter à Stockage Blob. 

## <a name="configure-the-function-app"></a>Configurer l’application de fonction

La fonction nécessite que la chaîne de connexion se connecte au compte de stockage Blob. Dans ce cas, `<blob_storage_account>` est le nom du compte de stockage Blob que vous avez créé dans le didacticiel précédent. Pour afficher la chaîne de connexion, exécutez la commande [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Le nom du conteneur d’images miniatures doit également être défini sur `thumbs`. Ajoutez ces paramètres d’application dans l’application de fonction à l’aide de la commande [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbs
```

Vous pouvez désormais déployer un projet de code de fonction dans cette application de fonction.

## <a name="deploy-the-function-code"></a>Déployer le code de fonction 

La fonction C# qui effectue le redimensionnement de l’image est disponible dans ce [dépôt d’exemples GitHub](https://github.com/Azure-Samples/function-image-upload-resize). Déployez ce projet de code de fonction dans l’application de fonction à l’aide de la commande [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config). 

Dans la commande suivante, `<function_app>` est l’application de fonction que vous avez créée dans le script précédent.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

La fonction de redimensionnement d’image est déclenchée par un abonnement à l’événement de création d’objet blob. Les données passées au déclencheur incluent l’URL de l’objet blob, qui est ensuite passée à la liaison d’entrée pour obtenir de l’image chargée à partir du stockage Blob. La fonction génère une image miniature et écrit le flux résultant dans un conteneur distinct du stockage Blob. Pour plus d’informations sur cette fonction, consultez le [fichier Lisez-moi dans le dépôt d’exemples](https://github.com/Azure-Samples/function-image-upload-resize/blob/master/README.md).
 
Le code de projet de fonction est déployé directement à partir du dépôt d’exemples publics. Pour plus d’informations sur les options de déploiement Azure Functions, consultez [Déploiement continu pour Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-your-event-subscription"></a>Créer un abonnement à un événement

Un abonnement d’événement indique les événements générés par le fournisseur que vous souhaitez envoyer vers un point de terminaison spécifique. Dans ce cas, le point de terminaison est exposé par votre fonction. Pour créer un abonnement d’événement à partir de votre fonction dans le portail Azure, effectuez les étapes suivantes : 

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur la flèche située en bas à gauche de l’écran pour développer tous les services, tapez `functions` dans le champ **Filtre**, puis sélectionnez **Applications de fonctions**. 

    ![Accéder aux applications de fonctions dans le portail Azure](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Développez votre application de fonction, choisissez la fonction **imageresizerfunc**, puis sélectionnez **Ajouter un abonnement Event Grid**.

    ![Accéder aux applications de fonctions dans le portail Azure](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Utilisez les paramètres d’abonnement d’événement, comme spécifié dans le tableau.

    ![Créer un abonnement d’événement à partir de la fonction dans le portail Azure](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | imageresizersub | Nom du nouvel abonnement d’événement. | 
    | **Type de rubrique** |  Comptes de stockage | Choisissez le fournisseur d’événements de compte de stockage. | 
    | **Abonnement** | Votre abonnement | Par défaut, votre abonnement actuel doit être sélectionné.   |
    | **Groupe de ressources** | myResourceGroup | Sélectionnez **Utiliser l’existant**, puis choisissez le groupe de ressources que vous avez utilisé dans cette rubrique.  |
    | **Instance** |  `<blob_storage_account>` |  Choisissez le compte de stockage Blob que vous avez créé. |
    | **Types d’événements** | BlobCreated | Décochez tous les types autres que **BlobCreated**. Seuls les types d’événements de `Microsoft.Storage.BlobCreated` sont passés à la fonction.| 
    | **Point de terminaison de l’abonné** | autogenerated | Utilisez l’URL de point de terminaison qui est générée automatiquement. | 
    | **Filtre de préfixe** | /blobServices/default/containers/images/blobs/ | Filtre les événements de stockage pour n’afficher que ceux concernant le conteneur **images**.| 

4. Cliquez sur **Créer** pour ajouter l’abonnement d’événement. Cela crée un abonnement d’événement qui déclenche **imageresizerfunc** lorsqu’un objet blob est ajouté au conteneur **images**. Les images redimensionnées sont ajoutées au conteneur **thumbs**.

Maintenant que les services backend sont configurés, testez la fonctionnalité de redimensionnement d’images dans l’exemple d’application web. 

## <a name="test-the-sample-app"></a>Tester l’exemple d’application

Pour tester le redimensionnement d’images dans l’application web, accédez à l’URL de l’application publiée. L’URL par défaut de l’application web est `https://<web_app>.azurewebsites.net`.

Cliquez sur la zone **Charger des photos** pour sélectionner et charger un fichier. Vous pouvez également faire glisser une photo dans cette zone. 

Notez que, lorsque l’image chargée disparaît, une copie de l’image chargée est affichée dans le carrousel **Miniatures générées**. Cette image a été redimensionnée par la fonction, ajoutée au conteneur thumbs et téléchargée par le client web. 

![Application web publiée dans le navigateur Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un compte de stockage Azure général
> * Déployer du code sans serveur à l’aide d’Azure Functions
> * Créer un abonnement d’événement Stockage Blob dans Event Grid

Passez à la troisième partie de la série de didacticiels sur le stockage pour savoir comment sécuriser l’accès au compte de stockage.

> [!div class="nextstepaction"]
> [Sécuriser l’accès aux données d’une application dans le cloud](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)


+ Pour plus d’informations sur Event Grid, consultez [Présentation d’Event Grid](overview.md). 
+ Pour essayer un autre didacticiel utilisant Azure Functions, consultez [Créer une fonction qui s’intègre à Azure Logic Apps](..\azure-functions\functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md