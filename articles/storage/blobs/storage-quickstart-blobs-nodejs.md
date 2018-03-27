---
title: Démarrage rapide Azure - Charger, télécharger et répertorier des objets blob dans Stockage Azure à l’aide de Node.js | Microsoft Docs
description: Dans le cadre de ce démarrage rapide, vous créez un compte de stockage et un conteneur. Ensuite, vous utilisez la bibliothèque de client de stockage pour Node.js, afin de charger un objet blob dans Stockage Azure, de télécharger un objet blob et de répertorier les objets blob dans un conteneur.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: cshoe
ms.openlocfilehash: 28f9936c297b6f641810e0c7783f4d84be108286
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide de Node.js

Dans ce guide de démarrage rapide, vous allez découvrir comment utiliser Node.js pour charger, télécharger et répertorier des objets blob de blocs dans un conteneur à l’aide du service de Stockage Blob Azure.

Pour suivre ce guide de démarrage rapide, vous devez disposer d’un [abonnement Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application

[L’exemple d’application](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) utilisé dans ce guide de démarrage rapide est une application console de base Node.js. Pour commencer, clonez le référentiel dans votre machine en exécutant la commande suivante :

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Pour ouvrir l’application, recherchez le dossier *storage-blobs-node-quickstart* et ouvrez-le dans l’environnement de modification de code de votre préférence.

## <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage

Avant d’exécuter l’application, vous devez fournir la chaîne de connexion de votre compte de stockage. Le référentiel d’exemple contient un fichier nommé *.env.example*. Vous pouvez renommer ce fichier en supprimant l’extension *.example*, ce qui donne un fichier nommé *.env*. Dans le fichier *.env*, ajoutez la valeur de votre chaîne de connexion après la clé *AZURE_STORAGE_CONNECTION_STRING*.

## <a name="install-required-packages"></a>Installer les packages nécessaires

Dans le répertoire de l’application, exécutez la commande *npm install* pour installer les packages nécessaires à l’application.

```bash
npm install
```

## <a name="run-the-sample"></a>Exécution de l'exemple
Maintenant que les dépendances sont installées, vous pouvez exécuter l’exemple en entrant des commandes dans le script. Par exemple, pour créer un conteneur d’objets blob, exécutez la commande suivante :

```bash
node index.js --command createContainer
```

Les commandes disponibles sont les suivantes :

| Commande | Description |
|---------|---------|
|*createContainer* | Crée un conteneur nommé *test-container* (fonctionne même si un conteneur existe déjà) |
|*upload*          | Charge le fichier *example.txt* dans le conteneur *test-container* |
|*télécharger*        | Télécharge le contenu de l’objet blob *example* dans le fichier *example.downloaded.txt* |
|*delete*          | Supprime l’objet blob *example* |
|*list*            | Répertorie le contenu du conteneur *test-container* sur la console |


## <a name="understanding-the-sample-code"></a>Présentation de l’exemple de code
Cet exemple de code utilise quelques modules pour communiquer avec le système de fichiers et la ligne de commande. 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

L’objectif de ce module est le suivant : 

- *dotenv* charge les variables d’environnement définies dans un fichier nommé *.env* dans le contexte d’exécution actuel
- *path* est requis pour déterminer le chemin d’accès absolu du fichier à charger dans le stockage d’objets blob
- *yargs* offre une interface simple pour accéder aux arguments de ligne de commande
- *azure-storage* est le module du [kit de développement logiciel Stockage Azure](/nodejs/api/azure-storage) pour Node.js

Une série de variables est ensuite initialisée :

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

Les variables sont définies sur les valeurs suivantes :

- *blobService* correspond à une nouvelle instance du service d’objets Blob Azure
- *containerName* correspond au nom du conteneur
- *sourceFilePath* correspond au chemin d’accès absolu du fichier à charger
- *blobName* est créée en prenant le nom de fichier et en supprimant son extension

Dans l’implémentation suivante, les fonctions *blobService* sont incluses dans une *Promesse*, ce qui permet d’accéder à la fonction Javascript *async* et d’*attendre* que l’opérateur simplifie la nature de rappel de l’[API Stockage Azure](/nodejs/api/azure-storage/blobservice). Lorsqu’une réponse réussie est retournée pour chaque fonction, la promesse est résolue avec des données cohérentes, ainsi qu’un message spécifique à l’action.

### <a name="create-a-blob-container"></a>Création d’un conteneur d’objets blob

La fonction *createContainer* appelle [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) et définit le niveau d’accès approprié pour l’objet blob.

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Le second paramètre (*options*) de la fonction **createContainerIfNotExists** accepte une valeur pour [publicAccessLevel](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists). La valeur *blob* pour *publicAccessLevel* spécifie que les données d’objets blob spécifiques sont exposées publiquement. Ce paramètre est contraire au niveau d’accès de *container*, qui offre la capacité de répertorier le contenu du conteneur.

L’utilisation de **createContainerIfNotExists** permet à l’application d’exécuter la commande *createContainer* plusieurs fois sans retourner d’erreurs lorsque le conteneur existe déjà. Dans un environnement de production, vous appelez souvent la fonction **createContainerIfNotExists** une seule fois, étant donné que le même conteneur est utilisé pour toute l’application. Dans ces cas, vous pouvez créer le conteneur en avance via le portail ou Azure CLI.

### <a name="upload-a-blob-to-the-container"></a>Charger un objet blob dans le conteneur

La fonction *upload* utilise la fonctionne [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) pour charger et créer ou remplacer un fichier du système de fichiers vers le stockage d’objets blob. 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
Dans le contexte de l’exemple d’application, le fichier nommé *example.txt* est chargé dans un objet blob nommé *example* à l’intérieur d’un conteneur nommé *test-container*. D’autres approches pour charger du contenu dans des objets blob sont disponibles, dans lesquelles on utilise du [texte](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) et des [flux](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream).

Pour vérifier que le fichier est chargé dans votre stockage d’objets blob, vous pouvez utiliser l’[Explorateur Stockage Azure](https://azure.microsoft.com/en-us/features/storage-explorer/) pour afficher les données dans votre compte.

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

La fonction *list* appelle la méthode [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) pour retourner une liste de métadonnées d’objets blob dans un conteneur. 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

Appeler la méthode *listBlobsSegmented* permet de retourner des métadonnées d’objets blob sous forme de tableau d’instances [BlobResult](/nodejs/api/azure-storage/blobresult). Les résultats y sont retournés par lots de 5 000 incréments (segments). S’il y a plus de 5 000 objets blob dans un conteneur, les résultats incluent une valeur pour **continuationToken**. Pour répertorier les segments suivants à partir d’un conteneur d’objets blob, vous pouvez passer le jeton de continuation dans **listBlobsSegmented** en tant que second argument.

### <a name="download-a-blob-from-the-container"></a>Télécharger un objet blob depuis le conteneur

La fonction *download* utilise la méthode [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile) pour télécharger le contenu de l’objet blob dans le chemin d’accès absolu spécifié.

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
L’implémentation ci-après modifie le chemin du fichier source en ajoutant *.downloaded.txt* au nom du fichier. Dans des contextes concrets, vous pouvez modifier l’emplacement et le nom du fichier lorsque vous sélectionnez un emplacement de téléchargement.

### <a name="delete-blobs-in-the-container"></a>Supprimer les objets blob du conteneur

La fonction *deleteBlock* (aussi appelée commande de console *delete*) appelle la fonction [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists). Comme son nom l’indique, cette fonction ne retourne pas d’erreur si l’objet blob a déjà été supprimé.

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>Charger et répertorier

Un des avantages à utiliser les promesses est la possibilité de lier les commandes entre elles. La fonction **uploadAndList** démontre la facilité à répertorier le contenu d’un objet blob directement après le chargement d’un fichier.

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>Appel de code

Pour exposer les fonctions implémentées à la ligne de commande, chaque fonction est mappée à un littéral d’objet.

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

Avec l’élément *_module* maintenant en place, chaque commande est disponible à partir de la ligne de commande.

```javascript
const commandExists = () => exists = !!_module[args.command];
```

Si une commande spécifiée n’existe pas, les propriétés de l’élément *_module* sont affichées sur la console en tant qu’aide textuelle pour l’utilisateur. 

La fonction *executeCommand* est une fonction *async* qui appelle la commande spécifiée à l’aide de l’opérateur *await* et enregistre tout message en données dans la console.

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

Enfin, l’exécution de code appelle d’abord *commandExists* pour vérifier qu’une commande connue a été passée dans le script. Si une commande existante est sélectionnée, elle est alors exécutée et toute erreur est enregistrée dans la console.

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>Supprimer des ressources
Si vous n’envisagez pas d’utiliser les données ou les comptes créés dans cet article, vous pouvez les supprimer et ainsi éviter toute facturation non désirée. Pour supprimer les objets blob et les conteneurs, vous pouvez utiliser les méthodes [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteBlobIfExists_container__blob__options__callback_) et [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteContainerIfExists_container__options__callback_). Vous pouvez également supprimer le compte de stockage [via le portail](../common/storage-create-storage-account.md).

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Ressources sur le développement d’applications Node.js avec des objets blob

Consultez ces ressources supplémentaires sur le développement Node.js avec le stockage Blob :

### <a name="binaries-and-source-code"></a>Fichiers binaires et code source

- Affichez et installez le [code source de la bibliothèque de client Node.js](https://github.com/Azure/azure-storage-node) pour Stockage Azure sur GitHub.

### <a name="client-library-reference-and-samples"></a>Référence et exemples de la bibliothèque de client

- Consultez la [référence API Node.js](https://docs.microsoft.com/en-us/javascript/api/overview/azure/storage) pour plus d’informations sur la bibliothèque de client Node.js.
- Explorez les [exemples de Stockage Blob](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) écrits avec la bibliothèque de client Node.js.

## <a name="next-steps"></a>Étapes suivantes

Ce guide de démarrage rapide explique comment charger un fichier entre un disque local et Stockage Blob Azure avec Node.js. Pour en savoir plus sur l’utilisation de Stockage Blob, consultez le Guide pratique de Stockage Blob.

> [!div class="nextstepaction"]
> [Guide pratique des opérations Stockage Blob](storage-nodejs-how-to-use-blob-storage.md)

Pour la référence Node.js pour le stockage Azure, consultez [Package azure-stockage](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest).