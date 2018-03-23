---
title: 'Connecter votre réseau local à un réseau virtuel Azure : VPN site à site : PowerShell | Microsoft Docs'
description: Étapes de création d’une connexion IPsec entre votre réseau local et un réseau virtuel Azure via l’Internet public. Ces étapes vous aideront à créer une connexion de passerelle VPN de site à site à l’aide de PowerShell.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2018
ms.author: cherylmc
ms.openlocfilehash: 84df8b286bd9f3ae3012f297c3a929ef57369ff6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Créer un réseau virtuel avec une connexion VPN de site à site à l’aide de PowerShell

Cet article vous explique comment utiliser PowerShell pour créer une connexion de passerelle VPN de site à site à partir de votre réseau local vers le réseau virtuel. Les étapes mentionnées dans cet article s’appliquent au modèle de déploiement Resource Manager. Vous pouvez également créer cette configuration à l’aide d’un autre outil ou modèle de déploiement en sélectionnant une option différente dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portail Azure (classique)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>


Une connexion de passerelle VPN de site à site permet de connecter votre réseau local à un réseau virtuel Azure via un tunnel VPN IPsec/IKE (IKEv1 ou IKEv2). Ce type de connexion requiert un périphérique VPN local disposant d’une adresse IP publique exposée en externe. Pour plus d’informations sur les passerelles VPN, consultez l’article [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md).

![Schéma de connexion intersite d’une passerelle VPN site à site](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

* Veillez à disposer d’un périphérique VPN compatible et à être entouré d’une personne en mesure de le configurer. Pour plus d’informations sur les périphériques VPN compatibles et la configuration de votre périphérique, consultez l’article [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md).
* Vérifiez que vous disposez d’une adresse IPv4 publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
* Si vous ne maîtrisez pas les plages d’adresses IP situées dans votre configuration de réseau local, vous devez contacter une personne en mesure de vous aider. Lorsque vous créez cette configuration, vous devez spécifier les préfixes des plages d’adresses IP qu’Azure acheminera vers votre emplacement local. Aucun des sous-réseaux de votre réseau local ne peut chevaucher les sous-réseaux du réseau virtuel auquel vous souhaitez vous connecter.
* Installez la dernière version des applets de commande PowerShell Azure Resource Manager. Les applets de commande PowerShell sont fréquemment mises à jour, et vous devez généralement mettre à jour les vôtres pour obtenir les toutes dernières fonctionnalités. Si vous ne mettez pas à jour vos applets de commande PowerShell, les valeurs spécifiées peuvent échouer. Pour plus d’informations sur le téléchargement et l’installation des applets de commande PowerShell, voir [How to install and configure Azure PowerShell (Guide pratique d’installation et de configuration d’Azure PowerShell)](/powershell/azure/overview).

### <a name="example"></a>Exemples de valeurs

Nous utilisons les valeurs suivantes dans les exemples de cet article. Vous pouvez utiliser ces valeurs pour créer un environnement de test ou vous y référer pour mieux comprendre les exemples de cet article.

```
#Example values

VnetName                = TestVNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.1.0/28 
GatewaySubnet           = 10.11.0.0/27
LocalNetworkGatewayName = Site2
LNG Public IP           = <VPN device IP address> 
Local Address Prefixes  = 10.0.0.0/24, 20.0.0.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2

```


## <a name="Login"></a>1. Connexion à votre abonnement

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="VNet"></a>2. Créer un réseau virtuel et un sous-réseau de passerelle

Si vous n’avez pas de réseau virtuel, créez-en un. Lorsque vous créez un réseau virtuel, vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas les espaces d’adressage de votre réseau local. 

>[!NOTE]
>Afin que ce réseau virtuel puisse se connecter à un emplacement local, vous devez coordonner avec votre administrateur de réseau local pour consacrer une plage d’adresses IP à utiliser spécifiquement pour ce réseau virtuel. Si une plage d’adresses en double existe des deux côtés de la connexion VPN, le trafic ne sera pas correctement acheminé. En outre, si vous souhaitez connecter ce réseau virtuel à un autre réseau virtuel, l’espace d’adressage ne peut pas se chevaucher avec un autre réseau virtuel. Veillez à planifier votre configuration réseau en conséquence.
>
>

### <a name="about-the-gateway-subnet"></a>À propos du sous-réseau de passerelle

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="vnet"></a>Création d’un réseau virtuel et d’un sous-réseau de passerelle

Cet exemple permet de créer un réseau virtuel et un sous-réseau de passerelle. Si vous disposez déjà d’un réseau virtuel auquel vous devez ajouter un sous-réseau de passerelle, consultez [Pour ajouter un sous-réseau de passerelle à un réseau virtuel que vous avez déjà créé](#gatewaysubnet).

Créez un groupe de ressources :

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location 'East US'
```

Créez votre réseau virtuel.

1. Définissez les variables.

  ```powershell
  $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.11.0.0/27
  $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix 10.11.1.0/28
  ```
2. Créez le réseau virtuel.

  ```powershell
  New-AzureRmVirtualNetwork -Name TestVNet1 -ResourceGroupName TestRG1 `
  -Location 'East US' -AddressPrefix 10.11.0.0/16 -Subnet $subnet1, $subnet2
  ```

### <a name="gatewaysubnet"></a>Pour ajouter un sous-réseau de passerelle à un réseau virtuel que vous avez déjà créé

1. Définissez les variables.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name TestVet1
  ```
2. Créez le sous-réseau de passerelle.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.11.0.0/27 -VirtualNetwork $vnet
  ```
3. Définissez la configuration.

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

## 3. <a name="localnet"></a>Créer la passerelle de réseau local

La passerelle de réseau local fait généralement référence à votre emplacement local. Donnez au site un nom auquel Azure pourra se référer, puis spécifiez l’adresse IP du périphérique VPN local vers lequel vous allez créer une connexion. Spécifiez également les préfixes d’adresses IP qui seront acheminés via la passerelle VPN vers le périphérique VPN. Les préfixes d’adresses que vous spécifiez sont les préfixes situés sur votre réseau local. Vous pouvez facilement mettre à jour ces préfixes si votre réseau local change.

Utilisez les valeurs suivantes :

* *GatewayIPAddress* est l’adresse IP de votre périphérique VPN local. Votre périphérique VPN ne peut pas se trouver derrière un NAT.
* *AddressPrefix* est votre espace d’adressage local.

Pour ajouter une passerelle de réseau local avec un préfixe d’adresse unique :

  ```powershell
  New-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.0.0.0/24'
  ```

Pour ajouter une passerelle de réseau local avec des préfixes d’adresse multiples :

  ```powershell
  New-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
  ```

Pour modifier des préfixes d’adresses IP de votre passerelle de réseau local :<br>
Parfois, les préfixes de votre passerelle de réseau local changent. Les étapes à suivre pour modifier vos préfixes d’adresses IP varient selon que vous avez créé une connexion à la passerelle VPN. Consultez la section [Modifier des préfixes d’adresses IP de votre passerelle de réseau local](#modify) de cet article.

## <a name="PublicIP"></a>4. Demander une adresse IP publique

Une passerelle VPN doit avoir une adresse IP publique. Vous commencez par demander la ressource d’adresse IP, puis vous y faites référence lors de la création de votre passerelle de réseau virtuel. L’adresse IP est affectée dynamiquement à la ressource lors de la création de la passerelle VPN. Actuellement, la passerelle VPN prend uniquement en charge l’allocation d’adresses IP publiques *dynamiques*. Vous ne pouvez pas demander d’affectation d’adresse IP publique statique. Toutefois, cela ne signifie pas que l’adresse IP change après son affectation à votre passerelle VPN. L’adresse IP publique change uniquement lorsque la passerelle est supprimée, puis recréée. Elle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN.

Demandez une adresse IP publique qui sera affectée à votre passerelle VPN de réseau virtuel.

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5. Créer la configuration de l’adressage IP de la passerelle

La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Utilisez l’exemple suivant pour créer la configuration de votre passerelle :

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name TestVNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6. Créer la passerelle VPN

Créez la passerelle VPN de réseau virtuel. La création d’une passerelle VPN peut prendre 45 minutes ou plus.

Utilisez les valeurs suivantes :

* Le paramètre *-GatewayType* pour une configuration de site à site est *Vpn*. Le type de passerelle dépend toujours de la configuration que vous implémentez. Par exemple, d’autres configurations de passerelle peuvent nécessiter GatewayType ExpressRoute.
* Le paramètre *-VpnType* peut avoir pour valeur *RouteBased* (appelé passerelle dynamique dans certaines documentations) ou *PolicyBased* (appelé passerelle statique dans certaines documentations). Pour plus d’informations sur les types de passerelles VPN, consultez [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md).
* Sélectionnez la référence SKU de la passerelle que vous souhaitez utiliser. Des limites de configuration s’appliquent à certaines références (SKU). Pour plus d’informations, consultez l’article [Références (SKU) de passerelle](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Si vous obtenez une erreur lors de la création de la passerelle VPN relative au paramètre -GatewaySku, vérifiez que vous avez installé la dernière version des applets de commande PowerShell.

```powershell
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="ConfigureVPNDevice"></a>7. Configuration de votre périphérique VPN

Les connexions site à site vers un réseau local nécessitent un périphérique VPN. Dans cette étape, vous configurez votre périphérique VPN. Pour configurer votre périphérique VPN, vous avez besoin des éléments suivants :

- Une clé partagée. Il s’agit de la clé partagée spécifiée lors de la création de la connexion VPN de site à site. Dans nos exemples, nous utilisons une clé partagée basique. Nous vous conseillons de générer une clé plus complexe.
- L’adresse IP publique de votre passerelle de réseau virtuel. Vous pouvez afficher l’adresse IP publique à l’aide du portail Azure, de PowerShell ou de l’interface de ligne de commande. Pour trouver l’adresse IP publique de votre passerelle de réseau virtuel à l’aide de PowerShell, utilisez l’exemple suivant :

  ```powershell
  Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>8. Créer la connexion VPN

Créez ensuite la connexion VPN de site à site entre votre passerelle de réseau virtuel et votre périphérique VPN. Assurez-vous de remplacer ces valeurs par les vôtres. La clé partagée doit correspondre à la valeur que vous avez utilisée pour la configuration de votre périphérique VPN. Notez que la valeur « -ConnectionType » pour la connexion de site à site est *IPsec*.

1. Définissez les variables.
  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
  $local = Get-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1
  ```

2. Créez la connexion.
  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite2 -ResourceGroupName TestRG1 `
  -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

Après un bref délai, la connexion sera établie.

## <a name="toverify"></a>9. Vérifier la connexion VPN

Il existe différentes façons de vérifier votre connexion VPN.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="connectVM"></a>Se connecter à une machine virtuelle

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="modify"></a>Modifier des préfixes d’adresses IP d’une passerelle de réseau local

Si les préfixes d’adresse IP que vous souhaitez acheminer vers votre emplacement local changent, vous pouvez modifier la passerelle de réseau local. Deux ensembles d’instructions vous sont fournis : Les instructions que vous choisissez d’appliquer varient selon que vous avez déjà créé ou non votre connexion à la passerelle.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Modifier l’adresse IP d’une passerelle de réseau local

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes

*  Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Pour plus d’informations sur le protocole BGP, consultez les articles [Vue d’ensemble du protocole BGP](vpn-gateway-bgp-overview.md) et [Comment configurer BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Pour en savoir plus sur la création d’une connexion VPN de site à site à l’aide du modèle Azure Resource Manager, consultez [Créer une connexion VPN de site à site](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Pour en savoir plus sur la création d’une connexion VPN de réseau virtuel à réseau virtuel à l’aide du modèle Azure Resource Manager, consultez [Déployer une géo-réplication HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
