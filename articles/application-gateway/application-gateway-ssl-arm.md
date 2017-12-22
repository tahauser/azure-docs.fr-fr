---
title: "Configurer le déchargement SSL - Passerelle Azure Application Gateway - PowerShell | Microsoft Docs"
description: "Cet article fournit des instructions pour la création d’une passerelle Application Gateway pour le déchargement SSL à l’aide d’Azure Resource Manager"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: ee48ca45ae0d337b5b919dbbb28341caf8af0d45
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configuration d’une passerelle Application Gateway pour le déchargement SSL à l’aide d’Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-ssl-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-ssl-arm.md)
> * [Azure Classic PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Il est possible de configurer Azure Application Gateway de façon à mettre fin à la session SSL (Secure Sockets Layer) sur la passerelle pour éviter les tâches de déchiffrement SSL coûteuses au niveau de la batterie de serveurs web. Le déchargement SSL simplifie aussi la configuration de serveur principal et la gestion de l’application web.

## <a name="before-you-begin"></a>Avant de commencer

1. Installez la dernière version des applets de commande Azure PowerShell à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](https://azure.microsoft.com/downloads/).
2. Créez un réseau virtuel et un sous-réseau pour la passerelle Application Gateway. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise le sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
3. Les serveurs que vous configurez pour utiliser la passerelle Application Gateway doivent exister ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP ou une adresse IP virtuelle (VIP) publique affectée.

## <a name="what-is-required-to-create-an-application-gateway"></a>Quels sont les éléments nécessaires pour créer une passerelle Application Gateway ?

* **Pool de serveurs principaux :** la liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou doivent correspondre à une adresse IP/VIP publique.
* **Paramètres du pool de serveurs principaux** : chaque pool comporte des paramètres tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
* **Port frontal** : il s’agit du port public ouvert sur la passerelle d’application. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
* **Écouteur** : l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL).
* **Règle** : la règle lie l’écouteur et le pool de serveurs principaux et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique. Actuellement, seule la règle *de base* est prise en charge. La règle de *base* est la distribution de charge par tourniquet.

**Notes de configuration supplémentaires :**

Pour configurer des certificats SSL, le protocole dans **HttpListener** doit passer à **Https** (sensible à la casse). Ajoutez l’élément **SslCertificate** à **HttpListener** avec la valeur de variable configurée pour le certificat SSL. Le port du serveur frontal doit être mis à jour sur **443**.

**Pour activer l’affinité basée sur les cookies** : vous pouvez configurer une passerelle Application Gateway pour garantir qu’une requête d’une session client est toujours dirigée vers la même machine virtuelle dans la batterie de serveurs web. Pour ce faire, insérez un cookie de session permettant à la passerelle de diriger le trafic de manière appropriée. Pour activer l’affinité basée sur les cookies, définissez **CookieBasedAffinity** sur **Activé** dans l’élément **BackendHttpSettings**.

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

La différence entre l’utilisation du modèle de déploiement Azure Classic et celle d’Azure Resource Manager réside dans l’ordre de création de la passerelle Application Gateway et des éléments à configurer.

Avec Resource Manager, tous les composants d’une passerelle Application Gateway sont configurés individuellement, puis regroupés pour créer une ressource Application Gateway.

La procédure de création d’une passerelle Application Gateway comporte les étapes suivantes :

1. [Créer un groupe de ressources pour Resource Manager](#create-a-resource-group-for-resource-manager)
2. [Créer un réseau virtuel, un sous-réseau et une adresse IP publique pour la passerelle Application Gateway](#create-virtual-network-subnet-and-public-IP-for-the-application-gateway)
3. [Créer un objet de configuration de passerelle Application Gateway](#create-an-application-gateway-configuration-object)
4. [Créer une ressource de passerelle Application Gateway](#create-an-application-gateway-resource)

## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour Resource Manager

Veillez à passer en mode PowerShell pour utiliser les applets de commande d’Azure Resource Manager. Pour plus d’informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](../powershell-azure-resource-manager.md).

   1. Entrez la commande suivante :

   ```powershell
   Login-AzureRmAccount
   ```

   2. Pour vérifier les abonnements pour le compte, entrez les commandes suivantes :

   ```powershell
   Get-AzureRmSubscription
   ```

   Vous êtes invité à vous authentifier à l’aide de vos informations d’identification.

   3. Pour sélectionner l’abonnement Azure que vous souhaitez utiliser, entrez la commande suivante :

   ```powershell
   Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
   ```

   4. Pour créer un groupe de ressources, utilisez la commande suivante. (Ignorez cette étape si vous utilisez un groupe de ressources existant.)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce paramètre est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple précédent, nous avons créé un groupe de ressources appelé **appgw-RG**, ainsi que l’emplacement **Ouest des États-Unis**.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

L’exemple ci-après indique comment créer un réseau virtuel à l’aide de Resource Manager :

   1. Entrez la commande suivante :

   ```powershell
   $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
   ```

   Cet exemple attribue la plage d’adresses **10.0.0.0/24** à une variable subnet à utiliser pour créer un réseau virtuel.

   2. Entrez la commande suivante :

   ```powershell
   $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

   Cet exemple crée un réseau virtuel nommé **appgwvnet** dans le groupe de ressources **appw-rg** pour la région **Ouest des États-Unis** à l’aide du préfixe **10.0.0.0/16** avec le sous-réseau **10.0.0.0/24**.

   3. Entrez la commande suivante :

   ```powershell
   $subnet = $vnet.Subnets[0]
   ```

   Cet exemple assigne l’objet de sous-réseau à la variable **$subnet** pour les étapes suivantes.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Création d'une adresse IP publique pour la configuration frontale

Pour créer une adresse IP publique pour la configuration frontale, entrez la commande suivante :

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Cet exemple crée une ressource IP publique **publicIP01** dans le groupe de ressources **appw-rg** pour la région **Ouest des États-Unis**.

## <a name="create-an-application-gateway-configuration-object"></a>Créer un objet de configuration de passerelle Application Gateway

   1. Pour créer un objet de configuration de passerelle Application Gateway, entrez la commande suivante :

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
   ```

   Cet exemple crée une configuration IP de passerelle Application Gateway nommée **gatewayIP01**. Au démarrage, la passerelle Application Gateway sélectionne une adresse IP du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

   2. Entrez la commande suivante :

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
   ```

   Cet exemple configure le pool d’adresses IP principal nommé **pool01** avec les adresses IP **134.170.185.46**, **134.170.188.221** et **134.170.185.50**. Ces valeurs correspondent aux adresses IP qui recevront le trafic réseau provenant du point de terminaison IP frontal. Remplacez les adresses IP de l’exemple précédent par les adresses IP des points de terminaison de votre application web.

   3. Entrez la commande suivante :

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
   ```

   Cet exemple configure le paramètre de passerelle Application Gateway **poolsetting01** pour équilibrer la charge du trafic réseau dans le pool principal.

   4. Entrez la commande suivante :

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
   ```

   Cet exemple configure le port IP frontal nommé **frontendport01** pour le point de terminaison IP public.

   5. Entrez la commande suivante :

   ```powershell
   $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
   ```

   Cet exemple configure le certificat utilisé pour la connexion SSL. Le certificat doit être au format PFX et le mot de passe contenir entre 4 et 12 caractères.

   6. Entrez la commande suivante :

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
   ```

   Cet exemple crée la configuration IP frontale nommée **fipconfig01** et associe l’adresse IP publique à cette configuration.

   7. Entrez la commande suivante :

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
   ```

   Cet exemple crée l’écouteur nommé **listener01** et associe le port frontal à la configuration IP et au certificat du serveur frontal.

   8. Entrez la commande suivante :

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   Cet exemple crée la règle d’acheminement d’équilibrage de charge nommée **rule01** qui configure le comportement d’équilibrage de charge.

   9. Entrez la commande suivante :

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   Cet exemple configure la taille d’instance de la passerelle Application Gateway.

   > [!NOTE]
   > La valeur par défaut du paramètre **InstanceCount** est de **2**, avec une valeur maximale de 10. La valeur par défaut du paramètre **GatewaySize** est **Medium**. Vous pouvez choisir entre Standard_Small, Standard_Medium et Standard_Large.

   10. Entrez la commande suivante :

   ```powershell
   $policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
   ```

   Cette étape permet de définir la stratégie SSL à utiliser sur la passerelle d’application. Pour plus d’informations, visitez la page [Configurer les versions de stratégie SSL et les suites de chiffrement sur Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Créer une passerelle Application Gateway avec New-AzureApplicationGateway

Entrez la commande suivante :

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

Cet exemple crée une passerelle Application Gateway avec tous les éléments de configuration de la procédure précédente. Dans notre exemple, la passerelle Application Gateway est appelée **appgwtest**.

## <a name="get-the-application-gateway-dns-name"></a>Obtenir le nom DNS d’une passerelle Application Gateway

Une fois la passerelle créée, l’étape suivante consiste à configurer le serveur frontal pour la communication. La passerelle Application Gateway requiert un nom DNS attribué dynamiquement lorsque vous utilisez une adresse IP publique, ce qui n’est pas convivial. Pour s’assurer que les utilisateurs finaux peuvent atteindre la passerelle Application Gateway, vous pouvez utiliser un enregistrement CNAME pour pointer vers le point de terminaison public de la passerelle d’application. Pour plus d’informations, consultez la page [Configuration d’un nom de domaine personnalisé dans Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Pour obtenir le nom DNS de la passerelle Application Gateway, récupérez les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément **PublicIPAddress** attaché à la passerelle Application Gateway. Utilisez le nom DNS de la passerelle d’application pour créer un enregistrement CNAME qui pointe les deux applications web sur ce nom DNS. L’utilisation de A-records n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle d’application.


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

Si vous voulez configurer une passerelle d’application à utiliser avec l’équilibreur de charge interne, consultez [Création d’une passerelle d’application avec un équilibrage de charge interne](application-gateway-ilb.md).

Pour plus d'informations sur les options d'équilibrage de charge en général, consultez :

* [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
