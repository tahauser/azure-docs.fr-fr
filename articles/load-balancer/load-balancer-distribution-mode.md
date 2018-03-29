---
title: Configuration d’un mode de distribution Azure Load Balancer | Microsoft Docs
description: Comment configurer le mode de distribution pour Azure Load Balancer pour prendre en charge l’affinité d’IP source.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ae793bad9cef86158418eb87e0c38ee0370a6bd2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configuration du mode de distribution pour Azure Load Balancer

## <a name="hash-based-distribution-mode"></a>Mode de distribution basé sur le hachage

Par défaut, le mode de distribution pour Azure Load Balancer est un hachage à 5 tuples. Le tuple est composé de l’IP source, du port source, de l’IP de destination, du port de destination et du type de protocole. Le hachage est utilisé pour mapper le trafic vers les serveurs disponibles, et l’algorithme fournit l’adhérence uniquement dans une session de transport. Les paquets de la même session sont dirigés vers la même instance IP de centre de données (DIP) derrière le point de terminaison d’équilibrage de charge. Lorsque le client démarre une nouvelle session à partir du même IP source, le port source change et contraint le trafic à se diriger vers un autre point de terminaison DIP.

![Mode de distribution basé sur le hachage à 5 tuples](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Mode d’affinité d’IP source

Load Balancer peut également être configuré à l’aide du mode de distribution d’affinité d’IP source. Ce mode de distribution est également connu sous le nom d’affinité de session ou d’affinité d’IP client. Il utilise un hachage à 2 tuples (IP source et IP de destination) ou à 3 tuples (IP source, IP de destination et type de protocole) pour mapper le trafic vers les serveurs disponibles. En utilisant l’affinité d’IP source, les connexions initiées depuis le même ordinateur client s’orientent vers le même point de terminaison DIP.

L’image suivante illustre une configuration à 2 tuples. Notez comment la distribution à 2 tuples passe de l’équilibrage de charge à la machine virtuelle 1 (VM1). La machine virtuelle VM1 est ensuite sauvegardée par VM2 et VM3.

![Mode de distribution d’affinité de session à 2 tuples](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Le mode d’affinité d’IP source permet de résoudre une incompatibilité entre Azure Load Balancer et la Passerelle des services Bureau à distance. En utilisant ce mode, vous pouvez générer une batterie de Passerelle des services Bureau à distance dans un seul service cloud.

Le chargement de médias constitue un autre scénario d’utilisation. Le chargement des données se produit via UDP, mais le plan de contrôle est obtenu via TCP :

* Un client initie une session TCP à l’adresse publique à charge équilibrée et est dirigé vers une adresse DIP spécifique. Ce canal reste actif, de sorte que l’intégrité de la connexion puisse être surveillée.
* Une nouvelle session UDP issue du même ordinateur client est initiée sur le même point de terminaison public d’équilibrage de charge. La connexion est dirigée vers le même point de terminaison DIP que la connexion TCP précédente. Le chargement de médias peut être exécuté à débit élevé alors que le canal de contrôle via TCP est maintenu.

> [!NOTE]
> Lorsqu’un jeu d’équilibrage de charge est modifié (suppression ou ajout d’une machine virtuelle), la distribution des demandes du client est recalculée. Vous ne pouvez pas dépendre de nouvelles connexions à partir de clients existants qui se retrouvent sur le même serveur. En outre, le mode de distribution d’affinité d’IP source peut entraîner une distribution inégale du trafic. Les clients qui s’exécutent derrière des serveurs proxy peuvent être vus comme une application cliente unique.

## <a name="configure-source-ip-affinity-settings"></a>Configurer les paramètres d’affinité IP source

Pour les machines virtuelles, utilisez Azure PowerShell pour modifier les paramètres de délai d’expiration. Ajoutez un point de terminaison Azure à une machine virtuelle et configurez le mode de distribution de l’équilibreur de charge :

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Définissez la valeur de l’élément `LoadBalancerDistribution` sur le volume d’équilibrage de charge souhaité. Spécifiez sourceIP pour l’équilibrage de charge à 2 tuples (IP source et IP de destination). Spécifiez sourceIPProtocol pour l’équilibrage de charge à 3 tuples (IP source, IP de destination et type de protocole) . Spécifiez none pour le comportement par défaut de l’équilibrage de charge à 5 tuples.

Récupérez la configuration du mode de distribution d’équilibrage de charge d’un point de terminaison en utilisant ces paramètres :

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

Si l’élément `LoadBalancerDistribution` n’est pas présent, Azure Load Balancer utilise l’algorithme par défaut à 5 tuples.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Configurer le mode de distribution sur un jeu de points de terminaison d’équilibrage de charge

Lorsque les points de terminaison font partie d’un jeu de points de terminaison d’équilibrage de charge, le mode de distribution doit être configuré sur ce jeu :

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Configurer le mode de distribution pour les points de terminaison de services cloud

Utilisez le Kit de développement logiciel (SDK) Azure pour .NET 2.5 pour mettre à jour votre service cloud. Les paramètres de point de terminaison des services cloud sont définis dans le fichier .csdef. Pour mettre à jour le mode de distribution d’équilibrage de charge pour un déploiement de services cloud, une mise à niveau du déploiement s’impose.

Voici un exemple de modifications apportées aux paramètres de point de terminaison dans .csdef :

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>Exemple d’API

L’exemple suivant montre comment reconfigurer le mode de distribution d’équilibrage de charge pour un jeu d’équilibrage de charge spécifié dans un déploiement. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Modifier le mode de distribution pour un jeu d’équilibrage de charge déployé

Utilisez le modèle de déploiement Azure Classic pour modifier une configuration de déploiement existante. Ajoutez l’en-tête `x-ms-version` et définissez la valeur sur la version 2014-09-01 ou une version ultérieure.

#### <a name="request"></a>Requête

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Comme décrit précédemment, définissez l’élément `LoadBalancerDistribution` sur sourceIP pour une affinité à 2 tuples, sur sourceIPProtocol pour une affinité à 3 tuples ou sur none pour ne définir aucune affinité (affinité à 5 tuples).

#### <a name="response"></a>response

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de l’équilibrage de charge interne](load-balancer-internal-overview.md)
* [Prise en main de la configuration d’un équilibreur de charge sur Internet](load-balancer-get-started-internet-arm-ps.md)
* [Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
