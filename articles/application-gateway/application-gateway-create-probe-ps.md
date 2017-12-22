---
title: "Créer une sonde personnalisée - Passerelle Azure Application Gateway - PowerShell | Microsoft Docs"
description: "Apprenez à créer une sonde personnalisée pour la passerelle Application Gateway à l'aide de PowerShell dans Resource Manager"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: davidmu
ms.openlocfilehash: 344d6922d1649449e26f2500e538b5a0b440476d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Création d'une sonde personnalisée pour Azure Application Gateway avec PowerShell pour Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-create-probe-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Dans cet article, une sonde personnalisée est ajoutée à une passerelle d’application existante à l’aide de PowerShell. Les sondes personnalisées sont utiles pour les applications qui ont une page de contrôle d’intégrité spécifique ou pour les applications qui ne fournissent pas de réponse correcte dans l’application web par défaut.

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md).  Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du [modèle de déploiement classique](application-gateway-create-probe-classic-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Créer une passerelle d’application avec une sonde personnalisée

### <a name="sign-in-and-create-resource-group"></a>Se connecter et créer un groupe de ressources

1. Utilisez `Login-AzureRmAccount` pour l’authentification.

  ```powershell
  Login-AzureRmAccount
  ```

1. Obtenez les abonnements associés au compte.

  ```powershell
  Get-AzureRmSubscription
  ```

1. Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

  ```powershell
  Select-AzureRmSubscription -Subscriptionid '{subscriptionGuid}'
  ```

1. Créez un groupe de ressources. Vous pouvez ignorer cette étape si vous disposez d’un groupe de ressources existant.

  ```powershell
  New-AzureRmResourceGroup -Name appgw-rg -Location 'West US'
  ```

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Celui-ci est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple précédent, nous avons créé un groupe de ressources appelé **appgw-RG** à l’emplacement **West US**.

### <a name="create-a-virtual-network-and-a-subnet"></a>Créer un réseau virtuel et un sous-réseau

L’exemple suivant crée un réseau virtuel et un sous-réseau pour la passerelle d’application. La passerelle d’application a besoin d’utiliser son propre sous-réseau. C’est pourquoi le sous-réseau créé pour la passerelle d’application doit être plus petit que l’espace d’adressage du réseau virtuel de façon à permettre la création et l’utilisation d’autres sous-réseaux.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Création d'une adresse IP publique pour la configuration frontale

Créez une ressource IP publique **publicIP01** dans le groupe de ressources **appgw-rg** pour la région « West US ». Cet exemple utilise une adresse IP publique comme adresse IP frontale de la passerelle d’application.  Sachant que la passerelle d’application exige que l’adresse IP publique possède un nom DNS créé dynamiquement, `-DomainNameLabel` ne peut pas être spécifié pendant la création de l’adresse IP publique.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Avant de créer la passerelle Application Gateway, vous devez installer tous les éléments de configuration. L’exemple suivant crée les éléments de configuration nécessaires à une ressource de passerelle d’application.

| **Composant** | **Description** |
|---|---|
| **Configuration IP de la passerelle** | Configuration IP d’une passerelle d’application.|
| **Pool back-end** | Pool d’adresses IP, noms de domaine complets ou cartes d’interface réseau pour les serveurs d’applications qui hébergent l’application web.|
| **Sonde d’intégrité** | Sonde personnalisée utilisée pour surveiller l’état des membres du pool back-end.|
| **Paramètres HTTP** | Collection de paramètres comprenant le port, le protocole, l’affinité basée sur les cookies, la sonde et le délai d’expiration.  Ces paramètres déterminent la façon dont le trafic est acheminé vers les membres du pool back-end.|
| **Port frontal** | Port sur lequel la passerelle d’application écoute le trafic.|
| **Écouteur** | Combinaison d’un protocole, d’une configuration d’adresses IP frontales et d’un port frontal. C’est ce qui écoute les demandes entrantes.
|**Règle**| Route le trafic vers le back-end approprié en fonction des paramètres HTTP.|

```powershell
# Creates a application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Ajouter une sonde à une passerelle d’application existante

L’extrait de code suivant ajoute une sonde à une passerelle d’application existante.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzureRmApplicationGateway.
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Supprimer une sonde d’une passerelle d’application existante

L’extrait de code suivant supprime une sonde au niveau d’une passerelle d’application existante.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzureRmApplicationGateway.
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Obtenir le nom DNS d’une passerelle Application Gateway

Une fois la passerelle créée, l’étape suivante consiste à configurer le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle Application Gateway requiert un nom DNS attribué dynamiquement, ce qui n’est pas convivial. Pour s’assurer que les utilisateurs finaux peuvent atteindre la passerelle Application Gateway, un enregistrement CNAME peut être utilisé pour pointer vers le point de terminaison public de la passerelle Application Gateway. [Configuration d’un nom de domaine personnalisé pour Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Pour ce faire, récupérez les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle Application Gateway. Le nom DNS de la passerelle Application Gateway doit être utilisé pour créer un enregistrement CNAME qui pointe les deux applications web sur ce nom DNS. L’utilisation de A-records n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle Application Gateway.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Étapes suivantes

Apprenez à configurer le déchargement SSL en consultant [Configurer le déchargement SSL](application-gateway-ssl-arm.md)

