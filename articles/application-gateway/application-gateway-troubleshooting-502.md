---
title: "Résolution des erreurs de passerelle incorrecte dans Azure Application Gateway (502) | Microsoft Docs"
description: "Découvrez comment résoudre les erreurs 502 dans Application Gateway"
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 1d431ead-d318-47d8-b3ad-9c69f7e08813
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.openlocfilehash: e0099734a81cd8b1edf5cf80cb56b5c322a5feee
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Résolution des erreurs de passerelle incorrecte dans Application Gateway

Découvrez comment résoudre les erreurs de passerelle incorrecte (502) reçues lors de l’utilisation d’Application Gateway.

## <a name="overview"></a>Vue d'ensemble

Après avoir configuré une passerelle Application Gateway, les utilisateurs peuvent rencontrer l’erreur « Erreur serveur 502 : le serveur Web a reçu une réponse erronée lors de son utilisation en tant que passerelle ou serveur proxy ». Cette erreur peut se produire pour les raisons suivantes :

* Un groupe de sécurité réseau, un routage défini par l’utilisateur ou un DNS personnalisé bloque l’accès aux membres du pool principal.
* Les machines virtuelles principales ou les instances du groupe de machines virtuelles identiques [ne répondent pas à la sonde d’intégrité par défaut](#problems-with-default-health-probe.md).
* [Configuration non valide ou incorrecte des sondes d’intégrité personnalisées](#problems-with-custom-health-probe.md).
* Le [pool principal d’Azure Application Gateway n’est pas configuré ou est vide](#empty-backendaddresspool).
* Aucune des machines virtuelles ou des instances du [groupe de machines virtuelles identiques](#unhealthy-instances-in-backendaddresspool) n’est intègre.
* [Problèmes de connectivité ou d’expiration](#request-time-out) des requêtes d’utilisateur.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problème de groupe de sécurité réseau, de routage défini par l’utilisateur ou de DNS personnalisé

### <a name="cause"></a>Cause :

Si l’accès au serveur principal est bloqué en raison de la présence d’un groupe de sécurité réseau, d’un routage défini par l’utilisateur ou d’un DNS personnalisé, les instances de la passerelle Application Gateway ne pourra par atteindre le pool principal et entraînera des échecs de sonde et par conséquent des erreurs de type 502. Notez que le groupe de sécurité réseau/le routage défini par l’utilisateur peut être présent dans le sous-réseau de la passerelle Application Gateway ou le sous-réseau sur lequel les machines virtuelles d’application sont déployées. De même, la présence d’un DNS personnalisé dans le réseau virtuel peut également entraîner des problèmes si le nom de domaine complet est utilisé pour les membres du pool principal et n’est pas résolu correctement par le serveur DNS configuré par l’utilisateur pour le réseau virtuel.

### <a name="solution"></a>Solution

Validez la configuration du groupe de sécurité réseau, du routage défini par l’utilisateur et du DNS en effectuant les étapes suivantes :
* Vérifiez les groupes de sécurité réseau associés au sous-réseau de la passerelle Application Gateway. Assurez-vous que la communication vers le serveur principal n’est pas bloquée.
* Vérifiez le routage défini par l’utilisateur associé au sous-réseau de la passerelle Application Gateway. Assurez-vous que le routage défini par l’utilisateur ne détourne pas le trafic du sous-réseau du serveur principal. Vérifiez par exemple le routage vers les appliances virtuelles du réseau ou les itinéraires par défaut annoncés sur le sous-réseau de la passerelle Application Gateway via ExpressRoute/VPN.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzureRmVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Vérifiez le groupe de sécurité réseau et le routage vers la machine virtuelle du serveur principal.

```powershell
Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzureRmEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Vérifiez la présence d’un DNS personnalisé dans le réseau virtuel. Le DNS peut être vérifié en examinant les détails des propriétés du réseau virtuel dans la sortie.

```json
Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Le cas échéant, vérifiez que le serveur DNS est en mesure de résoudre correctement le nom de domaine complet du membre du pool principal.

## <a name="problems-with-default-health-probe"></a>Problèmes avec la sonde d’intégrité par défaut

### <a name="cause"></a>Cause :

Les erreurs 502 peuvent également indiquer que la sonde d’intégrité par défaut n’est pas en mesure d’atteindre les machines virtuelles du serveur principal. Lorsqu’une instance Application Gateway est approvisionnée, elle configure automatiquement une sonde d’intégrité par défaut pour chaque BackendAddressPool à l’aide des propriétés de BackendHttpSetting. La configuration de cette sonde d’intégrité ne nécessite aucune action de la part de l’utilisateur. Plus précisément, lorsqu’une règle d’équilibrage de charge est configurée, une association est établie entre BackendHttpSetting et BackendAddressPool. Un contrôle par défaut est configuré pour chacune de ces associations et Application Gateway initie régulièrement une connexion de contrôle d’intégrité à chaque instance dans le BackendAddressPool au niveau du port spécifié dans l’élément BackendHttpSetting. Le tableau suivant répertorie les valeurs associées à la sonde d’intégrité par défaut.

| Propriétés de la sonde | Valeur | Description |
| --- | --- | --- |
| URL de sonde |http://127.0.0.1/ |Chemin d'accès de l'URL |
| Intervalle |30 |Intervalle d’analyse en secondes |
| Délai d’attente |30 |Délai d’expiration de l’analyse en secondes |
| Seuil de défaillance sur le plan de l’intégrité |3 |Nombre de tentatives d’analyse Le serveur principal est marqué comme étant défectueux après que le nombre d’échecs consécutifs a atteint le seuil de défaillance. |

### <a name="solution"></a>Solution

* Assurez-vous qu’un site par défaut est configuré et qu’il écoute sur le port 127.0.0.1.
* Si BackendHttpSetting spécifie un port autre que 80, le site par défaut doit être configuré pour écouter sur ce port.
* L’appel à http://127.0.0.1:port doit renvoyer un code de résultat HTTP 200. Ce code doit être retourné dans un délai de 30 secondes.
* Vérifiez que le port configuré est ouvert et qu’aucune règle de pare-feu ou aucun groupe de sécurité réseau Azure ne bloque le trafic entrant ou sortant sur le port configuré.
* Si vous utilisez des machines virtuelles Azure classiques ou un service cloud avec un nom de domaine complet ou une adresse IP publique, assurez-vous que le [point de terminaison](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) correspondant est ouvert.
* Si la machine virtuelle est configuré via Azure Resource Manager et se trouve en dehors du réseau virtuel où est déployé Application Gateway, le [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) doit être configuré pour autoriser l’accès sur le port souhaité.

## <a name="problems-with-custom-health-probe"></a>Problèmes avec la sonde d’intégrité personnalisée

### <a name="cause"></a>Cause :

Les sondes d’intégrité personnalisées apportent davantage de flexibilité au comportement de contrôle par défaut. En utilisant des sondes personnalisées, les utilisateurs peuvent configurer l’intervalle d’analyse, l’URL et le chemin à tester ainsi que le nombre de réponses en échec autorisé avant que l’instance de pool principal soit marquée comme étant défectueuse. Les propriétés supplémentaires suivantes sont ajoutées.

| Propriétés de la sonde | Description |
| --- | --- |
| NOM |Nom de la sonde. Ce nom est utilisé pour désigner la sonde dans les paramètres HTTP du serveur principal. |
| Protocole |Protocole utilisé pour envoyer la sonde. La sonde utilise le protocole défini dans les paramètres HTTP du serveur principal |
| Host |Nom d’hôte pour l’envoi de la sonde. S’applique uniquement lorsque plusieurs sites sont configurés sur Application Gateway. Ce nom est différent du nom d’hôte de la machine virtuelle. |
| path |Chemin relatif de la sonde. Le chemin valide commence par « / ». La sonde est envoyée à \<protocole\>://\<hôte\>:\<port\>\<chemin d’accès\> |
| Intervalle |Intervalle d’analyse en secondes. Il s’agit de l’intervalle de temps qui s’écoule entre deux analyses consécutives. |
| Délai d’attente |Délai d’expiration de l’analyse en secondes. Si aucune réponse valide n’est reçue dans le délai imparti, la sonde est marquée comme étant en échec. |
| Seuil de défaillance sur le plan de l’intégrité |Nombre de tentatives d’analyse Le serveur principal est marqué comme étant défectueux après que le nombre d’échecs consécutifs a atteint le seuil de défaillance. |

### <a name="solution"></a>Solution

Vérifiez que la sonde d’intégrité personnalisée est correctement configurée (voir la table précédente). Outre les étapes de dépannage précédentes, vérifiez également les points suivants :

* Assurez-vous que la sonde est correctement spécifiée suivant les indications du [guide](application-gateway-create-probe-ps.md).
* Si Application Gateway est configuré pour un seul site, le nom d’hôte par défaut doit être spécifié sous la forme « 127.0.0.1 », sauf s’il est configuré d’une autre manière dans la sonde personnalisée.
* Assurez-vous qu’un appel à http://\<hôte\>:\<port\>\<chemin d’accès\> retourne un code de résultat HTTP 200.
* Assurez-vous que les paramètres Interval, Time-out et UnhealtyThreshold se trouvent dans la plage acceptable.
* Si vous utilisez une sonde HTTPS, vérifiez que le serveur back-end ne nécessite pas SNI en configurant un certificat de secours sur le serveur back-end lui-même.

## <a name="request-time-out"></a>Délai d’attente de la demande

### <a name="cause"></a>Cause :

À réception d’une demande de l’utilisateur, Application Gateway applique les règles configurées à la demande et achemine cette demande à une instance de pool principal. Application Gateway observe un temps d’attente (configurable) pour recevoir une réponse de l’instance de serveur principal. Par défaut, cet intervalle est de **30 secondes**. Si Application Gateway ne reçoit pas de réponse de l’application principale dans cet intervalle, la demande de l’utilisateur renverra une erreur 502.

### <a name="solution"></a>Solution

Application Gateway permet aux utilisateurs de configurer ce paramètre via BackendHttpSetting pour l’appliquer ensuite à différents pools. Les pools de serveurs principaux peuvent avoir des paramètres BackendHttpSetting différents et, par conséquent, des délais d’attente différents.

```powershell
    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Pool d’adresses principal vide

### <a name="cause"></a>Cause :

Si Application Gateway ne dispose d’aucune machine virtuelle ou d’aucun groupe de machines virtuelles identiques configurés dans le pool d’adresses principal, il ne peut pas acheminer les demandes client et renvoie alors une erreur de passerelle incorrecte.

### <a name="solution"></a>Solution

Assurez-vous que le pool d’adresses principal n’est pas vide. Vous pouvez pour cela utiliser PowerShell, l’interface de ligne de commande ou le portail.

```powershell
Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

L’applet de commande ci-dessus doit renvoyer un pool d’adresses principal non vide. Dans l’exemple suivant, la commande retourne deux pools configurés avec un nom de domaine complet ou des adresses IP pour les machines virtuelles de serveur principal. L’approvisionnement de BackendAddressPool doit se trouver à l’état « Succeeded » (Réussi).

BackendAddressPoolsText :

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Instances non intègres dans BackendAddressPool

### <a name="cause"></a>Cause :

Si aucune des instances de BackendAddressPool n’est intègre, Application Gateway ne disposera d’aucun serveur principal vers lequel acheminer la demande utilisateur. Cette situation peut se produire lorsque les instances de serveur principal sont intègres, mais que l’application requise n’est pas déployée.

### <a name="solution"></a>Solution

Assurez-vous que les instances sont intègres et que l’application est correctement configurée. Vérifiez si les instances de serveur principal sont en mesure de répondre à une commande ping générée par une autre machine virtuelle résidant dans le même réseau virtuel. Si vous utilisez un point de terminaison public, assurez-vous qu’une demande de navigateur à l’application web est accessible.

## <a name="next-steps"></a>Étapes suivantes

Si les étapes précédentes ne vous permettent pas de résoudre le problème, ouvrez un [ticket d’incident](https://azure.microsoft.com/support/options/).

