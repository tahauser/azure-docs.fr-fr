---
title: "Utilisation du service Gestion des API Azure dans un réseau virtuel avec Application Gateway | Microsoft Docs"
description: "Découvrez comment installer et configurer le service Gestion des API Azure dans un réseau virtuel interne avec Application Gateway (WAF) en tant que FrontEnd"
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: f9bc3ffda9f943a37fd5aadf440abf7d33a6d1de
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Intégrer le service Gestion des API dans un réseau virtuel interne avec Application Gateway 

##<a name="overview"></a> Vue d'ensemble
 
Le service Gestion des API peut être configuré dans un réseau virtuel en mode interne, ce qui le rend uniquement accessible à partir du réseau virtuel. La passerelle Azure Application Gateway est un service PAAS qui propose un équilibreur de charge de couche 7. Il agit comme un service proxy inverse et fournit dans son offre un pare-feu d’applications web (WAF).

Combiner la gestion des API configurée dans un réseau virtuel interne avec le frontal Application Gateway permet les scénarios suivants :

* Utilisez la même ressource de gestion des API pour la consommation à la fois par les consommateurs internes et externes.
* Utilisez une seule ressource de gestion des API et mettez à disposition un sous-ensemble d’API défini dans la gestion des API pour les consommateurs externes.
* Fournissez un moyen clé en main d’activer et désactiver l’accès à la gestion des API à partir de l’Internet public. 

## <a name="prerequisites"></a>Composants requis

Pour effectuer les étapes décrites dans cet article, vous devez disposer des éléments suivants :

+ Un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Une instance APIM. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).

##<a name="scenario"></a> Scénario
Dans cet article, nous allons étudier comment utiliser un seul et même service Gestion des API pour les consommateurs internes et externes, et l’utiliser comme serveur frontal sur les API locales et cloud. Vous allez également voir comment exposer uniquement un sous-ensemble de vos API (dans cet exemple, elles sont mises en surbrillance en vert) pour une consommation externe, à l’aide de la fonctionnalité PathBasedRouting disponible dans Application Gateway.

Dans le premier exemple de configuration, toutes vos API sont gérées uniquement à partir de votre réseau virtuel. Les consommateurs internes (mis en surbrillance en orange) peuvent accéder à toutes vos API internes et externes. Le trafic ne sort jamais vers Internet et une performance élevée est fournie via les circuits ExpressRoute.

![itinéraire d’URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a> Avant de commencer

1. Installez la dernière version des applets de commande Azure PowerShell à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Créez un réseau virtuel et des sous-réseaux distincts pour le service Gestion des API et Application Gateway. 
3. Si vous envisagez de créer un serveur DNS personnalisé pour le réseau virtuel, faites-le avant de commencer le déploiement. Vérifiez qu’il fonctionne en vous assurant qu’une machine virtuelle créée dans un nouveau sous-réseau du réseau virtuel peut résoudre tous les points de terminaison de service Azure et y accéder.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Qu’est-ce qui est nécessaire pour créer une intégration entre le service Gestion des API et Application Gateway ?

* **Pool de serveurs principaux :** adresse IP virtuelle interne du service Gestion des API.
* **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont appliqués à tous les serveurs du pool.
* **Port frontal :** port public ouvert sur la passerelle Application Gateway. Le trafic l’atteignant est redirigé vers l’un des serveurs principaux.
* **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL).
* **Règle :** la règle relie un écouteur à un pool de serveurs principaux.
* **Sonde d’intégrité personnalisée :** Application Gateway, par défaut, utilise des sondes basées sur des adresses IP pour déterminer les serveurs actifs dans le BackendAddressPool. Le service Gestion des API répond uniquement aux demandes dont l’en-tête d’hôte est correct. C’est pourquoi les sondes par défaut échouent. Une sonde d’intégrité personnalisée doit être définie pour aider Application Gateway à déterminer que le service est actif et qu’il doit transférer les demandes.
* **Certificat de domaine personnalisé :** pour accéder au service Gestion des API à partir d’Internet, vous devez créer un mappage CNAME de son nom d’hôte au nom DNS frontal d’Application Gateway. Cela garantit que l’en-tête de nom d’hôte et le certificat envoyé à Application Gateway qui est transféré au service Gestion des API peuvent être reconnus comme valides par l’APIM.

## <a name="overview-steps"></a> Étapes requises pour l’intégration de la gestion des API et d’Application Gateway 

1. Créer un groupe de ressources pour Resource Manager
2. Créer un réseau virtuel, un sous-réseau et une adresse IP publique pour la passerelle Application Gateway Créer un autre sous-réseau pour le service Gestion des API
3. Créer un service Gestion des API dans le sous-réseau de réseau virtuel créé ci-dessus et veiller à l’utiliser en mode interne
4. Configurer le nom de domaine personnalisé dans le service Gestion des API
5. Créer un objet de configuration de passerelle Application Gateway
6. Créer une ressource Application Gateway
7. Créer un CNAME à partir du nom DNS public de la passerelle Application Gateway pour le nom d’hôte proxy du service Gestion des API

## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour Resource Manager

Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager).

### <a name="step-1"></a>Étape 1 :

Connexion à Azure

```powershell
Login-AzureRmAccount
```

Authentifiez-vous à l’aide de vos informations d’identification.<BR>

### <a name="step-2"></a>Étape 2

Vérifiez les abonnements associés au compte et sélectionnez-le.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>Étape 3 :

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant).

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

L’exemple ci-dessous indique comment créer un réseau virtuel à l’aide de Resource Manager.

### <a name="step-1"></a>Étape 1 :

Affectez la plage d’adresses 10.0.0.0/24 à la variable subnet à utiliser pour la passerelle Application Gateway lors de la création d’un réseau virtuel.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Étape 2

Affectez la plage d’adresses 10.0.1.0/24 à la variable subnet à utiliser pour le service Gestion des API lors de la création d’un réseau virtuel.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Étape 3

Créez un réseau virtuel nommé **appgwvnet** dans le groupe de ressources **apim-appGw-RG** pour la région États-Unis de l’Ouest utilisant le préfixe 10.0.0.0/16 avec les sous-réseaux 10.0.0.0/24 et 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Étape 4

Attribution d’une variable de sous-réseau pour les étapes suivantes

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Créer un service Gestion des API dans un réseau virtuel configuré en mode interne

L’exemple ci-dessous montre comment créer un service Gestion des API dans un réseau virtuel configuré pour un accès interne uniquement.

### <a name="step-1"></a>Étape 1 :
Créez un objet de réseau virtuel du service Gestion des API via le sous-réseau $apimsubnetdata créé ci-dessus.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Étape 2
Créez un service Gestion des API dans le réseau virtuel.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
Après la réussite de la commande ci-dessus, consultez la [configuration DNS requise pour accéder au service Gestion des API du réseau virtuel interne](api-management-using-with-internal-vnet.md#apim-dns-configuration) pour y accéder.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Configurer un nom de domaine personnalisé dans le service Gestion des API

### <a name="step-1"></a>Étape 1 :
Chargez le certificat avec la clé privée correspondant au domaine. Pour cet exemple, c’est `*.contoso.net`. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>Étape 2
Une fois le certificat chargé, créez un objet de configuration de nom d’hôte pour le proxy avec le nom d’hôte `api.contoso.net`, car l’exemple de certificat fournit une autorité pour le domaine `*.contoso.net`. 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Création d'une adresse IP publique pour la configuration frontale

Créez une ressource IP publique **publicIP01** dans le groupe de ressources **appGw-RG** pour la région « West US ».

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

Une adresse IP est affectée à la passerelle Application Gateway au démarrage du service.

## <a name="create-application-gateway-configuration"></a>Créer une configuration de passerelle Application Gateway

Tous les éléments de configuration doivent être installés avant de créer la passerelle Application Gateway. Les étapes suivantes permettent de créer les éléments de configuration nécessaires à une ressource Application Gateway.

### <a name="step-1"></a>Étape 1

Créez une configuration IP de passerelle Application Gateway nommée **gatewayIP01**. Lorsque la passerelle Application Gateway démarre, elle sélectionne une adresse IP à partir du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Étape 2 :

Configurez le port IP frontal pour le point de terminaison IP public. Ce port est le port auquel les utilisateurs finaux se connectent.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>Étape 3 :

Configurez l’adresse IP frontale avec un point de terminaison IP public.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Étape 4

Configurez le certificat pour la passerelle Application Gateway utilisée pour déchiffrer et rechiffrer le trafic transitant par celle-ci.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Étape 5

Créez l’écouteur HTTP pour la passerelle Application Gateway. Affectez-lui la configuration IP frontale, le port et le certificat SSL.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Étape 6

Créez une sonde personnalisée pour le point de terminaison de domaine de proxy `ContosoApi` du service Gestion des API. Le chemin d’accès `/status-0123456789abcdef` est un point de terminaison d’intégrité par défaut hébergé sur tous les services de gestion des API. Définissez `api.contoso.net` comme nom d’hôte de sonde personnalisée pour la protéger à l’aide du certificat SSL.

> [!NOTE]
> Le nom d’hôte `contosoapi.azure-api.net` est le nom d’hôte du proxy par défaut configuré lorsqu’un service nommé `contosoapi` est créé dans la version publique d’Azure. 
> 

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Étape 7

Chargez le certificat à utiliser sur les ressources du pool principal pour lequel le chiffrement SSL est activé. Il s’agit du certificat que vous avez fourni à l’étape 4 ci-dessus.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Étape 8

Configurez les paramètres de serveur principal HTTP de la passerelle Application Gateway. Définissez notamment une limite de délai d’expiration pour les demandes de serveur principal après laquelle elles sont annulées. Cette valeur est différente du délai d’expiration de la sonde.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Étape 9

Configurez un pool d’adresses IP du serveur principal nommé **apimbackend** avec l’adresse IP virtuelle interne du service Gestion des API créé ci-dessus.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Étape 10

Créez des paramètres pour un serveur principal factice (inexistant). Les demandes de chemins d’accès aux API que nous ne souhaitons pas exposer dans Gestion des API via Application Gateway atteignent ce serveur principal et retournent l’erreur 404.

Configurez les paramètres HTTP du serveur principal factice.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Configurez un serveur principal factice **dummyBackendPool**, qui pointe vers une adresse de nom de domaine complet **dummybackend.com**. Cette adresse de nom de domaine complet n’existe pas dans le réseau virtuel.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Créez un paramètre de règle que la passerelle Application Gateway utilisera par défaut pour pointer vers le serveur principal inexistant **dummybackend.com** dans le réseau virtuel.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>Étape 11

Configurez les chemins de règles d’URL pour les pools principaux. Vous pouvez ainsi sélectionner uniquement certaines des API du service Gestion des API pour les exposer au public. (Par exemple, pour `Echo API` (/echo/), `Calculator API` (/calc/), etc., rendez uniquement `Echo API` accessible à partir d’Internet). 

L’exemple suivant crée une règle simple pour le chemin d’accès « /echo/ » acheminant le trafic vers le serveur principal « apimProxyBackendPool ».

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

Si le chemin d’accès ne correspond aux règles de chemins d’accès que nous voulons activer dans Gestion des API, la configuration de mappage des chemins de règles configure également un pool d’adresses de serveurs principaux par défaut nommé **dummyBackendPool**. Par exemple, http://api.contoso.net/calc/ * permet d’accéder à **dummyBackendPool**, car il est défini comme pool par défaut pour le trafic sans correspondance.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

L’étape ci-dessus garantit que seules les demandes correspondant au chemin d’accès « /echo » sont autorisées via Application Gateway. Les demandes pour d’autres API configurées dans le service Gestion des API lèvent des erreurs 404 dans la passerelle Application Gateway en cas d’accès à partir d’Internet. 

### <a name="step-12"></a>Étape 12

Créez un paramètre de règle pour la passerelle Application Gateway pour utiliser le routage basé sur le chemin d’URL.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>Étape 13

Configurez le nombre d’instances et la taille de la passerelle Application Gateway. Ici, nous utilisons la [référence WAF](../application-gateway/application-gateway-webapplicationfirewall-overview.md) pour renforcer la sécurité de la ressource du service Gestion des API.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>Étape 14

Configurez WAF pour passer en mode Prévention.
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Créer une passerelle Application Gateway

Créez une passerelle Application Gateway avec tous les objets de configuration des étapes précédentes.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Définition du CNAME du nom d’hôte du proxy du service Gestion des API sur le nom DNS public de la ressource Application Gateway

Une fois la passerelle créée, l’étape suivante consiste à configurer le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle Application Gateway requiert un nom DNS attribué dynamiquement, qui risque de ne pas être facile à utiliser. 

Le nom DNS de la passerelle Application Gateway doit être utilisé pour créer un enregistrement CNAME qui pointe le nom d’hôte proxy APIM (`api.contoso.net` dans les exemples ci-dessus) vers ce nom DNS. Pour configurer l’enregistrement CNAME d’adresses IP frontales, récupérez les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress. L’utilisation de A-records n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"></a> Résumé
Le service Gestion des API Azure configuré dans un réseau virtuel fournit une interface de passerelle unique pour l’ensemble des API configurées, qu’elles soient hébergées en local ou dans le cloud. L’intégration d’Application Gateway au service Gestion des API vous permet d’activer facilement l’accessibilité d’API particulières sur Internet, tout en fournissant un pare-feu d’applications web en tant que pare-feu frontal pour votre instance de service Gestion des API.

##<a name="next-steps"></a> Étapes suivantes
* En savoir plus sur Azure Application Gateway
  * [Vue d’ensemble d’Application Gateway](../application-gateway/application-gateway-introduction.md)
  * [Pare-feu d’applications web sur Application Gateway](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Application Gateway à l’aide du routage basé sur le chemin](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* En savoir plus sur le service Gestion des API et les réseaux virtuels
  * [Utilisation de Gestion des API disponible uniquement dans le réseau virtuel](api-management-using-with-internal-vnet.md)
  * [Avec la gestion des API dans le réseau virtuel](api-management-using-with-vnet.md)
