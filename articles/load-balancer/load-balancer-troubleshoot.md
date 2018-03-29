---
title: Résoudre les problèmes liés à Azure Load Balancer | Microsoft Docs
description: Résoudre les problèmes connus liés à Azure Load Balancer
services: load-balancer
documentationcenter: na
author: RamanDhillon
manager: timlt
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 560e2ed7cafd1018f4b64ac6d0e45134560082b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-azure-load-balancer"></a>Résoudre les problèmes liés à Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Cette page contient des informations de résolution des problèmes liés aux questions courantes relatives à Azure Load Balancer. Lorsque la connectivité de l’équilibreur de charge n’est pas disponible, les symptômes les plus courants sont les suivants : 
- Les machines virtuelles situées derrière l’équilibreur de charge ne répondent pas aux sondes d’intégrité 
- Les machines virtuelles situées derrière l’équilibreur de charge ne répondent pas au trafic sur le port configuré

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Symptôme : les machines virtuelles situées derrière l’équilibreur de charge ne répondent pas aux sondes d’intégrité
Pour que les serveurs principaux participent au jeu d’équilibrage de charge, ils doivent réussir la vérification de la sonde. Pour plus d’informations sur les sondes d’intégrité, consultez la page [Comprendre les sondes de l’équilibrage de charge](load-balancer-custom-probe-overview.md). 

Les machines virtuelles du pool principal de l’équilibreur de charge peuvent ne pas répondre aux sondes en raison des raisons suivantes : 
- Une machine virtuelle du pool principal de l’équilibreur de charge est défectueuse 
- Une machine virtuelle du pool principal de l’équilibreur de charge n’écoute pas sur le port de la sonde 
- Un pare-feu ou un groupe de sécurité réseau bloque le port sur les machines virtuelles du pool principal de l’équilibreur de charge 
- Autres erreurs de configuration de l’équilibreur de charge

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Cause 1 : une machine virtuelle du pool principal de l’équilibreur de charge est défectueuse 

**Validation et résolution**

Pour résoudre ce problème, connectez-vous aux machines virtuelles participant et vérifiez si l’état de la machine virtuelle est sain et si elle peut répondre à la commande **PsPing** ou **TCPing** d’une autre machine virtuelle du pool. Si la machine virtuelle est défectueuse ou si elle ne peut pas répondre à la sonde, vous devez corriger le problème et rétablir la machine virtuelle à un état sain pour qu’elle puisse faire partie de l’équilibrage de charge.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Cause 2 : une machine virtuelle du pool principal de l’équilibreur de charge n’écoute pas sur le port de la sonde
Si la machine virtuelle est saine, mais ne répond pas à la sonde, il se peut que le port de la sonde ne soit pas ouvert sur la machine virtuelle participant ou que la machine virtuelle n’écoute pas sur ce port.

**Validation et résolution**

1. Connectez-vous à la machine virtuelle principale. 
2. Ouvrez une invite de commandes et exécutez la commande suivante pour vérifier qu’une application écoute sur le port de la sonde :   
            netstat -an
3. Si l’état du port indiqué n’est pas **ÉCOUTE**, configurez le port approprié. 
4. Vous pouvez également sélectionner un autre port, qui indique **ÉCOUTE**, et mettre à jour en conséquence la configuration de l’équilibreur de charge.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Cause 3 : un pare-feu ou un groupe de sécurité réseau bloque le port sur les machines virtuelles du pool principal de l’équilibreur de charge  
Si le pare-feu sur la machine virtuelle bloque le port de la sonde, ou un ou plusieurs groupes de sécurité réseau configurés sur le sous-réseau ou sur la machine virtuelle, ou n’autorise pas la sonde à atteindre le port, la machine virtuelle est incapable de répondre à la sonde d’intégrité.          

**Validation et résolution**

* Si le pare-feu est activé, vérifiez s’il est configuré pour autoriser le port de la sonde. Si tel n’est pas le cas, configurez le pare-feu pour autoriser le trafic sur le port de la sonde, puis procédez à un nouveau test. 
* Dans la liste des groupes de sécurité réseau, vérifiez si le trafic entrant ou sortant sur le port de la sonde subit des interférences. 
* Vérifiez également si une règle **Refuser tout** des groupes de sécurité réseau sur la carte d’interface réseau de la machine virtuelle ou du sous-réseau a une priorité supérieure à celle de la règle par défaut qui autorise les sondes et le trafic de l’équilibreur de charge (les groupes de sécurité réseau doivent autoriser l’adresse IP 168.63.129.16 de l’équilibreur de charge). 
* Si l’une de ces règles bloque le trafic de la sonde, supprimez et reconfigurez les règles pour autoriser le trafic de la sonde.  
* Vérifiez si la machine virtuelle répond à présent aux sondes d’intégrité. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Cause 4 : autres erreurs de configuration de l’équilibreur de charge
Si toutes les causes précédentes semblent vérifiées et résolues et si la machine virtuelle principale ne répond toujours pas à la sonde d’intégrité, testez manuellement la connectivité, puis collectez des traces pour comprendre la connectivité.

**Validation et résolution**

* Utilisez **Psping** à partir d’une des autres machines virtuelles dans le réseau virtuel pour tester la réponse du port de la sonde (exemple : .\psping.exe -t 10.0.0.4:3389) et enregistrez les résultats. 
* Utilisez **TCPing** à partir d’une des autres machines virtuelles dans le réseau virtuel pour tester la réponse du port de la sonde (exemple : .\tcping.exe 10.0.0.4 3389) et enregistrez les résultats. 
* Si aucune réponse n’est reçue dans ces tests ping,
    - exécutez une trace Netsh simultanée sur la machine virtuelle du pool principal cible et une autre machine virtuelle de test à partir du même réseau virtuel. À présent, exécutez un test PsPing pendant un certain temps, collectez des traces réseau, puis arrêtez le test. 
    - Analysez la capture du réseau et vérifiez que des paquets entrants et sortants sont associés à la requête ping. 
        - Si aucun paquet entrant n’est observé sur la machine virtuelle du pool principal, il se peut qu’une erreur de configuration des groupes de sécurité réseau ou UDR bloque le trafic. 
        - Si aucun paquet sortant n’est observé sur la machine virtuelle du pool principal, celle-ci doit être vérifiée pour y rechercher les problèmes non liés (par exemple, application bloquant le port de la sonde). 
    - Vérifiez si les paquets de la sonde sont contraints d’atteindre une autre destination (probablement via les paramètres UDR) avant l’équilibreur de charge. Ce faisant, le trafic peut ne jamais atteindre la machine virtuelle principale. 
* Modifiez le type de sonde (par exemple, remplacez HTTP par TCP) et configurez le port correspondant dans les listes de contrôle d’accès (ACL) des groupes de sécurité réseau et le pare-feu pour vérifier si le problème est lié à la configuration de la réponse de la sonde. Pour plus d’informations sur la configuration de la sonde d’intégrité, consultez la page [Endpoint Load Balancing health probe configuration (Configuration d’une sonde d’intégrité pour l’équilibrage de charge des points de terminaison)](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Symptôme : les machines virtuelles situées derrière l’équilibreur de charge ne répondent pas au trafic sur le port de données configuré

Si une machine virtuelle d’un pool principal est répertoriée comme saine et répond aux sondes d’intégrité, mais ne participe toujours pas à l’équilibrage de charge ou ne répond pas au trafic de données, ce peut être dû à l’une des raisons suivantes : 
* Une machine virtuelle du pool principal de l’équilibreur de charge n’écoute pas sur le port de données 
* Un groupe de sécurité réseau bloque le port sur la machine virtuelle du pool principal de l’équilibreur de charge  
* Accès à l’équilibreur de charge à partir des mêmes machine virtuelle et carte d’interface réseau 
* Accès à l’adresse IP virtuelle de l’équilibreur de charge Internet à partir de la machine virtuelle du pool principal de l’équilibreur de charge 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Cause 1 : une machine virtuelle du pool principal de l’équilibreur de charge n’écoute pas sur le port de données 
Si une machine virtuelle ne répond pas au trafic de données, il se peut que le port cible ne soit pas ouvert sur la machine virtuelle participant ou que la machine virtuelle n’écoute pas sur ce port. 

**Validation et résolution**

1. Connectez-vous à la machine virtuelle principale. 
2. Ouvrez une invite de commandes et exécutez la commande suivante pour vérifier qu’une application écoute sur le port de données :  
            netstat -an 
3. Si l’état du port indiqué n’est pas « ÉCOUTE », configurez le port d’écoute approprié. 
4. Si l’état du port est Écoute, recherchez dans l’application cible sur ce port des problèmes possibles. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Cause 2 : un groupe de sécurité réseau bloque le port sur la machine virtuelle du pool principal de l’équilibreur de charge  

Si un ou plusieurs groupes de sécurité réseau configurés sur le sous-réseau ou sur la machine virtuelle bloquent l’adresse IP source ou le port, alors la machine virtuelle ne peut pas répondre.

* Répertoriez les groupes de sécurité réseau configurés sur la machine virtuelle du pool principal. Pour plus d'informations, consultez les pages suivantes :
    -  [Gérer les groupes de sécurité réseau à l’aide du portail Azure](../virtual-network/virtual-network-manage-nsg-arm-portal.md)
    -  [Gérer des groupes de sécurité réseau à l’aide de PowerShell](../virtual-network/virtual-network-manage-nsg-arm-ps.md)
* Dans la liste des groupes de sécurité réseau, vérifiez si :
    - le trafic entrant ou sortant sur le port de données subit des interférences ; 
    - une règle **Refuser tout** des groupes de sécurité réseau sur la carte d’interface réseau de la machine virtuelle ou du sous-réseau a une priorité supérieure à celle de la règle par défaut qui autorise les sondes et le trafic de l’équilibreur de charge (les groupes de sécurité réseau doivent autoriser l’adresse IP 168.63.129.16 de l’équilibreur de charge qui correspond au port de la sonde). 
* Si l’une des règles bloque le trafic, supprimez et reconfigurez ces règles pour autoriser le trafic de données.  
* Vérifiez si la machine virtuelle répond à présent aux sondes d’intégrité.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Cause 3 : accès à l’équilibreur de charge à partir des mêmes machine virtuelle et interface réseau 

Si l’application hébergée sur la machine virtuelle principale d’un équilibreur de charge essaie d’accéder à une autre application hébergée dans la même machine virtuelle principale via la même interface réseau, ce scénario n’est pas pris en charge et échoue. 

**Résolution** : vous pouvez résoudre ce problème via l’une des méthodes suivantes :
* Configurez des machines virtuelles du pool principal distinctes par application. 
* Configurez l’application dans les machines virtuelles à double carte d’interface réseau afin que chaque application utilise sa propre interface réseau et adresse IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-vip-from-the-participating-load-balancer-backend-pool-vm"></a>Cause 4 : accès à l’adresse IP virtuelle de l’équilibreur de charge interne à partir de la machine virtuelle participante du pool principal de l’équilibreur de charge

Si une adresse IP virtuelle de l’équilibreur de charge interne est configurée dans un réseau virtuel et que l’une des machines virtuelles principales participantes essaie d’y accéder, cela entraîne une défaillance. Ce scénario n’est pas pris en charge.
**Résolution** : évaluez Application Gateway ou d’autres proxys (par exemple, nginx ou haproxy) pour prendre en charge ce type de scénario. Pour plus d’informations sur Application Gateway, consultez la page [Vue d’ensemble de la passerelle Application Gateway](../application-gateway/application-gateway-introduction.md).

## <a name="additional-network-captures"></a>Captures de réseau supplémentaires
Si vous décidez d’ouvrir un dossier de support, collectez les informations suivantes pour accélérer la résolution du problème. Choisissez une machine virtuelle principale unique pour effectuer les tests suivants :
- Utilisez Psping à partir d’une des machines virtuelles principales dans le réseau virtuel pour tester la réponse du port de la sonde (exemple : psping 10.0.0.4:3389) et enregistrez les résultats. 
- Utilisez TCPing à partir d’une des machines virtuelles principales dans le réseau virtuel pour tester la réponse du port de la sonde (exemple : tcping 10.0.0.4:3389) et enregistrez les résultats.
- Si aucune réponse n’est reçue dans ces tests Ping, exécutez une trace Netsh simultanée sur la machine virtuelle principale et la machine virtuelle de test du réseau virtuel tout en exécutant PsPing, puis arrêtez la trace Netsh. 
  
## <a name="next-steps"></a>Étapes suivantes

Si les étapes précédentes ne vous permettent pas de résoudre le problème, ouvrez un [ticket d’incident](https://azure.microsoft.com/support/options/).

