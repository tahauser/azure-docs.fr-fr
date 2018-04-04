---
title: 'Didacticiel : utiliser une image de machine virtuelle personnalisée dans un groupe identique avec Azure PowerShell | Microsoft Docs'
description: Découvrez comment utiliser Azure PowerShell afin de créer une image de machine virtuelle personnalisée que vous pouvez utiliser pour déployer un groupe de machines virtuelles identiques
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a6ff5ebdf678972c686c972fd03041b362c1ff81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Didacticiel : créer et utiliser une image personnalisée pour des groupes de machines virtuelles identiques avec Azure PowerShell
Lorsque vous créez un groupe identique, vous spécifiez une image à utiliser lors du déploiement des instances de machine virtuelle. Pour réduire le nombre de tâches une fois que les instances de machine virtuelle sont déployées, vous pouvez utiliser une image de machine virtuelle personnalisée. Cette image de machine virtuelle personnalisée inclut les configurations ou installations d’applications requises. Toutes les instances de machine virtuelle créées dans le groupe identique utilisent l’image de machine virtuelle personnalisée et sont prêtes à répondre à votre trafic d’application. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer et personnaliser une machine virtuelle
> * Déprovisionner et généraliser la machine virtuelle
> * Créer une image de machine virtuelle personnalisée à partir de la machine virtuelle source
> * Déployer un groupe identique qui utilise l’image de machine virtuelle personnalisée

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.6.0 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 


## <a name="create-and-configure-a-source-vm"></a>Créer et configurer une machine virtuelle source
Créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup), puis créez une machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Cette machine virtuelle est ensuite utilisée comme source d’une image de machine virtuelle personnalisée. L’exemple suivant montre la création d’une machine virtuelle nommée *myCustomVM* dans le groupe de ressources nommé *myResourceGroup*. Lorsque vous y êtes invité, entrez un nom d’utilisateur et un mot de passe qui serviront d’informations d’identification pour ouvrir une session de la machine virtuelle :

```azurepowershell-interactive
# Create a resource a group
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Pour vous connecter à votre machine virtuelle, obtenez l’adresse IP publique avec [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) comme suit :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Créez une connexion distante avec la machine virtuelle. Si vous utilisez Azure Cloud Shell, effectuez cette étape à partir d’une invite de commandes PowerShell locale ou du client Bureau à distance. Fournissez votre propre adresse IP à partir de la commande précédente. À l’invite, saisissez les informations d’identification que vous avez utilisées lors de la création de la machine virtuelle au moment de la première étape :

```powershell
mstsc /v:<IpAddress>
```

Pour personnaliser votre machine virtuelle, nous allons installer un serveur web de base. Lorsque l’instance de machine virtuelle dans le groupe identique est déployée, elle doit avoir tous les packages nécessaires pour exécuter une application web. Ouvrez une invite de commandes PowerShell locale sur la machine virtuelle et installez le serveur web IIS avec [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) comme suit :

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

La dernière étape de préparation de votre machine virtuelle pour une utilisation en tant qu’image personnalisée consiste à généraliser la machine virtuelle. Sysprep supprime toutes les informations et les configurations de votre compte personnel, et réinitialise l’état de la machine virtuelle pour les déploiements futurs. Pour plus d’informations, consultez [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

Pour généraliser la machine virtuelle, exécutez Sysprep et définissez la machine virtuelle dans l’optique d’une expérience prête à l’emploi. Lorsque vous avez terminé, demandez à Sysprep d’arrêter la machine virtuelle :

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

La connexion distante à la machine virtuelle est fermée automatiquement lorsque Sysprep termine le processus et que la machine virtuelle est arrêtée.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Créer une image de machine virtuelle personnalisée à partir de la machine virtuelle source
La machine virtuelle source est à présent personnalisée avec le serveur web IIS installé. Nous allons créer l’image de machine virtuelle personnalisée à utiliser avec un groupe identique.

Pour créer une image, la machine virtuelle doit être libérée. Libérez la machine virtuelle avec la commande [Stop-AzureRmVm](/powershell/module/azurerm.compute/stop-azurermvm). Définissez ensuite l’état de la machine virtuelle comme généralisé avec [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm) afin que la plateforme Azure sache que la machine virtuelle est prête pour l’utilisation d’une image personnalisée. Vous pouvez uniquement créer une image à partir d’une machine virtuelle généralisée :

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

Quelques minutes peuvent être nécessaires pour libérer et généraliser la machine virtuelle.

Créez à présent une image de la machine virtuelle avec [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) et [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). L’exemple suivant crée une image nommée *myImage* à partir de votre machine virtuelle :

```azurepowershell-interactive
# Get VM object
$vm = Get-AzureRmVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzureRmImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzureRmImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Créer un groupe identique à partir de l’image de machine virtuelle personnalisée
Créez à présent un groupe identique avec [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) qui utilise le paramètre `-ImageName` pour définir l’image de machine virtuelle personnalisée créée à l’étape précédente. Pour distribuer le trafic aux différentes instances de machine virtuelle, un équilibreur de charge est également créé. L’équilibreur de charge inclut des règles pour distribuer le trafic sur le port TCP 80, ainsi que pour autoriser le trafic Bureau à distance sur le port TCP 3389 et le trafic Accès distant PowerShell sur le port TCP 5985 :

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -ImageName "myImage"
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.


## <a name="test-your-scale-set"></a>Tester votre groupe identique
Pour voir votre groupe identique en action, obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress), comme suit :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Entrez l’adresse IP publique dans votre navigateur web. La page web IIS par défaut s’affiche comme dans l’exemple suivant :

![IIS s’exécutant à partir d’une image de machine virtuelle personnalisée](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Supprimer des ressources
Pour supprimer votre groupe identique et les ressources supplémentaires, supprimez le groupe de ressources et toutes ses ressources avec [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Le paramètre `-Force` confirme que vous souhaitez supprimer les ressources sans passer par une invite supplémentaire à cette fin. Le paramètre `-AsJob` retourne le contrôle à l’invite de commandes sans attendre que l’opération se termine.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à créer et utiliser une image de machine virtuelle personnalisée pour vos groupes identiques au moyen d’Azure PowerShell :

> [!div class="checklist"]
> * Créer et personnaliser une machine virtuelle
> * Déprovisionner et généraliser la machine virtuelle
> * Créer une image de machine virtuelle personnalisée
> * Déployer un groupe identique qui utilise l’image de machine virtuelle personnalisée

Passez au didacticiel suivant pour apprendre à déployer des applications dans votre groupe identique.

> [!div class="nextstepaction"]
> [Déployer des applications dans vos groupes identiques](tutorial-install-apps-powershell.md)