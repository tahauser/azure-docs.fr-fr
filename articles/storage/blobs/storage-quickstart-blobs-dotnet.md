---
title: Démarrage rapide Azure - Charger, télécharger et répertorier des objets blob dans Stockage Azure à l’aide de .NET | Microsoft Docs
description: Dans le cadre de ce démarrage rapide, vous créez un compte de stockage et un conteneur. Ensuite, vous utilisez la bibliothèque de client de stockage pour .NET, afin de charger un objet blob dans Stockage Azure, de télécharger un objet blob et de répertorier les objets blob dans un conteneur.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: tamram
ms.openlocfilehash: b84a56996a335f8a137c4219c55b9878e39b5a3b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide de .NET

Dans ce démarrage rapide, vous allez découvrir comment utiliser la bibliothèque cliente .NET pour le Stockage Azure afin de charger, télécharger et répertorier des objets blob de blocs dans un conteneur.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis


Pour terminer ce démarre rapide, commencez par créer un compte de stockage Azure dans le [portail Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Si vous avez besoin d’aide pour créer un compte de stockage, consultez [Créez un compte de stockage](../common/storage-quickstart-create-account.md).

Ensuite, téléchargez et installez .NET Core 2.0 pour votre système d’exploitation. Si vous exécutez Windows, vous pouvez installer Visual Studio et utiliser .NET Framework. Vous pouvez aussi, si vous voulez, installer un éditeur à utiliser avec votre système d’exploitation.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- Installez [.NET Core pour Windows](https://www.microsoft.com/net/download/windows) ou [.NET Framework](https://www.microsoft.com/net/download/windows) (inclus avec Visual Studio pour Windows)
- Installez [Visual Studio pour Windows](https://www.visualstudio.com/). Si vous utilisez .NET Core, l’installation de Visual Studio est facultative.  

Pour plus d’informations sur le choix entre .NET Core et .NET Framework, consultez [Choix entre .NET Core et .NET Framework pour les applications serveur](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- Installer [.NET Core pour Linux](https://www.microsoft.com/net/download/linux)
- Installer [Visual Studio Code](https://www.visualstudio.com/) et l’[extension C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068) (optionnel)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- Installer [.NET Core pour macOS](https://www.microsoft.com/net/download/macos)
- Installer [Visual Studio pour Mac](https://www.visualstudio.com/vs/visual-studio-mac/) (optionnel)

---

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application

L’exemple d’application utilisé dans ce guide de démarrage rapide est une application console de base. Vous pouvez explorer l’exemple d’application sur [GitHub](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart).

Utilisez [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Cette commande clone le dépôt dans votre dossier git local. Pour ouvrir la solution Visual Studio, recherchez le dossier storage-blobs-dotnet-quickstart, ouvrez-le et double-cliquez sur storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage

Pour exécuter l’application, vous devez fournir la chaîne de connexion de votre compte de stockage. Copiez votre chaîne de connexion depuis le portail Azure et écrivez-la dans une variable d’environnement. L’exemple lit la chaîne de connexion à partir de la variable d’environnement et l’utilise pour authentifier vos requêtes dans Stockage Azure.

### <a name="copy-your-connection-string-from-the-azure-portal"></a>Copiez votre chaîne de connexion à partir du portail Azure

Pour copier votre chaîne de connexion :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Recherchez votre compte de stockage.
3. Dans la section **Paramètres** de la présentation du compte de stockage, sélectionnez **Clés d’accès**.
4. Recherchez la valeur de **Chaîne de connexion** sous **clé1**, puis cliquez sur le bouton **Copier** pour copier la chaîne de connexion.  

    ![Capture d’écran montrant comment copier une chaîne de connexion à partir du portail Azure](media/storage-quickstart-blobs-dotnet/portal-connection-string.png)

## <a name="write-your-connection-string-to-an-environment-variable"></a>Écrire votre chaîne de connexion dans une variable d’environnement

Ensuite, écrivez la nouvelle variable d’environnement sur l’ordinateur local qui exécute l’application. Pour définir la variable d’environnement, ouvrez une fenêtre de console et suivez les instructions pour votre système d’exploitation. Remplacez `<yourconnectionstring>` par votre chaîne de connexion :

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Après avoir ajouté la variable d’environnement, vous devrez peut-être redémarrer tous les programmes en cours d’exécution qui devront la lire, y compris la fenêtre de console. Par exemple, si vous utilisez Visual Studio comme éditeur, redémarrez Visual Studio avant d’exécuter l’exemple. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

Après avoir ajouté la variable d’environnement, exécutez `source ~/.bashrc` depuis la fenêtre de console pour appliquer les changements.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Modifiez votre profil bash_profile, et ajoutez la variable d’environnement :

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

Après avoir ajouté la variable d’environnement, exécutez `source .bash_profile` depuis la fenêtre de console pour appliquer les changements.

---

## <a name="run-the-sample"></a>Exécution de l'exemple

Cet exemple crée un fichier de test dans votre dossier local **MyDocuments** et le charge dans votre stockage d’objets blob. L’exemple répertorie ensuite les objets blob du conteneur et télécharge le fichier avec un nouveau nom pour que vous puissiez comparer les deux fichiers. 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Si vous utilisez Visual Studio comme éditeur, vous pouvez appuyer sur **F5** pour exécuter. 

Sinon, accédez au répertoire de l’application, puis exécutez l’application avec la commande `dotnet run`.

```
dotnet run
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Accédez au répertoire de l’application, puis exécutez l’application avec la commande `dotnet run`.

```
dotnet run
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Accédez au répertoire de l’application, puis exécutez l’application avec la commande `dotnet run`.

```
dotnet run
```

---

Le résultat de l’exemple d’application ressemble à ce qui suit :

```
Azure Blob storage - .NET Quickstart sample

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the sample files and example container.
```

Quand vous appuyez sur la touche **Entrée**,l’application supprime le conteneur de stockage et les fichiers. Avant de les supprimer, consultez les deux fichiers dans votre dossier **MyDocuments**. Vous pouvez les ouvrir et constater qu’ils sont identiques. Copiez l’URL de l’objet blob depuis la fenêtre de console et collez-la dans un navigateur pour afficher son contenu.

Une fois que vous avez vérifié les fichiers, appuyez sur n’importe quelle touche pour terminer la démonstration et supprimer les fichiers de test. Maintenant que vous avec compris l’exemple, ouvrez le fichier Program.cs pour examiner le code. 

## <a name="understand-the-sample-code"></a>Découvrir l’exemple de code

Ensuite, explorez l’exemple de code pour comprendre son fonctionnement.

### <a name="try-parsing-the-connection-string"></a>Essayez d’analyser la chaîne de connexion.

L’exemple vérifie d’abord si la variable d’environnement contient une chaîne de connexion à analyser pour créer un objet [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) pointant vers le compte de stockage. Pour vérifier si la chaîne de connexion est valide, utilisez la méthode [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse). Si la méthode **TryParse** réussit, la variable *storageAccount* est initialisée et la valeur **true** est retournée.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell or application needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>Créer le conteneur et définir les autorisations

Ensuite, l’exemple crée un conteneur et définit ses autorisations afin que tous les objets blob du conteneur soient publics. Si un objet blob est public, n’importe quel client peut y accéder de façon anonyme.

Pour créer le conteneur, créez d’abord une instance de l’objet [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient) pointant vers le stockage d’objets blob de votre compte de stockage. Ensuite, créez une instance de l’objet [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer) puis le conteneur. 

Dans ce cas, l’exemple appelle la méthode [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync) pour créer le conteneur. Une valeur GUID est ajoutée au nom du conteneur pour s’assurer qu’il est unique. Dans un environnement de production, il est souvent préférable d’utiliser la méthode [CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync) pour créer un conteneur uniquement s’il n’existe pas déjà et ainsi éviter des conflits de nom.

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur l’affectation de noms aux conteneurs et objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Ensuite, l’exemple charge un fichier local à un objet blob de blocs. L’exemple de code attribue une référence à un objet **CloudBlockBlob** en appelant la méthode [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) sur le conteneur créé dans la section précédente. Il charge ensuite le fichier sélectionné dans l’objet blob en appelant la méthode [UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Cette méthode crée l’objet blob s’il n’existe pas déjà, et le remplace s’il existe. 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

L’exemple répertorie les objets blob du conteneur avec la méthode [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). Dans le cas de cet exemple, seul un objet blob a été ajouté au conteneur. Il n’y a donc qu’un objet blob répertorié.

Si trop d’objets blob sont retournés en un seul appel (plus de 5 000 par défaut), alors la méthode **ListBlobsSegmentedAsync** retourne un segment du jeu de résultats total ainsi qu’un jeton de continuation. Pour récupérer le segment d’objets blob suivant, vous devez fournir le jeton de continuation retourné par l’appel précédent, jusqu’à ce que le jeton soit null. Un jeton de continuation null indique que tous les objets blob ont été récupérés. L’exemple de code montre comment utiliser le jeton de continuation, dans l’intérêt des meilleures pratiques.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>Télécharger des objets blob

Ensuite, l’exemple télécharge l’objet blob créé précédemment dans votre système de fichiers local avec la méthode [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync). L’exemple de code ajoute le suffixe « _DOWNLOADED » au nom de l’objet blob afin que vous puissiez voir les deux fichiers dans votre système de fichiers local.

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Supprimer des ressources

L’exemple se débarrasse des ressources créées en supprimant l’ensemble du conteneur avec la méthode [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Si vous voulez, vous pouvez aussi supprimer les fichiers locaux.

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

## <a name="resources-for-developing-net-applications-with-blobs"></a>Ressources sur le développement d’applications .NET avec des objets blob

Consultez ces ressources supplémentaires sur le développement .NET avec le stockage Blob :

### <a name="binaries-and-source-code"></a>Fichiers binaires et code source

- Téléchargez le package NuGet pour obtenir la dernière version de la [bibliothèque de client .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) pour le stockage Azure. 
- Consultez le [code source de la bibliothèque de client .NET](https://github.com/Azure/azure-storage-net) sur GitHub.

### <a name="client-library-reference-and-samples"></a>Référence et exemples de la bibliothèque de client

- Consultez la [référence API .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage) pour plus d’informations sur la bibliothèque de client .NET.
- Explorez les [exemples de Stockage Blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob) écrits avec la bibliothèque de client .NET.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à charger, télécharger et répertorier des objets blob avec .NET. 

Pour apprendre à créer une application web qui peut charger une image dans le Stockage Blob, poursuivez vers [Charger des données d’image dans le cloud avec le Stockage Azure](storage-upload-process-images.md).

> [!div class="nextstepaction"]
> [Guide pratique des opérations Stockage Blob](storage-dotnet-how-to-use-blobs.md)

- Pour en savoir plus sur .NET Core, consultez [Prise en main de .NET en 10 minutes](https://www.microsoft.com/net/learn/get-started/).
- Pour explorer un exemple d’application que vous pouvez déployer depuis Visual Studio pour Windows, consultez l’[exemple d’application web de la galerie de photos .NET avec le Stockage Blob Azure](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
 