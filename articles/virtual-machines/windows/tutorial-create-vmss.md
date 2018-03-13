---
title: "Créer un groupe de machines virtuelles identiques pour Windows dans Azure | Microsoft Docs"
description: "Créez et déployez une application hautement disponible sur des machines virtuelles Windows en utilisant un groupe de machines virtuelles identiques"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d190d046f7572c51df0c5c9e14e14a41d93e3248
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows"></a>Créer un groupe de machines virtuelles identiques et déployer une application hautement disponible sur Windows
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Vous pouvez mettre à l’échelle manuellement le nombre de machines virtuelles du groupe identique ou définir des règles pour mettre à l’échelle automatiquement en fonction de l’utilisation des ressources (processeur, demande de mémoire ou trafic réseau). Ce didacticiel explique comment déployer un groupe de machines virtuelles identiques dans Azure. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Utiliser l’extension de script personnalisé pour définir un site IIS à mettre à l’échelle
> * Créer un équilibrage de charge pour votre groupe identique
> * Créer un groupe de machines virtuelles identiques
> * Augmenter ou réduire le nombre d’instances dans un groupe identique
> * Créer des règles de mise à l’échelle automatique

Ce didacticiel requiert le module Azure PowerShell version 5.1.1 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="scale-set-overview"></a>Vue d’ensemble des groupes identiques
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Les machines virtuelles d’un groupe identique sont réparties entre les domaines d’erreur logique et de mise à jour, dans un ou plusieurs *groupes de placement*. Ces groupes contiennent des machines virtuelles configurées de manière similaire, semblables à des [groupes à haute disponibilité](tutorial-availability-sets.md).

Les machines virtuelles sont créées en fonction des besoins dans un groupe identique. En définissant des règles de mise à l’échelle automatique, vous pouvez contrôler quand et comment les machines virtuelles sont ajoutées ou supprimées au niveau du groupe identique. Ces règles peuvent se déclencher en fonction de mesures telles que la charge du processeur, l’utilisation de la mémoire ou le trafic réseau.

Les groupes identiques peuvent prendre en charge jusqu’à 1 000 machines virtuelles quand vous utilisez une image de plateforme Azure. Pour les charges de travail qui s’accompagnent de contraintes importantes en matière d’installation ou de personnalisation de machines virtuelles, vous pouvez [créer une image de machine virtuelle personnalisée](tutorial-custom-images.md). Vous pouvez créer un maximum de 300 machines virtuelles dans un groupe identique lorsque vous utilisez une image personnalisée.


## <a name="create-an-app-to-scale"></a>Créer une application à mettre à l’échelle
Avant de créer un groupe identique, créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroupAutomate* dans l’emplacement *EastUS* :

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupScaleSet -Location EastUS
```

Dans un tutoriel précédent, vous avez appris à [automatiser la configuration des machines virtuelles](tutorial-automate-vm-deployment.md) à l’aide de l’extension de script personnalisé. Créez une configuration de groupe identique, puis appliquez une extension de script personnalisé pour installer et configurer IIS :

```powershell
# Create a config object
$vmssConfig = New-AzureRmVmssConfig `
    -Location EastUS `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings
```

## <a name="create-scale-load-balancer"></a>Créer un équilibreur de charge d’échelle
Un équilibreur de charge Azure est un équilibreur de charge de type Couche 4 (TCP, UDP) qui offre une haute disponibilité en répartissant le trafic entrant entre les machines virtuelles saines. Une sonde d’intégrité d’équilibreur de charge surveille un port donné sur chaque machine virtuelle et ne distribue le trafic qu’à une machine virtuelle opérationnelle. Pour plus d’informations, consultez le prochain tutoriel qui traite de [l’équilibrage de la charge des machines virtuelles Windows](tutorial-load-balancer.md).

Créez un équilibreur de charge doté d’une adresse IP publique qui distribue le trafic web sur le port 80 :

```powershell
# Create a public IP address
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupScaleSet `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIP

# Create a frontend and backend IP pool
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool

# Create the load balancer
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupScaleSet `
  -Name myLoadBalancer `
  -Location EastUS `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool

# Create a load balancer health probe on port 80
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2

# Create a load balancer rule to distribute traffic on port 80
Add-AzureRmLoadBalancerRuleConfig `
  -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

# Update the load balancer configuration
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="create-a-scale-set"></a>Créer un groupe identique
À présent, créez un groupe de machines virtuelles identiques avec [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant crée un groupe identique nommé *myScaleSet* :

```powershell
# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword P@ssword! `
  -ComputerNamePrefix myVM

# Create the virtual network resources
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myVnet" `
  -Location "EastUS" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$ipConfig = New-AzureRmVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -SubnetId $vnet.Subnets[0].Id

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -Name myScaleSet `
  -VirtualMachineScaleSet $vmssConfig
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.


## <a name="test-your-app"></a>Test de l'application
Pour voir fonctionner votre site web IIS, obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L’exemple suivant obtient l’adresse IP pour *myPublicIP* qui a été créée pour le groupe identique :

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupScaleSet -Name myPublicIP | select IpAddress
```

Entrez l’adresse IP publique dans un navigateur web. L’application s’affiche, avec notamment le nom d’hôte de la machine virtuelle sur laquelle l’équilibrage de charge a distribué le trafic :

![Site IIS en cours d’exécution](./media/tutorial-create-vmss/running-iis-site.png)

Pour voir fonctionner le groupe identique, vous pouvez forcer l’actualisation de votre navigateur web. L’équilibreur de charge répartit alors le trafic entre toutes les machines virtuelles exécutant votre application.


## <a name="management-tasks"></a>Tâches de gestion
Tout au long du cycle de vie du groupe identique, vous devrez peut-être exécuter une ou plusieurs tâches de gestion. En outre, vous souhaiterez peut-être créer des scripts pour automatiser les diverses tâches liées au cycle de vie. Azure PowerShell offre un moyen rapide d’effectuer ces tâches. Voici quelques tâches courantes.

### <a name="view-vms-in-a-scale-set"></a>Voir les machines virtuelles d’un groupe identique
Pour afficher la liste des machines virtuelles s’exécutant dans votre groupe identique, utilisez [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) comme suit :

```powershell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -VMScaleSetName myScaleSet

# Loop through the instanaces in your scale set
for ($i=1; $i -le ($scaleset.Sku.Capacity - 1); $i++) {
    Get-AzureRmVmssVM -ResourceGroupName myResourceGroupScaleSet `
      -VMScaleSetName myScaleSet `
      -InstanceId $i
}
```


### <a name="increase-or-decrease-vm-instances"></a>Augmenter ou diminuer les instances de machines virtuelles
Pour afficher le nombre d’instances actuellement présentes dans un groupe identique, utilisez [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) et formulez une requête *sku.capacity* :

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroupScaleSet `
    -VMScaleSetName myScaleSet | `
    Select -ExpandProperty Sku
```

Vous pouvez ensuite augmenter ou diminuer manuellement le nombre de machines virtuelles présentes dans le groupe identique avec [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). L’exemple suivant fixe le nombre de machines virtuelles présentes dans votre groupe identique à *3* :

```powershell
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName myResourceGroupScaleSet `
  -VMScaleSetName myScaleSet

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName myResourceGroupScaleSet `
    -Name myScaleSet `
    -VirtualMachineScaleSet $scaleset
```

Plusieurs minutes sont nécessaires avant que le nombre d’instances spécifié soit mis à jour pour votre groupe identique.


### <a name="configure-autoscale-rules"></a>Configurer des règles de mise à l’échelle automatique
Au lieu d’adapter manuellement le nombre d’instances présentes dans votre groupe identique, vous pouvez définir des règles de mise à l’échelle automatique. Ces règles surveillent les instances présentes dans votre groupe identique et répondent en conséquence en fonction des métriques et des seuils que vous définissez. L’exemple suivant augmente le nombre d’instances d’une unité dès que la charge moyenne du processeur dépasse 60 % sur une période de 5 minutes. Si la charge moyenne du processeur descend ensuite en dessous de 30 % sur une période de 5 minutes, le nombre d’instances diminue d’une unité :

```powershell
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```

Pour plus d’informations de conception sur l’utilisation de la mise à l’échelle automatique, consultez [Meilleures pratiques relatives à la mise à l’échelle automatique](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Étapes suivantes
Ce didacticiel vous a montré comment créer un groupe de machines virtuelles identiques. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Utiliser l’extension de script personnalisé pour définir un site IIS à mettre à l’échelle
> * Créer un équilibrage de charge pour votre groupe identique
> * Créer un groupe de machines virtuelles identiques
> * Augmenter ou réduire le nombre d’instances dans un groupe identique
> * Créer des règles de mise à l’échelle automatique

Passez au didacticiel suivant pour en savoir plus sur les concepts de l’équilibrage de charge des machines virtuelles.

> [!div class="nextstepaction"]
> [Équilibrage de charge des machines virtuelles](tutorial-load-balancer.md)
