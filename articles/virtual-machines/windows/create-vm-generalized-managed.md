---
title: Créer une machine virtuelle à partir d’une image managée dans Azure | Microsoft Docs
description: Créez une machine virtuelle Windows à partir d’une image managée généralisée à l’aide d’Azure PowerShell ou du portail Azure, dans le modèle de déploiement Gestionnaire des ressources.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: cynthn
ms.openlocfilehash: 1d543bd9590664e74cff70cf55e8f7bd42f2c6f0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-vm-from-a-managed-image"></a>Créer une machine virtuelle à partir d’une image gérée

Vous pouvez créer plusieurs machines virtuelles à partir d’une image de machine virtuelle managée à l’aide de PowerShell ou du portail Azure. Une image de machine virtuelle managée contient les informations nécessaires pour créer une machine virtuelle, y compris le disque du système d’exploitation et les disques de données. Les disques durs virtuels qui composent l’image, y compris les disques du système d’exploitation et les disques de données, sont stockés en tant que disques managés. 

Vous devez déjà avoir [créé une image de machine virtuelle managée](capture-image-resource.md) à utiliser pour créer la nouvelle machine virtuelle. 

## <a name="use-the-portal"></a>Utiliser le portail 

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Toutes les ressources**. Vous pouvez trier les ressources par **Type** pour rechercher facilement vos images.
3. Sélectionnez l’image à utiliser dans la liste. La page **Vue d’ensemble** de l’image s’ouvre.
4. Cliquez sur **+ Créer machine virtuelle** dans le menu.
5. Saisissez les informations de la machine virtuelle. Le nom d’utilisateur et le mot de passe que vous avez entrés vous serviront pour vous connecter à la machine virtuelle. Lorsque vous avez terminé, cliquez sur **OK**. Vous pouvez créer la nouvelle machine virtuelle dans un groupe de ressources existant ou sélectionner **Créer nouveau** pour créer un nouveau groupe de ressources pour stocker la machine virtuelle.
6. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. 
7. Sous **Paramètres**, procédez aux modifications nécessaires et cliquez sur **OK**. 
8. Dans la page Résumé, vous pouvez voir le nom de votre image dans la liste **Image privée**. Cliquez sur **Ok** pour démarrer le déploiement de la machine virtuelle.


## <a name="use-powershell"></a>Utiliser PowerShell

Vous pouvez utiliser PowerShell pour créer une machine virtuelle à partir d’une image à l’aide du paramètre simplifié défini pour la cmdlet [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). L’image doit se trouver dans le même groupe de ressources que celui dans lequel vous souhaitez créer la machine virtuelle.

Cet exemple nécessite l’utilisation du module AzureRM version 5.6.0 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

Le paramètre simplifié défini pour New-AzureRmVm nécessite uniquement un nom, le nom du groupe de ressources et de l’image pour créer une machine virtuelle à partir d’une image, mais il utilise la valeur du paramètre **-Nom** comme nom de toutes les ressources qu’il crée automatiquement. Dans cet exemple, nous fournissons des noms plus détaillés pour chaque ressource, mais laissons la cmdlet les créer automatiquement. Vous pouvez également créer des ressources, comme le réseau virtuel, en avance et passer le nom dans la cmdlet. Il utilisera les ressources existantes s’il peut les trouver par leur nom.

L’exemple suivant crée une machine virtuelle nommée *myVMfromImage* dans le groupe de ressources *myResourceGroup*, à partir de l’image nommée *myImage*. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>Étapes suivantes
Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, voir [Créer et gérer des machines virtuelles Windows avec le module Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

