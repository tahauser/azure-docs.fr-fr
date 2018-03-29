---
title: Créer un équilibrage de charge interne Azure à l’aide de PowerShell | Microsoft Docs
description: Découvrez comment créer un équilibrage de charge interne dans le module Azure PowerShell avec Azure Resource Manager
services: load-balancer
documentationcenter: na
author: KumudD
manager: jennoc
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 54846ddc142a5bf7cd37c03fd9c069dd0c94897f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Créer un équilibreur de charge interne à l’aide du module Azure PowerShell

> [!div class="op_single_selector"]
> * [Portail Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [interface de ligne de commande Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modèle](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>Prise en main de la configuration

Cet article décrit la création d’un équilibreur de charge interne à l’aide d’Azure Resource Manager avec le module Azure PowerShell. Dans le modèle de déploiement du Gestionnaire des ressources, les objets qui sont nécessaires pour créer un équilibreur de charge interne sont configurés individuellement. Une fois que les objets sont créés et configurés, ils sont combinés pour créer un équilibreur de charge.

Pour déployer un équilibreur de charge, il est nécessaire de créer et de configure les objets suivants :

* Pool d’adresses IP frontales : l’adresse IP privée pour l’ensemble du trafic réseau entrant.
* Pool d’adresses de serveur principal : les interfaces réseau destinées à recevoir le trafic à charge équilibrée à partir de l’adresse IP frontale.
* Règles d’équilibrage de charge : configure le port (local et source) pour l’équilibreur de charge.
* Configuration de sonde : vérifications de l’état d’intégrité pour les machines virtuelles.
* Les règles NAT de trafic entrant : les règles de port permettant l’accès direct aux machines virtuelles.

Pour plus d’informations sur les composants de l’équilibreur de charge, consultez [Prise en charge d’un équilibreur de charge par Azure Resource Manager](load-balancer-arm.md).

Les étapes suivantes expliquent comment configurer un équilibreur de charge entre deux machines virtuelles.

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurer PowerShell pour utiliser Resource Manager

Assurez-vous que vous disposez de la dernière version de production du module Azure PowerShell. PowerShell doit être correctement configuré pour accéder à votre abonnement Azure.

### <a name="step-1-start-powershell"></a>Étape 1 : Démarrer PowerShell

Démarrez le module PowerShell pour Azure Resource Manager.

```powershell
Login-AzureRmAccount
```

### <a name="step-2-view-your-subscriptions"></a>Étape 2 : Visualiser vos abonnements

Vérifiez vos abonnements Azure disponibles.

```powershell
Get-AzureRmSubscription
```

Entrez vos informations d’identification lorsqu’on vous demande de vous authentifier.

### <a name="step-3-select-the-subscription-to-use"></a>Étape 3 : Sélectionner l’abonnement à utiliser

Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour déployer l’équilibreur de charge.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>Étape 4 : Choisir le Groupe de ressources pour l’équilibreur de charge

Créez un nouveau groupe de ressources pour l’équilibreur de charge. Ignorez cette étape si vous utilisez un groupe de ressources existant.

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Cet emplacement est utilisé comme emplacement par défaut pour toutes les ressources du groupe de ressources. Utilisez toujours le même groupe de ressources pour toutes les commandes liées à la création de l’équilibreur de charge.

Dans cet exemple, nous avons créé un groupe de ressources appelé **NRP-RG** avec l’emplacement « West US ».

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>Créez le réseau virtuel et l’adresse IP pour le pool d’adresses IP frontales.

Créez un sous-réseau pour le réseau virtuel et affectez-le à la variable **$backendSubnet**.

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Créez un réseau virtuel.

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Le réseau virtuel est créé. Le sous-réseau **LB-Subnet-BE** est ajouté au réseau virtuel **NRPVNet**. Ces valeurs sont affectées à la variable **$vnet**.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>Créez un pool d’adresses IP frontales et un pool d’adresses principales.

Créez un pool d’adresses IP frontales pour le trafic entrant et un pool d’adresses principales pour recevoir le trafic à charge équilibrée.

### <a name="step-1-create-a-front-end-ip-pool"></a>Étape 1 : Créer un pool d’adresses IP frontales

Créez un pool d’adresses IP frontales avec l’adresse IP privée 10.0.2.5 pour le sous-réseau 10.0.2.0/24. Cette adresse est le point de terminaison du trafic réseau entrant.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>Étape 2 : Créer un pool d’adresses principales

Créez un pool d’adresses IP principales pour recevoir le trafic entrant à partir du pool d’adresses IP frontales.

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>Créez les règles de configuration, la sonde et l’équilibreur de charge

Une fois que le pool d’adresses IP frontales et le pool d’adresses principales sont créés, spécifiez des règles pour la ressource d’équilibreur de charge.

### <a name="step-1-create-the-configuration-rules"></a>Étape 1 : Créer les règles de configuration

L’exemple crée les quatre objets suivants :

* Une règle NAT de trafic entrant pour le protocole RDP (Remote Desktop) : redirige tout le trafic entrant sur le port 3441 au port 3389.
* Une deuxième règle NAT de trafic entrant pour le protocole RDP (Remote Desktop) : redirige tout le trafic entrant sur le port 3442 au port 3389.
* Une règle de vérification d’intégrité : vérifie l’état d’intégrité du chemin d’accès « HealthProbe.aspx ».
* Une règle d’équilibrage de charge : équilibre la charge pour tout le trafic entrant sur le port public 80 vers le port local 80 dans le pool d’adresses principales

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>Étape 2 : créer l’équilibreur de charge

Créer l’équilibrage de charge et de combiner les objets de règle (NAT de trafic entrant pour RDP, équilibrage de charge et sonde d’intégrité) :

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>Créez les interfaces réseau.

Après avoir créé l’équilibrage de charge interne, définissez les interfaces réseau qui recevront le trafic entrant du réseau à charge équilibrée, les règles NAT et la sonde. Chaque interface réseau est configurée individuellement et est affectée à une machine virtuelle par la suite.

### <a name="step-1-create-the-first-network-interface"></a>Étape 1 : Créer la première interface réseau

Notez le réseau virtuel et le sous-réseau de ressource. Ces valeurs sont utilisées pour créer des interfaces réseau :

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Créez la première interface réseau avec le nom **lb-nic1-be**. Affectez l’interface au pool principal de l’équilibreur de charge. Associez la première règle NAT pour le protocole RDP à cette carte réseau :

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>Étape 2 : Créer la deuxième interface réseau

Créez la deuxième interface réseau avec le nom **lb-nic2-be**. Affectez la deuxième interface au même pool principal d’équilibrage de charge que la première interface. Associez la deuxième carte réseau à la deuxième règle NAT pour le protocole RDP :

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Examinez la configuration :

    $backendnic1

Les paramètres doivent se présenter comme suit :

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3-assign-the-nic-to-a-vm"></a>Étape 3 : Affecter la carte réseau à une machine virtuelle

Affectez la carte réseau à une machine virtuelle en utilisant la commande `Add-AzureRmVMNetworkInterface`.

Pour obtenir des instructions étape par étape pour créer une machine virtuelle et l’affecter à une carte réseau, consultez [Créer une machine virtuelle Azure à l’aide de PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Ajouter l’interface réseau

Une fois que la machine virtuelle a été créée, ajoutez l’interface réseau.

### <a name="step-1-store-the-load-balancer-resource"></a>Étape 1 : Stocker la ressource d’équilibrage de charge

Stockez la ressource d'équilibrage de charge dans une variable (si vous ne l'avez pas encore fait). Nous utilisons le nom de variable **$lb**. Pour les valeurs d’attribut dans le script, utilisez les noms des ressources d’équilibrage de charge qui ont été créées dans les étapes précédentes.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>Étape 2 : Stocker la configuration de serveur principal

Stockez la configuration de serveur principal dans la variable **$backend**.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>Étape 3 : Stocker l’interface réseau

Stockez l’interface réseau dans une autre variable. Cette interface a été créée dans « Créer les interfaces réseau, étape 1. » Nous utilisons le nom de variable **$nic1**. Utilisez le même nom d’interface réseau utilisé dans l'exemple précédent.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>Étape 4 : Modifier la configuration de serveur principal

Modifiez la configuration du serveur principal sur l’interface réseau.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>Étape 5 : Enregistrer l'objet d'interface réseau

Enregistrer l'objet d'interface réseau.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Une fois que l’interface est ajoutée au pool principal, le trafic réseau est équilibré conformément aux règles. Ces règles ont été configurées dans « Créer les règles de configuration, la sonde et l’équilibreur de charge. »

## <a name="update-an-existing-load-balancer"></a>Mettre à jour un équilibreur de charge existant

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>Étape 1 : Affecter l’objet d’équilibrage de charge à une variable

Affectez l’objet d’équilibrage de charge (à partir de l’exemple précédent) à la variable **$slb** en utilisant la commande `Get-AzureRmLoadBalancer` :

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>Étape 2 : Ajouter une règle NAT

Ajoutez une règle NAT de trafic entrant à un équilibreur de charge existant. Utilisez le port 81 pour le pool frontal et le port 8181 pour le pool principal :

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>Étape 3 : Enregistrer la configuration

Enregistrez la nouvelle configuration à l’aide de la commande `Set-AzureLoadBalancer`.

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>Supprimer un équilibreur de charge existant

Supprimez l’équilibreur de charge **NRP-LB** dans le groupe de ressources **NRP-RG** à l’aide de la commande `Remove-AzureRmLoadBalancer` :

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Utilisez le commutateur facultatif **-Force** pour empêcher l’invite de confirmation pour la suppression.

## <a name="next-steps"></a>Étapes suivantes

* [Configuration d’un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)
* [Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
