---
title: "Gérer l’expiration de contenu web dans Azure Content Delivery Network | Microsoft Docs"
description: "Découvrez les options de contrôle de la durée de vie des objets blob dans la mise en cache Azure CDN."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: 8c15d198e92b1478b84b2140df416df3909ba141
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>Gérer l’expiration de contenu web dans Azure Content Delivery Network
> [!div class="op_single_selector"]
> * [Contenu web Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [stockage d’objets blob Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

Le [service Stockage Blob](../storage/common/storage-introduction.md#blob-storage) dans Stockage Azure fait partie des différentes origines Azure intégrées à Azure Content Delivery Network (CDN). Tout contenu d’objet BLOB publiquement accessible peut être mis en cache dans Azure CDN jusqu’à l’expiration de sa durée de vie. La durée de vie est déterminée par l’en-tête `Cache-Control`dans la réponse HTTP du serveur d’origine. Cet article décrit plusieurs façons de définir l’en-tête `Cache-Control` d’un blob dans Stockage Azure.

> [!TIP]
> Vous pouvez choisir de ne pas définir de durée de vie sur un objet blob. Dans ce cas, Azure CDN applique automatiquement une durée de vie de sept jours par défaut. Cette valeur TTL par défaut s’applique uniquement aux optimisations de remise web générales. Pour optimiser les fichiers volumineux, la durée de vie par défaut est de 1 jour, et pour les optimisations du streaming multimédia, la durée de vie par défaut est de 1 an.
> 
> Pour plus d’informations sur la façon dont Azure CDN accélère l’accès aux fichiers et à d’autres ressources, consultez [Vue d’ensemble d’Azure Content Delivery Network](cdn-overview.md).
> 
> Pour plus d’informations sur le stockage Blob Azure, consultez [Présentation du Stockage Blob](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Définition d’en-têtes Cache-Control à l’aide de PowerShell
[Azure PowerShell](/powershell/azure/overview) est l’un des moyens les plus rapides et puissants de gérer vos services Azure. Utilisez l’applet de commande `Get-AzureStorageBlob` pour obtenir une référence à l’objet BLOB, puis définissez la propriété `.ICloudBlob.Properties.CacheControl`. 

Par exemple :

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Vous pouvez également utiliser PowerShell pour [gérer vos profils et points de terminaison CDN](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Définition d’en-têtes Cache-Control à l’aide de .NET
Pour définir l’en-tête `Cache-Control` d’un objet blob à l’aide de code .NET, utilisez la [bibliothèque cliente Stockage Azure pour .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) pour définir la propriété [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx).

Par exemple :

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> D’autres exemples de code .NET sont disponibles dans les [exemples Stockage Blob Azure pour .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Définition d’en-têtes Cache-Control à l’aide d’autres méthodes

### <a name="azure-storage-explorer"></a>Explorateur de stockage Azure
Avec l’[Explorateur Stockage Microsoft Azure](https://azure.microsoft.com/en-us/features/storage-explorer/), vous pouvez afficher et modifier vos ressources de stockage blob, y compris les propriétés telles que *CacheControl*. 

### <a name="azure-command-line-interface"></a>Interface de ligne de commande Azure
Lorsque vous chargez un objet blob, vous pouvez définir la propriété *cacheControl* avec le commutateur `-p` dans l’[Interface de ligne de commande Azure](../cli-install-nodejs.md). L’exemple suivant montre comment définir la durée de vie sur 1 heure (3 600 secondes) :
  
    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```

### <a name="azure-storage-services-rest-api"></a>API REST des services Stockage Azure
Vous pouvez utiliser l’[API REST des services Stockage Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx) pour définir explicitement la propriété *x-ms-blob-cache-control* en utilisant les opérations suivantes sur une demande :
  
   - [Put Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx)
   - [Put Block List](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)
   - [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## <a name="testing-the-cache-control-header"></a>Test de l’en-tête Cache-Control
Vous pouvez facilement vérifier la durée de vie de vos objets blob. Avec les [outils de développement](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) de votre navigateur, vérifiez que votre objet blob comprend l’en-tête de réponse `Cache-Control`. Vous pouvez également utiliser un outil tel que [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/) ou [Fiddler](http://www.telerik.com/fiddler) pour examiner les en-têtes de réponse.

## <a name="next-steps"></a>Étapes suivantes
* [Comment gérer l’expiration des contenus de service cloud dans Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)

