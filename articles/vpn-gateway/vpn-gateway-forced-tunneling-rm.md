---
title: "Configurer le tunneling forcé pour les connexions de site à site - Resource Manager | Microsoft Docs"
description: "Comment rediriger ou « forcer » tout le trafic Internet vers votre emplacement local."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: 00330f49d4acc9bd2d720a60b743b78c86b08f86
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configuration du tunneling forcé à l’aide du modèle de déploiement Azure Resource Manager

Le tunneling forcé vous permet de rediriger ou de « forcer » tout le trafic Internet vers votre emplacement local via un tunnel VPN site à site pour l’inspection et l’audit. Il s’agit d’une condition de sécurité critique pour la plupart des stratégies informatiques d’entreprise. Sans le tunneling forcé, le trafic Internet depuis vos machines virtuelles dans Azure se fait toujours à travers l’infrastructure du réseau Azure directement vers Internet, sans vous permettre d’inspecter ou de vérifier le trafic. L’accès Internet non autorisés est susceptible d’entraîner la divulgation d’informations ou tout autre type de violation de sécurité.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Cet article vous guide dans la configuration du tunneling forcé pour les réseaux virtuels créés à l’aide du modèle de déploiement Resource Manager. Le tunneling forcé peut être configuré à l’aide de PowerShell, et non pas dans le portail. Si vous souhaitez configurer le tunneling forcé pour le modèle de déploiement de classique, sélectionnez l’article Classique dans la liste déroulante suivante :

> [!div class="op_single_selector"]
> * [PowerShell - Classique](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell - Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>À propos du tunneling forcé

Le diagramme suivant illustre le fonctionnement du tunneling forcé. 

![Tunneling forcé](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

Dans l’exemple ci-dessus, le sous-réseau frontal n’utilise pas le tunneling forcé. Les charges de travail du sous-réseau frontal peuvent continuer à accepter et à répondre aux demandes des clients directement à partir d’Internet. Les sous-réseaux intermédiaire et principal utilisent le tunneling forcé. Toutes les connexions sortantes à partir de ces deux sous-réseaux vers Internet seront forcées ou redirigées vers un site local via l’un des tunnels VPN S2S.

Cela vous permet de restreindre et d’inspecter l’accès à Internet à partir de vos machines virtuelles ou des services cloud dans Azure, tout en continuant d’activer votre architecture de service multiniveaux requise. Vous avez également la possibilité d’appliquer le tunneling forcé à tous les réseaux virtuels s’il n’existe aucune charge de travail Internet dans vos réseaux virtuels.

## <a name="requirements-and-considerations"></a>Conditions requises et éléments à prendre en compte

Le tunneling forcé dans Azure est configuré via les itinéraires de réseau virtuel définis par l’utilisateur. La redirection du trafic vers un site local est exprimée comme un itinéraire par défaut vers la passerelle VPN Azure. Pour plus d’informations sur les réseaux virtuels et les itinéraires définis par l’utilisateur, consultez [Itinéraires définis par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).

* Chaque sous-réseau du réseau virtuel dispose d’une table de routage système intégrée. La table de routage système comporte les trois groupes d’itinéraires suivants :
  
  * **Itinéraires de réseau virtuel local :** directement vers les machines virtuelles de destination dans le même réseau virtuel.
  * **Itinéraires locaux :** vers la passerelle VPN Azure.
  * **Itinéraire par défaut :** directement vers Internet. Les paquets destinés aux adresses IP privées non couvertes par les deux itinéraires précédents sont supprimés.
* Cette procédure utilise des itinéraires définis par l’utilisateur (UDR) pour créer une table de routage et ajouter un itinéraire par défaut, puis associer la table de routage à vos sous-réseaux de réseaux virtuels pour activer le tunneling forcé sur ces sous-réseaux.
* Le tunneling forcé doit être associé à un réseau virtuel équipé d’une passerelle VPN avec itinéraire. Vous devez définir un « site par défaut » parmi les sites locaux intersites connectés au réseau virtuel. En outre, le périphérique VPN local doit être configuré à l’aide de 0.0.0.0/0 comme des sélecteurs de trafic. 
* Le tunneling forcé ExpressRoute n'est pas configuré de cette manière, mais il est activé par la publication d’un itinéraire par défaut via les sessions d'homologation BGP ExpressRoute. Pour plus d’informations, consultez la [Documentation ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Présentation de la configuration

La procédure suivante vous aide à créer un groupe de ressources et un réseau virtuel. Vous créerez ensuite une passerelle VPN et configurerez le tunneling forcé. Dans cette procédure, le réseau virtuel « MultiTier-VNet » comporte trois sous-réseaux : « Frontend », « Midtier » et « Backend », ainsi que quatre connexions intersites : « DefaultSiteHQ » et trois « Branches ».

Les étapes de la procédure définissent « DefaultSiteHQ » comme connexion de site par défaut pour le tunneling forcé et configure les sous-réseaux « Midtier » et « Backend » de manière à utiliser le tunneling forcé.

## <a name="before"></a>Avant de commencer

Installez la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Vous devez installer la dernière version des applets de commande PowerShell. Sinon, vous risquez de recevoir des erreurs de validation à l’exécution de certaines des applets de commande.
>
>

### <a name="to-log-in"></a>Pour se connecter

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>Configurer un tunneling forcé

> [!NOTE]
> Peuvent s’afficher des alertes comme « The output object type of this cmdlet will be modified in a future release » (« le type d’objet de sortie de cette applet de commande sera modifié dans une version ultérieure »). Ce comportement est normal ; vous pouvez ignorer ces avertissements.
>
>


1. Créez un groupe de ressources.

  ```powershell
  New-AzureRmResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
  ```
2. Créez un réseau virtuel et spécifiez vos sous-réseaux.

  ```powershell 
  $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
  $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
  $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
  $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
  $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
  ```
3. Créez les passerelles de réseau local.

  ```powershell
  $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
  $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
  $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
  $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
  ```
4. Créez la table d’itinéraires et la règle de routage.

  ```powershell
  New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
  $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
  Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
  Set-AzureRmRouteTable -RouteTable $rt
  ```
5. Associez la table d’itinéraire vers le niveau intermédiaire et les sous-réseaux principaux.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Créez la passerelle de réseau virtuel. Cette opération prend un certain temps, parfois 45 minutes voire plus, car vous créez et configurez la passerelle. Si vous voyez des erreurs ValidateSet relatives à la valeur GatewaySKU, vérifiez que vous avez installé la [dernière version des applets de commande PowerShell](#before). La dernière version des applets de commande PowerShell contient les nouvelles valeurs validées pour les dernières références SKU de passerelle.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
  $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
  New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
  ```
7. Affecter un site par défaut à la passerelle de réseau virtuel. **-GatewayDefaultSite** est le paramètre d’applet de commande qui permet à la configuration de routage forcé de fonctionner. Configurez ce paramètre correctement. 

  ```powershell
  $LocalGateway = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
  $VirtualGateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
  ```
8. Établissez des connexions VPN de site à site.

  ```powershell
  $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
  $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
  $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
  $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
  Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
  ```
