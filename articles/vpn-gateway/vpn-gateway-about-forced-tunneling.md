---
title: "Configurer le tunneling forcé pour les connexions de site à site Azure : modèle de déploiement classique | Microsoft Docs"
description: "Comment rediriger ou « forcer » tout le trafic Internet vers votre emplacement local."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 5c0177f1-540c-4474-9b80-f541fa44240b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 79bf6892c823da282c3e763921e830f986419854
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configuration du tunneling forcé à l’aide du modèle de déploiement classique

Le tunneling forcé vous permet de rediriger ou de « forcer » tout le trafic Internet vers votre emplacement local via un tunnel VPN site à site pour l’inspection et l’audit. Il s’agit d’une condition de sécurité critique pour la plupart des stratégies informatiques d’entreprise. Sans le tunneling forcé, le trafic Internet depuis vos machines virtuelles dans Azure se fera toujours à partir de l’infrastructure du réseau Azure directement vers Internet, sans vous laisser inspecter ou vérifier le trafic. L’accès Internet non autorisés est susceptible d’entraîner la divulgation d’informations ou tout autre type de violation de sécurité.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Cet article vous guide dans la configuration du tunneling forcé pour les réseaux virtuels créés à l’aide du modèle de déploiement Classic. Le tunneling forcé peut être configuré à l’aide de PowerShell, et non pas dans le portail. Si vous souhaitez configurer le tunneling forcé pour le modèle de déploiement de Resource Manager, sélectionnez l’article Classique dans la liste déroulante suivante :

> [!div class="op_single_selector"]
> * [PowerShell - Classique](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell - Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Conditions requises et éléments à prendre en compte
Le tunneling forcé dans Azure est configuré par le biais d’itinéraires définis par l’utilisateur de réseau virtuel. La redirection du trafic vers un site local est exprimée comme un itinéraire par défaut vers la passerelle VPN Azure. La section suivante répertorie la limite actuelle de la table et des itinéraires de routage pour un réseau virtuel Azure :

* Chaque sous-réseau du réseau virtuel dispose d’une table de routage système intégrée. La table de routage système comporte les trois groupes d’itinéraires suivants :

  * **Itinéraires de réseau virtuel local :** directement vers les machines virtuelles de destination dans le même réseau virtuel.
  * **Itinéraires locaux :** vers la passerelle VPN Azure.
  * **Itinéraire par défaut :** directement vers Internet. Les paquets destinés à des adresses IP privées non couvertes par les deux itinéraires précédents sont supprimés.
* Grâce aux itinéraires définis par l’utilisateur, vous pouvez créer une table de routage, y ajouter un itinéraire par défaut, puis associer cette table à un ou plusieurs sous-réseaux de réseau virtuel pour activer le tunneling forcé sur ces derniers.
* Vous devez définir un « site par défaut » parmi les sites locaux intersites connectés au réseau virtuel.
* Le tunneling forcé doit être associé à un réseau virtuel équipé d'une passerelle VPN à routage dynamique (pas de passerelle statique).
* Le tunneling forcé ExpressRoute n'est pas configuré de cette manière, mais il est activé par la publication d’un itinéraire par défaut via les sessions d'homologation BGP ExpressRoute. Pour plus d’informations, voir [Documentation ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) .

## <a name="configuration-overview"></a>Présentation de la configuration
Dans l’exemple suivant, le sous-réseau Frontend n’utilise pas le tunneling forcé. Les charges de travail du sous-réseau frontal peuvent continuer à accepter et à répondre aux demandes des clients directement à partir d’Internet. Les sous-réseaux intermédiaire et principal utilisent le tunneling forcé. Toutes les connexions sortantes à partir de ces deux sous-réseaux vers Internet seront forcées ou redirigées vers un site local via l’un des tunnels VPN S2S.

Cela vous permet de restreindre et d’inspecter l’accès à Internet à partir de vos machines virtuelles ou des services cloud dans Azure, tout en continuant d’activer votre architecture de service multiniveaux requise. Vous avez également la possibilité d’appliquer le tunneling forcé à tous les réseaux virtuels s’il n’existe aucune charge de travail Internet dans vos réseaux virtuels.

![Tunneling forcé](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Avant de commencer
Vérifiez que vous disposez des éléments suivants avant de commencer la configuration.

* Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Un réseau virtuel configuré. 
* La version la plus récente des applets de commande Azure PowerShell. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

## <a name="configure-forced-tunneling"></a>Configurer un tunneling forcé
La procédure suivant vous permettra de spécifier le tunneling forcé dans un réseau virtuel. Les étapes de configuration correspondent au fichier de configuration de réseau virtuel.

```
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

Dans cet exemple, le réseau virtuel « MultiTier-VNet » comporte trois sous-réseaux : « Frontend », « Midtier » et « Backend », ainsi que quatre connexions intersites : « DefaultSiteHQ » et trois branches. 

La procédure définit « DefaultSiteHQ » comme connexion de site par défaut pour le tunneling forcé et configure les sous-réseaux « Midtier » et « Backend » de manière à ce qu’ils utilisent le tunneling forcé.

1. Créez une table de routage. Utilisez l’applet de commande suivante pour créer votre table d’itinéraires.

  ```powershell
  New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
  ```
2. Ajoutez un itinéraire par défaut à la table de routage. 

  L’exemple suivant ajoute un itinéraire par défaut à la table de routage créée à l’étape 1. Le seul itinéraire pris en charge est le préfixe de destination de « 0.0.0.0/0 » vers le prochain saut « VPNGateway ».

  ```powershell
  Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
  ```
3. Associez la table de routage aux sous-réseaux. 

  Une fois la table de routage créée et l’itinéraire ajouté, utilisez l’exemple suivant pour ajouter ou associer la table d’itinéraires à un sous-réseau de réseau virtuel. L’exemple ajoute la table d’itinéraires « MyRouteTable » aux sous-réseaux Midtier et Backend du réseau virtuel multiniveau.

  ```powershell
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
  ```
4. Affectez un site par défaut pour le tunneling forcé. 

  Dans l’étape précédente, les exemples de scripts d’applet de commande ont créé la table de routage et associé la table d’itinéraires à deux des sous-réseaux de réseau virtuel. La dernière étape consiste à sélectionner un site local parmi les connexions multisites du réseau virtuel en tant que site ou tunnel par défaut.

  ```powershell
  $DefaultSite = @("DefaultSiteHQ")
  Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
  ```

## <a name="additional-powershell-cmdlets"></a>Autres applets de commande PowerShell
### <a name="to-delete-a-route-table"></a>Pour supprimer une table d’itinéraires

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Pour répertorier une table d’itinéraires

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Pour supprimer un itinéraire d’une table d’itinéraires

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Pour supprimer un itinéraire d’un sous-réseau

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Pour répertorier la table d’itinéraires associée à un sous-réseau

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Pour supprimer un site par défaut d’une passerelle VPN de réseau virtuel

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```