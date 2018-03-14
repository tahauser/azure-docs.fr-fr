---
title: "Démarrage rapide Azure - Charger, télécharger et répertorier des objets blob dans Stockage Azure à l’aide de Go | Microsoft Docs"
description: "Dans le cadre de ce démarrage rapide, vous créez un compte de stockage et un conteneur. Ensuite, vous utilisez la bibliothèque de client de stockage pour Go, afin de charger un objet blob dans Stockage Azure, de télécharger un objet blob et de répertorier les objets blob dans un conteneur."
services: storage
author: seguler
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: seguler
ms.openlocfilehash: a30d012ea663b04028a4b0ebb2880532a596500b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide de Go

Dans ce guide de démarrage rapide, vous apprenez à utiliser le langage de programmation Go pour charger, télécharger et lister des objets blob de blocs dans un conteneur de stockage blob Azure. 

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide : 
* Installer [Go 1.8 ou une version ultérieure](https://golang.org/dl/)
* Téléchargez et installez le [SDK Stockage Azure pour Go](https://github.com/azure/azure-storage-blob-go/) à l’aide de `go get -u github.com/azure/azure-storage-blob-go/2016-05-31/azblob`. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application
L’[exemple d’application](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git) utilisé dans ce démarrage rapide est une application Go de base.  

Utilisez [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

Cette commande clone le dépôt dans votre dossier git local. Pour ouvrir l’exemple Go pour le stockage d’objets Blob, recherchez le fichier storage-quickstart.go.  

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage
Cette solution demande que le nom et la clé de votre compte de stockage soient stockés en toute sécurité dans les variables d’environnement locales de la machine exécutant l’exemple. Suivez l’un des exemples ci-dessous, en fonction de votre système d’exploitation, pour créer la variable d’environnement.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>Exécution de l'exemple
Cet exemple crée un fichier de test dans le dossier actuel, le charge dans Stockage Blob, liste les objets blob du conteneur, puis charge le fichier dans la mémoire tampon. 

Pour exécutez l’exemple, utilisez la commande suivante : 

```go run storage-quickstart.go```

La sortie suivante est un exemple de sortie retournée durant l’exécution de l’application :
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
Quand vous appuyez sur la touche pour continuer, l’exemple de programme supprime le conteneur de stockage et les fichiers. 

> [!TIP]
> Vous pouvez également utiliser un outil comme l’[Explorateur Stockage Azure](http://storageexplorer.com) pour afficher les fichiers dans Stockage Blob. L’Explorateur Stockage Azure est un outil multiplateforme gratuit qui vous permet d’accéder aux informations de votre compte de stockage. 
>

## <a name="understand-the-sample-code"></a>Découvrir l’exemple de code

Ensuite, nous allons parcourir l’exemple de code pas à pas pour vous montrer son fonctionnement.

### <a name="create-containerurl-and-bloburl-objects"></a>Créer des objets ContainerURL et BlobURL
La première chose à faire est de créer les références aux objets ContainerURL et BlobURL utilisés pour accéder et gérer Stockage Blob. Ces objets offrent des API de bas niveau telles que Create, PutBlob et GetBlob pour publier des API REST.

* Utilisez la structure **SharedKeyCredential** pour stocker vos informations d’identification. 

* Créez un **pipeline** utilisant les options et les informations d’identification. Le pipeline spécifie des éléments tels que les stratégies de nouvelle tentative, la journalisation, la désérialisation des charges utiles de réponse HTTP et bien plus encore.  

* Instanciez un nouvel ContainerURL et un nouvel objet BlobURL pour exécuter des opérations sur le conteneur (Create) et les objets blob (PutBlob et GetBlob).


Une fois que vous avez le ContainerURL, vous pouvez instancier l’objet **BlobURL** qui pointe vers un objet blob et effectuer des opérations telles que le chargement, le téléchargement et la copie.

> [!IMPORTANT]
> Les noms de conteneurs doivent être en minuscules. Pour plus d’informations sur les noms des conteneurs et des objets blob, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Dans cette section, vous créez un nouveau conteneur. Le conteneur est appelé **quickstartblobs-[chaîne aléatoire]**. 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential := azblob.NewSharedKeyCredential(accountName, accountKey)
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err := containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Comme les objets blob de blocs sont les plus couramment utilisés, nous les utilisons dans ce démarrage rapide.  

Pour télécharger un fichier vers un objet blob, ouvrez le fichier à l’aide de **os.Open**. Vous pouvez ensuite télécharger le fichier vers le chemin d’accès spécifié à l’aide de l’une des API REST : PutBlob, PutBlock/PutBlockList. 

Sinon, le kit de développement logiciel offre des [API de niveau supérieur](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go) qui s’appuient sur les API REST de bas niveau. Par exemple, la fonction ***UploadFileToBlockBlob*** utilise des opérations PutBlock pour télécharger simultanément un fichier en plusieurs blocs afin d’optimiser le débit. Si le fichier fait moins de 256 Mo, il utilise PutBlob à la place pour effectuer le transfert en une seule transaction.

L’exemple suivant charge le fichier dans votre conteneur nommé **quickstartblobs-[chaîne aléatoire]**.

```go
// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level PutBlob API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that PutBlob can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/en-us/rest/api/storageservices/put-blob
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.PutBlob(ctx, file, azblob.BlobHTTPHeaders{}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls PutBlock/PutBlockList for files larger 256 MBs, and calls PutBlob for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Obtenez la liste des fichiers du conteneur à l’aide de la méthode **listBlobs** sur un **ContainerURL**. ListBlobs retourne un seul segment d’objets blob (jusqu’à 5 000) à partir du **marqueur** spécifié. Utilisez un marqueur vide pour commencer l’énumération depuis le début. Les noms des objets blob retournés dans l’ordre lexicographique. Après avoir obtenu un segment, traitez-le, puis appelez de nouveau ListBlobs en passant le marqueur retourné précédemment.  

```go
// List the blobs in the container
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobs(ctx, marker, azblob.ListBlobsOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Blobs.Blob {
        fmt.Print("Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>Télécharger l’objet blob

Téléchargez des objets BLOB à l’aide de la méthode de bas niveau **GetBlob** sur un BlobURL. Vous pouvez également créer un flux de données et lire les plages avec lui en utilisant l’API de haut niveau **NewDownloadStream** fournie dans [highlevel.go](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go). La fonction NewDownloadStream effectue une nouvelle tentative en cas d’échec de connexion, tandis que l’API GetBlob effectue une nouvelle tentative uniquement après les codes d’état HTTP tels que 503 (serveur occupé). Le code suivant télécharge l’objet blob à l’aide de la fonction **NewDownloadStream**. Le contenu de l’objet blob est écrit dans une mémoire tampon et affiché sur la console.

```go
// Here's how to download the blob. NOTE: This method automatically retries if the connection fails
// during download (the low-level GetBlob function does NOT retry errors when reading from its stream).
stream := azblob.NewDownloadStream(ctx, blobURL.GetBlob, azblob.DownloadStreamOptions{})
downloadedData := &bytes.Buffer{}
_, err = downloadedData.ReadFrom(stream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>Supprimer des ressources
Si vous n’avez plus besoin des objets blob chargés dans ce guide de démarrage rapide, vous pouvez supprimer l’intégralité du conteneur à l’aide de la méthode **Delete**. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press the enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="next-steps"></a>Étapes suivantes
 
Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et Stockage Blob Azure avec Go. Pour plus d’informations sur le kit de développement logiciel Azure Storage Blob, affichez le [Code Source](https://github.com/Azure/azure-storage-blob-go/) et la [référence de l’API](https://godoc.org/github.com/Azure/azure-storage-blob-go/2016-05-31/azblob).
