---
title: Création d’une passerelle VPN basée sur un itinéraire - PowerShell | Microsoft Docs
description: Création rapide d’une passerelle VPN basée sur un itinéraire à l’aide de PowerShell
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2018
ms.author: cherylmc
ms.openlocfilehash: 17e27ce81ea73b687f65dcd0a05573d28f109676
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Création d’une passerelle VPN basée sur un itinéraire à l’aide de PowerShell

Cet article vous aide à créer rapidement une passerelle VPN basée sur un itinéraire à l’aide de PowerShell. Une passerelle VPN est utilisée lors de la création d’une connexion VPN à votre réseau local. La passerelle VPN vous permet également de connecter des réseaux virtuels.

Les étapes décrites dans cet article permettent la création d’un réseau virtuel, d’un sous-réseau, d’un sous-réseau de passerelle et d’une passerelle VPN basée sur un itinéraire (passerelle de réseau virtuel). Une fois la création de la passerelle terminée, vous pouvez créer des connexions. Ces étapes nécessitent un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.3.0 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="vnet"></a>Créer un réseau virtuel

Créez un réseau virtuel avec [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L’exemple suivant consiste en la création d’un réseau virtuel nommé **VNet1** à l’emplacement **EastUS** :

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Créez une configuration de sous-réseau à l’aide de l’applet de commande [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig).

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Définissez la configuration de sous-réseau pour la réseau virtuel à l’aide de l’applet de commande [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork).


```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="gwsubnet"></a>Ajouter un sous-réseau de passerelle

Le sous-réseau de passerelle contient les adresses IP réservées utilisées par les services de passerelle de réseau virtuel. Appuyez-vous sur les exemples suivants pour ajouter un sous-réseau de passerelle :

Définissez une variable pour votre réseau virtuel.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Créez le sous-réseau de passerelle à l’aide de l’applet de commande [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/Add-AzureRmVirtualNetworkSubnetConfig).

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Définissez la configuration de sous-réseau pour le réseau virtuel à l’aide de l’applet de commande [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork).

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="PublicIP"></a>Demander une adresse IP publique

Une passerelle VPN doit présenter une adresse IP allouée dynamiquement. Lorsque vous créez une connexion à une passerelle VPN, il s’agit de l’adresse IP que vous spécifiez. Appuyez-vous sur l’exemple suivant pour demander une adresse IP publique :

```azurepowershell-interactive
$gwpip= New-AzureRmPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>Créer la configuration de l’adresse IP de la passerelle

La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Utilisez l’exemple suivant pour créer la configuration de votre passerelle :

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="CreateGateway"></a>Créer la passerelle VPN

La création de la passerelle VPN peut nécessiter 45 minutes ou plus. Une fois l’opération terminée, vous êtes en mesure d’établir une connexion entre votre réseau virtuel et un autre réseau virtuel. Sinon, créez une connexion entre votre réseau virtuel et un emplacement local. Créez une passerelle VPN à l’aide de l’applet de commande [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="viewgw"></a>Afficher la passerelle VPN

Vous pouvez consulter la passerelle VPN à l’aide de l’applet de commande [Get-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Get-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

Le résultat ressemble à l’exemple suivant :

```
Name                   : VNet1GW
ResourceGroupName      : TestRG1
Location               : eastus
Id                     : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provide
                         rs/Microsoft.Network/virtualNetworkGateways/VNet1GW
Etag                   : W/"0952d-9da8-4d7d-a8ed-28c8ca0413"
ResourceGuid           : dc6ce1de-2c4494-9d0b-20b03ac595
ProvisioningState      : Succeeded
Tags                   :
IpConfigurations       : [
                           {
                             "PrivateIpAllocationMethod": "Dynamic",
                             "Subnet": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet"
                             },
                             "PublicIpAddress": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWPIP"
                             },
                             "Name": "default",
                             "Etag": "W/\"0952d-9da8-4d7d-a8ed-28c8ca0413\"",
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                         RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/de
                         fault"
                           }
                         ]
GatewayType            : Vpn
VpnType                : RouteBased
EnableBgp              : False
ActiveActive           : False
GatewayDefaultSite     : null
Sku                    : {
                           "Capacity": 2,
                           "Name": "VpnGw1",
                           "Tier": "VpnGw1"
                         }
VpnClientConfiguration : null
BgpSettings            : {
     
```

## <a name="viewgwpip"></a>Afficher l’adresse IP publique

Pour afficher l’adresse IP publique de votre passerelle VPN, utilisez l’applet de commande [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/Get-AzureRmPublicIpAddress).

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1
```

Dans l’exemple de réponse, la valeur IpAddress est l’adresse IP publique.

```
Name                     : VNet1GWPIP
ResourceGroupName        : TestRG1
Location                 : eastus
Id                       : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provi
                           ders/Microsoft.Network/publicIPAddresses/VNet1GWPIP
Etag                     : W/"5001666a-bc2a-484b-bcf5-ad488dabd8ca"
ResourceGuid             : 3c7c481e-9828-4dae-abdc-f95b383
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.90.153.3
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                           RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/
                           default"
                           }
DnsSettings              : null
Zones                    : {}
Sku                      : {
                             "Name": "Basic"
                           }
IpTags                   : {}
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’avez plus besoin des ressources créées, utilisez la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources. Ce faisant, vous supprimez le groupe de ressources et l’ensemble des ressources qu’il contient.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Étapes suivantes

Une fois la création de la passerelle achevée, vous êtes en mesure d’établir une connexion entre votre réseau virtuel et un autre réseau virtuel. Sinon, créez une connexion entre votre réseau virtuel et un emplacement local.

> [!div class="nextstepaction"]
> [Créer une connexion de site à site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Créer une connexion de point à site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Créer une connexion à un autre réseau virtuel](vpn-gateway-vnet-vnet-rm-ps.md)