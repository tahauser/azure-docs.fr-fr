---
title: "Création, démarrage ou suppression d’une passerelle Application Gateway | Microsoft Docs"
description: "Cette page fournit des instructions pour créer, configurer, démarrer et supprimer une passerelle Application Gateway Azure"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 577054ca-8368-4fbf-8d53-a813f29dc3bc
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 7fb54e96d20d34f453b7b016094b84504348335b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="create-start-or-delete-an-application-gateway-with-powershell"></a>Création, démarrage ou suppression d’une passerelle Application Gateway avec PowerShell 

> [!div class="op_single_selector"]
> * [portail Azure](application-gateway-create-gateway-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modèle Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Interface de ligne de commande Azure](application-gateway-create-gateway-cli.md)

La passerelle Azure Application Gateway est un équilibreur de charge de couche 7. Elle assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. Application Gateway offre de nombreuses fonctionnalités de contrôleur de livraison d’applications (ADC) : équilibrage de charge HTTP, affinité de session basée sur les cookies, déchargement SSL (Secure Sockets Layer), sondes d’intégrité personnalisées, prise en charge de plusieurs sites, etc. Pour obtenir une liste complète des fonctionnalités prises en charge, consultez [Vue d’ensemble d’Application Gateway](application-gateway-introduction.md)

Cet article vous guide lors des étapes de création, de configuration, de démarrage et de suppression d’une passerelle Application Gateway.

## <a name="before-you-begin"></a>Avant de commencer

1. Installez la dernière version des applets de commande Azure PowerShell à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](https://azure.microsoft.com/downloads/).
2. Si vous disposez d’un réseau virtuel, sélectionnez un sous-réseau vide existant ou créez un sous-réseau de votre réseau virtuel existant uniquement pour une utilisation par la passerelle Application Gateway. Vous ne pouvez pas déployer la passerelle Application Gateway sur un autre réseau virtuel constitué par les ressources que vous avez l’intention de déployer derrière la passerelle Application Gateway, sauf si l’homologation de réseaux virtuels est utilisée. Pour en savoir plus, consultez [Homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md)
3. Vérifiez que vous disposez d'un réseau virtuel qui fonctionne avec un sous-réseau valide. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise le sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
4. Les serveurs que vous configurez pour utiliser la passerelle Application Gateway doivent exister ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.

## <a name="what-is-required-to-create-an-application-gateway"></a>Quels sont les éléments nécessaires pour créer une passerelle Application Gateway ?

Lorsque vous utilisez la commande `New-AzureApplicationGateway` pour créer la passerelle Application Gateway, aucune configuration n’est définie et vous devez configurer la ressource créée à l’aide de XML ou d’un objet de configuration.

Les valeurs sont :

* **Pool de serveurs principaux :** liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou doivent correspondre à une adresse IP/VIP publique.
* **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
* **Port frontal :** il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
* **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL).
* **Règle :** la règle lie l’écouteur et le pool de serveurs principaux et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique.

## <a name="create-an-application-gateway"></a>Créez une passerelle d’application

Pour créer une passerelle d’application :

1. Créez une ressource de passerelle d’application.
2. Créez un fichier XML de configuration ou un objet de configuration.
3. Validez la configuration de la ressource Application Gateway nouvellement créée.

> [!NOTE]
> Si vous devez configurer une sonde personnalisée pour votre passerelle Application Gateway, consultez [Création d’une passerelle Application Gateway avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-classic-ps.md). Pour plus d’informations, découvrez les [sondes personnalisées et l’analyse du fonctionnement](application-gateway-probe-overview.md) .

![Exemple de scénario][scenario]

### <a name="create-an-application-gateway-resource"></a>Créer une ressource de passerelle d’application

Pour créer la passerelle, utilisez l’applet de commande `New-AzureApplicationGateway` en remplaçant les valeurs par les vôtres. La facturation de la passerelle ne démarre pas à ce stade. La facturation commence à une étape ultérieure, lorsque la passerelle a démarré correctement.

L’exemple ci-après illustre la création d’une passerelle Application Gateway à l’aide d’un réseau virtuel appelé « testvnet1 » et d’un sous-réseau appelé « subnet-1 » :

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

*Description*, *InstanceCount* et *GatewaySize* sont des paramètres facultatifs.

Pour valider la création de la passerelle, vous pouvez utiliser l’applet de commande `Get-AzureApplicationGateway`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Stopped
VirtualIPs    : {}
DnsName       :
```

> [!NOTE]
> La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Vous pouvez choisir Small, Medium ou Large.

Les paramètres *VirtualIPs* et *DnsName* sont sans valeur, car la passerelle n’a pas encore démarré. Ces valeurs seront créées une fois la passerelle en cours d'exécution.

## <a name="configure-the-application-gateway"></a>Configurer la passerelle Application Gateway

Vous pouvez configurer la passerelle Application Gateway à l’aide d’un objet de configuration ou de XML.

### <a name="configure-the-application-gateway-by-using-xml"></a>Configurer la passerelle Application Gateway à l’aide de XML

Dans l’exemple ci-dessous, vous allez utiliser un fichier XML pour configurer tous les paramètres de la passerelle d’application et les valider dans la ressource de passerelle d’application.  

#### <a name="step-1"></a>Étape 1 :

Copiez le texte suivant dans le Bloc-notes.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendPorts>
        <FrontendPort>
            <Name>(name-of-your-frontend-port)</Name>
            <Port>(port number)</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>(name-of-your-backend-pool)</Name>
            <IPAddresses>
                <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>(backend-setting-name-to-configure-rule)</Name>
            <Port>80</Port>
            <Protocol>[Http|Https]</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>(name-of-the-listener)</Name>
            <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
            <Protocol>[Http|Https]</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>(name-of-load-balancing-rule)</Name>
            <Type>basic</Type>
            <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
            <Listener>(name-of-the-listener)</Listener>
            <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Modifiez les valeurs entre parenthèses pour les éléments de configuration. Enregistrez le fichier avec l’extension .xml.

> [!IMPORTANT]
> L’élément de protocole Http ou Https est sensible à la casse.

L’exemple suivant montre comment configurer la passerelle Application Gateway à l’aide d’un fichier de configuration. L’exemple de charge équilibre le trafic HTTP sur le port public 80 et envoie le trafic réseau au port 80 principal entre deux adresses IP.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

#### <a name="step-2"></a>Étape 2 :

Ensuite, définissez la passerelle Application Gateway. Utilisez l’applet de commande `Set-AzureApplicationGatewayConfig` avec un fichier XML de configuration.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"
```

### <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>Configurer la passerelle Application Gateway à l’aide d’un objet de configuration

L’exemple suivant montre comment configurer la passerelle Application Gateway à l’aide d’objets de configuration. Vous devez configurer tous les éléments de configuration individuellement, puis les ajouter à un objet de configuration de passerelle d’application. Après avoir créé l’objet de configuration, vous utilisez la commande `Set-AzureApplicationGateway` pour valider la configuration dans la ressource Application Gateway créée précédemment.

> [!NOTE]
> Avant d’affecter une valeur à chaque objet de configuration, vous devez déclarer le type d’objet dans lequel PowerShell le stockera. La première ligne de création des différents éléments définit les objets `Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(object name)` utilisés.

#### <a name="step-1"></a>Étape 1

Créez tous les éléments de configuration.

Créez l’IP frontale comme indiqué dans l’exemple suivant.

```powershell
$fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
$fip.Name = "fip1"
$fip.Type = "Private"
$fip.StaticIPAddress = "10.0.0.5"
```

Créez le port frontal comme indiqué dans l’exemple suivant.

```powershell
$fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
$fep.Name = "fep1"
$fep.Port = 80
```

Créez le pool de serveurs principaux.

Définissez les adresses IP qui sont ajoutées au pool de serveurs principaux, comme indiqué dans l’exemple suivant.

```powershell
$servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
$servers.Add("10.0.0.1")
$servers.Add("10.0.0.2")
```

Utilisez l’objet $server pour ajouter les valeurs à l’objet de pool principal ($pool)

```powershell
$pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
$pool.BackendServers = $servers
$pool.Name = "pool1"
```

Créez le paramètre de pool de serveurs principaux.

```powershell
$setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
$setting.Name = "setting1"
$setting.CookieBasedAffinity = "enabled"
$setting.Port = 80
$setting.Protocol = "http"
```

Créez l’écouteur.

```powershell
$listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
$listener.Name = "listener1"
$listener.FrontendPort = "fep1"
$listener.FrontendIP = "fip1"
$listener.Protocol = "http"
$listener.SslCert = ""
```

Créez la règle.

```powershell
$rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
$rule.Name = "rule1"
$rule.Type = "basic"
$rule.BackendHttpSettings = "setting1"
$rule.Listener = "listener1"
$rule.BackendAddressPool = "pool1"
```

#### <a name="step-2"></a>Étape 2 :

Affectez tous les éléments de configuration à un objet de configuration Application Gateway ($appgwconfig).

Ajoutez l’adresse IP frontale à la configuration.

```powershell
$appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
$appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
$appgwconfig.FrontendIPConfigurations.Add($fip)
```

Ajoutez le port frontal à la configuration.

```powershell
$appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
$appgwconfig.FrontendPorts.Add($fep)
```
Ajoutez le pool de serveurs principaux à la configuration.

```powershell
$appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
$appgwconfig.BackendAddressPools.Add($pool)
```

Ajoutez le paramètre de pool principal à la configuration.

```powershell
$appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
$appgwconfig.BackendHttpSettingsList.Add($setting)
```

Ajoutez l’écouteur à la configuration.

```powershell
$appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
$appgwconfig.HttpListeners.Add($listener)
```

Ajoutez la règle à la configuration.

```powershell
$appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
$appgwconfig.HttpLoadBalancingRules.Add($rule)
```

### <a name="step-3"></a>Étape 3
Validez l’objet de configuration dans la ressource Application Gateway à l’aide de `Set-AzureApplicationGatewayConfig`.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig
```

## <a name="start-the-gateway"></a>Démarrer la passerelle

Une fois la passerelle configurée, utilisez l’applet de commande `Start-AzureApplicationGateway` pour démarrer la passerelle. La facturation pour une passerelle Application Gateway commence une fois la passerelle démarrée avec succès.

> [!NOTE]
> La règle `Start-AzureApplicationGateway` peut prendre jusqu’à 15 à 20 minutes.

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Vérifier l'état de la passerelle

Utilisez l’applet de commande `Get-AzureApplicationGateway` pour vérifier l’état de la passerelle. Si `Start-AzureApplicationGateway` a réussi à l’étape précédente, *l’état* doit être En cours d’exécution, et les paramètres *Vip* et *DnsName* doivent posséder des entrées valides.

L’exemple suivant montre une passerelle Application Gateway opérationnelle, en cours d’exécution et prête à prendre le trafic destiné à `http://<generated-dns-name>.cloudapp.net`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
Name          : AppGwTest
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
Vip           : 138.91.170.26
DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net
```

## <a name="delete-the-application-gateway"></a>Supprimer la passerelle Application Gateway

Pour supprimer la passerelle Application Gateway :

1. Utilisez l’applet de commande `Stop-AzureApplicationGateway` pour arrêter la passerelle.
2. Utilisez l’applet de commande `Remove-AzureApplicationGateway` pour supprimer la passerelle.
3. Vérifiez que la passerelle a été supprimée à l’aide de l’applet de commande `Get-AzureApplicationGateway`.

L’exemple suivant montre l’applet de commande `Stop-AzureApplicationGateway` sur la première ligne, suivie de la sortie.

```powershell
Stop-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Une fois la passerelle Application Gateway dans un état arrêté, utilisez l’applet de commande `Remove-AzureApplicationGateway` pour supprimer le service.

```powershell
Remove-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

Pour vérifier que le service a été supprimé, vous pouvez utiliser l’applet de commande `Get-AzureApplicationGateway`. Cette étape n'est pas requise.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
.....
```

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez configurer le déchargement SSL, consultez [Configuration d’une passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl.md).

Si vous voulez configurer une passerelle Application Gateway à utiliser avec l’équilibreur de charge interne, consultez [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

* [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png
