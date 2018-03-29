---
title: Configurer Load Balancer pour SQL Server Always On | Microsoft Docs
description: Configurer Load Balancer pour fonctionner avec SQL Server Always On et découvrir comment utiliser PowerShell pour créer un équilibreur de charge pour l’implémentation de SQL Server
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: a0c2345b47b9103ac6a7ae998f13a12332e3907e
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>Configurer un équilibreur de charge pour SQL Server Always On



Les groupes de disponibilité SQL Server Always On peuvent maintenant s’exécuter avec un équilibreur de charge interne. Un groupe de disponibilité constitue la solution phare de SQL Server pour bénéficier d’une haute disponibilité et de la récupération d’urgence. L’écouteur du groupe de disponibilité permet aux applications clientes de se connecter de manière fluide au réplica principal, quel que soit le nombre de réplicas dans la configuration.

Le nom d’écouteur (DNS) est mappé à une adresse IP à charge équilibrée. Azure Load Balancer dirige le trafic entrant uniquement vers le serveur principal dans le jeu de réplicas.

Vous pouvez utiliser le support de l’équilibreur de charge interne pour les points de terminaison de SQL Server Always On (écouteur). Vous contrôlez désormais l’accessibilité de l’écouteur. Vous pouvez choisir l’adresse IP à charge équilibrée à partir d’un sous-réseau spécifique dans votre réseau virtuel.

À l’aide de l’équilibreur de charge interne sur l’écouteur, le point de terminaison SQL Server (par exemple, Server=tcp:ListenerName,1433;Database=DatabaseName) est accessible uniquement par les éléments suivants :

* Services et machines virtuelles dans le même réseau virtuel
* Services et machines virtuelles de réseaux locaux connectés
* Services et machines virtuelles de réseaux virtuels interconnectés

![Équilibreur de charge interne pour SQL Server Always On](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>Ajouter un équilibreur de charge interne au service

1. Dans l’exemple suivant, configurez un réseau virtuel contenant un sous-réseau nommé « Subnet-1 » :

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Ajoutez des points de terminaison à charge équilibrée pour un équilibreur de charge interne sur chaque machine virtuelle.

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    Dans l’exemple précédent, vous avez deux machines virtuelles appelées « sqlsvc1 » et « sqlsvc2 » qui s’exécutent dans le service cloud « Sqlsvc ». Après avoir créé l’équilibreur de charge interne avec le commutateur `DirectServerReturn`, ajoutez des points de terminaison à charge équilibrée à l’équilibreur de charge interne. Les points de terminaison à charge équilibrée permettent à SQL Server de configurer les écouteurs pour les groupes de disponibilité.

Pour plus d’informations sur SQL Server Always On, consultez [Configurer un équilibreur de charge interne pour un groupe de disponibilité Always On dans Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Voir aussi
* [Bien démarrer avec la configuration d’un équilibreur de charge interne](load-balancer-get-started-internet-arm-ps.md)
* [Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)
* [Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)
* [Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
