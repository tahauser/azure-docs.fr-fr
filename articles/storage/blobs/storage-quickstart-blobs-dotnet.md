---
title: "Guide de démarrage rapide Azure - Transférer des objets vers/à partir de Stockage Blob Azure avec .NET | Microsoft Docs"
description: "Apprenez rapidement à transférer des objets vers/à partir de Stockage Blob Azure avec .NET"
services: storage
documentationcenter: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/10/2017
ms.author: tamram
ms.openlocfilehash: ca4cb2dea9cdd2e46c3aef042e525acdfc09de8e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Transférer des objets vers/à partir de Stockage Blob Azure avec .NET

Dans ce démarrage rapide, vous allez découvrir comment utiliser la bibliothèque cliente .NET pour le Stockage Azure afin de charger, télécharger et répertorier des objets blob de blocs dans un conteneur.

## <a name="prerequisites"></a>Composants requis

Pour effectuer ce démarrage rapide, vous devez installer [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) avec la charge de travail suivante :

- **Développement Azure**

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application

L’exemple d’application utilisé dans ce guide de démarrage rapide est une application console de base. 

Utilisez [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Cette commande clone le dépôt dans votre dossier git local. Pour ouvrir la solution Visual Studio, recherchez le dossier storage-blobs-dotnet-quickstart, ouvrez-le et double-cliquez sur storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage

Dans l’application, vous devez fournir la chaîne de connexion de votre compte de stockage. Ouvrez le fichier `app.config` à partir de l’Explorateur de solutions dans Visual Studio. Recherchez l'entrée `StorageConnectionString`. Dans le champ **valeur**, remplacez la valeur entière de la chaîne de connexion par celle que vous avez enregistrée à partir du portail Azure. Votre `storageConnectionString` doit être semblable à ce qui suit :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;
    AccountName=<NameHere>;
    AccountKey=<KeyHere>" />
  </appSettings>
</configuration>
```

## <a name="run-the-sample"></a>Exécution de l'exemple

Cet exemple crée un fichier de test dans Mes Documents, le charge dans Stockage Blob, liste les objets blob dans le conteneur, puis télécharge le fichier avec un nouveau nom pour vous permettre de comparer les fichiers anciens et nouveaux. 

Exécutez l’exemple en appuyant sur F5. La sortie s’affiche dans une fenêtre de console et ressemble à ce qui suit : 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

Quand vous appuyez sur n’importe quelle touche pour continuer, le conteneur de stockage et les fichiers sont supprimés. Avant de continuer, recherchez les deux fichiers dans votre dossier Mes documents. Vous pouvez les ouvrir et constater qu’ils sont identiques. Copiez l’URL de l’objet blob en dehors de la fenêtre de console et collez-la dans un navigateur pour afficher le contenu du fichier dans Stockage Blob.

Vous pouvez également utiliser un outil comme l’[Explorateur Stockage Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour afficher les fichiers dans Stockage Blob. L’Explorateur Stockage Azure est un outil multiplateforme gratuit qui vous permet d’accéder aux informations de votre compte de stockage. 

Une fois que vous avez vérifié les fichiers, appuyez sur n’importe quelle touche pour terminer la démonstration et supprimer les fichiers de test. Maintenant que vous avec compris l’exemple, ouvrez le fichier Program.cs pour examiner le code. 

## <a name="understand-the-sample-code"></a>Découvrir l’exemple de code

Ensuite, nous allons parcourir l’exemple de code pas à pas pour vous montrer son fonctionnement.

### <a name="get-references-to-the-storage-objects"></a>Obtenir des références aux objets de stockage

La première chose à faire est de créer les références aux objets utilisés pour accéder et gérer Stockage Blob. Ces objets reposent l’un sur l’autre : chacun est utilisé par le suivant dans la liste.

* Créez une instance de l’objet **CloudStorageAccount** pointant vers le compte de stockage. 

* Créez une instance de l’objet **CloudBlobClient** pointant vers le service BLOB de votre compte de stockage. 

* Créez une instance de l’objet **CloudBlobContainer** qui représente le conteneur auquel vous accédez. Les conteneurs sont utilisés pour organiser vos objets blob de la même façon que vous utilisez des dossiers pour organiser vos fichiers sur votre ordinateur.

Une fois que vous avez le **CloudBlobContainer**, vous pouvez créer une instance de l’objet **CloudBlockBlob** qui pointe vers l’objet blob spécifique qui vous intéresse, et effectuer une opération de chargement, téléchargement, copie, etc.

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur les noms des conteneurs et des objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Dans cette section, vous créez une instance des objets, un conteneur, puis définissez des autorisations sur le conteneur pour les objets blob soient publics et accessibles par une simple URL. Le conteneur est appelé **quickstartblobs**. 

Cet exemple utilise **CreateIfNotExists**, car nous voulons créer un conteneur chaque fois que l’exemple est exécuté. Dans un environnement de production où vous utilisez le même conteneur pour toute l’application, il est préférable d’appeler une seule fois **CreateIfNotExists**. Ou vous pouvez créer le conteneur à l’avance pour ne pas avoir à le créer dans le code.

```csharp
// Create a CloudStorageAccount instance pointing to your storage account.
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs");
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions();
permissions.PublicAccess = BlobContainerPublicAccessType.Blob;
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Les objets blob de blocs sont les plus couramment utilisés et nous les utilisons donc dans ce guide de démarrage rapide. 

Pour charger un fichier dans un objet blob, obtenez une référence à l’objet blob dans le conteneur cible. Une fois que vous avez la référence d’objet blob, vous pouvez y charger des données à l’aide de [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Cette opération crée l’objet blob s’il n’existe pas déjà, et le remplace s’il existe.

L’exemple de code crée un fichier local à utiliser pour le chargement et le téléchargement, en stockant le fichier à charger comme **fileAndPath** et le nom de l’objet blob dans **localFileName**. L'exemple suivant charge le fichier dans votre conteneur nommé **quickstartblobs**.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string fileAndPath = Path.Combine(localPath, localFileName);
File.WriteAllText(fileAndPath, "Hello, World!");

//Upload the file.
CloudBlockBlob blockBlob = container.GetBlockBlobReference(localFileName);
await blockBlob.UploadFromFileAsync(fileAndPath);
```

Il existe plusieurs méthodes de chargement que vous pouvez utiliser avec Stockage Blob. Par exemple, si vous avez un flux de mémoire, vous pouvez utiliser la méthode UploadFromStreamAsync au lieu de UploadFromFileAsync. 

Les objets blob de blocs peuvent être n’importe quel type de fichier texte ou binaire. Les objets blob de pages sont principalement utilisés pour les fichiers VHD utilisés pour stocker des machines virtuelles IaaS. Les objets blob d’ajout sont utilisés pour la journalisation, par exemple, quand vous voulez écrire dans un fichier et continuer à ajouter d’autres informations. La plupart des objets stockés dans Stockage Blob sont des objets blob de blocs.

### <a name="list-the-blobs-in-a-container"></a>Création d'une liste d'objets blob dans un conteneur

Vous pouvez obtenir la liste des fichiers du conteneur à l’aide de [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). Le code suivant récupère la liste des objets blob, puis effectue une itération sur ces derniers pour montrer les URI des objets blob rencontrés. Vous pouvez copier l’URI à partir de la fenêtre Commande et la coller dans un navigateur pour afficher le fichier.

Si vous avez moins de 5 000 objets blob dans le conteneur, tous les noms d’objets blob sont récupérés en un appel à ListBlobsSegmentedAsync. Si vous avez plus de 5 000 objets blob dans le conteneur, le service récupère la liste par groupes de 5 000 jusqu'à ce que tous les noms d’objets blob soient récupérés. La première fois que cette API est appelée, elle retourne les 5 000 premiers noms d’objet blob et un jeton de liaison. La deuxième fois, vous fournissez le jeton et le service récupère le groupe suivant de noms d’objet blob et ainsi de suite, jusqu'à la nullité du jeton de continuation, qui indique que tous les noms d’objet blob ont été récupérés. 

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    foreach(IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null);
```

### <a name="download-blobs"></a>Télécharger des objets blob

Téléchargez des objets blob sur votre disque local à l’aide de [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync).

Le code suivant télécharge l’objet blob chargé dans la section précédente et ajoute le suffixe « _DOWNLOADED » au nom de l’objet blob pour pouvoir voir les deux fichiers sur le disque local. 

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
string fileAndPath2 = fileAndPath.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", fileAndPath2);
await cloudBlockBlob.DownloadToFileAsync(fileAndPath2, FileMode.Create);
```

### <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des objets blob chargés dans ce guide de démarrage rapide, vous pouvez supprimer le conteneur à l’aide de [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Supprimez aussi les fichiers créés s’ils ne sont plus nécessaires.

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et Stockage Blob Azure avec .NET. Pour en savoir plus sur l’utilisation de Stockage Blob, consultez le Guide pratique de Stockage Blob.

> [!div class="nextstepaction"]
> [Guide pratique des opérations Stockage Blob](storage-dotnet-how-to-use-blobs.md)

Pour obtenir d’autres exemples de code de Stockage Azure que vous pouvez télécharger et exécuter, consultez la liste [Exemples de Stockage Azure avec .NET](../common/storage-samples-dotnet.md).

Pour plus d’informations sur l’Explorateur Stockage et les objets blob, consultez [Gérer les ressources de stockage Blob Azure avec l’Explorateur Stockage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
