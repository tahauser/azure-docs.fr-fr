---
title: Réseau virtuel pour services Azure | Microsoft Docs
description: Découvrez les avantages du déploiement de ressources sur un réseau virtuel. Les ressources situées sur les réseaux virtuels peuvent communiquer les unes avec les autres, ainsi qu’avec les ressources locales, sans impliquer de trafic via Internet.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 15a5ede13aebb039c1d17f118ee1acdcf9031d50
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="virtual-network-integration-for-azure-services"></a>Intégration d’un réseau virtuel pour les services Azure

L’intégration de services Azure dans un réseau virtuel Azure vous permet de bénéficier d’un accès privé à partir des instances d’un service déployé dans ce réseau.

Vous pouvez intégrer des services Azure dans votre réseau virtuel, grâce aux options suivantes :
- Déploiement direct d’instances dédiées du service au sein d’un réseau virtuel. Les instances dédiées de ces services sont accessibles de manière privée dans le réseau virtuel, et à partir des réseaux locaux.
- En étendant un réseau virtuel au service, à l’aide de points de terminaison de service. Les points de terminaison fournisseur permettent de sécuriser les ressources de service au sein du réseau virtuel.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Déployer des services Azure sur des réseaux virtuels

Vous pouvez communiquer avec la plupart des ressources Azure via Internet au moyen d’adresses IP publiques. Lorsque vous déployez des services Azure sur un [réseau virtuel](virtual-networks-overview.md), vous pouvez communiquer avec les ressources de service de manière privée, par le biais d’adresses IP privées.

![Services déployés sur un réseau virtuel](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Le déploiement de services au sein d’un réseau virtuel fournit les fonctionnalités suivantes :

- Les ressources situées sur le réseau virtuel peuvent communiquer entre elles de manière privée, par le biais d’adresses IP privées. Exemple : le transfert direct des données entre HDInsight et SQL Server sur une machine virtuelle, au sein d’un réseau virtuel.
- Les ressources locales peuvent accéder aux ressources d’un réseau virtuel à l’aide d’adresses IP privées par le biais d’une connexion [VPN site à site (passerelle VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) ou [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Les réseaux virtuels peuvent être [appairés](virtual-network-peering-overview.md) pour permettre aux ressources situées sur les réseaux virtuels de communiquer entre elles, par le biais d’adresses IP privées.
- Les instances de service situées dans un réseau virtuel sont entièrement gérées par le service Azure, pour surveiller l’intégrité des instances et fournir une mise à l’échelle adaptée en fonction de la charge.
- Les instances de service sont déployées dans un sous-réseau dédié d’un réseau virtuel. L’accès au réseau entrant ou sortant doit être ouvert par le biais de [groupes de sécurité réseau](security-overview.md#network-security-groups) du sous-réseau, selon les recommandations fournies par les services.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Services pouvant être déployés dans un réseau virtuel

Chaque service directement déployé sur le réseau virtuel a ses propres exigences concernant le routage et le type de trafic entrant et sortant des sous-réseaux. Pour plus d'informations, consultez les pages suivantes : 
 
- Machines virtuelles : [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service Fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Environnement App Service](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gestion des API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway (interne)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Moteur Azure Container Service](../container-service/container-service-intro.md?toc=%2fazure%2fvirtual-network%2ftoc.json): Azure Container Service crée un réseau virtuel par défaut. Vous pouvez créer un réseau virtuel personnalisé en vue de l’utiliser avec le [moteur Azure Container Service](https://github.com/Azure/acs-engine/tree/master/examples/vnet).
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) : réseau virtuel (Classic) uniquement
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [Cloud Services](https://msdn.microsoft.com/library/azure/jj156091) : réseau virtuel (Classic) uniquement

Vous pouvez déployer un [équilibreur de charge Azure interne](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour équilibrer la charge des ressources de la liste précédente. Dans certains cas, le service crée et déploie automatiquement un équilibreur de charge lorsque vous créez une ressource.

## <a name="service-endpoints-for-azure-services"></a>Points de terminaison de service pour les services Azure

Certains services Azure ne peuvent pas être déployés sur des réseaux virtuels. Vous pouvez restreindre l’accès aux ressources de service à certains sous-réseaux du réseau virtuel, en activant un point de terminaison de service de réseau virtuel. En savoir plus sur les [points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md).

Les points de terminaison fournisseur sont actuellement pris en charge par les services suivants : 
- **Stockage Azure** : [Sécurisation de comptes de stockage Azure sur des réseaux virtuels](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- **Microsoft Azure SQL Database** : [Sécurisation de Microsoft Azure SQL Database sur des réseaux virtuels](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Intégration d’un réseau virtuel à plusieurs services Azure

Vous pouvez déployer un service Azure sur un sous-réseau d’un réseau virtuel et sécuriser les ressources de service critiques sur ce sous-réseau. Par exemple, vous pouvez déployer HDInsight sur votre réseau virtuel et sécuriser un compte de stockage sur le sous-réseau HDInsight.





