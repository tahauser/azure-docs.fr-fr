---
title: "Configurer un pare-feu d’applications web : passerelle Azure Application Gateway | Microsoft Docs"
description: "Cet article explique comment utiliser un pare-feu d’applications web sur une passerelle Application Gateway nouvelle ou existante."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: e8106805d21b325e33fb3ab376db75cd783b9042
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurer un pare-feu d’application web sur une passerelle Application Gateway nouvelle ou existante

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Interface de ligne de commande Azure](application-gateway-web-application-firewall-cli.md)

Découvrez comment créer une passerelle d’application avec le pare-feu d’applications web (WAF) activé. Découvrez également comment ajouter un pare-feu WAF à une passerelle d’application existante.

Le pare-feu WAF d’Azure Application Gateway protège les applications web des attaques basées sur le web courantes comme l’injection de code SQL, les attaques de script de site à site et les piratages de session.

 Application Gateway est un équilibrage de charge de couche 7. La solution assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. Application Gateway fournit de nombreuses fonctionnalités de contrôleur de livraison d'applications (ADC) :

 * Équilibrage de charge HTTP
 * Affinité de session basée sur les cookies
 * Déchargement SSL (Secure Sockets Layer)
 * Sondes d’intégrité personnalisées
 * Prise en charge de la fonctionnalité multisite
 
 Pour obtenir une liste complète des fonctionnalités prises en charge, consultez : [Vue d’ensemble d’Application Gateway](application-gateway-introduction.md).

Cet article explique comment [ajouter un pare-feu WAF à une passerelle Application Gateway existante](#add-web-application-firewall-to-an-existing-application-gateway). Il explique également comment [créer une passerelle Application Gateway utilisant un pare-feu WAF](#create-an-application-gateway-with-web-application-firewall).

![image du scénario][scenario]

## <a name="waf-configuration-differences"></a>Différences de configuration WAF

Si vous avez lu la rubrique [Création d’une passerelle Application Gateway avec PowerShell](application-gateway-create-gateway-arm.md), vous connaissez les paramètres de référence (SKU) à configurer lorsque vous créez une passerelle Application Gateway. Le pare-feu WAF inclut des paramètres supplémentaires à définir lorsque vous configurez une référence SKU sur une passerelle Application Gateway. Aucune autre modification n’est nécessaire sur la passerelle d’application elle-même.

| **Paramètre** | **Détails**
|---|---|
|**Référence (SKU)** |Une passerelle Application Gateway normale sans un pare-feu WAF prend en charge les tailles **Standard\_Small**, **Standard\_Medium** et **Standard\_Large**. Avec l’introduction d’un pare-feu WAF, deux autres SKU sont disponibles : **WAF\_Medium** et **WAF\_Large**. Les pare-feu WAF ne sont pas pris en charge sur les petites passerelles Application Gateway.|
|**Niveau** | Les valeurs disponibles sont **Standard** ou **WAF**. Lorsque vous utilisez un pare-feu WAF, vous devez choisir **WAF**.|
|**Mode** | Ce paramètre indique le mode du pare-feu WAF. Les valeurs autorisées sont **Détection** et **Prévention**. Lorsque le pare-feu WAF est configuré en mode **Détection**, toutes les menaces sont stockées dans un fichier journal. En mode **Prévention**, les événements sont toujours consignés, mais l’attaquant reçoit une erreur d’autorisation de type 403 de la part de la passerelle Application Gateway.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Ajout d’un pare-feu d’applications web à une passerelle Application Gateway existante

Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](../powershell-azure-resource-manager.md).

1. Connectez-vous à votre compte Azure.

    ```powershell
    Login-AzureRmAccount
    ```

2. Sélectionnez l’abonnement à utiliser pour ce scénario.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. Récupérez la passerelle à laquelle vous souhaitez ajouter un pare-feu WAF.

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

4. Configurez la référence SKU WAF. Les tailles disponibles sont **WAF\_Large** et **WAF\_Medium**. Lorsque vous utilisez un pare-feu WAF, le niveau doit être défini sur **WAF**. Confirmez la capacité lorsque vous définissez la référence SKU.

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

5. Configurez les paramètres WAF comme indiqué dans l’exemple suivant. Pour **FirewallMode**, les valeurs disponibles sont **Prévention** et **Détection**.

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

6. Mettez à jour la passerelle Application Gateway avec les paramètres définis à l’étape précédente.

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

Cette commande met à jour la passerelle Application Gateway avec un pare-feu WAF. Pour comprendre comment afficher les journaux de votre passerelle Application Gateway, consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md). En raison des critères de sécurité inhérents à un pare-feu WAF, consultez régulièrement les journaux pour comprendre la politique de sécurité appliquée à vos applications web.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Créer une passerelle Application Gateway avec un pare-feu d’applications web

Les étapes suivantes vous guident lors du processus de création d’une passerelle Application Gateway avec un pare-feu WAF.

Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](../powershell-azure-resource-manager.md).

1. Connectez-vous à Azure en exécutant `Login-AzureRmAccount`. Vous êtes invité à vous authentifier à l’aide de vos informations d’identification.

2. Vérifiez les abonnements pour le compte en exécutant `Get-AzureRmSubscription`.

3. Sélectionnez l’abonnement Azure à utiliser.

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources pour la passerelle d’application.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Celui-ci est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple précédent, nous avons créé un groupe de ressources appelé « appgw-RG » avec l’emplacement « Ouest des États-Unis ».

> [!NOTE]
> Si vous devez configurer une sonde personnalisée pour votre passerelle Application Gateway, consultez [Création d’une passerelle Application Gateway avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-ps.md). Pour plus d’informations, consultez [Sondes personnalisées et surveillance de l’intégrité](application-gateway-probe-overview.md).

### <a name="configure-a-virtual-network"></a>Configurer un réseau virtuel

Une passerelle Application Gateway nécessite son propre sous-réseau. Dans cette étape, vous allez créer un réseau virtuel avec un espace d’adressage de 10.0.0.0/16 et deux sous-réseaux, un pour la passerelle Application Gateway et un pour les membres du pool backend.

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for application gateway instances. With a smaller subnet, you might not be able to add more instances of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the back-end pool members subnet.
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previously created subnets.
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-the-public-ip-address"></a>Configurer l’adresse IP publique

Pour pouvoir traiter les demandes externes, la passerelle Application Gateway a besoin d’une adresse IP publique. Cette adresse IP publique ne doit pas avoir un `DomainNameLabel` défini pour être utilisé par la passerelle d’application.

```powershell
# Create a public IP address for use with the application gateway. Defining the `DomainNameLabel` during creation is not supported for use with the application gateway.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>Configurer la passerelle Application Gateway

```powershell
# Create an IP configuration to configure which subnet the application gateway uses. When the application gateway starts, it picks up an IP address from the configured subnet and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a back-end pool to hold the addresses or NIC handles for the application that the application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authentication certificate to be used to communicate with the back-end servers.
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Configure the back-end HTTP settings to be used to define how traffic is routed to the back-end pool. The authentication certificate used in the previous step is added to the back-end HTTP settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a front-end port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a front-end IP configuration to associate the public IP address with the application gateway.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end IP configuration, port, and SSL certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a load-balancer routing rule that configures the load balancer behavior. In this example, a basic round-robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway.
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use.
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Configure the WAF configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway by using all the previously created configuration objects.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> Les passerelles Application Gateway créées avec la configuration WAF de base sont définies avec la solution CRS 3.0 dédiée aux protections.

## <a name="get-an-application-gateway-dns-name"></a>Obtenir le nom DNS d’une passerelle d’application

Une fois la passerelle créée, l’étape suivante consiste à configurer le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle Application Gateway requiert un nom DNS attribué dynamiquement, ce qui n’est pas convivial. Pour s’assurer que les utilisateurs finaux peuvent atteindre la passerelle Application Gateway, utilisez un enregistrement CNAME pour pointer vers le point de terminaison public de la passerelle d’application. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé pour un service cloud Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Pour configurer un alias, récupérez les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle Application Gateway. Utilisez le nom DNS de la passerelle d’application pour créer un enregistrement CNAME qui pointe les deux applications web sur ce nom DNS. Il n’est pas recommandé d’utiliser des enregistrements A, car l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle Application Gateway.

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

Pour savoir comment configurer la journalisation des diagnostics et consigner les événements détectés ou bloqués par un pare-feu WAF, consultez [Diagnostics de la passerelle Application Gateway](application-gateway-diagnostics.md).

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png
