---
title: Démarrage rapide Azure - Charger, télécharger et répertorier des objets blob dans Stockage Azure à l’aide de Azure PowerShell | Microsoft Docs
description: Dans le cadre de ce démarrage rapide, vous utilisez Azure PowerShell pour créer un compte de stockage et un conteneur. Ensuite, vous utilisez PowerShell pour charger un objet blob dans Stockage Azure, télécharger un objet blob et répertorier les objets blob dans un conteneur.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: rogarana
ms.openlocfilehash: 29eb69f95680babfc1004d483a6a1c2bc44edda8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-azure-powershell"></a>Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide de Azure PowerShell

Le module Azure PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande PowerShell ou dans des scripts. Ce guide décrit en détail l’utilisation de PowerShell pour transférer des fichiers entre un disque local et le Stockage Blob Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Ce démarrage rapide requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Créez un conteneur.

Les objets blob sont toujours chargés dans un conteneur. Vous pouvez organiser des groupes d’objets blob de la même façon que vous organisez vos fichiers dans les dossiers de l’ordinateur.

Définissez le nom du conteneur, puis créez le conteneur à l’aide de la commande [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer), et définissez les autorisations sur « blob » pour autoriser l’accès public aux fichiers. Le nom du conteneur dans cet exemple est *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Charger des objets blob dans le conteneur

Le Stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. Les fichiers de disque dur virtuel utilisés pour appuyer les machines virtuelles IaaS sont des objets blob de pages. Les objets blob d’ajout sont utilisés pour la journalisation, par exemple, quand vous voulez écrire dans un fichier et continuer à ajouter d’autres informations. La plupart des fichiers stockés dans Stockage Blob sont des objets blob de blocs. 

Pour charger un fichier sur un objet blob de blocs, obtenez une référence de conteneur, puis obtenez une référence à l’objet blob de blocs dans ce conteneur. Une fois que vous avez la référence à l’objet blob, vous pouvez y charger des données à l’aide de [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Cette opération crée l’objet blob s’il n’existe pas déjà, et le remplace s’il existe.

Les exemples suivants chargent Image001.jpg et Image002.png à partir du dossier D:\\_TestImages sur le disque local, dans le conteneur que vous avez créé.

```powershell
# upload a file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Chargez autant de fichiers que vous le souhaitez avant de continuer.

## <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Obtenez la liste des objets blob dans le conteneur à l’aide de la commande [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob). Cet exemple montre uniquement les noms des objets blob chargés.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Télécharger des objets blob

Téléchargez les objets blob sur votre disque local. Pour chaque objet blob à télécharger, définissez le nom et appelez [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) pour télécharger l’objet blob.

Cet exemple télécharge les objets blob dans D:\\_TestImages\Downloads sur le disque local. 

```powershell
# download first blob
Get-AzureStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzureStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 
```

## <a name="data-transfer-with-azcopy"></a>Transfert de données avec AzCopy

L’utilitaire [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) est une autre option hautes performances pour transférer des données par script avec le stockage Azure. Vous pouvez utiliser AzCopy pour transférer des données à destination et à partir du stockage Blob, Table et Fichier.

Exemple rapide : voici la commande AzCopy qui permet de charger un fichier appelé *myfile.txt* dans le conteneur *mystoragecontainer* à partir de la fenêtre de PowerShell.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Supprimez toutes les ressources que vous avez créées. Pour ce faire, le plus simple consiste à supprimer le groupe de ressources. Cette opération supprime également toutes les ressources contenues dans le groupe. Dans le cas présent, le compte de stockage et le groupe de ressources sont supprimés.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et le stockage Blob Azure. Pour en savoir plus sur l’utilisation de Stockage Blob, consultez le Guide pratique de Stockage Blob.

> [!div class="nextstepaction"]
> [Guide pratique des opérations de stockage Blob](storage-how-to-use-blobs-powershell.md)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Référence des applets de commande de stockage Microsoft Azure PowerShell
* [Applets de commande PowerShell - Stockage](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorateur Stockage Microsoft Azure
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.