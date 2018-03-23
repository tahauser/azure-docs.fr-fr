---
title: Connecter des réseaux virtuels à l’aide de l’appairage de réseaux virtuels - PowerShell | Microsoft Docs
description: Apprenez à connecter des réseaux virtuels à l’aide de l’appairage de réseaux virtuels.
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
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c7b3fa2b566ab02e7fb4a03055db83f1545895e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Connecter des réseaux virtuels à l’aide de l’appairage de réseaux virtuels en utilisant PowerShell

Vous pouvez connecter des réseaux virtuels entre eux à l’aide de l’appairage de réseaux virtuels. Une fois que les deux réseaux virtuels sont appairés, leurs ressources peuvent communiquer entre elles avec les mêmes bande passante et latence, comme si elles se trouvaient sur le même réseau virtuel. Cet article décrit la création et l’appairage de deux réseaux virtuels. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer deux réseaux virtuels
> * Créer un appairage entre des réseaux virtuels
> * Tester l’appairage

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 3.6 ou ultérieure pour les besoins de cet article. Exécutez ` Get-Module -ListAvailable AzureRM` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 

## <a name="create-virtual-networks"></a>Créer des réseaux virtuels

Avant de créer un réseau virtuel, vous devez créer un groupe de ressources pour le réseau virtuel et toutes les autres ressources créées dans cet article. Créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Créez un réseau virtuel avec [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L’exemple suivant permet de créer un réseau virtuel nommé *myVirtualNetwork1* avec le préfixe d’adresse *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Créez une configuration de sous-réseau à l’aide de la commande [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). L’exemple suivant crée une configuration de sous-réseau avec un préfixe d’adresse 10.0.0.0/24 :

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Écrivez la configuration du sous-réseau dans le réseau virtuel à l’aide de la commande [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), ce qui crée le sous-réseau :

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

Créez un réseau virtuel avec un préfixe d’adresse 10.1.0.0/16 et un sous-réseau :

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

Le préfixe d’adresse pour le réseau virtuel *myVirtualNetwork2* ne chevauche pas le préfixe d’adresse du réseau virtuel *myVirtualNetwork1*. Il est impossible d’appairer des réseaux virtuels dont les préfixes d’adresse se chevauchent.

## <a name="peer-virtual-networks"></a>Appairer des réseaux virtuels

Créez un appairage avec [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering). L’exemple suivant appaire *myVirtualNetwork1* avec *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Dans la sortie obtenue après l’exécution de la commande ci-dessus, vous constatez que la valeur de **PeeringState** (état de l’appairage) est *Initiated*. L’appairage reste dans l’état *Initiated* jusqu’à ce que vous créiez l’appairage entre *myVirtualNetwork2* et *myVirtualNetwork1*. Créez un appairage entre *myVirtualNetwork2* et *myVirtualNetwork1*. 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Dans la sortie obtenue après l’exécution de la commande ci-dessus, vous constatez que **PeeringState** (état de l’appairage) est *Connected*. Azure a également remplacé l’état de l’appairage *myVirtualNetwork1-myVirtualNetwork2* par *Connected*. Vérifiez que l’état de l’appairage *myVirtualNetwork1-myVirtualNetwork2* a été remplacé *Connected* à l’aide de [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Les ressources d’un réseau virtuel ne peuvent pas communiquer avec les ressources d’un autre réseau virtuel tant que la valeur de **PeeringState** pour les appairages dans les deux réseaux virtuels soit *Connected*. 

Les appairages sont effectués entre deux réseaux virtuels, mais ils ne sont pas transitifs. Ainsi, par exemple, si vous souhaitez également appairer *myVirtualNetwork2* avec *myVirtualNetwork3*, vous devez créer un appairage supplémentaire entre les réseaux virtuels *myVirtualNetwork2* et *myVirtualNetwork3*. Bien que *myVirtualNetwork1* soit appairé avec *myVirtualNetwork2*, les ressources au sein de *myVirtualNetwork1* ne peuvent accéder aux ressources de *myVirtualNetwork3* que si *myVirtualNetwork1* a été également appairé avec *myVirtualNetwork3*. 

Avant d’appairer des réseaux virtuels en production, il est recommandé de bien vous familiariser avec la [vue d’ensemble de l’appairage](virtual-network-peering-overview.md), la [gestion de l’appairage](virtual-network-manage-peering.md) et les [limites de réseau virtuel](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Bien que cet article décrive un appairage entre deux réseaux virtuels appartenant aux mêmes abonnement et emplacement, vous pouvez également appairer des réseaux virtuels de [régions différentes](#register) et d’[abonnements Azure distincts](create-peering-different-subscriptions.md#powershell). Vous pouvez également créer des [conceptions de réseaux Hub and Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) à l’aide de l’appairage.

## <a name="test-peering"></a>Tester l’appairage

Pour tester la communication réseau entre les machines virtuelles sur différents réseaux virtuels via un appairage, déployez une machine virtuelle sur chaque sous-réseau, puis établissez la communication entre les machines virtuelles. 

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez une machine virtuelle dans chaque réseau virtuel pour pouvoir valider ultérieurement la communication entre elles.

Créez une machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant crée une machine virtuelle nommée *myVm1* dans le réseau virtuel *myVirtualNetwork1*. L’option `-AsJob` crée la machine virtuelle en arrière-plan. Vous pouvez donc passer à l’étape suivante. Lorsque vous y êtes invité, entrez le nom d’utilisateur et le mot de passe qui vous serviront pour vous connecter à la machine virtuelle.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

Azure attribue automatiquement 10.0.0.4 comme adresse IP privée de la machine virtuelle, 10.0.0.4 étant la première adresse IP disponible dans le sous-réseau *Subnet1* de *myVirtualNetwork1*. 

Créez une machine virtuelle dans le réseau virtuel *myVirtualNetwork2*.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

La création de la machine virtuelle prend quelques minutes. Bien que ce ne soit pas dans la sortie renvoyée, Azure a attribué 10.1.0.4 comme adresse IP privée de la machine virtuelle, 10.1.0.4 étant la première adresse IP disponible dans le sous-réseau *Subnet1* du réseau *myVirtualNetwork2*. 

Attendez qu’Azure ait crée la machine virtuelle et renvoyé une sortie à PowerShell pour passer aux étapes suivantes.

### <a name="test-virtual-machine-communication"></a>Tester la communication avec la machine virtuelle

Vous pouvez vous connecter à l’adresse IP publique d’une machine virtuelle depuis Internet. Utilisez la commande [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) pour retourner l’adresse IP publique d’une machine virtuelle. L’exemple suivant retourne l’adresse IP publique de la machine virtuelle *myVm1* :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Utilisez la commande suivante pour créer une session Bureau à distance avec la machine virtuelle *myVm1* à partir de votre ordinateur local. Remplacez `<publicIpAddress>` par l’adresse IP retournée par la commande précédente.

```
mstsc /v:<publicIpAddress>
```

Un fichier .rdp (Remote Desktop Protocol) est créé, téléchargé sur votre ordinateur, puis ouvert. Entrez le nom d’utilisateur et le mot de passe (il se peut que vous deviez choisir **Plus de choix**, puis **Utiliser un compte différent** pour spécifier les informations d’identification que vous avez entrées lors de la création de la machine virtuelle), puis cliquez sur **OK**. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur **Oui** ou **Continuer** pour continuer le processus de connexion.

À partir d’une invite de commandes, activez une commande ping via le pare-feu Windows afin d’effectuer un test ping sur cette machine virtuelle à partir de *myVm2* à une étape ultérieure.

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Bien que cet article utilise un test ping, il n’est pas recommandé d’autoriser le protocole IMCP dans le pare-feu Windows lors de déploiements en production.

Pour établir la connexion à la machine virtuelle *myVm2*, entrez la commande suivante à partir d’une invite de commandes sur la machine virtuelle *myVm1* :

```
mstsc /v:10.1.0.4
```

Étant donné que vous avez activé la commande ping sur *myVm1*, vous pouvez maintenant effectuer un test ping par adresse IP à partir d’une invite de commandes sur la machine virtuelle *myVm2* :

```
ping 10.0.0.4
```

Vous recevez quatre réponses. Si vous effectuez un test ping avec le nom de la machine virtuelle (*myVm1*) au lieu de son adresse IP, le test ping échoue, car *myVm1* est un nom d’hôte inconnu. La résolution de noms par défaut d’Azure fonctionne entre des machines virtuelles appartenant au même réseau virtuel, mais pas entre des machines virtuelles situées dans des réseaux virtuels différents. Pour résoudre les noms sur plusieurs réseaux virtuels, vous devez [déployer votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) ou utiliser des [domaines privés Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Déconnectez vos sessions RDP de *myVm1* et de *myVm2*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin d’un groupe de ressources, utilisez la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe et toutes les ressources qu’il contient.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

**<a name="register"></a>Inscription à la préversion de l’appairage de réseaux virtuels mondiaux**

L’appairage de réseaux virtuels au sein d’une même région est généralement possible. L’appairage de réseaux virtuels dans des régions différentes est actuellement en version préliminaire. Consultez [Virtual network updates](https://azure.microsoft.com/updates/?product=virtual-network) (Mises à jour du réseau virtuel) pour les régions disponibles. Pour appairer des réseaux virtuels dans différentes régions, vous devez d’abord vous inscrire à la préversion, en effectuant les étapes suivantes (dans l’abonnement dans lequel se trouve chaque réseau virtuel à appairer) :

1. Pour inscrire l’abonnement dans lequel se trouve chaque réseau virtuel à homologuer à la préversion, entrez les commandes suivantes :

    ```powershell-interactive
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
2. Vérifiez que vous êtes inscrit à la préversion en saisissant la commande suivante :

    ```powershell-interactive    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Si vous tentez d’appairer des réseaux virtuels dans des régions différentes avant que la valeur de **RegistrationState** (état d’inscription) de sortie reçue après la saisie de la commande précédente soit **Registered** (inscrit) pour les deux abonnements, l’appairage échoue.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à connecter deux réseaux avec l’appairage de réseaux virtuels. Vous pouvez [connecter votre propre ordinateur à un réseau virtuel](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via un VPN et interagir avec les ressources d’un réseau virtuel, ou dans des réseaux virtuels appariés.

Consultez les exemples de script pour obtenir des scripts réutilisables permettant d’accomplir un grand nombre des tâches présentées dans les articles sur les réseaux virtuels.

> [!div class="nextstepaction"]
> [Exemples de scripts de réseau virtuel](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
