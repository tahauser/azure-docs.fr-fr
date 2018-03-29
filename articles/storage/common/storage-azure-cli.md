---
title: Utilisation d’Azure CLI 2.0 avec le stockage Azure | Microsoft Docs
description: Découvrez comment utiliser Azure CLI 2.0 avec le stockage Azure pour créer et gérer des comptes de stockage et utiliser des fichiers et objets blob Azure. Azure CLI 2.0 est un outil multiplateforme écrit en Python.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: rogarana
ms.openlocfilehash: 99e67b9c8469f08f1cbfc980568eec35694d1ae2
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>Utilisation d’Azure CLI 2.0 avec le stockage Azure

L’interface Azure CLI 2.0 multiplateforme et open source offre un ensemble de commandes dédiées à l’utilisation de la plateforme Azure. Elle offre des fonctionnalités similaires à celles du [portail Azure](https://portal.azure.com), notamment l’accès étendu aux données.

Dans ce guide, nous vous montrons comment utiliser [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) pour effectuer plusieurs tâches à l’aide de ressources dans votre compte de stockage Azure. Nous vous recommandons de télécharger et d’installer ou de mettre à niveau la CLI 2.0 vers la dernière version avant d’utiliser ce guide.

Les exemples dans le guide partent du principe que vous utilisez le shell Bash sur Ubuntu, mais les autres plateformes devraient fonctionner de manière similaire. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Prérequis

Ce guide part du principe que vous comprenez les concepts de base de Microsoft Azure Storage. Il suppose également que vous êtes en mesure de satisfaire les exigences de création de compte spécifiées ci-dessous pour Azure et le service Stockage.

### <a name="accounts"></a>Comptes
* **Compte Azure** : si vous ne possédez pas encore d’abonnement Azure, [créez un compte Azure gratuit](https://azure.microsoft.com/free/).
* **Compte de stockage** : voir la section [Créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account) de l’article [À propos des comptes de stockage Azure](storage-create-storage-account.md).

### <a name="install-the-azure-cli-20"></a>Installer Azure CLI 2.0

Téléchargez et installez Azure CLI 2.0 en suivant les instructions fournies dans la section [Installer Azure CLI 2.0](/cli/azure/install-az-cli2).

> [!TIP]
> Si vous rencontrez un problème avec l’installation, consultez la section [Dépannage de l’installation](/cli/azure/install-az-cli2#installation-troubleshooting) de l’article et le guide [Dépannage de l’installation](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) sur GitHub.
>

## <a name="working-with-the-cli"></a>Utilisation de la CLI

Une fois la CLI installée, vous pouvez utiliser la commande `az` de votre interface de ligne de commande (invite de ligne de commande, Terminal, Bash) afin d’accéder aux commandes de la CLI Azure. Tapez la commande `az` pour afficher la liste complète des commandes de base (l’exemple de sortie suivant a été tronqué) :

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

Dans l’interface de ligne de commande, exécutez la commande `az storage --help` pour répertorier les sous-groupes de la commande `storage`. Les descriptions des sous-groupes fournissent une vue d’ensemble de la fonctionnalité que la CLI Azure fournit pour l’utilisation de vos ressources de stockage.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Connexion de la CLI à votre abonnement Azure

Pour utiliser les ressources dans votre abonnement Azure, vous devez tout d’abord vous connecter à votre compte Azure avec `az login`. Vous pouvez vous connecter de plusieurs façons :

* **Connexion interactive** : `az login`
* **Connexion avec nom d’utilisateur et mot de passe** : `az login -u johndoe@contoso.com -p VerySecret`
  * Ceci ne fonctionne pas avec les comptes Microsoft ou les comptes qui utilisent l’authentification multifacteur.
* **Connexion avec un principal du service** : `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-20-sample-script"></a>Exemple de script CLI 2.0 Azure

Ensuite, nous allons utiliser un petit script shell qui émet plusieurs commandes CLI 2.0 Azure de base pour interagir avec les ressources de stockage Azure. Tout d’abord, le script crée un nouveau conteneur dans votre compte de stockage, puis télécharge un fichier existant (comme un objet blob) dans ce conteneur. Ensuite, il répertorie tous les objets blob dans le conteneur et pour terminer, il télécharge le fichier vers une destination sur l’ordinateur local que vous spécifiez.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**Configuration et exécution du script**

1. Ouvrez votre éditeur de texte préféré, puis copiez et collez le script précédent dans l’éditeur.

2. Ensuite, mettez à jour les variables du script pour refléter vos paramètres de configuration. Remplacez les valeurs suivantes comme indiqué :

   * **\<storage_account_name\>** : nom de votre compte de stockage.
   * **\<storage_account_key\>** : clé d’accès primaire ou secondaire pour votre compte de stockage.
   * **\<container_name\>** : nom du nouveau conteneur à créer, par exemple « azure-cli-sample-container ».
   * **\<blob_name\>** : nom de l’objet blob de destination dans le conteneur.
   * **\<file_to_upload\>** : chemin d’accès au petit fichier sur l’ordinateur local, par exemple « ~/images/HelloWorld.png ».
   * **\<destination_file\>** : chemin d’accès au fichier de destination, par exemple « ~/downloadedImage.png ».

3. Une fois que vous avez mis à jour les variables nécessaires, enregistrez le script et quittez l’éditeur. Les étapes suivantes supposent que vous avez nommé votre script **my_storage_sample.sh**.

4. Rendez le script exécutable, si nécessaire : `chmod +x my_storage_sample.sh`

5. Exécutez le script. Par exemple, dans Bash : `./my_storage_sample.sh`

Vous devriez voir une sortie similaire à ce qui suit et la variable **\<destination_file\>** spécifiée dans le script doit s’afficher sur votre ordinateur local.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> La sortie précédente est au format **table**. Vous pouvez spécifier le format de sortie à utiliser en spécifiant l’argument `--output` dans vos commandes CLI ou en le définissant globalement à l’aide de `az configure`.
>

## <a name="manage-storage-accounts"></a>Gestion des comptes de stockage

### <a name="create-a-new-storage-account"></a>Création d’un nouveau compte de stockage
Pour utiliser le Stockage Azure, vous avez besoin d’un compte de stockage. Vous pouvez créer un nouveau compte de stockage Azure après avoir configuré votre ordinateur pour qu’il se [connecte à votre abonnement](#connect-to-your-azure-subscription).

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [Obligatoire] : emplacement. Par exemple, « États-Unis de l’Ouest ».
* `--name` [Obligatoire] : nom du compte de stockage. Le nom doit comporter entre 3 et 24 caractères, lesquels ne peuvent être que des caractères alphanumériques minuscules.
* `--resource-group` [Obligatoire] : nom du groupe de ressources.
* `--sku` [Obligatoire] : référence du compte de stockage. Valeurs autorisées :
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`


### <a name="set-default-azure-storage-account-environment-variables"></a>Définition des variables d’environnement par défaut pour le compte de stockage Azure
Vous pouvez disposer de plusieurs comptes de stockage dans votre abonnement Azure. Pour en sélectionner un à utiliser pour toutes les commandes de stockage suivantes, vous pouvez définir ces variables d’environnement :

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Vous pouvez également définir un compte de stockage par défaut via une chaîne de connexion. Commencez par obtenir la chaîne de connexion à l’aide de la commande `show-connection-string` :

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Ensuite, copiez la chaîne de connexion de sortie et définissez la variable d’environnement `AZURE_STORAGE_CONNECTION_STRING` (vous devrez peut-être mettre la chaîne de connexion entre guillemets) :

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Tous les exemples des sections suivantes de cet article supposent que vous avez défini les variables d’environnement `AZURE_STORAGE_ACCOUNT` et `AZURE_STORAGE_ACCESS_KEY`.
>

## <a name="create-and-manage-blobs"></a>Créer et gérer des objets blob
Le stockage d’objets blob Azure est un service permettant de stocker de gros volumes de données non structurées, telles que du texte ou des données binaires, accessibles depuis n’importe où dans le monde via HTTP ou HTTPS. Cette section suppose que vous êtes déjà familiarisé avec les concepts du Stockage Blob Azure. Pour obtenir des informations détaillées, consultez [Prise en main du Stockage Blob Azure à l’aide de .NET](../blobs/storage-dotnet-how-to-use-blobs.md) et [Concepts de service Blob](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Créez un conteneur.
Chaque objet blob du stockage Azure doit se trouver dans un conteneur. Vous pouvez créer un conteneur à l’aide de la commande `az storage container create` :

```azurecli
az storage container create --name <container_name>
```

Vous pouvez définir l’un des trois niveaux d’accès en lecture pour un nouveau conteneur en spécifiant l’argument facultatif `--public-access` :

* `off` (par défaut) : les données du conteneur sont privées et accessibles uniquement par le propriétaire du compte.
* `blob` : accès en lecture public pour les objets blob.
* `container` : accès en lecture et en création de listes public à l’intégralité du conteneur.

Pour plus d’informations, consultez [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Chargement d’un objet blob dans un conteneur
Le Stockage Blob Azure prend en charge les objets blob de blocs, d’ajout et de page. Chargez des objets blob dans un conteneur à l’aide de la commande `blob upload` :

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

 Sinon, la commande `blob upload` charge par défaut les fichiers *.vhd dans les objets blob de pages ou de blocs. Pour spécifier un autre type lorsque vous chargez un objet blob, vous pouvez utiliser l’argument `--type` ; les valeurs autorisée sont `append`, `block` et `page`.

 Pour plus d’informations sur les différents objets blob, consultez [Présentation des objets blob de blocs, des objets blob d’ajout et des objets blob de pages](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Télécharger un objet blob depuis un conteneur
Cet exemple indique comment télécharger un objet blob à partir d’un conteneur :

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Répertoriez les objets blob dans un conteneur à l’aide de la commande [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list).

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Copier des objets blob
Vous pouvez copier des objets blob au sein d’un compte de stockage, ou vers des comptes de stockage et des régions et ce, de façon asynchrone.

L’exemple suivant indique comment copier des objets blob depuis un compte de stockage et les coller dans un autre. Nous créons d’abord un conteneur dans le compte de stockage source, en spécifiant l’accès en lecture public pour ses objets blob. Ensuite, nous chargeons un fichier dans le conteneur. Pour finir, nous copions l’objet blob de ce conteneur vers un conteneur du compte de stockage de destination.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

Dans l’exemple indiqué ci-dessus, le conteneur de destination doit déjà exister dans le compte de stockage de destination pour que l’opération de copie réussisse. En outre, l’objet blob source spécifié dans l’argument `--source-uri` doit inclure un jeton de signature d’accès partagé, ou être accessible publiquement, comme dans cet exemple.

### <a name="delete-a-blob"></a>Supprimer un objet blob
Pour supprimer un objet blob, utilisez la commande `blob delete` :

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Créer et gérer des partages de fichiers
Le service Azure Files propose un stockage partagé pour les applications utilisant le protocole SMB. Les services cloud et les machines virtuelles Microsoft Azure, ainsi que les applications locales, peuvent partager des données de fichiers via des partages montés. Vous pouvez gérer des partages de fichiers et des données de fichiers via la CLI Azure. Pour plus d’informations sur Azure Files, voir [Présentation d’Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Créer un partage de fichiers
Un partage de fichiers Azure est un partage de fichiers SMB dans Microsoft Azure. Tous les répertoires et fichiers doivent être créés dans un partage de fichiers. Un compte peut contenir un nombre illimité de partages, et un partage peut stocker un nombre illimité de fichiers, dans les limites de capacité du compte de stockage. L’exemple suivant détaille la création d’un partage de fichiers nommé **MonPartage**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Créer un répertoire
Un répertoire fournit une structure hiérarchique dans un partage de fichiers Azure. L’exemple suivant crée un répertoire nommé **MonRép** dans le partage de fichiers.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Un chemin d’accès peut inclure plusieurs niveaux, par exemple **dir1/dir2**. Toutefois, avant de créer un sous-répertoire, vous devez vous assurer que tous les répertoires parents existent. Par exemple, pour le chemin d’accès **dir1/dir2**, vous devez créer le répertoire **dir1**, puis le répertoire **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Chargement d’un fichier local vers un partage
Dans l’exemple suivant, un fichier est chargé à partir de l’emplacement **~/temp/samplefile.txt** vers la racine du partage de fichiers **myshare**. L’argument `--source` spécifie le fichier local existant à charger.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Comme pour la création d’un répertoire, vous pouvez spécifier un chemin d’accès au répertoire dans le partage pour charger le fichier vers un répertoire existant dans le partage :

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Dans le partage, un fichier peut présenter une taille maximale de 1 To.

### <a name="list-the-files-in-a-share"></a>Liste des fichiers dans un partage
Vous pouvez répertorier les fichiers et les répertoires dans un partage à l’aide de la commande `az storage file list` :

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Copie des fichiers      
Vous pouvez copier un fichier dans un autre, un fichier dans un objet blob ou un objet blob dans un fichier. Par exemple, pour copier un fichier vers un répertoire dans un partage différent :        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Créer un instantané de partage
Vous pouvez supprimer un instantané de partage à l’aide de la commande `az storage share snapshot` :

```cli
az storage share snapshot -n <share name>
```

Exemple de sortie
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>Répertorier les instantanés de partage

Vous pouvez lister les instantanés d’un partage spécifique à l’aide de la commande `az storage share list --include-snapshots`.

```cli
az storage share list --include-snapshots
```

**Exemple de sortie**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>Parcourir les instantanés de partage
Vous pouvez également accéder à un instantané de partage spécifique pour afficher son contenu à l’aide de la commande `az storage file list`. Vous devez spécifier le nom de partage `--share-name <snare name>` et l’horodatage `--snapshot '2017-10-04T19:45:18.0000000Z'`.

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Exemple de sortie**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>Effectuer une restauration à partir d’instantanés de partage

Vous pouvez restaurer un fichier en le copiant ou téléchargeant à partir d’un instantané de partage à l’aide de la commande `az storage file download`.

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Exemple de sortie**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>Supprimer un instantané de partage
Vous pouvez supprimer un instantané de partage à l’aide de la commande `az storage share delete` en définissant le paramètre `--snapshot` sur l’horodatage de l’instantané de partage :

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Exemple de sortie
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>Étapes suivantes
Voici quelques ressources supplémentaires pour en savoir plus sur l’utilisation d’Azure CLI 2.0.

* [Prise en main d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Référence des commandes Azure CLI 2.0](/cli/azure)
* [Azure CLI 2.0 sur GitHub](https://github.com/Azure/azure-cli)
