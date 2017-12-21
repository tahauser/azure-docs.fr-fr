---
title: "Créer une passerelle Application Gateway avec des règles de routage d’URL | Microsoft Docs"
description: "Cette page fournit des instructions pour créer et configurer une passerelle Azure Application Gateway avec les règles de routage d’URL."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: f0b085ebf922cd5b14acd91bf86b9262a6921e9e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing"></a>Créer une passerelle Application Gateway à l’aide du routage basé sur le chemin

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-create-url-route-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

Le routage basé sur le chemin associe des routes basées sur le chemin d’URL de la requête HTTP. Il vérifie s’il existe une route vers un pool de serveur back-end configuré pour les URL listées dans la passerelle d’application, puis envoie le trafic réseau vers le pool défini. Une utilisation courante du routage basé sur l’URL consiste à équilibrer la charge des demandes pour différents types de contenu entre différents pools de serveurs principaux.

La passerelle Azure Application Gateway a deux types de règles : des routes de base et des routes basées sur le chemin. Le type de route de base fournit un service de tourniquet pour les pools principaux. En plus de distribuer les tourniquets, les routes basées sur le chemin utilisent également le modèle de chemin d’accès de la requête URL pour choisir le pool principal.

## <a name="scenario"></a>Scénario

Dans l’exemple suivant, la passerelle Application Gateway gère le trafic pour contoso.com avec deux pools de serveurs principaux : un pool de serveurs vidéo et un pool de serveurs d’images.

Les demandes pour http://contoso.com/image* sont routées vers le pool de serveurs d’images (**pool1**) et celles pour http://contoso.com/video* sont routées vers le pool de serveurs vidéo (**pool2**). Si aucun des modèles de chemin ne correspond, un pool de serveurs par défaut (**pool1**) est sélectionné.

![Itinéraire d’URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Avant de commencer

1. Installez la dernière version des applets de commande Azure PowerShell à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](https://azure.microsoft.com/downloads/).
2. Créez un réseau virtuel et un sous-réseau pour la passerelle Application Gateway. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise ce sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
3. Veillez à ce que les serveurs ajoutés au pool principal pour utiliser la passerelle Application Gateway existent. Le cas échéant, vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.

## <a name="requirements-to-create-an-application-gateway"></a>Configuration requise pour créer une Application Gateway

* **Pool de serveurs principaux :** la liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou doivent correspondre à une adresse IP/VIP publique.
* **Paramètres du pool de serveurs principaux** : tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
* **Port frontal** : port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
* **Écouteur** : l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL).
* **Règle** : la règle lie l’écouteur et le pool de serveurs principaux et définit vers quel pool le trafic doit être dirigé quand il atteint un écouteur.

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

La différence entre l’utilisation du modèle de déploiement classique et celle d’Azure Resource Manager réside dans l’ordre de création de la passerelle d’application et des éléments à configurer.

Avec Resource Manager, tous les éléments constitutifs d’une passerelle Application Gateway sont configurés individuellement, puis regroupés pour créer la ressource Application Gateway.

Pour créer une passerelle Application Gateway, procédez comme suit :

1. Créer un groupe de ressources pour Resource Manager
2. Créer un réseau virtuel, un sous-réseau et une adresse IP publique pour la passerelle Application Gateway
3. Créer un objet de configuration de passerelle Application Gateway
4. Créez une ressource Application Gateway.

## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour Resource Manager

Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1

Connectez-vous à Azure.

```powershell
Login-AzureRmAccount
```

Vous êtes invité à vous authentifier à l’aide de vos informations d’identification.<BR>

### <a name="step-2"></a>Étape 2

Vérifiez les abonnements associés au compte.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Étape 3

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser. <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Étape 4

Créez un groupe de ressources. (Ignorez cette étape si vous utilisez un groupe de ressources existant.)

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

Vous pouvez aussi créer des balises pour un groupe de ressources pour une passerelle Application Gateway :

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

Azure Resource Manager nécessite que les groupes de ressources spécifient un emplacement par défaut, utilisé pour toutes les ressources du groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple précédent, nous avons créé un groupe de ressources appelé « appgw-RG » et utilisé l’emplacement « Ouest des États-Unis ».

> [!NOTE]
> Si vous devez configurer une sonde personnalisée pour votre passerelle Application Gateway, consultez [Création d’une passerelle Application Gateway avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-ps.md). Pour plus d’informations, consultez [Vue d’ensemble de l’analyse d’intégrité Application Gateway](application-gateway-probe-overview.md).
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

L’exemple ci-après indique comment créer un réseau virtuel à l’aide de Resource Manager. Cet exemple crée un réseau virtuel pour la passerelle Application Gateway. La passerelle Application Gateway Azure requiert son propre sous-réseau. C’est la raison pour laquelle le sous-réseau créé pour la passerelle Application Gateway est plus petit que l’espace d’adressage du réseau virtuel. Ainsi, d’autres ressources, y compris sans s’y limiter les serveurs web, sont configurées dans le même réseau virtuel.

### <a name="step-1"></a>Étape 1

Attribuez la plage d’adresses 10.0.0.0/24 à la variable subnet à utiliser pour créer un réseau virtuel.  Cette étape crée l’objet de configuration du sous-réseau pour la passerelle Application Gateway, qui est utilisé dans l’exemple suivant.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Étape 2

Créez un réseau virtuel nommé **appgwvnet** dans le groupe de ressources **appgw-rg** pour la région « Ouest des États-Unis » à l’aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24. Cette étape termine la configuration du réseau virtuel avec un seul sous-réseau de résidence pour la passerelle Application Gateway.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Étape 3 :

Attribuez la variable de sous-réseau pour les étapes suivantes. Cette variable est transmise au cmdlet `New-AzureRMApplicationGateway` lors de l’étape 4.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Création d'une adresse IP publique pour la configuration frontale

Créez une ressource IP publique **publicIP01** dans le groupe de ressources **appgw-rg** pour la région « West US ». La passerelle Application Gateway peut utiliser une adresse IP publique, l’adresse IP ou les deux pour recevoir les demandes d’équilibrage de charge.  Cet exemple utilise uniquement une adresse IP publique. Dans l’exemple suivant, aucun nom DNS n’est configuré pour la création de l’adresse IP publique, car la passerelle Application Gateway ne prend pas en charge les noms DNS personnalisés sur les adresses IP publiques.  Si un nom personnalisé est requis pour le point de terminaison public, créez un enregistrement CNAME pour pointer vers le nom DNS généré automatiquement pour l’adresse IP publique.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Une adresse IP est affectée à la passerelle Application Gateway au démarrage du service.

## <a name="create-the-application-gateway-configuration"></a>Créer la configuration de passerelle Application Gateway

Tous les éléments de configuration doivent être installés avant de créer la passerelle Application Gateway. Les étapes suivantes permettent de créer les éléments de configuration nécessaires à une ressource de passerelle Application Gateway.

### <a name="step-1"></a>Étape 1

Créez une configuration IP de passerelle Application Gateway nommée **gatewayIP01**. Au démarrage, la passerelle Application Gateway sélectionne une adresse IP du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Étape 2

Configurez les pools d’adresses IP principaux nommés **pool1** et **pool2** avec les adresses IP pour **pool1** et **pool2**. Ce sont les adresses IP des ressources qui hébergent l’application web devant être protégée par la passerelle Application Gateway. L’intégrité des membres du pool principal est validée par des sondes de base ou personnalisées. Le trafic est alors acheminé vers ces membres lorsque les demandes arrivent dans Application Gateway. Les pools principaux peuvent être utilisés par plusieurs règles au sein d’une passerelle Application Gateway. Un même pool principal peut donc être utilisé pour plusieurs applications web hébergées par un même hôte.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

Dans cet exemple, deux pools principaux acheminent le trafic réseau selon le chemin d’URL. Un pool reçoit le trafic du chemin d’URL « /video » et l’autre pool reçoit le trafic du chemin « /image ». Remplacez les adresses IP précédentes pour ajouter vos propres points de terminaison d’adresse IP d’application. 

### <a name="step-3"></a>Étape 3 :

Configurez les paramètres de passerelle Application Gateway **poolsetting01** et **poolsetting02** pour le trafic réseau à charge équilibrée dans le pool principal. Dans cet exemple, vous configurez différents paramètres pour les pools principaux. Chaque pool back-end peut avoir ses propres paramètres.  Les règles utilisent les paramètres HTTP du serveur principal pour router le trafic vers les membres appropriés du pool principal. Ils déterminent le protocole et le port utilisés lors de l’envoi du trafic vers les membres du pool principal. Les sessions basées sur les cookies sont également déterminées par les paramètres HTTP du serveur principal. Si elle est activée, l’affinité de session basée sur les cookies envoie le trafic vers le même serveur back-end que les requêtes précédentes pour chaque paquet.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Étape 4

Configurez l’adresse IP frontale avec des points de terminaison IP publics. Un écouteur utilise l’objet de configuration de l’adresse IP frontale pour associer l’adresse IP vers l’extérieur avec l’écouteur.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Étape 5

Configurez le port frontal pour une passerelle Application Gateway. L’écouteur utilise l’objet de configuration du port frontal pour définir le port écouté par la passerelle Application Gateway pour connaître le trafic sur l’écouteur.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>Étape 6

Configurez l’écouteur pour l’adresse IP publique et le port utilisé pour recevoir le trafic réseau entrant. L’exemple suivant utilise la configuration de l’adresse IP frontale configurée précédemment, la configuration de port frontal et un protocole (http ou https, qui respectent la casse), et il configure l’écouteur. Dans cet exemple, l’écouteur écoute le trafic HTTP sur le port 80 sur l’adresse IP publique créée précédemment.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>Étape 7

Configurez les chemins de règles d’URL pour les pools principaux. Cette étape configure le chemin d’accès relatif utilisé par la passerelle Application Gateway et définit le mappage entre le chemin d’URL et le pool principal qui est assigné pour gérer le trafic entrant.

> [!IMPORTANT]
> Chaque chemin doit commencer par une barre oblique « / ». L’astérisque n’est autorisé qu’à la fin. Exemples valides /xyz, /xyz*, ou /xyz/*. La chaîne transmise à l’outil de correspondance de chemin n’inclut pas de texte après le premier signe « ? » ou « # ». De plus, ces caractères ne sont pas autorisés. 

L’exemple suivant crée deux règles : une pour un chemin « /image/ » qui achemine le trafic vers le pool principal **pool1** et une autre pour un chemin « /video/ » qui achemine le trafic vers le pool principal **pool2**. Ces règles garantissent que le trafic de chaque jeu d’URL est routé vers le serveur principal. Par exemple, http://contoso.com/image/figure1.jpg accède à **pool1** et http://contoso.com/video/example.mp4 à **pool2**.

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

Si le chemin ne correspond à aucune des règles de chemins prédéfinies, la configuration de mappage des chemins de règles configure également un pool d’adresses principal par défaut. Par exemple, http://contoso.com/shoppingcart/test.html accède à **pool1**, car il est défini en tant que pool par défaut pour le trafic sans correspondance.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>Étape 8

Créez un paramètre de règle. Cette étape configure la passerelle Application Gateway pour utiliser le routage basé sur le chemin d’URL. La variable `$urlPathMap` définie à l’étape précédente est maintenant utilisée pour créer la règle de chemin d’accès. Dans cette étape, nous associons la règle avec un écouteur et le mappage de chemin d’accès d’URL créé précédemment.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>Étape 9

Configurez le nombre d’instances et taille de la passerelle Application Gateway.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Créez une passerelle Application Gateway avec tous les objets de configuration à partir de la procédure précédente.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-an-application-gateway-dns-name"></a>Obtenir le nom DNS d’une passerelle d’application

Après avoir créé la passerelle, vous devez configurer le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle Application Gateway requiert un nom DNS attribué dynamiquement, ce qui n’est pas convivial. Pour s’assurer que les clients peuvent atteindre la passerelle Application Gateway, vous pouvez utiliser un enregistrement CNAME pour pointer vers le point de terminaison public de la passerelle Application Gateway. Pour plus d’informations, consultez [Configuration d’un nom de domaine personnalisé pour un service cloud Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

Pour configurer l’enregistrement CNAME d’adresses IP frontales, récupérez les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle Application Gateway. Utilisez le nom DNS de la passerelle Application Gateway pour créer un enregistrement CNAME. L’utilisation de A-records n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle Application Gateway.

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

Pour en savoir plus sur le déchargement SSL (Secure Sockets Layer), consultez [Configurer une passerelle d’application pour le déchargement SSL à l’aide d’Azure Resource Manager](application-gateway-ssl-arm.md).

