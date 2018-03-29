---
title: Créer un groupe de machines virtuelles identiques à l’aide d’Azure PowerShell | Microsoft Docs
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
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: c5ca90ae303d62c4ad453971f84da78866b0c599
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2018
---
# <a name="create-a-virtual-machine-scale-set-with-azure-powershell"></a>Créer un groupe de machines virtuelles identiques à l’aide d’Azure PowerShell
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Vous pouvez mettre à l’échelle manuellement le nombre de machines virtuelles du groupe identique ou définir des règles pour mettre à l’échelle automatiquement en fonction de l’utilisation des ressources (processeur, demande de mémoire ou trafic réseau). Dans cet article de prise en main, vous créez un groupe de machines virtuelles identiques avec Azure PowerShell. Vous pouvez également créer un groupe identique avec [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md) ou le [portail Azure](virtual-machine-scale-sets-create-portal.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 4.4.1 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.


## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Avant de créer un groupe identique, créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *EastUS* :

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```


## <a name="create-load-balancer"></a>Créer un équilibreur de charge
Les instances de machine virtuelle dans un groupe identique sont connectées à un équilibreur de charge. Un équilibreur de charge Azure est un équilibreur de charge de type Couche 4 (TCP, UDP) qui offre une haute disponibilité en répartissant le trafic entrant entre les machines virtuelles saines. Une sonde d’intégrité d’équilibreur de charge surveille un port donné sur chaque machine virtuelle et ne distribue le trafic que vers une machine virtuelle opérationnelle.

Créez un réseau virtuel et un équilibreur de charge doté d’une adresse IP publique qui distribue le trafic web sur le port 80. Pour créer ces ressources, copiez et collez les cmdlets PowerShell suivantes :

```azurepowershell-interactive
# Create a virtual network subnet
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVnet" `
  -Location "EastUS" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet

# Create a public IP address
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -Name "myPublicIP"

# Create a frontend and backend IP pool
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"

# Create a Network Address Translation (NAT) pool
$inboundNATPool = New-AzureRmLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389

# Create the load balancer
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName "myResourceGroup" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool

# Create a load balancer health probe on port 80
Add-AzureRmLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2

# Create a load balancer rule to distribute traffic on port 80
Add-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80

# Update the load balancer configuration
Set-AzureRmLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzureRmVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id
```


## <a name="create-a-scale-set"></a>Créer un groupe identique
À présent, créez un groupe de machines virtuelles identiques avec [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). L’exemple suivant crée un groupe identique nommé *myScaleSet* qui utilise l’image de plateforme *Windows Server 2016 Datacenter*. L’objet *vmssConfig* crée 2 instances de machine virtuelle dans East US avec les informations d’identification telles que spécifiées dans les variables *adminUsername* et *securePassword*. Fournissez vos propres informations d’identification et créez un groupe identique comme suit :

```azurepowershell-interactive
# Provide your own secure password for use with the VM instances
$securePassword = "P@ssword!"
$adminUsername = "azureuser"

# Create a config object
$vmssConfig = New-AzureRmVmssConfig `
    -Location "EastUS" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher "MicrosoftWindowsServer" `
  -ImageReferenceOffer "WindowsServer" `
  -ImageReferenceSku "2016-Datacenter" `
  -ImageReferenceVersion "latest"

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername $adminUsername `
  -AdminPassword $securePassword `
  -ComputerNamePrefix "myVM"

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmssConfig
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.


## <a name="install-iis-webserver"></a>Installer le serveur web IIS
Pour tester votre groupe identique, utilisez l’extension de script personnalisé pour télécharger et exécuter un script qui installe IIS sur les instances de machine virtuelle. Cette extension est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Pour plus d’informations, consultez [Vue d’ensemble de l’extension de script personnalisé](../virtual-machines/windows/extensions-customscript.md).

Appliquez l’extension de script personnalisé qui installe IIS comme suit :

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


## <a name="test-your-web-server"></a>Tester votre serveur web
Pour voir fonctionner votre serveur web, obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L’exemple suivant obtient l’adresse IP créée dans le groupe de ressources *myResourceGroup* :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Saisissez l’adresse IP publique de l’équilibreur de charge dans un navigateur web. L’équilibreur de charge répartit le trafic vers l’une de vos instances de machine virtuelle, comme illustré dans l’exemple suivant :

![Site IIS en cours d’exécution](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Supprimer des ressources
Quand vous n’en avez plus besoin, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, le groupe identique et toutes les ressources associées comme suit :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Étapes suivantes
Dans cet article de prise en main, vous avez créé un groupe identique de base et vous avez utilisé l’extension de script personnalisé afin d’installer un serveur web IIS de base sur les instances de machine virtuelle. Pour plus d’automatisation et d’évolutivité, développez votre groupe identique à l’aide des articles de procédures suivants :

- [Déployer votre application sur des groupes de machines virtuelles identiques](virtual-machine-scale-sets-deploy-app.md)
- Faites une mise à l’échelle automatique avec [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md) ou le [portail Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Mises à niveau automatiques du système d’exploitation dans des groupes de machines virtuelles identiques Azure](virtual-machine-scale-sets-automatic-upgrade.md)
