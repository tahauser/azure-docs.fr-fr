---
title: "Créer une machine virtuelle Windows avec l’applet de commande New-AzureRMVM simplifiée dans Azure Cloud Shell | Microsoft Docs"
description: "Apprenez rapidement à créer des machines virtuelles Windows avec l’applet de commande New-AzureRMVM simplifiée dans Azure Cloud Shell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 94eb6232cf59d502a9d70545785c3788398f4d27
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2017
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>Créer une machine virtuelle Windows avec l’applet de commande New-AzureRMVM simplifiée dans Cloud Shell 

L’applet de commande [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) a ajouté un jeu de paramètres simplifié pour la création d’une nouvelle machine virtuelle à l’aide de PowerShell. Cette rubrique explique comment utiliser PowerShell dans Azure Cloud Shell, avec la dernière version de l’applet de commande New-AzureVM préinstallée, pour créer une machine virtuelle. Nous allons utiliser un jeu de paramètres simplifié qui crée automatiquement toutes les ressources nécessaires à l’aide de valeurs par défaut pertinentes. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.1.1 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-the-vm"></a>Création de la machine virtuelle

Vous pouvez utiliser l’applet de commande [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) pour créer une machine virtuelle avec les valeurs par défaut, ce qui implique l’utilisation de l’image Windows Server 2016 Datacenter de Place de marché Azure. Vous pouvez utiliser New-AzureRMVM avec le paramètre **-Name** uniquement, et l’applet de commande utilisera cette valeur pour tous les noms de ressources. Dans cet exemple, nous définissons le paramètre **-Name** en tant que *myVM*. 

Vérifiez que **PowerShell** est sélectionné dans Cloud Shell et tapez :

```azurepowershell-interactive
New-AzureRMVm -Name myVM
```

Vous êtes invité à créer un nom d’utilisateur et un mot de passe pour la machine virtuelle, qui seront utilisés quand vous vous connecterez à la machine virtuelle plus loin dans cette rubrique. Le mot de passe doit compter 12 à 123 caractères et comprendre trois des quatre caractères suivants : une minuscule, une majuscule, un chiffre et un caractère spécial.

La création de la machine virtuelle et des ressources associées prend une minute. Lorsque vous avez terminé, vous pouvez voir toutes les ressources qui ont été créées avec l’applet de commande [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

Une fois le déploiement terminé, créez une connexion Bureau à distance avec la machine virtuelle.

Utilisez la commande [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) pour renvoyer l’adresse IP publique de la machine virtuelle. Notez cette adresse IP.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Sur votre ordinateur local, ouvrez une invite de commandes et utilisez la commande **mstsc** pour démarrer une session Bureau à distance avec votre nouvelle machine virtuelle. Remplacez &lt;publicIPAddress&gt; par l’adresse IP de votre machine virtuelle. Lorsque vous y êtes invité, entrez le nom d’utilisateur et le mot de passe que vous aviez fournis pour votre machine virtuelle lors de sa création.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Spécifier des noms de ressources différents

Vous pouvez également fournir des noms de ressources plus descriptifs et continuer à les créer automatiquement. Dans l’exemple suivant, nous avons nommé plusieurs ressources pour la nouvelle machine virtuelle, y compris un nouveau groupe de ressources.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVM
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans cette rubrique, vous avez déployé une machine virtuelle simple avec New-AzVM et vous l’avez connectée via RDP. Pour en savoir plus sur les machines virtuelles Azure, suivez le didacticiel pour les machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Windows](./tutorial-manage-vm.md)
