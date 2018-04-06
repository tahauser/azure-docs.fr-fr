---
title: Configurer l’adressage IP pour se connecter après le basculement vers Azure avec Azure Site Recovery | Microsoft Docs
description: Décrit comment configurer l’adressage IP pour se connecter à des machines virtuelles Azure après le basculement depuis un site local avec Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/24/2018
ms.author: manayar
ms.openlocfilehash: e74ad30ea8c4be994c1c67735ee91ef8c033b9b3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="set-up-ip-addressing-to-connect-after-failover-to-azure"></a>Configurer l’adressage IP pour se connecter après le basculement vers Azure

Cet article explique la configuration réseau requise pour se connecter aux machines virtuelles Azure après avoir utilisé le service [Azure Site Recovery](site-recovery-overview.md) pour la réplication et le basculement vers Azure.

Cet article porte sur les points suivants :

> [!div class="checklist"]
> * Les méthodes de connexion que vous pouvez utiliser
> * L’utilisation d’une adresse IP différente pour les machines virtuelles Azure répliquées
> * La conservation des adresses IP pour les machines virtuelles Azure après le basculement

## <a name="connecting-to-replica-vms"></a>Connexion aux machines virtuelles de réplication

Lorsque vous planifiez votre stratégie de basculement et de réplication, l’une des questions clés est de savoir comment se connecter à la machine virtuelle Azure après le basculement. Deux options s’offrent à vous lors de la conception de votre stratégie réseau pour les machines virtuelles Azure réplica :

- **Utiliser une adresse IP différente** : vous pouvez choisir d’utiliser une autre plage d’adresses IP pour le réseau de la machine virtuelle Azure répliquée. Dans ce scénario la machine virtuelle reçoit une nouvelle adresse IP après le basculement, et une mise à jour DNS est nécessaire.
- **Conserver la même adresse IP** : vous souhaiterez peut-être utiliser la même plage d’adresses IP que celle du site principal local pour le réseau Azure après le basculement. La conservation des mêmes adresses IP simplifie la récupération en réduisant les problèmes liés au réseau après le basculement. Toutefois, lorsque vous effectuez une réplication vers Azure, vous devez mettre à jour des itinéraires avec le nouvel emplacement des adresses IP après le basculement.

## <a name="retaining-ip-addresses"></a>Conservation des adresses IP

Site Recovery offre la possibilité de conserver des adresses IP fixes lors du basculement vers Azure, grâce à un basculement de sous-réseau.

- Avec le basculement de sous-réseau, un sous-réseau spécifique est présent sur le Site 1 ou sur le Site 2, mais jamais sur les deux sites simultanément.
- Pour conserver l’espace d’adressage IP en cas de basculement, vous pouvez mettre en place un programme pour que l’infrastructure du routeur déplace les sous-réseaux d’un site à un autre.
- Pendant le basculement, les sous-réseaux se déplacent avec les machines virtuelles protégées associées. Le principal inconvénient est qu’en cas de défaillance, vous devez déplacer le sous-réseau tout entier.


### <a name="failover-example"></a>Exemple de basculement

Examinons un exemple de basculement vers Azure à l’aide d’une société fictive, Woodgrove Bank.

- Woodgrove Bank héberge ses applications métier sur un site local. Elle héberge ses applications mobiles sur Azure.
- Il existe une connectivité de site à site VPN entre le réseau de périmètre local et le réseau virtuel Azure. En raison de la connexion VPN, le réseau virtuel dans Azure s’affiche en tant qu’extension du réseau local.
- Woodgrove souhaite répliquer les charges de travail locales vers Azure à l’aide de Site Recovery.
 - Comme certaines applications dépendent d’adresses IP codées en dur, Woodgrove doit conserver les adresses IP des applications après le basculement vers Azure.
 - Les ressources qui s’exécutent dans Azure utilisent la plage d’adresses IP 172.16.1.0/24 - 172.16.2.0/24.

![Avant le basculement de sous-réseau](./media/site-recovery-network-design/network-design7.png)

**Infrastructure avant le basculement**


Pour pouvoir répliquer ses machines virtuelles vers Azure en conservant les adresses IP, voici ce que Woodgrove doit faire :


1. Créer un réseau virtuel Azure dans lequel les machines virtuelles Azure sont créées après le basculement des machines locales. Il doit s’agir d’une extension du réseau local afin que les applications puissent basculer en toute transparence.
2. Avant le basculement, dans Site Recovery, attribuer la même adresse IP dans les propriétés de la machine. Après le basculement, Site Recovery affecte cette adresse à la machine virtuelle Azure.
3. Une fois le basculement exécuté et les machines virtuelles Azure créées avec la même adresse IP, se connecter au réseau en utilisant une [connexion de réseau virtuel à réseau virtuel](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Cette action peut être scriptée.
4. Modifier les itinéraires, afin de refléter le fait que 192.168.1.0/24 a été déplacé vers Azure.


**Infrastructure après le basculement**

![Après le basculement de sous-réseau](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Connexion de site à site

Outre la connexion de réseau virtuel à réseau virtuel, après le basculement, Woodgrove peut configurer la connectivité VPN de site à site :
- Quand vous configurez une connexion de site à site, dans le réseau Azure, vous pouvez uniquement acheminer le trafic vers l’emplacement local (local-ntwork) si la plage d’adresses IP est différente de la plage d’adresses IP locales. Cela est dû au fait qu’Azure ne prend pas en charge les sous-réseaux étirés. Si vous avez un sous-réseau local 192.168.1.0/24, vous ne pouvez donc pas ajouter un réseau local 192.168.1.0/24 dans le réseau Azure. Ceci est dû au fait que Azure ne sait pas qu’aucune machine virtuelle n’est active dans le sous-réseau et que le sous-réseau est créé uniquement à des fins de récupération d’urgence.
- Pour pouvoir acheminer correctement le trafic réseau à partir d’un réseau Azure, les sous-réseaux du réseau et le réseau local ne doivent pas entrer en conflit.




## <a name="assigning-new-ip-addresses"></a>Affectation de nouvelles adresses IP

Cet [article de blog](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) explique comment configurer le service d’infrastructure réseau Azure lorsqu’il n’est pas nécessaire de conserver les adresses IP après le basculement. Il commence par une description de l’application, aborde la procédure de configuration des services de mise en réseau locaux et se conclut par des informations sur l’exécution des basculements.

## <a name="next-steps"></a>Étapes suivantes
[Exécuter un basculement](site-recovery-failover.md)
