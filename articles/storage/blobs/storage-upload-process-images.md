---
title: "Charger des données d’image dans le cloud avec le Stockage Azure | Microsoft Docs"
description: "Utiliser le Stockage Blob Azure avec une application web pour stocker les données d’application"
services: storage
documentationcenter: 
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: e3c40d0f3db1a33a405a341a714a7ce199908ca4
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>Charger des données d’image dans le cloud avec le Stockage Azure

Ce didacticiel est la première partie d’une série d’étapes. Ce didacticiel vous montre comment déployer une application web qui utilise la bibliothèque de client du Stockage Azure pour charger des images sur un compte de stockage. À la fin, vous disposerez d’une application web qui stocke et affiche des images à partir du Stockage Azure.

![Affichage du conteneur d’images](media/storage-upload-process-images/figure2.png)

Dans ce premier volet, vous apprenez à :

> [!div class="checklist"]
> * Créez un compte de stockage.
> * Créer un conteneur et définir des autorisations
> * Récupérer une clé d’accès
> * Configurer les paramètres de l’application
> * Déployer une application web sur Azure
> * Interagir avec l’application web

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel exige que vous exécutiez Azure CLI version 2.0.4 ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Créer un groupe de ressources 

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.
 
L’exemple suivant crée un groupe de ressources nommé `myResourceGroup`.
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
 
L’exemple charge des images sur un conteneur Blob dans un compte de stockage Azure. Le compte de stockage Azure fournit un espace de noms unique pour stocker les objets de données de Stockage Azure et y accéder. Créez un compte de stockage dans le groupe de ressources que vous avez créé à l’aide de la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create). 

> [!IMPORTANT] 
> Dans la deuxième partie de ce didacticiel, vous utilisez des abonnements aux événements pour le Stockage Blob. Actuellement, ceux-ci ne sont pris en charge pour les comptes de Stockage Blob que dans l’Ouest du centre des États-Unis et les États-Unis de l’Ouest 2. En raison de cette restriction, vous devez créer un compte de stockage Blob, qui sera utilisé par l’exemple d’application pour stocker les images et les miniatures.   

Dans la commande suivante, indiquez le nom global unique de votre compte de stockage Blob dans l’espace réservé `<blob_storage_account>`.  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>Créer des conteneurs de Stockage Blob
 
L’application utilise deux conteneurs dans le compte de stockage Blob. Les conteneurs s’apparentent à des dossiers et servent à stocker les objets Blob. C’est dans le conteneur _images_ que l’application charge les images pleine résolution. Dans l’une des parties suivantes de la série, une application de fonction Azure charge les miniatures d’images redimensionnées dans le conteneur _thumbnails_. 

Récupérez la clé du compte de stockage avec la commande [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list). Vous utiliserez ensuite cette clé pour créer deux conteneurs avec la commande [az storage container create](/cli/azure/storage/container#az_storage_container_create).  
 
Dans ce cas, `<blob_storage_account>` est le nom du compte de stockage Blob que vous avez créé. L’accès public aux conteneurs _images_ est défini sur `off`, celui des conteneurs _thumbnails_ sur `container`. Le paramètre d’accès public `container` permet de rendre les miniatures visibles pour les personnes qui consultent la page web.
 
```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

Notez le nom et la clé de votre compte de stockage Blob. L’exemple d’application utilise ces paramètres pour se connecter au compte de stockage afin de charger les images. 

## <a name="create-an-app-service-plan"></a>Créer un plan App Service 

Un [plan App Service](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) spécifie l’emplacement, la taille et les fonctionnalités de la batterie de serveurs web qui héberge votre application. 

Créez un plan App Service avec la commande [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create). 

L’exemple suivant crée un plan App Service nommé `myAppServicePlan` dans le niveau tarifaire **Gratuit** : 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>Créer une application web 

L’application web offre un espace d’hébergement au code de l’exemple d’application qui est déployé à partir du référentiel d’exemples GitHub. Créez une [application web](../../app-service/app-service-web-overview.md) dans le plan App Service `myAppServicePlan` avec la commande [az webapp create](/cli/azure/webapp#az_webapp_create).  
 
Dans la commande suivante, remplacez`<web_app>` par un nom unique (les caractères autorisés sont `a-z`, `0-9` et `-`). Si `<web_app>` n’est pas une valeur unique, un message d’erreur s’affiche : _Un site web portant le nom `<web_app>` existe déjà_. L’URL par défaut de l’application web est `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Déployer l’exemple d’application à partir du référentiel GitHub 

App Service prend en charge plusieurs façons de déployer du contenu vers une application web. Dans ce didacticiel, vous déployez l’application web depuis un [exemple de référentiel GitHub public](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configurez le déploiement GitHub sur l’application web avec la commande [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config). Remplacez `<web_app>` par le nom de l’application web que vous avez créée à l’étape précédente.

L’exemple de projet contient une application [ASP.NET MVC](https://www.asp.net/mvc) qui accepte une image, l’enregistre dans un compte de stockage et affiche des images à partir d’un conteneur de miniatures. L’application web utilise les espaces de noms [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet)et [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) de la bibliothèque de client du Stockage Azure pour interagir avec le Stockage Azure. 

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>Configurer les paramètres de l’application web 

L’exemple d’application web utilise la [bibliothèque de client du Stockage Azure](/dotnet/api/overview/azure/storage?view=azure-dotnet) pour demander des jetons d’accès de requête, qui servent à charger des images. Les informations d’identification du compte de stockage utilisées par le Kit de développement logiciel (SDK) Stockage sont définies dans les paramètres de l’application web. Ajoutez des paramètres d’application à l’application déployée avec la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set). 

Dans la commande suivante, `<blob_storage_account>` est le nom de votre compte de stockage Blob et `<blob_storage_key>` la clé associée. Remplacez `<web_app>` par le nom de l’application web que vous avez créée à l’étape précédente.     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

Une fois l’application web déployée et configurée, vous pouvez tester la fonctionnalité de chargement d’images dans l’application.   

## <a name="upload-an-image"></a>Charger une image 

Pour tester l’application web, accédez à l’URL de l’application publiée. L’URL par défaut de l’application web est `https://<web_app>.azurewebsites.net`. Sélectionnez la zone **Charger des photos** pour sélectionner et charger un fichier, ou glissez-déplacez un fichier sur la zone. L’image disparaît si le chargement a réussi.

![Application ImageResizer](media/storage-upload-process-images/figure1.png)

Dans l’exemple de code, la tâche `UploadFiletoStorage` du fichier `Storagehelper.cs` sert à charger les images dans le conteneur `images` au sein du compte de stockage suivant la méthode [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet). L’exemple de code suivant contient la tâche `UploadFiletoStorage`. 

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

Les classes et méthodes suivantes sont utilisées dans la tâche précédente :

|Classe  |Méthode  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Vérifier que l’image s’affiche dans le compte de stockage

Connectez-vous au [Portail Azure](https://portal.azure.com). Dans le menu de gauche, sélectionnez **Comptes de stockage**, puis sélectionnez le nom de votre compte de stockage. Sous **vue d’ensemble**, sélectionnez le conteneur **images**.

Vérifiez que l’image s’affiche dans le conteneur.

![Affichage du conteneur d’images](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Tester l’affichage des miniatures

Pour tester l’affichage des miniatures, vous allez charger une image dans le conteneur de miniatures afin de vérifier que l’application peut lire le conteneur de miniatures.

Connectez-vous au [Portail Azure](https://portal.azure.com). Dans le menu de gauche, sélectionnez **Comptes de stockage**, puis sélectionnez le nom de votre compte de stockage. Sélectionnez **Conteneurs** sous **Service Blob** et sélectionnez le conteneur **thumbnails**. Cliquez sur **Charger** pour ouvrir le volet **Charger des objets Blob**.

Choisissez un fichier en utilisant le sélecteur de fichiers et sélectionnez **Charger**.

Revenez à votre application pour vérifier que l’image chargée dans le conteneur **thumbnails** est visible.

![Affichage du conteneur d’images](media/storage-upload-process-images/figure2.png)

Dans le conteneur **thumbnails**, sur le Portail Azure, sélectionnez l’image que vous avez chargée et sélectionnez **Supprimer** pour supprimer l’image. Dans la deuxième partie de la série, vous automatiserez la création d’images miniatures ; cette image de test n’est donc pas nécessaire.

CDN peut être activé pour mettre en cache du contenu à partir de votre compte de stockage Azure. Même si cette procédure n’est pas décrite dans ce didacticiel, vous pouvez consulter la page [Intégrer un compte de stockage Azure à Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md) pour savoir comment activer le CDN avec votre compte de stockage Azure.

## <a name="next-steps"></a>Étapes suivantes

Dans la première partie de la série, vous avez appris à configurer une application web qui interagit avec le stockage, et notamment à :

> [!div class="checklist"]
> * Créez un compte de stockage.
> * Créer un conteneur et définir des autorisations
> * Récupérer une clé d’accès
> * Configurer les paramètres de l’application
> * Déployer une application web sur Azure
> * Interagir avec l’application web

Passez à la deuxième partie de la série pour savoir comment utiliser Event Grid pour déclencher une fonction Azure afin de redimensionner une image.

> [!div class="nextstepaction"]
> [Utiliser Event Grid pour déclencher une fonction Azure afin de redimensionner une image chargée](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
