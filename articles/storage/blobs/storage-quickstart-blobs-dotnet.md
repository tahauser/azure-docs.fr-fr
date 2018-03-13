---
title: "Démarrage rapide Azure - Charger, télécharger et répertorier des objets blob dans Stockage Azure à l’aide de .NET | Microsoft Docs"
description: "Dans le cadre de ce démarrage rapide, vous créez un compte de stockage et un conteneur. Ensuite, vous utilisez la bibliothèque de client de stockage pour .NET, afin de charger un objet blob dans Stockage Azure, de télécharger un objet blob et de répertorier les objets blob dans un conteneur."
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: tamram
ms.openlocfilehash: 265691ff189c628156f234083645a4b2ca4b637b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide de .NET

Dans ce démarrage rapide, vous allez découvrir comment utiliser la bibliothèque cliente .NET pour le Stockage Azure afin de charger, télécharger et répertorier des objets blob de blocs dans un conteneur.

## <a name="prerequisites"></a>configuration requise

Pour effectuer ce démarrage rapide :

* Installez .NET core 2.0 pour [Linux](/dotnet/core/linux-prerequisites?tabs=netcore2x) ou [Windows](/dotnet/core/windows-prerequisites?tabs=netcore2x).

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

Dans l’application, vous devez fournir la chaîne de connexion de votre compte de stockage. Il est recommandé de stocker cette chaîne de connexion dans une variable d’environnement sur l’ordinateur local exécutant l’application. Suivez l’un des exemples ci-dessous, en fonction de votre système d’exploitation, pour créer la variable d’environnement.  Remplacez \<yourconnectionstring\> par votre chaîne de connexion.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```
### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

## <a name="run-the-sample"></a>Exécution de l'exemple

Cet exemple crée un fichier de test dans Mes Documents, le charge dans Stockage Blob, liste les objets blob dans le conteneur, puis télécharge le fichier avec un nouveau nom pour vous permettre de comparer les fichiers anciens et nouveaux. 

Accédez au répertoire de l’application, puis exécutez l’application avec la commande `dotnet run`.

```
dotnet run
```

Le résultat ressemble à l’exemple suivant :

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

Quand vous appuyez sur n’importe quelle touche pour continuer, le conteneur de stockage et les fichiers sont supprimés. Avant de continuer, recherchez les deux fichiers dans votre dossier Mes documents. Vous pouvez les ouvrir et constater qu’ils sont identiques. Copiez l’URL de l’objet blob en dehors de la fenêtre de console et collez-la dans un navigateur pour afficher le contenu du fichier dans Stockage Blob.

Vous pouvez également utiliser un outil comme l’[Explorateur Stockage Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour afficher les fichiers dans Stockage Blob. L’Explorateur Stockage Azure est un outil multiplateforme gratuit qui vous permet d’accéder aux informations de votre compte de stockage. 

Une fois que vous avez vérifié les fichiers, appuyez sur n’importe quelle touche pour terminer la démonstration et supprimer les fichiers de test. Maintenant que vous avec compris l’exemple, ouvrez le fichier Program.cs pour examiner le code. 

## <a name="understand-the-sample-code"></a>Découvrir l’exemple de code

Ensuite, nous allons parcourir l’exemple de code pas à pas pour vous montrer son fonctionnement.

### <a name="get-references-to-the-storage-objects"></a>Obtenir des références aux objets de stockage

La première chose à faire est de créer les références aux objets utilisés pour accéder et gérer Stockage Blob. Ces objets reposent l’un sur l’autre : chacun est utilisé par le suivant dans la liste.

* Créez une instance de l’objet [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet) pointant vers le compte de stockage.

* Créez une instance de l’objet [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet) pointant vers le service BLOB de votre compte de stockage.

* Créez une instance de l’objet [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet) qui représente le conteneur auquel vous accédez. Les conteneurs sont utilisés pour organiser vos objets blob de la même façon que vous utilisez des dossiers pour organiser vos fichiers sur votre ordinateur.

Une fois que vous avez le [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet), vous pouvez créer une instance de l’objet [CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet) qui pointe vers l’objet blob spécifique qui vous intéresse, et effectuer une opération de chargement, téléchargement, copie, etc.

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur les noms des conteneurs et des objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Dans cette section, vous créez une instance des objets, un conteneur, puis définissez des autorisations sur le conteneur pour les objets blob soient publics et accessibles par une simple URL. Le conteneur est appelé **quickstartblobs**.

Cet exemple utilise [CreateIfNotExists](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexists?view=azure-dotnet), car nous voulons créer un conteneur chaque fois que l’exemple est exécuté. Dans un environnement de production où vous utilisez le même conteneur pour toute l’application, il est préférable d’appeler une seule fois **CreateIfNotExists**. Ou vous pouvez créer le conteneur à l’avance pour ne pas avoir à le créer dans le code.

```csharp
// Load the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");
if (storageConnectionString == null)
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable name 'storageconnectionstring' with the actual storage " +
        "connection string as a value.");
}
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
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
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the location where the blob is going to go, then upload the file.
// Upload the file you created, use localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

Il existe plusieurs méthodes de chargement que vous pouvez utiliser avec Stockage Blob. Par exemple, si vous avez un flux de mémoire, vous pouvez utiliser la méthode UploadFromStreamAsync au lieu de UploadFromFileAsync.

Les objets blob de blocs peuvent être n’importe quel type de fichier texte ou binaire. Les objets blob de pages sont principalement utilisés pour les fichiers VHD utilisés pour stocker des machines virtuelles IaaS. Les objets blob d’ajout sont utilisés pour la journalisation, par exemple, quand vous voulez écrire dans un fichier et continuer à ajouter d’autres informations. La plupart des objets stockés dans Stockage Blob sont des objets blob de blocs.

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Vous pouvez obtenir la liste des fichiers du conteneur à l’aide de [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). Le code suivant récupère la liste des objets blob, puis effectue une itération sur ces derniers pour montrer les URI des objets blob rencontrés. Vous pouvez copier l’URI à partir de la fenêtre Commande et la coller dans un navigateur pour afficher le fichier.

Si vous avez moins de 5 000 objets blob dans le conteneur, tous les noms d’objets blob sont récupérés en un appel à ListBlobsSegmentedAsync. Si vous avez plus de 5 000 objets blob dans le conteneur, le service récupère la liste par groupes de 5 000 jusqu'à ce que tous les noms d’objets blob soient récupérés. La première fois que cette API est appelée, elle retourne les 5 000 premiers noms d’objet blob et un jeton de liaison. La deuxième fois, vous fournissez le jeton et le service récupère le groupe suivant de noms d’objet blob et ainsi de suite, jusqu'à la nullité du jeton de continuation, qui indique que tous les noms d’objet blob ont été récupérés.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
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
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des objets blob chargés dans ce guide de démarrage rapide, vous pouvez supprimer le conteneur à l’aide de [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Supprimez aussi les fichiers créés s’ils ne sont plus nécessaires.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et Stockage Blob Azure avec .NET. Pour en savoir plus sur l’utilisation de Stockage Blob, consultez le Guide pratique de Stockage Blob.

> [!div class="nextstepaction"]
> [Guide pratique des opérations Stockage Blob](storage-dotnet-how-to-use-blobs.md)

Pour obtenir d’autres exemples de code de Stockage Azure que vous pouvez télécharger et exécuter, consultez la liste [Exemples de Stockage Azure avec .NET](../common/storage-samples-dotnet.md).

Pour plus d’informations sur l’Explorateur Stockage et les objets blob, consultez [Gérer les ressources de stockage Blob Azure avec l’Explorateur Stockage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
