---
title: "Équilibrage de la charge des machines virtuelles Windows dans Azure | Microsoft Docs"
description: "Découvrez comment utiliser l’équilibreur de charge Azure pour créer une application hautement disponible et sécurisée entre trois machines virtuelles Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: f0e154d0ac917d2ef2799431a72969a96415e0c0
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="how-to-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Équilibrage de la charge des machines virtuelles Windows dans Azure pour créer une application hautement disponible
L’équilibrage de charge offre un niveau plus élevé de disponibilité en répartissant les demandes entrantes sur plusieurs machines virtuelles. Dans ce didacticiel, vous allez découvrir les différents composants de l’équilibreur de charge Azure qui répartissent le trafic et fournissent une haute disponibilité. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Crée un équilibrage de charge Azure
> * Créer une sonde d’intégrité d’équilibreur de charge
> * Créer des règles de trafic pour l’équilibrage de charge
> * Utilisez l’extension de script personnalisé pour créer un site de base IIS
> * Créer des machines virtuelles et les attacher à un équilibrage de charge
> * Afficher un équilibrage de charge en action
> * Ajouter et supprimer des machines virtuelles d’un équilibreur de charge

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.3 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 


## <a name="azure-load-balancer-overview"></a>Vue d’ensemble de l’équilibreur de charge Azure
Un équilibreur de charge Azure est un équilibreur de charge de type Couche 4 (TCP, UDP) qui offre une haute disponibilité en répartissant le trafic entrant entre les machines virtuelles saines. Une sonde d’intégrité d’équilibreur de charge surveille un port donné sur chaque machine virtuelle et ne distribue le trafic que vers une machine virtuelle opérationnelle.

Vous définissez une configuration IP frontale qui contient une ou plusieurs adresses IP publiques. Cette configuration IP frontale permet d’accéder à votre équilibreur de charge et à vos applications via Internet. 

Les machines virtuelles se connectent à un équilibreur de charge à l’aide de leur carte d’interface réseau virtuelle (NIC). Pour distribuer le trafic vers les machines virtuelles, un pool d’adresses principal contient les adresses IP des cartes d’interface réseau virtuelles connectées à l’équilibreur de charge.

Pour contrôler le flux de trafic, vous définissez les règles d’équilibrage de charge pour les ports et les protocoles spécifiques qui correspondent à vos machines virtuelles.


## <a name="create-azure-load-balancer"></a>Créer un équilibreur de charge Azure
Cette section explique en détail comment vous pouvez créer et configurer chaque composant de l’équilibreur de charge. Avant de créer un équilibreur de charge, créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroupLoadBalancer* dans l’emplacement *EastUS* :

```azurepowershell-interactive
New-AzureRmResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>Créer une adresse IP publique
Pour accéder à votre application sur Internet, vous avez besoin d’une adresse IP publique pour l’équilibreur de charge. Créez une adresse IP publique avec [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). L’exemple suivant crée une adresse IP publique nommée *myPublicIP* dans le groupe de ressources *myResourceGroupLoadBalancer* :

```azurepowershell-interactive
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>Créer un équilibrage de charge
Créez un pool IP frontal avec [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). L’exemple suivant crée un pool IP frontal nommé *myFrontEndPool* et joint l’adresse *myPublicIP* : 

```azurepowershell-interactive
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

Créez un pool d’adresses principales avec [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). Les machines virtuelles s’attachent à ce pool principal lors des étapes suivantes. L’exemple suivant crée un pool d’adresses principales nommé *myBackEndPool* :

```azurepowershell-interactive
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```

Maintenant, créez l’équilibreur de charge avec [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). L’exemple suivant crée un équilibreur de charge nommé *myLoadBalancer* à l’aide du pool IP frontal et du pool IP principal créés lors des étapes précédentes :

```azurepowershell-interactive
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité
Pour permettre à l’équilibrage de charge de surveiller l’état de votre application, vous utilisez une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime dynamiquement des machines virtuelles de la rotation d’équilibrage de charge en fonction de leur réponse aux vérifications d’intégrité. Par défaut, une machine virtuelle est supprimée de la distribution d’équilibrage de charge après deux échecs consécutifs à des intervalles de 15 secondes. Vous créez une sonde d’intégrité selon un protocole ou une page de vérification d’intégrité spécifique pour votre application. 

L’exemple suivant permet de créer une sonde TCP. Vous pouvez également créer des sondes HTTP personnalisées pour des contrôles d’intégrité plus affinés. Lorsque vous utilisez une sonde HTTP personnalisée, vous devez créer la page de contrôle d’intégrité, par exemple *healthcheck.aspx*. La sonde doit retourner une réponse **HTTP 200 OK** pour l’équilibreur de charge pour assurer la rotation de l’hôte.

Pour créer une sonde d’intégrité TCP, utilisez [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). L’exemple suivant crée une sonde d’intégrité nommée *myHealthProbe* qui surveille chaque machine virtuelle sur le port *TCP* *80* :

```azurepowershell-interactive
Add-AzureRmLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Pour appliquer la sonde d’intégrité, mettez à jour l’équilibreur de charge avec [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) :

```azurepowershell-interactive
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge
Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP frontale pour le trafic entrant et le pool d’adresses IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. Pour veiller à ce que seules les machines virtuelles saines reçoivent le trafic, vous devez également définir la sonde d’intégrité à utiliser.

Créez une règle d’équilibreur de charge avec [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). L’exemple suivant crée une règle d’équilibreur de charge nommée *myLoadBalancerRule* et équilibre le trafic sur le port *TCP* *80* :

```azurepowershell-interactive
$probe = Get-AzureRmLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Mettez à jour l’équilibreur de charge avec [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) :

```azurepowershell-interactive
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>Configurer un réseau virtuel
Avant de déployer des machines virtuelles et de pouvoir tester votre équilibreur, créez les ressources de réseau virtuel de prise en charge. Pour plus d’informations sur les réseaux virtuels, consultez le didacticiel [Manage Azure Virtual Networks (Gérer les réseaux virtuels Azure)](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Créer des ressources réseau
Créez un réseau virtuel avec [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L’exemple suivant crée un réseau virtuel nommé *myVnet* avec un sous-réseau nommé *mySubnet* :

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Des cartes d’interface réseau virtuelles sont créées avec [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). L’exemple suivant crée trois cartes réseau virtuelles. (Une carte d’interface réseau virtuelle pour chaque machine virtuelle que vous créez pour votre application dans les étapes suivantes). Vous pouvez créer des machines virtuelles et des cartes d’interface réseau virtuelles supplémentaires à tout moment et les ajouter à l’équilibreur de charge :

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>Créer des machines virtuelles
Pour améliorer la haute disponibilité de votre application, placez vos machines virtuelles dans un groupe à haute disponibilité.

Créez un groupe à haute disponibilité avec la commande [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). L’exemple suivant permet de créer un groupe à haute disponibilité nommé *myAvailabilitySet* :

```azurepowershell-interactive
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Définissez un nom d’utilisateur administrateur et un mot de passe pour les machines virtuelles avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = Get-Credential
```

Vous pouvez maintenant créer les machines virtuelles avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Dans l’exemple suivant, vous créez trois machines virtuelles et générez les composants de réseau virtuel requis s’ils n’existent pas déjà :

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Le paramètre `-AsJob` crée la machine virtuelle en tant que tâche en arrière-plan. Vous recevez donc les invites PowerShell. Vous pouvez afficher les détails des travaux en arrière-plan à l’aide du cmdlet `Job`. La création et la configuration des trois machines virtuelles prennent quelques minutes.


### <a name="install-iis-with-custom-script-extension"></a>Installer IIS avec l’extension de script personnalisé
Dans un didacticiel précédent [How to customize a Windows virtual machine (Personnalisation d’une machine virtuelle Windows)](tutorial-automate-vm-deployment.md), vous avez appris à automatiser la personnalisation des machines virtuelles avec l’extension de script personnalisé pour Windows. Vous pouvez utiliser la même approche pour installer et configurer IIS sur vos machines virtuelles.

Utilisez [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) pour installer l’extension de script personnalisé. L’extension exécute `powershell Add-WindowsFeature Web-Server` pour installer le serveur web IIS, puis met à jour la page *Default.htm* pour qu’elle affiche le nom d’hôte de la machine virtuelle :

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Tester l’équilibreur de charge
Obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L’exemple suivant obtient l’adresse IP pour *myPublicIP* créée précédemment :

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

Vous pouvez alors entrer l’adresse IP publique dans un navigateur web. Le site web s’affiche, avec notamment le nom d’hôte de la machine virtuelle sur laquelle l’équilibreur de charge a distribué le trafic comme dans l’exemple suivant :

![Site web IIS en cours d’exécution](./media/tutorial-load-balancer/running-iis-website.png)

Pour visualiser la distribution de trafic par l’équilibreur de charge sur les trois machines virtuelles exécutant votre application, vous pouvez forcer l’actualisation de votre navigateur web.


## <a name="add-and-remove-vms"></a>Ajouter et supprimer des machines virtuelles
Vous devrez peut-être effectuer la maintenance sur la machine virtuelle exécutant votre application, avec par exemple l’installation des mises à jour du système d’exploitation. Pour faire face à une augmentation du trafic vers votre application, vous devrez peut-être ajouter des machines virtuelles supplémentaires. Cette section vous indique comment supprimer ou ajouter une machine virtuelle pour l’équilibrage de charge.

### <a name="remove-a-vm-from-the-load-balancer"></a>Supprimer une machine virtuelle de l’équilibrage de charge
Obtenez la carte d’interface réseau avec [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface), puis définissez la propriété *LoadBalancerBackendAddressPools* de la carte d’interface réseau virtuelle sur *$null*. Pour finir, mettez à jour la carte d’interface réseau virtuelle.

```azurepowershell-interactive
$nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Pour visualiser la distribution de trafic par l’équilibreur de charge sur les deux machines virtuelles restantes exécutant votre application, vous pouvez forcer l’actualisation de votre navigateur web. Vous pouvez maintenant effectuer la maintenance sur la machine virtuelle, avec par exemple l’installation des mises à jour du système d’exploitation ou le redémarrage de la machine virtuelle.

### <a name="add-a-vm-to-the-load-balancer"></a>Ajouter une machine virtuelle à l’équilibrage de charge
Après avoir effectué des opérations de maintenance sur la machine virtuelle ou si vous devez développer sa capacité, définissez la propriété *LoadBalancerBackendAddressPools* de la carte d’interface réseau virtuelle sur le *BackendAddressPool* de [Get-AzureRMLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) :

Récupérez l’équilibreur de charge :

```azurepowershell-interactive
$lb = Get-AzureRMLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a montré comment créer un équilibrage de charge et y attacher des machines virtuelles. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Crée un équilibrage de charge Azure
> * Créer une sonde d’intégrité d’équilibreur de charge
> * Créer des règles de trafic pour l’équilibrage de charge
> * Utilisez l’extension de script personnalisé pour créer un site de base IIS
> * Créer des machines virtuelles et les attacher à un équilibrage de charge
> * Afficher un équilibrage de charge en action
> * Ajouter et supprimer des machines virtuelles d’un équilibrage de charge

Passez au didacticiel suivant pour découvrir comment gérer la mise en réseau des machines virtuelles.

> [!div class="nextstepaction"]
> [Gérer les machines virtuelles et les réseaux virtuels](./tutorial-virtual-network.md)
