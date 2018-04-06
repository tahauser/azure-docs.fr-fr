---
title: Prendre en main Stockage Blob et les services connectés de Visual Studio (ASP.NET Core) | Microsoft Docs
description: Comment prendre en main Stockage Blob Azure dans un projet ASP.NET Core Visual Studio après avoir créé un compte de stockage à l’aide des services connectés de Visual Studio
services: storage
documentationcenter: ''
author: kraigb
manager: ghogen
editor: ''
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: e3814533b955d5b6444692a7b565219d28002262
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Prendre en main Stockage Blob Azure et les services connectés de Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Cet article explique comment prendre en main Stockage Blob Azure dans Visual Studio, après avoir créé ou référencé un compte de stockage Azure dans un projet ASP.NET Core à l'aide de la fonctionnalité **Services connectés** de Visual Studio. L’opération **Services connectés** installe les packages NuGet appropriés pour accéder au stockage Azure de votre projet et ajoute la chaîne de connexion pour le compte de stockage aux fichiers de configuration de votre projet. (Pour des informations générales sur Azure Storage, consultez la [documentation relative au stockage](https://azure.microsoft.com/documentation/services/storage/).)

Azure Blob storage is a service for storing large amounts of unstructured data that can be accessed from anywhere in the world via HTTP or HTTPS. Les objets blob peuvent être de toutes tailles. Il peut s'agir d'images, de fichiers audio ou vidéo, de données brutes ou de fichiers de documents. Cet article explique comment prendre en main Stockage Blob après avoir créé un compte de stockage Azure à l'aide de la fonctionnalité **Services connectés** de Visual Studio dans un projet ASP.NET Core.

De la même manière que les fichiers résident dans des dossiers, le stockage des objets blob s'effectue dans des conteneurs. Après avoir créé un objet blob, créez un ou plusieurs conteneurs dans cet objet blob. Par exemple, dans un objet blob appelé « Scrapbook », vous pouvez créer des conteneurs nommés « images » pour stocker des photos et un autre nommé « audio » pour stocker des fichiers audio. Une fois que vous avez créé les conteneurs, vous pouvez y charger des fichiers individuels. Pour plus d’informations sur la manipulation des objets blob par programme, consultez la page [Démarrage rapide : charger, télécharger et lister des objets blob avec .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) .

Certaines des API Azure Storage sont asynchrones, et le code dans cet article suppose l'utilisation de méthodes asynchrones. Voir [Programmation asynchrone](https://docs.microsoft.com/dotnet/csharp/async) pour plus d'informations.

## <a name="access-blob-containers-in-code"></a>Accès aux conteneurs d'objets blob dans le code

Pour accéder aux objets blob de projets ASP.NET Core par programmation, vous devez ajouter le code suivant, s’il n'est pas déjà présent :

1. Ajoutez les instructions `using` requises :

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Obtenez un objet `CloudStorageAccount` qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir votre chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure :

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Utilisez un objet `CloudBlobClient` pour obtenir une référence `CloudBlobContainer` vers un conteneur de votre compte de stockage :

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Création d'un conteneur dans le code

Vous pouvez aussi utiliser l'objet `CloudBlobClient` pour créer un conteneur dans votre compte de stockage en appelant `CreateIfNotExistsAsync` :

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Pour rendre publics les fichiers présents dans le conteneur, configurez le conteneur afin de le rendre public :

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Charger un objet blob dans un conteneur

Pour télécharger un fichier blob vers un conteneur, obtenez la référence du conteneur et utilisez-la pour celle de l'objet blob. Ensuite, chargez n'importe quel flux de données dans cette référence en appelant la méthode `UploadFromStreamAsync`. Cette opération crée l’objet blob s'il n'existe pas, ou le remplace s’il existe. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Pour répertorier les objets blob dans un conteneur, commencez par obtenir une référence de conteneur, puis appelez la méthode `ListBlobsSegmentedAsync` pour récupérer les objets blob et/ou les répertoires qu'il contient. Pour accéder aux nombreuses propriétés et méthodes d'un objet `IListBlobItem` renvoyé, affectez-le à un objet `CloudBlockBlob`, `CloudPageBlob` ou `CloudBlobDirectory`. Si vous ne savez pas de quel type d’objet blob il s’agit, lancez une vérification de type pour déterminer la cible de l’appel.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
        }

        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
        }

        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }
} while (token != null);
```

Pour découvrir d’autres moyens de lister le contenu d’un conteneur d’objets blob, consultez la page [Démarrage rapide : charger, télécharger et lister des objets blob avec .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) .

## <a name="download-a-blob"></a>Télécharger un objet blob

Pour télécharger un objet blob, commencez par en obtenir la référence, puis appelez la méthode `DownloadToStreamAsync`. L’exemple ci-après utilise la méthode `DownloadToStreamAsync` pour transférer le contenu des objets blob vers un objet de flux que vous pouvez ensuite enregistrer sous la forme d’un fichier local.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Pour découvrir d’autres moyens d’enregistrer des objets blob en tant que fichiers, consultez la page [Démarrage rapide : charger, télécharger et lister des objets blob avec .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) .

## <a name="delete-a-blob"></a>Supprimer un objet blob

Pour supprimer un objet blob, commencez par en obtenir la référence, puis appelez la méthode `DeleteAsync` :

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
