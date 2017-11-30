---
title: "Démarrage rapide Azure - Créer un compte de stockage à l’aide de PowerShell | Microsoft Docs"
description: "Apprendre rapidement à créer un compte de stockage avec PowerShell"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/29/2017
ms.author: tamram
ms.openlocfilehash: b4b917adfb3644cca71b6696df005fbf9e295240
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-storage-account-using-powershell"></a>Créer un compte de stockage à l’aide de PowerShell

Le module Azure PowerShell est utilisé pour créer et gérer des ressources Azure à partir de la ligne de commande PowerShell ou dans des scripts. Ce guide décrit en détail l’utilisation de PowerShell pour créer un compte de stockage Azure. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Ce démarrage rapide requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

Si vous ne savez pas quelle localisation utiliser, affichez la liste des localisations disponibles. Dans la liste, trouvez celle que vous souhaitez utiliser. Cet exemple utilise la localisation **eastus**. Stockez-la dans une variable et utilisez cette variable. Vous pourrez ainsi changer son contenu dans un seul emplacement.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Créer un compte de stockage standard à usage général

Il existe plusieurs types de comptes de stockage, selon le mode d’utilisation envisagé et le service (objets blob, fichiers, tables ou files d’attente) auxquels ils sont destinés. Ce tableau présente les possibilités.

|**Type de compte de stockage**|**Édition Standard à usage général**|**Édition Premium à usage général**|**Stockage d’objets blob, niveaux d’accès froid et chaud**|
|-----|-----|-----|-----|
|**Services pris en charge**| Services d’objet blob, de fichier, de table et de file d’attente | Service d'objets blob | Service d'objets blob|
|**Types d’objets blob pris en charge**|Objets blob de blocs, de pages et d’ajout | Objets blob de pages | Objets blob de blocs et objets blob d’ajout|

Utilisez la commande [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) pour créer un compte de stockage standard à usage général que vous pouvez utiliser pour les quatre services. Nommez le compte de stockage *contosomvcstandard*, puis configurez-le pour activer le stockage localement redondant et le chiffrement d’objet blob.

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources associées. Dans ce cas, la commande supprime le compte de stockage que vous avez créé.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un compte de stockage standard à usage général. Pour savoir comment charger et télécharger des objets blob avec votre compte de stockage, passez au démarrage rapide du Stockage Blob.
> [!div class="nextstepaction"]
> [Transférer des objets à destination/à partir du stockage Blob Azure à l’aide de PowerShell](../blobs/storage-quickstart-blobs-powershell.md)