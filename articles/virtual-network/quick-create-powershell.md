---
title: Créer un réseau virtuel Azure - PowerShell | Microsoft Docs
description: Découvrez rapidement comment créer un réseau virtuel à l’aide de PowerShell. Un réseau virtuel permet à des ressources Azure, par exemple des machines virtuelles, de communiquer en privé entre elles et avec Internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: fe171000f83c27f23972569b93e351340f4426ad
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Créer un réseau virtuel à l’aide de PowerShell

Un réseau virtuel permet à des ressources Azure, par exemple des machines virtuelles, de communiquer en privé entre elles et avec Internet. Dans cet article, vous allez apprendre à créer un réseau virtuel. Après avoir créé un réseau virtuel, déployez deux machines virtuelles dans le réseau virtuel. Vous vous connectez alors à une machine virtuelle à partir d’internet et vous communiquez en privé entre les deux machines virtuelles.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module AzureRM PowerShell version 5.4.1 ou version ultérieure pour les besoins de cet article. Pour trouver la version installée, exécutez ` Get-Module -ListAvailable AzureRM`. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Avant de pouvoir créer un réseau virtuel, vous devez créer un groupe de ressources pour qu’il contienne le réseau virtuel. Créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Créez un réseau virtuel avec [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L’exemple suivant crée un réseau virtuel par défaut nommé *myVirtualNetwork* dans la région *EastUS* :

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Des ressources Azure sont déployées sur un sous-réseau au sein d’un réseau virtuel. Vous devez alors créer un sous-réseau. Créez une configuration de sous-réseau à l’aide de la commande [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Écrivez les configurations de sous-réseaux dans le réseau virtuel à l’aide de [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), ce qui crée le sous-réseau dans le réseau virtuel :

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel :

### <a name="create-the-first-vm"></a>Créer la première machine virtuelle

Créez une machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Lors de l’exécution de la commande qui suit, vous êtes invité à saisir vos informations d’identification. Les valeurs que vous saisissez sont configurées comme le nom d’utilisateur et le mot de passe pour la machine virtuelle. L’option `-AsJob` crée la machine virtuelle en arrière-plan. Vous pouvez donc passer à l’étape suivante.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Une sortie semblable à celle de l’exemple suivant est retournée et Azure démarre la création de la machine virtuelle en arrière-plan.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-vm"></a>Créer la seconde machine virtuelle 

Entrez la commande suivante :

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

La création de la machine virtuelle ne nécessite que quelques minutes. Ne passez pas à l’étape suivante jusqu'à ce que la commande précédente s’exécute et que la sortie soit retournée à PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’internet

Utilisez [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) pour retourner l’adresse IP publique d’une machine virtuelle. L’exemple suivant retourne l’adresse IP publique de la machine virtuelle *myVm1* :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Remplacez `<publicIpAddress>` dans la commande suivante par l’adresse IP publique adresse retournée à partir de la commande précédente, puis entrez la commande suivante : 

```
mstsc /v:<publicIpAddress>
```

Un fichier .rdp (Remote Desktop Protocol) est créé et téléchargé sur votre ordinateur. Ouvrez le fichier .rdp téléchargé. Si vous y êtes invité, sélectionnez **Connexion**. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle. Vous devrez peut-être sélectionner **Plus de choix**, puis **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle. Sélectionnez **OK**. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez l’avertissement, sélectionnez **Oui** ou **Continuer** pour poursuivre le processus de connexion.

## <a name="communicate-privately-between-vms"></a>Communiquer en privé entre les machines virtuelles

À partir de PowerShell sur la machine virtuelle *myVm1*, entrez `ping myvm2`. Le test Ping échoue, étant donné qu’il utilise le protocole ICMP (Internet Control Message Protocol) et ICMP n’est pas autorisé via le pare-feu Windows, par défaut.

Pour autoriser *myVm2* à effectuer un test ping *myVm1* par la suite, entrez la commande suivante à partir de PowerShell, qui permet ICMP entrant via le pare-feu Windows :

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Fermez la connexion Bureau à distance sur *myVm1*. 

Effectuez à nouveau les étapes dans [Se connecter à une machine virtuelle à partir d’internet](#connect-to-a-vm-from-the-internet), mais connectez-vous à *myVm2*. 

À partir d’une invite de commandes sur la machine virtuelle *myVm2*, entrez `ping myvm1`.

Vous recevez des réponses de *myVm1*, car vous autorisez ICMP via le pare-feu Windows sur la machine virtuelle *myVm1* lors d’une étape précédente.

Fermez la connexion Bureau à distance sur *myVm2*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin d’un groupe de ressources, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour le supprimer et toutes les ressources qu’il contient :

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un réseau virtuel par défaut et deux machines virtuelles. Vous vous êtes connecté à une machine virtuelle à partir d’Internet et communiqué en privé entre la machine virtuelle et une autre. Pour plus d’informations sur les paramètres des réseaux virtuels, consultez [Gérer un réseau virtuel](manage-virtual-network.md). 

Par défaut, Azure autorise une communication privée illimitée entre des machines virtuelles, mais permet uniquement les connexions Bureau à distance entrantes pour les machines virtuelles Windows à partir d’Internet. Pour découvrir comment autoriser ou limiter les différents types de communication réseau vers et depuis les machines virtuelles, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [Filtrer le trafic réseau](tutorial-filter-network-traffic.md)
