---
title: "Créer une passerelle Application Gateway pour héberger plusieurs sites | Microsoft Docs"
description: "Cette page fournit des instructions pour créer, configurer une passerelle Application Gateway Azure pour l’hébergement de plusieurs applications web sur la même passerelle."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: b107d647-c9be-499f-8b55-809c4310c783
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
ms.openlocfilehash: d42efa7d359f5c87c14afbfd138328b37c8ae6c2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>Créer une passerelle Application Gateway pour l’hébergement de plusieurs applications web

> [!div class="op_single_selector"]
> * [portail Azure](application-gateway-create-multisite-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-multisite-azureresourcemanager-powershell.md)

L’hébergement de plusieurs sites vous permet de déployer plusieurs applications web sur la même passerelle Application Gateway. Il s’appuie sur la présence de l’en-tête de l’hôte dans la requête HTTP entrante pour déterminer l’écouteur qui doit recevoir le trafic. L’écouteur dirige ensuite le trafic vers le pool principal approprié tel que configuré dans la définition des règles de la passerelle. Dans les applications web SSL, la passerelle Application Gateway s’appuie sur l’extension d’indication du nom du serveur (SNI) pour choisir l’écouteur approprié au trafic web. Une utilisation courante de l’hébergement de plusieurs sites consiste à équilibrer la charge des demandes pour différents domaines Web entre différents pools de serveurs principaux. De même, plusieurs sous-domaines du même domaine racine pourraient également être hébergés sur la même passerelle Application Gateway.

## <a name="scenario"></a>Scénario

Dans l’exemple suivant, la passerelle Application Gateway gère le trafic pour contoso.com et fabrikam.com avec deux pools de serveurs principaux : un pool de serveurs contoso et un pool de serveurs fabrikam. Une configuration similaire pourrait servir à héberger des sous-domaines hôtes comme app.contoso.com et blog.contoso.com.

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>Avant de commencer

1. Installez la dernière version des applets de commande Azure PowerShell à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](https://azure.microsoft.com/downloads/).
2. Les serveurs ajoutés au pool principal pour utiliser la passerelle Application Gateway doivent exister, ou vous devez créer leurs points de terminaison sur le réseau virtuel dans un autre sous-réseau ou avec une adresse IP/VIP publique affectée.

## <a name="requirements"></a>Configuration requise

* **Pool de serveurs principaux :** liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou doivent correspondre à une adresse IP/VIP publique. Le nom de domaine complet peut également être utilisé.
* **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
* **Port frontal :** il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
* **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL). Pour les passerelles Application Gateway activées pour plusieurs sites, le nom d’hôte et les indicateurs SNI sont également ajoutés.
* **Règle :** la règle lie l’écouteur et le pool de serveurs principaux, et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique. Les règles sont traitées dans l’ordre où elles sont répertoriées, et le trafic est orienté selon la première règle correspondante, quelle que soit sa spécificité. Par exemple, si une règle utilise un écouteur de base et qu’une autre utilise un écouteur multisite sur le même port, la règle avec l’écouteur multisite doit être répertoriée avant la règle avec l’écouteur de base pour que la règle multisite fonctionne comme prévu.

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Procédez comme suit pour créer une passerelle Application Gateway :

1. Créer un groupe de ressources pour Resource Manager
2. Créer un réseau virtuel, des sous-réseaux et une adresse IP publique pour la passerelle Application Gateway.
3. Créer un objet de configuration de passerelle Application Gateway
4. Créez une ressource Application Gateway.

## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour Resource Manager

Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, voir l’article [Utilisation de Windows Powershell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1

Connexion à Azure

```powershell
Login-AzureRmAccount
```
Vous êtes invité à vous authentifier à l’aide de vos informations d’identification.

### <a name="step-2"></a>Étape 2 :

Vérifiez les abonnements associés au compte.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Étape 3

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant).

```powershell
New-AzureRmResourceGroup -Name appgw-RG -location "West US"
```

Vous pouvez également créer des balises pour un groupe de ressources pour la passerelle Application Gateway :

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}
```

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Celui-ci est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé **appgw-RG** avec pour emplacement **États-Unis de l’Ouest**.

> [!NOTE]
> Si vous devez configurer une sonde personnalisée pour votre passerelle Application Gateway, consultez [Création d’une passerelle Application Gateway avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-ps.md). Consultez les informations sur les [sondes personnalisées et l’analyse du fonctionnement](application-gateway-probe-overview.md).

## <a name="create-a-virtual-network-and-subnets"></a>Créer un réseau virtuel et des sous-réseaux

L’exemple ci-après indique comment créer un réseau virtuel à l’aide de Resource Manager. Deux sous-réseaux sont créés au cours de cette étape. Le premier sous-réseau est pour la passerelle Application Gateway elle-même. La passerelle Application Gateway doit avoir son propre sous-réseau contenant ses instances. Seules d’autres passerelles Application Gateway peuvent être déployées dans ce sous-réseau. Le deuxième sous-réseau est utilisé pour contenir les serveurs principaux d’applications.

### <a name="step-1"></a>Étape 1

Attribuez la plage d’adresses 10.0.0.0/24 à la variable subnet à utiliser pour contenir la passerelle Application Gateway.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24
```
### <a name="step-2"></a>Étape 2

Attribuez la plage d’adresses 10.0.1.0/24 à la variable subnet2 à utiliser pour les pools principaux.

```powershell
$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>Étape 3

Créez un réseau virtuel nommé **appgwvnet** dans le groupe de ressources **appwrg** pour la région États-Unis de l’Ouest à l’aide du préfixe 10.0.0.0/16 avec les sous-réseaux 10.0.0.0/24 et 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2
```

### <a name="step-4"></a>Étape 4

Attribuez une variable de sous-réseau pour les prochaines étapes, qui permet de créer une passerelle Application Gateway.

```powershell
$appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
$backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Création d'une adresse IP publique pour la configuration frontale

Créez une ressource IP publique **publicIP01** dans le groupe de ressources **appgw-rg** pour la région « West US ».

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Une adresse IP est affectée à la passerelle Application Gateway au démarrage du service.

## <a name="create-application-gateway-configuration"></a>Créer une configuration de passerelle Application Gateway

Avant de créer la passerelle Application Gateway, vous devez installer tous les éléments de configuration. Les étapes suivantes permettent de créer les éléments de configuration nécessaires à une ressource Application Gateway.

### <a name="step-1"></a>Étape 1

Créez une configuration IP de passerelle Application Gateway nommée **gatewayIP01**. Lorsque la passerelle Application Gateway démarre, elle sélectionne une adresse IP à partir du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet
```

### <a name="step-2"></a>Étape 2 :

Configurez les pools d’adresses IP principaux nommés **pool01** et **pool2** avec les adresses IP **134.170.185.46**, **134.170.188.221**, **134.170.185.50** pour **pool1** et **134.170.186.46**, **134.170.189.221** et **134.170.186.50** pour **pool2**.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105
```

Dans cet exemple, il existe deux pools principaux pour acheminer le trafic réseau selon le site demandé. Un pool reçoit le trafic du site « contoso.com » et l’autre le trafic du site « fabrikam.com ». Vous devez remplacer les adresses IP précédentes pour ajouter vos propres points de terminaison d’adresse IP d’application. À la place des adresses IP internes, vous pouvez également utiliser des adresses IP publiques, un nom de domaine complet ou une carte réseau d’une machine virtuelle pour les instances de backend. Utilisez le paramètre « -BackendFQDNs » pour spécifier les noms de domaine complets au lieu des adresses IP dans PowerShell.

### <a name="step-3"></a>Étape 3 :

Configurez les paramètres de passerelle Application Gateway **poolsetting01** et **poolsetting02** pour le trafic réseau à charge équilibrée dans le pool principal. Dans cet exemple, vous configurez différents paramètres pour les pools principaux. Chaque pool principal peut avoir son propre paramètre de pool principal.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Étape 4

Configurez l’adresse IP frontale avec un point de terminaison IP public.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Étape 5

Configurez le port frontal pour une passerelle Application Gateway.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443
```

### <a name="step-6"></a>Étape 6

Configurer deux certificats SSL pour les deux sites Web que nous allons traiter dans cet exemple. Un certificat est pour le trafic de contoso.com et l’autre pour le trafic fabrikam.com. Il doit s’agir de certificats émis par une autorité de certification pour vos sites Web. Les certificats auto-signés sont pris en charge mais déconseillés pour le trafic de production.

```powershell
$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>
```

### <a name="step-7"></a>Étape 7

Dans cet exemple, configurez deux écouteurs pour les deux sites Web. Cette étape permet de configurer les écouteurs pour l’adresse IP publique, ainsi que le port et l’hôte utilisés pour recevoir le trafic entrant. Le paramètre HostName est requis pour la prise en charge de plusieurs sites et doit être défini sur le site Web approprié pour lequel le trafic est reçu. Le paramètre RequireServerNameIndication doit être défini sur true pour les sites web ayant besoin d’une prise en charge SSL dans un scénario à plusieurs hôtes. Si la prise en charge SSL est requise, vous devez également spécifier le certificat SSL utilisé pour sécuriser le trafic de cette application web. L’association de FrontendIPConfiguration, FrontendPort et de HostName doit être unique pour un écouteur. Chaque écouteur peut prendre en charge un seul certificat.

```powershell
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02
```

### <a name="step-8"></a>Étape 8

Dans cet exemple, créez deux paramètres de règle pour les deux applications web. Une règle associe les écouteurs, les pools principaux et les paramètres http. Cette étape configure la passerelle Application Gateway afin d’utiliser la règle de routage de base (une pour chaque site web). Le trafic vers chaque site web est reçu par son écouteur configuré, puis dirigé vers son pool principal configuré, à l’aide des propriétés spécifiées dans BackendHttpSettings.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2
```

### <a name="step-9"></a>Étape 9

Configurez le nombre d’instances et taille de la passerelle Application Gateway.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>Créer une passerelle Application Gateway

Créez une passerelle Application Gateway avec tous les objets de configuration à partir de la procédure précédente.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02
```

> [!IMPORTANT]
> L’approvisionnement de la passerelle Application Gateway est une opération longue qui peut prendre du temps.
> 
> 

## <a name="get-application-gateway-dns-name"></a>Obtenir le nom DNS d’une passerelle Application Gateway

Une fois la passerelle créée, l’étape suivante consiste à configurer le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle Application Gateway requiert un nom DNS attribué dynamiquement, ce qui n’est pas convivial. Pour s’assurer que les utilisateurs finaux peuvent atteindre la passerelle d’application, un enregistrement CNAME peut être utilisé pour pointer vers le point de terminaison public de la passerelle d’application. [Configuration d’un nom de domaine personnalisé pour Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Pour ce faire, récupérez les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle Application Gateway. Le nom DNS de la passerelle Application Gateway doit être utilisé pour créer un enregistrement CNAME qui pointe les deux applications web sur ce nom DNS. L’utilisation de A-records n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle Application Gateway.

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

Découvrez comment protéger vos sites web grâce au [Pare-feu d’applications web sur Application Gateway](application-gateway-webapplicationfirewall-overview.md)

