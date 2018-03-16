---
title: "Conserver les adresses IP pour le basculement de machines virtuelles Azure vers une autre région Azure | Microsoft Docs"
description: "Décrit comment conserver les adresses IP pour les scénarios de basculement Azure vers Azure avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 15f87ba87d90cee765f52d3188796bc1ff7b8a35
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="ip-address-retention-for-azure-virtual-machine-failover"></a>Conservation des adresses IP pour le basculement de machines virtuelles Azure

Azure Site Recovery permet la récupération d’urgence de machines virtuelles Azure. En cas de basculement d’une région Azure vers une autre, les clients veulent généralement conserver leurs configurations IP. Par défaut, Site Recovery imite le réseau virtuel et la structure de sous-réseau sources durant la création de ces ressources dans la région cible. Pour les machines virtuelles Azure configurées avec des adresses IP statiques privées, Site Recovery permet également de tenter plus efficacement de configurer la même adresse IP privée sur la machine virtuelle cible, si cette adresse IP n’est pas déjà bloquée par une ressource Azure ou une machine virtuelle répliquée.

Pour les applications simples, la configuration par défaut ci-dessus est suffisante. Pour les applications d’entreprise plus complexes, les clients peuvent avoir à provisionner des ressources réseau supplémentaires pour garantir la connectivité avec les autres composants de leur infrastructure après le basculement. Cet article explique la configuration réseau requise pour le basculement de machines virtuelles Azure d’une région vers une autre, tout en conservant les adresses IP des machines virtuelles.

## <a name="azure-to-azure-connectivity"></a>Connectivité Azure vers Azure

Pour le premier scénario, prenons le cas d’une **société A** qui exécute l’ensemble de son infrastructure d’applications dans Azure. Pour des raisons de conformité et de continuité d’activité, la **société A** décide d’utiliser Azure Site Recovery pour protéger ses applications.

En raison de la nécessité de conserver les adresses IP (par exemple, pour les liaisons des applications), la société A dispose du même réseau virtuel et de la même structure de sous-réseau dans la région cible. Pour réduire davantage l’objectif de délai de récupération (RTO), la **société A** utilise des nœuds de réplica pour les contrôleurs de domaine, SQL Always ON, etc. Ces nœuds sont placés dans un autre réseau virtuel de la région cible. L’utilisation d’un espace d’adressage différent pour les nœuds de réplica permet à la **société A** d’établir une connectivité VPN de site à site entre les régions source et cible, ce qui serait autrement impossible si le même espace d’adressage était utilisé des deux côtés.

Voici à quoi ressemble l’architecture réseau avant le basculement :
- Les machines virtuelles des applications sont hébergées dans la région Azure East Asia (Asie de l’Est), et elles utilisent un réseau virtuel Azure avec l’espace d’adressage 10.1.0.0/16. Ce réseau virtuel est nommé **Source VNet**.
- Les charges de travail d’application sont réparties entre trois sous-réseaux (10.1.0.0/24, 10.1.1.0/24 et 10.1.2.0/24), nommés respectivement **Subnet 1**, **Subnet 2** et **Subnet 3**.
- Azure Southeast Asia (Asie du Sud-Est) est la région cible. Elle dispose d’un réseau virtuel de récupération qui imite la configuration de sous-réseau et de l’espace d’adressage de la source. Ce réseau virtuel est nommé **Recovery VNet**.
- Les nœuds de réplica (comme ceux nécessaires pour le contrôleur de domaine, Always On, etc.) sont placés dans un réseau virtuel avec l’espace d’adressage 20.1.0.0/16, dans le sous-réseau 4 (Subnet 4) avec l’adresse 20.1.0.0/24. Le réseau virtuel est nommé **Azure VNet** et se trouve dans la région Azure Southeast Asia.
- **Source VNet** et **Azure VNet** sont connectés via une connectivité VPN de site à site.
- **Recovery VNet** n’est connecté à aucun autre réseau virtuel.
- La **société A** affecte/vérifie une adresse IP cible pour les éléments répliqués. Dans cet exemple, l’adresse IP cible est identique à l’adresse IP source pour chaque machine virtuelle.

![Connectivité Azure vers Azure avant le basculement](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>Basculement d’une région entière

En cas de panne régionale, la **société A** peut rapidement et facilement récupérer l’intégralité de son déploiement à l’aide de [plans de récupération](site-recovery-create-recovery-plans.md) efficaces d’Azure Site Recovery. Ayant déjà défini l’adresse IP cible pour chaque machine virtuelle avant le basculement, la **société A** peut orchestrer le basculement et automatiser l’établissement de la connexion entre les réseaux virtuels Recovery VNet et Azure Vnet, comme le montre le diagramme ci-dessous.

![Connectivité Azure vers Azure - Basculement d’une région entière](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover.png)

Selon la configuration requise pour les applications, les connexions entre les deux réseaux virtuels dans la région cible peuvent être établies avant, pendant (en tant qu’étape intermédiaire) ou après le basculement. Utilisez des [plans de récupération](site-recovery-create-recovery-plans.md) pour ajouter des scripts et définir l’ordre de basculement.

La société A peut également choisir d’utiliser VNet Peering ou le réseau privé virtuel de site à site pour établir la connectivité entre Recovery VNet et Azure VNet. VNet Peering n’utilise pas une passerelle VPN et possède d’autres contraintes. En outre, la [tarification de VNet Peering](https://azure.microsoft.com/pricing/details/virtual-network) est différente de la [tarification de la passerelle VPN de réseau virtuel à réseau virtuel](https://azure.microsoft.com/pricing/details/vpn-gateway). Pour les basculements, il est généralement recommandé d’imiter la connectivité source, y compris le type de connexion, pour minimiser le risque d’incidents imprévisibles que peuvent entraîner des modifications du réseau.

### <a name="isolated-application-failover"></a>Basculement d’applications isolées

Dans certains cas, les utilisateurs peuvent avoir besoin de basculer uniquement des parties de leur infrastructure d’applications. Il peut s’agir, par exemple, du basculement d’une couche ou d’une application spécifique hébergée dans un sous-réseau dédié. Bien qu’un basculement de sous-réseau avec la conservation des adresses IP soit possible, cette méthode n’est pas recommandée dans la plupart des cas, car elle augmente considérablement les incohérences de connectivité. Vous perdrez également la connectivité de sous-réseau aux autres sous-réseaux du même réseau virtuel Azure.

Pour prendre en compte la configuration requise pour le basculement d’applications au niveau des sous-réseaux, nous vous recommandons plutôt d’utiliser différentes adresses IP cibles pour le basculement (si la connectivité est nécessaire pour d’autres sous-réseaux du réseau virtuel source) ou d’isoler chaque application dans son propre réseau virtuel dédié sur la source. Cette approche vous permet d’établir une connectivité inter-réseau sur la source et d’émuler la même durant le basculement vers la région cible.

Pour concevoir des applications individuelles garantissant la résilience, nous vous conseillons d’héberger une application dans son propre réseau virtuel dédié et d’établir la connectivité entre ces réseaux virtuels en fonction des besoins. Cela permet de basculer des applications isolées tout en conservant les adresses IP privées d’origine.

La configuration de prébasculement se présente alors comme suit :
- Les machines virtuelles des applications sont hébergées dans la région Azure East Asia, et elles utilisent un réseau virtuel Azure avec l’espace d’adressage 10.1.0.0/16 pour la première application et 15.1.0.0/16 pour la deuxième. Les réseaux virtuels sont nommés **Source VNet1** pour la première application, et **Source VNet2** pour la deuxième.
- Chaque réseau virtuel est divisé en deux sous-réseaux.
- Azure Southeast Asia est la région cible. Elle dispose des réseaux virtuels de récupération Recovery VNet1 et Recovery VNet2.
- Les nœuds de réplica (comme ceux nécessaires pour le contrôleur de domaine, Always On, etc.) sont placés dans un réseau virtuel avec l’espace d’adressage 20.1.0.0/16 dans le sous-réseau 4 **(Subnet 4)** avec l’adresse 20.1.0.0/24. Le réseau virtuel est nommé Azure VNet et se trouve dans la région Azure Southeast Asia.
- **Source VNet1** et **Azure VNet** sont connectés via une connectivité VPN de site à site. **Source VNet2** et **Azure VNet** sont également connectés via une connectivité VPN de site à site.
- **Source VNet1** et **Source VNet2** sont aussi connectés via VPN S2S dans cet exemple. Étant donné que les deux réseaux virtuels se trouvent dans la même région, VNet Peering peut également être utilisé à la place de VPN S2S.
- **Recovery VNet1** et **Recovery VNet2** ne sont connectés à aucun autre réseau virtuel.
- Pour réduire l’objectif de délai de récupération (RTO), les passerelles VPN sont configurées sur **Recovery VNet1** et **Recovery VNet2** avant le basculement.

![Connectivité Azure vers Azure d’une application isolée avant le basculement](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover.png)

En cas de situation d’urgence affectant une seule application (dans cet exemple, elle est hébergée dans Source VNet2), la société A peut récupérer l’application affectée comme suit :
- Les connexions VPN entre **Source VNet1** et **Source VNet2**, et entre **Source VNet2** et **Azure VNet**, sont déconnectées.
- Les connexions VPN sont établies entre **Source VNet1** et **Recovery VNet2**, et entre **Recovery VNet2** et **Azure VNet**.
- Les machines virtuelles de **Source VNet2** sont basculées vers **Recovery VNet2**.

![Connectivité Azure vers Azure d’une application isolée après le basculement](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover.png)

L’exemple de basculement isolé ci-dessus peut être étendu pour inclure d’autres applications et connexions réseau. Dans la mesure du possible, nous vous recommandons de suivre un modèle de connexion de type égal à égal pour le basculement de la source vers la cible.

### <a name="further-considerations"></a>Autres considérations

Les passerelles VPN utilisent des adresses IP publiques et des tronçons de passerelle pour établir les connexions. Si vous ne souhaitez pas utiliser d’adresses IP publiques et/ou préférez éviter tout tronçon supplémentaire, vous pouvez désormais utiliser Global VNet Peering pour l’homologation de réseaux virtuels entre les régions Azure.

Cette fonctionnalité est actuellement disponible en préversion publique et est en cours d’extension pour prendre en charge un plus grand nombre de régions, pour une connectivité de machine virtuelle à machine virtuelle directe sans passer par le réseau Internet public ni utiliser de tronçons supplémentaires.

Pour plus d’informations, reportez-vous à la [documentation sur l’homologation](../virtual-network/tutorial-connect-virtual-networks-portal.md#register) et aux informations relatives aux [prix](https://azure.microsoft.com/pricing/details/virtual-network/).

## <a name="on-premises-to-azure-connectivity"></a>Connectivité locale vers Azure

Pour le deuxième scénario, prenons le cas d’une **société B** qui exécute une partie de son infrastructure d’applications sur Azure, et le reste localement. Pour des raisons de conformité et de continuité d’activité, la **société B** décide d’utiliser Azure Site Recovery pour protéger ses applications exécutées dans Azure.

Voici à quoi ressemble l’architecture réseau avant le basculement :
- Les machines virtuelles des applications sont hébergées dans la région Azure East Asia (Asie de l’Est), et elles utilisent un réseau virtuel Azure avec l’espace d’adressage 10.1.0.0/16. Ce réseau virtuel est nommé **Source VNet**.
- Les charges de travail d’application sont réparties entre trois sous-réseaux (10.1.0.0/24, 10.1.1.0/24 et 10.1.2.0/24), nommés respectivement **Subnet 1**, **Subnet 2** et **Subnet 3**.
- Azure Southeast Asia (Asie du Sud-Est) est la région cible. Elle dispose d’un réseau virtuel de récupération qui imite la configuration de sous-réseau et de l’espace d’adressage de la source. Ce réseau virtuel est nommé **Recovery VNet**.
- Les machines virtuelles dans Azure East Asia sont connectées au centre de données local via ExpressRoute ou le réseau privé virtuel de site à site.
- Pour réduire l’objectif de délai de récupération (RTO), la société B provisionne des passerelles sur Recovery VNet dans Azure Southeast Asia avant le basculement.
- La **société B** affecte/vérifie une adresse IP cible pour les éléments répliqués. Dans cet exemple, l’adresse IP cible est identique à l’adresse IP source pour chaque machine virtuelle.

![Connectivité locale vers Azure avant le basculement](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>Basculement d’une région entière

En cas de panne régionale, la **société B** peut rapidement et facilement récupérer l’intégralité de son déploiement à l’aide de [plans de récupération](site-recovery-create-recovery-plans.md) efficaces d’Azure Site Recovery. Ayant déjà défini l’adresse IP cible pour chaque machine virtuelle avant le basculement, la **société B** peut orchestrer le basculement et automatiser l’établissement de la connexion entre le réseau virtuel Recovery VNet et le centre de données local, comme le montre le diagramme ci-dessous.

La connexion d’origine entre Azure East Asia et le centre de données local doit être déconnectée avant d’établir la connexion entre Azure Southeast Asia et le centre de données local. Le routage local est également reconfiguré pour pointer vers la région cible et les passerelles après le basculement.

![Connectivité locale vers Azure après le basculement](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover.png)

### <a name="subnet-failover"></a>Basculement de sous-réseau

À la différence du scénario Azure vers Azure décrit pour la **société A**, un basculement au niveau des sous-réseaux n’est pas possible dans ce cas pour la **société B**. Cette limitation est due au fait que l’espace d’adressage sur les réseaux virtuels source et de récupération est le même, et que la connexion « source à local » d’origine est active.

Pour garantir la résilience des applications, nous vous recommandons d’héberger chaque application dans son propre réseau virtuel Azure dédié. Les applications peuvent ensuite être basculées de manière isolée, et les connexions « local à source » nécessaires peuvent être acheminées vers la région cible, comme décrit ci-dessus.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez-en plus sur les [plans de récupération](site-recovery-create-recovery-plans.md).
