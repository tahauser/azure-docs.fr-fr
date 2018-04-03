---
title: 'Démarrage rapide : créer un groupe de machines virtuelles identiques à l’aide d’Azure PowerShell | Microsoft Docs'
description: Apprendre à créer rapidement un groupe de machines virtuelles identiques avec Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/18
ms.author: iainfou
ms.openlocfilehash: 8b56502b216246bd0bce9697eb7b401a89173ba8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-azure-powershell"></a>Démarrage rapide : créer un groupe de machines virtuelles identiques à l’aide d’Azure PowerShell
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Vous pouvez mettre à l’échelle manuellement le nombre de machines virtuelles du groupe identique ou définir des règles de mise à l’échelle automatique en fonction de l’utilisation des ressources telles que l’UC, la demande de mémoire ou le trafic réseau. Un équilibreur de charge Azure distribue ensuite le trafic vers les instances de machine virtuelle du groupe identique. Dans cet article de démarrage rapide, vous créez un groupe de machines virtuelles identiques et déployez un exemple d’application avec Azure PowerShell.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.6.0 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.


## <a name="create-a-scale-set"></a>Créer un groupe identique
Créez un groupe identique de machines virtuelles avec [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). L’exemple suivant crée un groupe identique nommé *myScaleSet* qui utilise l’image de plateforme *Windows Server 2016 Datacenter*. Les ressources réseau Azure pour le réseau virtuel, l’adresse IP publique et l’équilibreur de charge sont automatiquement créées. Lorsque vous y êtes invité, fournissez vos propres informations d’identification d’administration souhaitées pour les instances de machine virtuelle dans le groupe identique :

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic"
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.


## <a name="deploy-sample-application"></a>Déployer un exemple d’application
Pour tester votre groupe identique, installez une application web de base. L’extension de script personnalisé Azure permet de télécharger et d’exécuter un script qui installe IIS sur les instances de machine virtuelle. Cette extension est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Pour plus d’informations, consultez [Vue d’ensemble de l’extension de script personnalisé](../virtual-machines/windows/extensions-customscript.md).

Utilisez l’extension de script personnalisé pour installer un serveur web IIS de base. Appliquez l’extension de script personnalisé qui installe IIS comme suit :

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-scale-set"></a>Tester votre groupe identique
Pour voir votre groupe identique en action, accédez à l’exemple d’application web dans un navigateur web. Obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L’exemple suivant obtient l’adresse IP créée dans le groupe de ressources *myResourceGroup* :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Saisissez l’adresse IP publique de l’équilibreur de charge dans un navigateur web. L’équilibreur de charge répartit le trafic vers l’une de vos instances de machine virtuelle, comme illustré dans l’exemple suivant :

![Site IIS en cours d’exécution](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Supprimer des ressources
Quand vous n’en avez plus besoin, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, le groupe identique et toutes les ressources associées comme suit. Le paramètre `-Force` confirme que vous souhaitez supprimer les ressources sans passer par une invite supplémentaire à cette fin. Le paramètre `-AsJob` retourne le contrôle à l’invite de commandes sans attendre que l’opération se termine.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Étapes suivantes
Dans cet article de démarrage rapide, vous avez créé un groupe identique de base et vous avez utilisé l’extension de script personnalisé afin d’installer un serveur web IIS de base sur les instances de machine virtuelle. Pour en savoir plus, passez au didacticiel pour savoir comment créer et gérer des groupes de machines virtuelles identiques Azure.

> [!div class="nextstepaction"]
> [Créer et gérer des groupes de machines virtuelles identiques Azure](tutorial-create-and-manage-powershell.md)