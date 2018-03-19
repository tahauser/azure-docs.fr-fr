---
title: "Sécuriser l’accès aux données d’une application dans le cloud avec le stockage Azure | Microsoft Docs"
description: "Utiliser les jetons SAS, le chiffrement et HTTPS pour sécuriser les données de votre application dans le cloud"
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: tutorial
ms.date: 03/06/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 66a5f7e6872a76c91f1f5f1a4b0b1973cb890b0f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Sécuriser l’accès aux données d’une application dans le cloud

Ce didacticiel est le troisième volet d’une série. Vous apprenez à sécuriser l’accès au compte de stockage. 

Dans ce troisième volet, vous apprenez à :

> [!div class="checklist"]
> * Utiliser des jetons SAS pour accéder à des images miniatures
> * Activer le chiffrement côté serveur
> * Activer le transport HTTPS uniquement

Le [stockage Blob Azure](../common/storage-introduction.md#blob-storage) fournit un service fiable pour stocker les fichiers des applications. Ce didacticiel est un prolongement de [la rubrique précédente][previous-tutorial] et décrit comment sécuriser l’accès à votre compte de stockage à partir d’une application web. À la fin de ce didacticiel, les images sont chiffrées et l’application web utilise des jetons SAS sécurisés pour accéder aux images miniatures.

## <a name="prerequisites"></a>Prérequis


Pour effectuer ce didacticiel, vous devez avoir terminé le didacticiel précédent sur le stockage : [Automatiser le redimensionnement des images chargées à l’aide d’Event Grid][previous-tutorial]. 

## <a name="set-container-public-access"></a>Définir un conteneur d’accès public

Dans ce volet de la série de didacticiels, les jetons SAS sont utilisés pour accéder aux miniatures. Dans cette étape, vous définissez l’accès public du conteneur de _miniatures_ sur `off`.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbs  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Configurer des jetons SAS pour les miniatures

Dans le premier volet de cette série de didacticiels, l’application web affichait des images à partir d’un conteneur public. Dans ce volet de la série, vous utilisez des jetons de [signature d’accès partagé (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) pour récupérer les images miniatures. Les jetons SAS vous permettent de fournir un accès limité à un conteneur ou un objet blob en fonction de l’adresse IP, du protocole, de l’intervalle de temps ou des droits autorisés.

Dans cet exemple, le dépôt de code source utilise la branche `sasTokens`, qui a un exemple de code mis à jour. Supprimez le déploiement GitHub existant avec la commande [az webapp deployment source delete](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_delete). Ensuite, configurez un déploiement GitHub local sur l’application web avec la commande [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config).  

Dans la commande suivante, `<web-app>` est le nom de votre application web.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

La branche `sasTokens` du dépôt met à jour le fichier `StorageHelper.cs`. Elle remplace la tâche `GetThumbNailUrls` par l’exemple de code ci-dessous. La tâche de mise à jour récupère les URL de miniature en définissant une stratégie [SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet) pour spécifier l’heure de début, l’heure d’expiration et des autorisations pour le jeton SAS. Après le déploiement, l’application web récupère les miniatures avec une URL à l’aide d’un jeton SAS. La tâche mise à jour est illustrée dans l’exemple suivant :
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    // Set the permission of the container to public
    await container.SetPermissionsAsync(new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

Les classes, propriétés et méthodes suivantes sont utilisées dans la tâche précédente :

|Classe  |properties| Méthodes  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     | |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.windowsazure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment?view=azure-dotnet)    | [Résultats](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[autorisations](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>Chiffrement côté serveur

Le [chiffrement du service de stockage (SSE) Azure](../common/storage-service-encryption.md) vous permet de protéger vos données. SSE chiffre les données au repos et assure le chiffrement, le déchiffrement et la gestion de clés. Toutes les données sont chiffrées à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)256 bits, l’un des algorithmes de chiffrement par blocs les plus puissants disponibles.

Le chiffrement du service de stockage chiffre automatiquement les données pour tous les niveaux de performance (Standard ou Premium), tous les modèles de déploiement (Azure Resource Manager et Classic) et tous les services de stockage Azure (blob, file d’attente, table et fichier). 

## <a name="enable-https-only"></a>Activer HTTPS uniquement

Pour vérifier que les demandes de données vers et à partir d’un compte de stockage sont sécurisées, vous pouvez limiter les demandes à HTTPS uniquement. Mettez à jour le protocole demandé par le compte de stockage à l’aide de la commande [az storage account update](/cli/azure/storage/account#az_storage_account_update).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Testez la connexion à l’aide de `curl` avec le protocole `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Maintenant que le transfert sécurisé est obligatoire, vous recevez le message d’erreur suivant :

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Étapes suivantes

Dans le troisième volet de la série, vous avez appris à sécuriser l’accès au compte de stockage, notamment :

> [!div class="checklist"]
> * Utiliser des jetons SAS pour accéder à des images miniatures
> * Activer le chiffrement côté serveur
> * Activer le transport HTTPS uniquement

Passer au quatrième volet de la série pour apprendre à surveiller et résoudre les problèmes d’une application de stockage cloud.

> [!div class="nextstepaction"]
> [Surveiller et résoudre les problèmes de stockage d’une application cloud](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json