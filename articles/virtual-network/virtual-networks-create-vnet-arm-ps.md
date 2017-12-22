---
title: "Créer un réseau virtuel - Azure PowerShell | Microsoft Docs"
description: "Découvrez comment créer un réseau virtuel à l’aide de PowerShell."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a31f4f12-54ee-4339-b968-1a8097ca77d3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e7072ddf51570d46578111e2e392e3cbea53f2aa
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-network-using-powershell"></a>Créer un réseau virtuel à l’aide de PowerShell

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure propose deux modèles de déploiement : Azure Resource Manager et classique. Microsoft recommande la création de ressources via le modèle de déploiement Resource Manager. Pour en savoir plus sur les différences entre deux modèles, lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md).
 
Cet article explique comment créer un réseau virtuel dans le modèle de déploiement Resource Manager à l’aide de PowerShell. Vous pouvez également créer un réseau virtuel via Resource Manager à l’aide d’autres outils ou créer un réseau virtuel via le modèle de déploiement classique en sélectionnant une option différente dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [INTERFACE DE LIGNE DE COMMANDE](virtual-networks-create-vnet-arm-cli.md)
> * [Modèle](virtual-networks-create-vnet-arm-template-click.md)
> * [Portail (classique)](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell (classique)](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [Interface de ligne de commande (classique)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Pour créer un réseau virtuel à l’aide de PowerShell, procédez comme suit :

1. Installez et configurez Azure PowerShell en suivant les instructions de l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

2. Si nécessaire, créez un groupe de ressources, comme indiqué ci-dessous. Dans le cadre de ce scénario, créez un groupe de ressources appelé *TestRG*. Pour plus d’informations sur les groupes de ressources, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    ```powershell   
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    Sortie attendue :

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/[Subscription Id]/resourceGroups/TestRG    
3. Créez un réseau virtuel appelé *TestVNet* :

    ```powershell
    New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location centralus
    ```

    Sortie attendue :

        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                   : W/"[Id]"
        ProvisioningState          : Succeeded
        Tags                       : 
        AddressSpace               : {
                                   "AddressPrefixes": [
                                     "192.168.0.0/16"
                                   ]
                                  }
        DhcpOptions                : {}
        Subnets                    : []
        VirtualNetworkPeerings     : []
4. Stockez l’objet de réseau virtuel en tant que variable :

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

   > [!TIP]
   > Vous pouvez combiner les étapes 3 et 4 en exécutant `$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus`.
   > 

5. Ajoutez un sous-réseau à la nouvelle variable de réseau virtuel :

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
    ```

    Sortie attendue :
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {}
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24"
                                  }
                                ]
        VirtualNetworkPeerings     : []

6. Répétez l’étape 5 ci-dessus pour chaque sous-réseau à créer. La commande suivante crée le sous-réseau *BackEnd* du scénario :

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24
    ```

7. Bien que vous créiez des sous-réseaux, ils n’existent actuellement que dans la variable locale utilisée pour récupérer le réseau virtuel créé à l’étape 4 ci-dessus. Pour enregistrer les modifications apportées à Azure, exécutez la commande suivante :

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```
   
    Sortie attendue :
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {
                                  "DnsServers": []
                                }
        Subnets               : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  },
                                  {
                                    "Name": "BackEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                    "AddressPrefix": "192.168.2.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  }
                                ]
        VirtualNetworkPeerings : []

## <a name="next-steps"></a>Étapes suivantes

Apprenez à connecter :

- Une machine virtuelle à un réseau virtuel en lisant l’article [Création d’une machine virtuelle Windows](../virtual-machines/virtual-machines-windows-ps-create.md). Au lieu de créer un réseau virtuel et un sous-réseau comme indiqué dans les procédures de ces articles, vous pouvez sélectionner un réseau virtuel et un sous-réseau existants pour établir la connexion à une machine virtuelle.
- Le réseau virtuel à d’autres réseaux virtuels en lisant l’article sur la [connexion des réseaux virtuels](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Le réseau virtuel à un réseau local à l’aide d’un réseau privé virtuel (VPN) site à site ou d’un circuit ExpressRoute. Découvrez comment en lisant les articles [Connecter un réseau virtuel à un réseau local à l’aide d’un VPN de site à site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) et [Lier un réseau virtuel à un circuit ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md).
