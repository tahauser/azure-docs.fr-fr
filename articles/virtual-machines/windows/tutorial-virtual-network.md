---
title: Réseaux virtuels Azure et machines virtuelles Windows | Microsoft Docs
description: 'Didacticiel : Gérer des réseaux virtuels Azure et des machines virtuelles Windows avec Azure PowerShell'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: feaef679a3090491b64c69ac69bf22153c281d31
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Gérer des réseaux virtuels Azure et des machines virtuelles Windows avec Azure PowerShell

Les machines virtuelles Azure utilisent la gestion réseau Azure pour la communication réseau interne et externe. Ce didacticiel vous guide dans le déploiement de deux machines virtuelles et la configuration de la gestion réseau Azure pour celles-ci. Les exemples de ce didacticiel supposent que les machines virtuelles hébergent une application web avec un back-end de base de données. Le didacticiel ne comprend cependant pas le déploiement d’une application. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un réseau virtuel et un sous-réseau
> * Créer une adresse IP publique
> * Créer une machine virtuelle frontale
> * sécurisent le trafic réseau
> * Créer une machine virtuelle principale



Ce didacticiel nécessite le module AzureRM.Compute 4.3.1 ou ultérieur. Exécutez `Get-Module -ListAvailable AzureRM.Compute` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="vm-networking-overview"></a>Vue d’ensemble de la mise en réseau de machines virtuelles

Les réseaux virtuels Azure permettent des connexions réseau sécurisées entre des machines virtuelles, Internet et d’autres services Azure SQL Database. Les réseaux virtuels sont divisés en segments logiques, appelés sous-réseaux. Les sous-réseaux sont utilisés pour contrôler le flux du réseau et comme une limite de sécurité. Quand vous déployez une machine virtuelle, elle inclut généralement une interface de réseau virtuel, qui est attachée à un sous-réseau.

En suivant ce didacticiel, vous pourrez créer les ressources suivantes :

![Réseau virtuel avec deux sous-réseaux](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* : réseau virtuel que les machines virtuelles utilisent pour communiquer entre elles et avec Internet.
- *myFrontendSubnet* : sous-réseau dans *myVNet* utilisé par les ressources frontales.
- *myPublicIPAddress* : adresse IP publique utilisée pour accéder à *myFrontendVM* à partir d’Internet.
- *myFrontentNic* : interface réseau utilisée par *myFrontendVM* pour communiquer avec *myBackendVM*.
- *myFrontendVM :* : machine virtuelle utilisée pour les communications entre Internet et *myBackendVM*.
- *myBackendNSG* : groupe de sécurité réseau qui contrôle la communication entre *myFrontendVM* et *myBackendVM*.
- *myBackendSubnet* : sous-réseau associé à *myBackendNSG* et utilisé par les ressources du serveur principal.
- *myBackendNic* : interface réseau utilisée par *myBackendVM* pour communiquer avec *myFrontendVM*.
- *myBackendVM* : machine virtuelle qui utilise le port 1433 pour communiquer avec *myFrontendVM*.


## <a name="create-a-virtual-network-and-subnet"></a>Créer un réseau virtuel et un sous-réseau

Pour ce didacticiel, un seul réseau virtuel est créé avec deux sous-réseaux. Un sous-réseau frontal pour l’hébergement d’une application web et un sous-réseau principal pour l’hébergement d’un serveur de base de données.

Avant de créer une machine virtuelle, créez un groupe de ressources à l’aide de [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myRGNetwork* à l’emplacement *EastUS* :

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

### <a name="create-subnet-configurations"></a>Créez des configurations de sous-réseau

Créez une configuration de sous-réseau nommée *myFrontendSubnet* à l’aide de [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) :

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Ainsi qu’une autre configuration de sous-réseau nommée *myBackendSubnet* :

```azurepowershell-interactive
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

### <a name="create-virtual-network"></a>Création d’un réseau virtuel

Créez un réseau virtuel nommé *myVNet* en utilisant *myFrontendSubnet* et *myBackendSubnet* avec [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) :

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

À ce stade, un réseau a été créé et segmenté en deux sous-réseaux, un pour les services frontaux et un autre pour les services principaux. Dans la section suivante, des machines virtuelles sont créées et connectées à ces sous-réseaux.

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Une adresse IP publique permet aux ressources Azure d’être accessibles sur Internet. La méthode d’allocation de l’adresse IP publique peut être configurée comme dynamique ou statique. Par défaut, une adresse IP publique est allouée dynamiquement. Les adresses IP dynamiques sont libérées quand une machine virtuelle est désallouée. Ce comportement fait que l’adresse IP change lors de toute opération qui inclut une désallocation de la machine virtuelle.

La méthode d’allocation peut être définie comme étant statique, ce qui garantit que l’adresse IP reste affectée à une machine virtuelle, même lorsqu’elle est désallouée. Quand vous utilisez une adresse IP allouée de manière statique, vous ne pouvez pas spécifier l’adresse IP elle-même. Au lieu de cela, elle est allouée à partir d’un pool d’adresses disponibles.

Créez une adresse IP publique nommée *myPublicIPAddress* avec [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) :

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Vous pouvez changer le paramètre -AllocationMethod en `Static` pour assigner une adresse IP publique statique.

## <a name="create-a-front-end-vm"></a>Créer une machine virtuelle frontale

Pour communiquer avec un réseau virtuel, une machine virtuelle a besoin d’une interface réseau virtuelle. Créez une carte réseau avec [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) :

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Définissez le nom d’utilisateur et le mot de passe pour le compte Administrateur sur la machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Vous utilisez ces informations d’identification pour vous connecter à la machine virtuelle en suivant des étapes supplémentaires :

```azurepowershell-interactive
$cred = Get-Credential
```

Créez les machines virtuelles avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>sécurisent le trafic réseau

Un groupe de sécurité réseau (NSG) contient une liste de règles de sécurité qui autorisent ou rejettent le trafic réseau vers les ressources connectées aux réseaux virtuels Azure (VNet). Les groupes de sécurité réseau peuvent être associés à des sous-réseaux ou à des interfaces réseau individuelles. Quand un groupe de sécurité réseau est associé à une interface réseau, il s’applique seulement à la machine virtuelle associée. Lorsqu’un NSG est associé à un sous-réseau, les règles s’appliquent à toutes les ressources connectées au sous-réseau.

### <a name="network-security-group-rules"></a>Règles de groupe de sécurité réseau

Les règles de groupe de sécurité réseau définissent les ports réseau sur lesquels le trafic est autorisé ou refusé. Les règles peuvent comprendre des plages d’adresses IP sources et de destination, de façon à ce que le trafic soit contrôlé entre des systèmes ou des sous-réseaux spécifiques. Les règles de groupe de sécurité réseau ont également une priorité (entre 1 et 4 096). Les règles sont évaluées dans l’ordre des priorités. Une règle avec une priorité de 100 est évaluée avant une règle avec une priorité de 200.

Tous les groupes de ressources réseau contiennent un ensemble de règles par défaut. Les règles par défaut ne peuvent pas être supprimées, mais comme la priorité la plus basse leur est attribuée, elles peuvent être remplacées par les règles que vous créez.

- **Réseau virtuel** : le trafic en provenance et à destination d’un réseau virtuel est autorisé à la fois dans les directions entrante et sortante.
- **Internet** : le trafic sortant est autorisé, mais le trafic entrant est bloqué.
- **Équilibreur de charge** : autoriser l’équilibreur de charge d’Azure à tester l’intégrité de vos machines virtuelles et des instances de rôle. Si vous n’utilisez pas un groupe à charge équilibrée, vous pouvez remplacer cette règle.

### <a name="create-network-security-groups"></a>Créer des groupes de sécurité réseau

Créez une règle de trafic entrant nommée *myFrontendNSGRule* pour autoriser le trafic web entrant sur *myFrontendVM* avec [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) :

```azurepowershell-interactive
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Vous pouvez limiter le trafic interne vers *myBackendVM* en provenance uniquement de *myFrontendVM* en créant un groupe de sécurité réseau pour le sous-réseau back-end. L’exemple suivant crée une règle de groupe de sécurité réseau nommée *myBackendNSGRule* :

```azurepowershell-interactive
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Ajoutez un groupe de sécurité réseau nommé *myFrontendNSG* avec [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) :

```azurepowershell-interactive
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Ajoutez un groupe de sécurité réseau nommé *myBackendNSG* avec New-AzureRmNetworkSecurityGroup :

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Ajoutez les groupes de sécurité réseau aux sous-réseaux :

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Créer une machine virtuelle principale

Pour créer une machine virtuelle principale dans ce didacticiel, le plus simple consiste à utiliser une image SQL Server. Ce didacticiel crée la machine virtuelle avec le serveur de base de données, mais il ne fournit pas d’informations sur l’accès à la base de données.

Créez *myBackendNic* :

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Définissez le nom d’utilisateur et le mot de passe pour le compte Administrateur sur la machine virtuelle avec Get-Credential :

```azurepowershell-interactive
$cred = Get-Credential
```

Créez *myBackendVM*.

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

L’image utilisée inclut SQL Server, mais elle n’est pas utilisée dans ce didacticiel. Elle est présentée pour vous montrer comment configurer une machine virtuelle afin de gérer le trafic web, et comment configurer une machine virtuelle afin de traiter la gestion de la base de données.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé et sécurisé des réseaux Azure concernant les machines virtuelles. 

> [!div class="checklist"]
> * Créer un réseau virtuel et un sous-réseau
> * Créer une adresse IP publique
> * Créer une machine virtuelle frontale
> * sécurisent le trafic réseau
> * Créer une machine virtuelle principale

Passez au didacticiel suivant pour découvrir comment sécuriser et surveiller les données sur des machines virtuelles avec Sauvegarde Azure.

> [!div class="nextstepaction"]
> [Sauvegarde de machines virtuelles Windows dans Azure](./tutorial-backup-vms.md)
