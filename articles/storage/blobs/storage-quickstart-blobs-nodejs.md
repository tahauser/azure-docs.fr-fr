---
title: "Démarrage rapide Azure - Charger, télécharger et répertorier des objets blob dans Stockage Azure à l’aide de Node.js | Microsoft Docs"
description: "Dans le cadre de ce démarrage rapide, vous créez un compte de stockage et un conteneur. Ensuite, vous utilisez la bibliothèque de client de stockage pour Node.js, afin de charger un objet blob dans Stockage Azure, de télécharger un objet blob et de répertorier les objets blob dans un conteneur."
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: cshoe
ms.openlocfilehash: ad0d4a2242aef99e0307f732175e0c50010580ce
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide de Node.js

Dans ce guide de démarrage rapide, vous allez découvrir comment utiliser Node.js pour charger, télécharger et répertorier des objets blob de blocs dans un conteneur Stockage Blob Azure.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide :

* Installez [Node.js](https://nodejs.org/en/)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application

[L’exemple d’application](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) utilisé dans ce guide de démarrage rapide est une application console de base. 

Utilisez [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Cette commande clone le dépôt dans votre dossier git local. Pour ouvrir l’application, recherchez le dossier storage-blobs-node-quickstart, ouvrez-le, puis double-cliquez sur index.js.

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage

Dans l’application, vous devez fournir la chaîne de connexion de votre compte de stockage. Ouvrez le fichier `index.js` et recherchez la variable `connectionString`. Remplacez la valeur entière de la chaîne de connexion par celle que vous avez enregistrée à partir du portail Azure. Votre chaîne de connexion de stockage doit ressembler à ce qui suit :

```javascript
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>Installer les packages nécessaires

Dans le répertoire de l’application, exécutez `npm install` pour installer les packages nécessaires répertoriés dans le fichier `package.json`.

```javascript
npm install
```

## <a name="run-the-sample"></a>Exécution de l'exemple

Cet exemple crée un fichier de test dans Mes Documents, le charge dans Stockage Blob, liste les objets blob dans le conteneur, puis télécharge le fichier avec un nouveau nom pour vous permettre de comparer les fichiers anciens et nouveaux.

Exécutez l’exemple en tapant `node index.js`. La sortie suivante provient d’un système Windows.  Vous recevrez une sortie similaire avec des chemins de fichiers appropriés si vous utilisez Linux.

```
Azure Storage Node.js Client Library Blobs Quick Start

1. Creating a container with public access: quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d

2. Creating a file in ~/Documents folder to test the upload and download

   Local File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

3. Uploading BlockBlob: quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

   Uploaded Blob URL: https://mystorageaccount.blob.core.windows.net/quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d/quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

4. Listing blobs in container

   - quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt (type: BlockBlob)


5. Downloading blob

   Downloaded File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d_DOWNLOADED.txt

Sample finished running. When you hit <ENTER> key, the temporary files will be deleted and the sample application will exit.
```

Avant de continuer, recherchez les deux fichiers dans votre dossier Mes documents. Vous pouvez les ouvrir et constater qu’ils sont identiques.

Vous pouvez également utiliser un outil comme l’[Explorateur Stockage Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pour afficher les fichiers dans Stockage Blob. L’Explorateur Stockage Azure est un outil multiplateforme gratuit qui vous permet d’accéder aux informations de votre compte de stockage.

Une fois que vous avez vérifié les fichiers, appuyez sur n’importe quelle touche pour terminer la démonstration et supprimer les fichiers de test. Maintenant que vous avec compris l’exemple, ouvrez le fichier index.js pour examiner le code. 

## <a name="understand-the-sample-code"></a>Découvrir l’exemple de code

Ensuite, nous allons parcourir l’exemple de code pas à pas pour vous montrer son fonctionnement.

### <a name="get-references-to-the-storage-objects"></a>Obtenir des références aux objets de stockage

La première chose à faire est de créer la référence à l’objet **BlobService** utilisé pour accéder au Stockage Blob et le gérer. Ces objets reposent l’un sur l’autre : chacun est utilisé par le suivant dans la liste.

* Créez une instance de l’objet [BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor) pointant vers le service BLOB de votre compte de stockage.

* Créez un conteneur, puis définissez des autorisations sur le conteneur pour que les objets blob soient publics et accessibles par une simple URL. Le conteneur commence par **quickstartcontainer-**.

Cet exemple utilise [createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists), car nous voulons créer un conteneur chaque fois que l’exemple est exécuté. Dans un environnement de production où vous utilisez le même conteneur pour toute l’application, il est préférable d’appeler une seule fois CreateIfNotExists. Ou vous pouvez créer le conteneur à l’avance pour ne pas avoir à le créer dans le code.

```javascript
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

### <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Les objets blob de blocs sont les plus couramment utilisés. Ils conviennent parfaitement au stockage de données texte et binaires, c’est pourquoi nous les utilisons dans ce guide de démarrage rapide.

Pour charger un fichier dans un objet blob, vous utilisez la méthode [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile). Cette opération crée l’objet blob s’il n’existe pas déjà, et le remplace s’il existe.

L’exemple de code crée un fichier local à utiliser pour le chargement et le téléchargement, en stockant le fichier à charger comme **localPath** et le nom de l’objet blob dans **localFileToUpload**. L’exemple suivant charge le fichier dans votre conteneur qui commence par **quickstartcontainer-**.

```javascript
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

Il existe plusieurs méthodes de chargement que vous pouvez utiliser avec Stockage Blob. Par exemple, si vous avez un flux de mémoire, vous pouvez utiliser la méthode [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream) plutôt que [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile).

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Ensuite, l’application obtient une liste des fichiers du conteneur à l’aide de [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Le code suivant récupère la liste des objets blob, puis effectue une itération sur ces derniers pour montrer les URI des objets blob rencontrés. Vous pouvez copier l’URI à partir de la fenêtre Commande et la coller dans un navigateur pour afficher le fichier.

Si vous avez moins de 5 000 objets blob dans le conteneur, tous les noms d’objets blob sont récupérés en un appel à [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Si vous avez plus de 5 000 objets blob dans le conteneur, le service récupère la liste par groupes de 5 000 jusqu'à ce que tous les noms d’objets blob soient récupérés. La première fois que cette API est appelée, elle retourne les 5 000 premiers noms d’objet blob et un jeton de liaison. La deuxième fois, vous fournissez le jeton et le service récupère le groupe suivant de noms d’objet blob et ainsi de suite, jusqu'à la nullité du jeton de continuation, qui indique que tous les noms d’objet blob ont été récupérés.

```javascript
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

### <a name="download-blobs"></a>Télécharger des objets blob

Téléchargez les objets blob sur votre disque local à l’aide de [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile).

Le code suivant télécharge l’objet blob chargé dans la section précédente et ajoute le suffixe « _DOWNLOADED » au nom de l’objet blob pour pouvoir voir les deux fichiers sur le disque local. 

```javascript
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

### <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des objets blob chargés dans ce guide de démarrage rapide, vous pouvez supprimer l’intégralité du conteneur à l’aide de [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) et [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists). Supprimez aussi les fichiers créés s’ils ne sont plus nécessaires. Cette opération est effectuée dans l’application quand vous appuyez sur la touche Entrée pour quitter l’application.

```javascript
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et Stockage Blob Azure avec Node.js. Pour en savoir plus sur l’utilisation de Stockage Blob, consultez le Guide pratique de Stockage Blob.

> [!div class="nextstepaction"]
> [Guide pratique des opérations Stockage Blob](storage-nodejs-how-to-use-blob-storage.md)

Pour la référence Node.js pour le stockage Azure, consultez [Package azure-stockage](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest).