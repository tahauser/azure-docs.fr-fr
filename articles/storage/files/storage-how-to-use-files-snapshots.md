---
title: "Utiliser des instantanés de partage (préversion) | Microsoft Docs"
description: "Un instantané de partage est une version en lecture seule d’un partage Azure Files enregistrée à un point dans le temps pour sauvegarder le partage."
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.openlocfilehash: eb5c7d0fcb9e1106dbc0aa577ba5ecfa1bf6bee7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="work-with-share-snapshots-preview"></a>Utiliser des instantanés de partage (préversion)
Un instantané de partage (préversion) est une version en lecture seule d’un partage de fichiers Azure pris à un certain point dans le temps. Une fois créé, un instantané de partage peut être lu, copié ou supprimé, mais pas modifié. Un instantané de partage offre un moyen de sauvegarder le partage tel qu’il apparaît à un certain moment. 

Dans cet article, vous découvrez comment créer, gérer et supprimer des instantanés de partage. Pour plus d’informations, consultez la [vue d’ensemble des instantanés de partage](storage-snapshots-files.md) ou le [FAQ sur les instantanés](storage-files-faq.md).

## <a name="create-a-share-snapshot"></a>Créer un instantané de partage

Vous pouvez créer un instantané de partage en utilisant le portail Azure, PowerShell, Azure CLI, l’API REST ou n’importe quel SDK de Stockage Azure. Les sections suivantes décrivent comment créer un instantané de partage avec le portail, l’interface CLI et PowerShell. 

Vous pouvez prendre un instantané d’un partage de fichiers alors qu’il est en cours d’utilisation. Toutefois, les instantanés de partage capturent seulement les données qui étaient déjà écrites dans un partage de fichiers au moment de l’émission de la commande de l’instantané de partage. Ainsi, toutes les données mises en cache par les applications ou le système d’exploitation risquent d’être exclues.

### <a name="create-a-share-snapshot-by-using-the-portal"></a>Créer un instantané de partage en utilisant le portail  
Pour créer un instantané de partage à un point dans le temps, accédez à votre partage de fichiers dans le portail et sélectionnez **Créer un instantané**.

>   ![Menu Instantané dans le portail](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-a-share-snapshot-by-using-azure-cli-20"></a>Créer un instantané de partage en utilisant Azure CLI 2.0
Vous pouvez supprimer un instantané de partage à l’aide de la commande `az storage share snapshot` :

```azurecli-interactive
az storage share snapshot -n <share name>
```

Exemple de sortie :
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

### <a name="create-a-share-snapshot-by-using-powershell"></a>Créer un instantané de partage en utilisant PowerShell
Vous pouvez supprimer un instantané de partage à l’aide de la commande `$share.Snapshot()` :

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```

## <a name="perform-common-share-snapshot-operations"></a>Effectuer des opérations courantes sur les instantanés de partage

Vous pouvez énumérer les instantanés de partage associés à votre partage de fichiers en utilisant l’onglet **Versions précédentes** dans Windows, via l’API REST, la bibliothèque cliente, PowerShell et le portail. Une fois le partage de fichiers monté, vous pouvez afficher toutes les versions antérieures du fichier en utilisant l’onglet **Versions précédentes** dans Windows. 

Les sections suivantes décrivent comment utiliser le portail Azure, Windows et Azure CLI 2.0 pour répertorier, accéder à et restaurer des instantanés de partage.

### <a name="share-snapshot-operations-in-the-portal"></a>Opérations sur les instantanés de partage dans le portail

Dans le portail, vous pouvez répertorier tous les instantanés de partage pour un partage de fichiers et accéder à un instantané de partage pour afficher son contenu.

#### <a name="view-a-share-snapshot"></a>Afficher un instantané de partage
Sur votre partage de fichiers, sous **Instantané**, sélectionnez **Afficher les instantanés**.

![Commande « Afficher les instantanés » dans le portail](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-to-share-snapshot-content"></a>Répertorier et accéder au contenu d’un instantané de partage
Affichez la liste des instantanés de partage, puis accédez directement au contenu d’un instantané en sélectionnant l’horodatage souhaité.

![Instantané sélectionné dans la liste des horodatages](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

Vous pouvez également sélectionner le bouton **Se connecter** dans la vue de la liste des instantanés pour obtenir la commande `net use` et le chemin du répertoire d’un instantané de partage particulier. Vous pouvez ensuite accéder directement à cet instantané.


![Volet Se connecter avec la zone de commande](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-a-share-snapshot"></a>Effectuer un téléchargement ou une restauration à partir d’un instantané de partage
Dans le portail, téléchargez ou restaurez un fichier d’un instantané en utilisant le bouton **Télécharger** ou **Restaurer**, respectivement.

![Boutons Télécharger et Restaurer](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="share-snapshot-operations-in-windows"></a>Opérations sur les instantanés de partage dans Windows
Si vous avez déjà pris des instantanés de votre partage de fichiers, vous pouvez afficher les versions précédentes d’un partage, d’un répertoire ou d’un fichier particulier à partir de votre partage de fichiers monté sur Windows. Par exemple, voici comment vous pouvez utiliser la fonctionnalité Versions précédentes pour afficher et restaurer une version précédente d’un répertoire dans Windows.

> [!Note]  
> Vous pouvez effectuer les mêmes opérations au niveau d’un partage et d’un fichier. Seule la version qui contient des modifications pour ce répertoire ou ce fichier figure dans la liste. Si un répertoire ou un fichier particulier n’a pas changé entre deux instantanés de partage, l’instantané de partage apparaît dans la liste des versions précédentes au niveau du partage, mais pas dans la liste des versions précédentes du répertoire ou du fichier.

#### <a name="mount-a-file-share"></a>Monter un partage de fichiers
Pour commencer, montez le partage de fichiers avec la commande `net use`.

#### <a name="open-a-mounted-share-in-file-explorer"></a>Ouvrir un partage monté dans l’Explorateur de fichiers
Accédez à l’Explorateur de fichiers, puis recherchez le partage monté.

![Partage monté dans l’Explorateur de fichiers](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>Répertorier les versions précédentes
Accédez à l’élément ou à l’élément parent à restaurer. Double-cliquez pour accéder au répertoire souhaité. Cliquez avec le bouton droit et sélectionnez **Propriétés** dans le menu.

![Menu contextuel pour un répertoire sélectionné](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

Sélectionnez **Versions précédentes** pour afficher la liste des instantanés de partage pour ce répertoire. Le chargement de la liste peut prendre quelques secondes, selon la vitesse du réseau et le nombre d’instantanés de partage du répertoire.

![Onglet Versions précédentes](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

Vous pouvez sélectionner **Ouvrir** pour ouvrir un instantané particulier. 

![Instantané ouvert](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Restaurer à partir d’une version précédente
Sélectionnez **Restaurer** pour copier à l’emplacement d’origine le contenu de l’ensemble du répertoire de façon récursive à l’heure de création de l’instantané de partage.
 ![Bouton Restaurer dans un message d’avertissement](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="share-snapshot-operations-in-azure-cli-20"></a>Opérations sur les instantanés de partage dans Azure CLI 2.0
Vous pouvez utiliser Azure CLI 2.0 pour effectuer des opérations comme répertorier les instantanés de partage, explorer le contenu des instantanés de partage, restaurer ou télécharger des fichiers à partir des instantanés de partage ou supprimer des instantanés de partage.

#### <a name="list-share-snapshots"></a>Répertorier les instantanés de partage

Vous pouvez répertorier les instantanés d’un partage spécifique avec la commande [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) avec `--include-snapshots` :

```azurecli-interactive 
az storage share list --include-snapshots
```

La commande vous donne une liste d’instantanés de partage, ainsi que toutes leurs propriétés associées. Voici un exemple de sortie :

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

#### <a name="browse-to-a-share-snapshot"></a>Accéder à un instantané de partage
Vous pouvez accéder à un instantané de partage spécifique et voir son contenu avec la commande [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Spécifiez le nom du partage et l’horodatage auquel vous voulez accéder, comme illustré dans l’exemple suivant :

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

Dans la sortie, vous voyez que le contenu de l’instantané de partage est identique au contenu du partage au moment où cet instantané de partage a été créé :

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
#### <a name="restore-from-a-share-snapshot"></a>Restaurer à partir d’un instantané de partage

Vous pouvez restaurer un fichier en le copiant ou le téléchargeant à partir de l’instantané de partage. Utilisez la commande `az storage file download`, comme illustré dans l’exemple suivant :

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

Dans la sortie, vous voyez que le contenu du fichier téléchargé et ses propriétés sont identiques au contenu et aux propriétés au moment de la création de l’instantané de partage :

```json
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

### <a name="file-share-snapshot-operations-in-azure-powershell"></a>Opérations sur les instantanés de partage de fichiers dans Azure PowerShell
Vous pouvez utiliser Azure PowerShell pour effectuer les mêmes opérations, telles que répertorier les instantanés de partage, explorer le contenu des instantanés de partage, restaurer ou télécharger des fichiers à partir des instantanés de partage, ou supprimer des instantanés de partage.

#### <a name="list-share-snapshots"></a>Répertorier les instantanés de partage

Vous pouvez lister les instantanés d’un partage spécifique à l’aide de la commande `Get-AzureStorageShare`.

```powershell
Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
```

#### <a name="browse-share-snapshots"></a>Parcourir les instantanés de partage
Vous pouvez également parcourir un instantané de partage spécifique pour afficher son contenu en utilisant `Get-AzureStorageFile` avec la valeur `-Share` pointant vers cet instantané

```powershell
$snapshot = Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
Get-AzureStorageFile -Share $snapshot
```

#### <a name="restore-from-share-snapshots"></a>Effectuer une restauration à partir d’instantanés de partage

Vous pouvez restaurer un fichier en le copiant ou téléchargeant à partir de l’instantané de partage à l’aide de la commande `Get-AzureStorageFileContent`.

```powershell
$download='C:\Temp\Download'
Get-AzureStorageFileContent -Share $snapshot -Path $file -Destination $download
```

```powershell
$snapshot = Get-AzureStorageShare -Name "ContosoShare06" -SnapshotTime "6/16/2017 9:48:41 AM +00:00"
$directory = Get-AzureStorageFile -ShareName "ContosoShare06" -Path "ContosoWorkingFolder" | Get-AzureStorageFile
Get-AzureStorageFileContent -Share $snapshot -Path $file -Destination $directory
```

## <a name="delete-a-share-snapshot"></a>Supprimer un instantané de partage

Vous pouvez supprimer des instantanés de partage en utilisant le portail Azure, PowerShell, l’interface CLI, l’API REST ou n’importe quel SDK Stockage. Les sections suivantes décrivent comment supprimer un instantané de partage avec le portail Azure, l’interface CLI et PowerShell.

Vous pouvez accéder à des instantanés de partage et voir les différences entre deux instantanés avec n’importe quel outil de comparaison. Vous pouvez ensuite déterminer quel instantané de partage vous voulez supprimer. 

Vous ne pouvez pas supprimer un partage qui a un instantané de partage. Pour pouvoir supprimer le partage, vous devez d’abord supprimer tous ses instantanés de partage.

### <a name="delete-a-share-snapshot-by-using-the-portal"></a>Supprimer un instantané de partage en utilisant le portail  
Dans le portail, vous pouvez accéder au panneau des partages de fichier, puis utiliser le bouton **Supprimer** pour supprimer un ou plusieurs instantanés de partage.

>   ![Bouton Supprimer](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-by-using-azure-cli-20"></a>Supprimer un instantané de partage en utilisant Azure CLI 2.0
Vous pouvez supprimer un instantané de partage avec la commande `[az storage share delete]`. Utilisez l’horodatage de votre instantané de partage pour le paramètre `--snapshot '2017-10-04T23:28:35.0000000Z' ` dans l’exemple suivant :

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Exemple de sortie :
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-by-using-powershell"></a>Supprimer un instantané de partage en utilisant PowerShell
Vous pouvez supprimer un instantané de partage avec la commande `Remove-AzureStorageShare -Share` :

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##Create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>Étapes suivantes
* [Vue d’ensemble des instantanés](storage-snapshots-files.md)
* [Forum aux questions sur les instantanés](storage-files-faq.md)
