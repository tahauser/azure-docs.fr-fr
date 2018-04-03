---
title: Gestion des partages de fichiers Azure avec Azure PowerShell
description: Découvrez comment gérer les partages de fichiers Azure avec Azure PowerShell.
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: c30bcc293cfe57452844dd74378593c234146802
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-azure-powershell"></a>Gestion des partages de fichiers Azure avec Azure PowerShell 
[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés dans Windows, Linux et macOS. Ce guide vous explique les bases de l’utilisation du partage de fichiers avec PowerShell. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un groupe de ressources et un compte de stockage
> * Crée un partage de fichiers Azure 
> * Créer un répertoire
> * Charger un fichier 
> * Téléchargement d’un fichier
> * Créer et utiliser un instantané de partage

Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous souhaitez installer et utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.1.1 ou version ultérieure pour les besoins de ce guide. Pour connaître la version du module Azure PowerShell que vous exécutez, utilisez la commande `Get-Module -ListAvailable AzureRM`. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Si vous n’avez pas déjà un groupe de ressources Azure, vous pouvez en créer un avec la cmdlet [`New-AzureRmResourceGroup`](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

L’exemple suivant permet de créer un groupe de ressources nommé *myResourceGroup* dans la région Est des États-Unis :

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
Un compte de stockage est un pool partagé de stockage que vous pouvez utiliser pour déployer un partage de fichiers Azure, ou d’autres ressources de stockage comme les objets blob ou les files d’attente. Un compte de stockage peut contenir un nombre illimité de partages, et un partage peut stocker un nombre illimité de fichiers, dans les limites de capacité du compte de stockage.

Cet exemple crée un compte de stockage nommé `mystorageacct<random number>` et place une référence à ce compte de stockage dans la variable **$storageAcct**. Les noms de compte de stockage doivent être uniques. Par conséquent, utilisez `Get-Random` pour ajouter un numéro au nom pour le rendre unique. 

```azurepowershell-interactive 
$storageAcct = New-AzureRmStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure
Vous pouvez désormais créer votre premier partage de fichiers Azure. Vous pouvez créer un partage de fichier à l’aide de la cmdlet [New-AzureStorageShare](/powershell/module/azurerm.storage/new-azurestorageshare). Cet exemple crée un partage nommé `myshare`.

```azurepowershell-interactive
New-AzureStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

> [!Important]  
> Le nom des partages ne doit contenir que des minuscules, des nombres et des traits d’union uniques, mais ne peut commencer par un trait d’union. Pour plus d’informations sur la façon de nommer des partages de fichiers et des fichiers, consultez la rubrique [Affectation de noms et références aux partages, répertoires, fichiers et métadonnées](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Manipulation du contenu du partage de fichiers Azure
Maintenant que vous avez créé un partage de fichiers Azure, vous pouvez monter le partage de fichiers avec SMB sur [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) ou [macOS](storage-how-to-use-files-mac.md). Vous pouvez également manipuler votre partage de fichiers Azure avec le module Azure PowerShell. Cette méthode est d’ailleurs plus avantageuse que le montage du partage de fichiers avec SMB, car toutes les requêtes faites avec PowerShell sont faites avec l’API REST File, ce qui vous permet de créer, modifier et supprimer des fichiers et des répertoires dans votre partage de fichiers depuis :

- PowerShell Cloud Shell (qui ne peut pas monter les partages de fichiers sur SMB).
- Les clients qui ne peuvent pas monter de partages SMB, comme les clients locaux qui n’ont pas débloqué le port 445.
- Les scénarios sans serveur, comme dans [Azure Functions](../../azure-functions/functions-overview.md). 

### <a name="create-directory"></a>Créer un répertoire
Pour créer un répertoire nommé *myDirectory* à la racine de votre partage de fichiers Azure, utilisez la cmdlet [`New-AzureStorageDirectory`](/powershell/module/azurerm.storage/new-azurestoragedirectory).

```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

### <a name="upload-a-file"></a>Charger un fichier
Pour montrer comment charger un fichier avec la cmdlet [`Set-AzureStorageFileContent`](/powershell/module/azure.storage/set-azurestoragefilecontent), nous devons d’abord créer un fichier dans le disque de travail de PowerShell Cloud Shell à charger. 

Cet exemple place la date et l’heure actuelles dans un nouveau fichier sur votre disque de travail, puis charge le fichier vers le partage de fichiers.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzureStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Si vous exécutez PowerShell en local, vous devez remplacer `C:\Users\ContainerAdministrator\CloudDrive\` par un chemin existant sur votre machine.

Après avoir chargé le fichier, vous pouvez exécuter la cmdlet [`Get-AzureStorageFile`](/powershell/module/Azure.Storage/Get-AzureStorageFile) pour vérifier que le fichier a bien été chargé dans votre partage de fichiers Azure. 

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

### <a name="download-a-file"></a>Téléchargement d’un fichier
Vous pouvez exécuter la cmdlet [`Get-AzureStorageFileContent`](/powershell/module/azure.storage/get-azurestoragefilecontent) pour télécharger une copie du fichier que vous venez de charger sur le disque de travail de Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzureStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

Une fois le fichier téléchargé, vous pouvez utiliser `Get-ChildItem` pour voir si le fichier a été téléchargé sur votre disque de travail de PowerShell Cloud Shell.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

### <a name="copy-files"></a>Copie des fichiers
Une tâche courante consiste à copier les fichiers d’un partage de fichiers vers un autre partage de fichiers, ou dans/depuis un conteneur de stockage d’objets blob Azure. Pour illustrer cette fonctionnalité, vous pouvez créer un nouveau partage et copier le fichier que vous venez de charger dans ce nouveau partage avec la cmdlet [`Start-AzureStorageFileCopy`](/powershell/module/azure.storage/start-azurestoragefilecopy). 

```azurepowershell-interactive
New-AzureStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzureStorageFileCopy 
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Maintenant, si vous répertoriez les fichiers dans le nouveau partage, vous devez voir votre fichier copié.

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -Share "myshare2" -Path "myDirectory2" 
```

Bien que la cmdlet `Start-AzureStorageFileCopy` soit pratique pour déplacer des fichiers ad-hoc entre des partages de fichiers Azure et des conteneurs de stockage d’objets blob Azure, nous recommandons AzCopy pour des déplacements plus volumineux (en termes de nombre ou de taille des fichiers déplacés). En savoir plus sur [AzCopy pour Windows](../common/storage-use-azcopy.md) et [AzCopy pour Linux](../common/storage-use-azcopy-linux.md). AzCopy doit être installé en local (il n’est pas disponible dans Cloud Shell) 

## <a name="create-and-modify-share-snapshots"></a>Créer et modifier des instantanés de partage
Avec un partage de fichiers Azure, vous pouvez aussi créer des instantanés de partage. Un instantané conserve un point dans le temps pour un partage de fichiers Azure. Les instantanés de partage sont similaires aux technologies de systèmes d’exploitation que vous connaissez peut-être déjà comme :
- Le [service VSS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) pour les systèmes de fichiers Windows comme NTFS et ReFS
- Les instantanés du [Gestionnaire de Volume logique (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pour les systèmes Linux
- Les instantanés du [système de fichiers Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pour macOS. 

Vous pouvez créer un instantané de partage pour un partage à l’aide de la méthode `Snapshot` sur un objet PowerShell pour un partage de fichiers, qui est récupéré avec la cmdlet [`Get-AzureStorageShare`](/powershell/module/azure.storage/get-azurestorageshare). 

```azurepowershell-interactive
$share = Get-AzureStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Parcourir les instantanés de partage
Vous pouvez parcourir le contenu de l’instantané de partage en transmettant la référence d’instantané (`$snapshot`) au paramètre `-Share` de la cmdlet `Get-AzureStorageFile`.

```azurepowershell-interactive
Get-AzureStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Répertorier les instantanés de partage
Vous pouvez afficher la liste d’instantanés pris pour votre partage avec la commande suivante.

```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Restaurer à partir d’un instantané de partage
Vous pouvez restaurer un fichier avec la commande `Start-AzureStorageFileCopy` que nous avons utilisée plus tôt. Dans le cadre de ce démarrage rapide, nous allons tout d’abord supprimer notre fichier `SampleUpload.txt` que nous avons téléchargé précédemment afin de pouvoir le restaurer à partir de l’instantané.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzureStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzureStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Supprimer un instantané de partage
Pour supprimer un instantané de capture, exécutez la cmdlet [`Remove-AzureStorageShare`](/powershell/module/azure.storage/remove-azurestorageshare), avec la variable contenant la référence `$snapshot` du paramètre `-Share`.

```azurepowershell-interactive
Remove-AzureStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous avez terminé, vous pouvez utiliser la cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources associées. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Vous pouvez aussi supprimer les ressources une par une :

- Pour supprimer les partages de fichiers Azure que nous avons créés pour ce guide de démarrage rapide.
```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
    Remove-AzureStorageShare -Context $storageAcct.Context -Name $_.Name
}
```

- Pour supprimer le compte de stockage (ce qui supprimera par la même occasion les partages de fichiers créés ainsi que toutes les autres ressources de stockage créées telles qu’un conteneur de stockage d’objets blob Azure).
```azurepowershell-interactive
Remove-AzureRmStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
```

## <a name="next-steps"></a>Étapes suivantes
- [Gestion des partages de fichiers avec le portail Azure](storage-how-to-use-files-portal.md)
- [Gestion des partages de fichiers avec Azure CLI](storage-how-to-use-files-cli.md)
- [Gestion des partages de fichiers avec l’Explorateur Stockage](storage-how-to-use-files-storage-explorer.md)
- [Planification d’un déploiement Azure Files](storage-files-planning.md)