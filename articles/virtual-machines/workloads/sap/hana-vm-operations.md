---
title: "Opérations SAP HANA sur Azure | Microsoft Docs"
description: "Opérations de SAP HANA sur les machines virtuelles Azure natives"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0328bdc40429e1e82a76f290f5bde39089db0a9d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>Guide des opérations des SAP HANA sur Azure
Ce guide fournit des instructions pour l’exploitation des systèmes SAP HANA qui ont été déployés sur des machines virtuelles Azure. Ce document n’a pas pour but de remplacer la documentation SAP standard. Vous trouverez des guides et des notes SAP aux emplacements suivants :

- [SAP Administration Guide (Guide d’administration de SAP)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP Installation Guide (Guide d’installation de SAP)](https://service.sap.com/instguides)
- [SAP Note (Note relative à SAP)](https://sservice.sap.com/notes)

La condition préalable est que vous disposiez des connaissances de base quant aux différents composants Azure suivants :

- [Machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Mise en réseau et réseaux virtuels Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Stockage Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Une documentation supplémentaire sur SAP NetWeaver et les autres composants de SAP sur Azure est disponible dans la section [SAP sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) de la [documentation sur Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considérations de base relatives à la configuration
### <a name="connecting-into-azure"></a>Connexion à Azure
Comme décrit dans [Planification et implémentation de Machines virtuelles Azure pour SAP NetWeaver] [guide de planification], il existe deux méthodes de base pour se connecter à Machines virtuelles Azure. 

- Connexion par le biais de points de terminaison publics et Internet sur une machine virtuelle Jump ou la machine virtuelle qui exécute SAP HANA
- Connexion par le biais d’un [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou d’Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)

Pour les scénarios de production ou les scénarios dans lesquels des scénarios hors production alimentent des scénarios de production conjointement avec le logiciel SAP, vous devez avoir une connectivité de site à site par le biais d’un VPN ou d’ExpressRoute comme indiqué dans ce graphique :

![Connectivité intersite](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choice-of-azure-vm-types"></a>Choix des types de machines virtuelles Azure
Les types de machines virtuelles Azure qui peuvent être utilisés pour les scénarios de production sont répertoriés [ici](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Pour les scénarios hors production, vous pouvez choisir une plus large gamme de machines virtuelles Azure natives. Vous devez toutefois vous limiter aux types de machines virtuelles mentionnées dans la [Note SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533). Le déploiement de ces machines virtuelles dans Azure peut être effectué par le biais :

- Du portail Azure
- Des applets de commande Azure PowerShell
- D’Azure CLI

Vous pouvez également déployer une plateforme SAP HANA installée et complète sur les services de machine virtuelle Azure par le biais de la [plateforme SAP Cloud](https://cal.sap.com/) comme décrit [ici](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="choice-of-azure-storage"></a>Choix du stockage Azure
Azure fournit deux principaux types de stockage adaptés aux machines virtuelles Azure exécutant SAP HANA.

- [Stockage Standard Azure](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Stockage Premium Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure offre deux méthodes de déploiement avec les disques durs virtuels sur le stockage Azure Standard et Premium. Si le scénario global le permet, nous vous recommandons de tirer parti des déploiements des [disques managés Azure](https://azure.microsoft.com/services/managed-disks/).

Pour en savoir plus sur les types de stockage précis et les contrats SLA relatifs à ces types de stockage, consultez [cette documentation](https://azure.microsoft.com/pricing/details/managed-disks/)

Nous vous recommandons d’utiliser des disques Azure Premium pour les volumes /hana/data et /hana/log. La création d’un gestionnaire de volume logique RAID sur plusieurs disques de stockage Premium et l’utilisation de ces volumes RAID en tant que volumes /hana/data et /hana/log sont des opérations qui sont prises en charge.

Voici un exemple de configuration possible pour différents types de machines virtuelles courants que les clients utilisent jusqu’à présent pour l’hébergement de SAP HANA sur des machines virtuelles Azure :

| Référence de la machine virtuelle | RAM | /hana/data et /hana/log<br /> agrégés avec LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128 Go | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256 Go | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 Go | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 Go | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1 To | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1,7 To | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 To | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3,8 To | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="azure-networking"></a>Mise en réseau Azure
En supposant que vous disposez d’une connectivité de site à site VPN ou ExpressRoute dans Azure, vous avez au minimum un [réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) connecté par le biais d’une passerelle virtuelle au circuit ExpressRoute ou VPN. La passerelle virtuelle réside sur un sous-réseau du réseau virtuel Azure. Pour pouvoir installer HANA, vous devez créer deux autres sous-réseaux sur le réseau virtuel : un qui héberge les machines virtuelles exécutant les instances de SAP HANA, et un autre qui exécute l’éventuelle Jumpbox ou les machines virtuelles de gestion susceptibles d’héberger SAP HANA Studio ou d’autres logiciels de gestion.
Quand vous installez les machines virtuelles qui doivent exécuter HANA, celles-ci doivent avoir :

- Deux cartes réseau virtuelles installées, dont une qui se connecte au sous-réseau de gestion et une autre qui sert à se connecter à l’instance de SAP HANA dans la machine virtuelle Azure à partir du réseau local ou d’un autre réseau.
- Des adresses IP statiques privées déployées pour les deux cartes réseau virtuelles.

Une vue d’ensemble des différentes possibilités d’attribution des adresses IP est disponible [ici](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

L’acheminement du trafic directement vers l’instance de SAP HANA ou vers la jumpbox est effectué par des [groupes de sécurité réseau Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg) qui sont associés au sous-réseau HANA et au sous-réseau de gestion.

Globalement, le schéma de déploiement ressemble à ce qui suit :

![Schéma de déploiement approximatif pour SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Si vous déployez simplement SAP HANA dans Azure sans avoir de connexion de site à site (VPN ou ExpressRoute vers Azure), vous accédez à l’instance de SAP HANA par le biais d’une adresse IP publique affectée à la machine virtuelle Azure exécutant votre machine virtuelle Jumpbox. Dans le cas le plus simple, vous vous fiez également aux services DNS intégrés à Azure pour résoudre les noms d’hôtes. En particulier lors de l’utilisation d’adresses IP publiques, il est préférable d’utiliser des groupes de sécurité réseau Azure pour limiter les ports ouverts ou les plages d’adresses IP autorisées à se connecter aux sous-réseaux Azure qui exécutent des ressources avec des adresses IP publiques. Le schéma d’un tel déploiement pourrait ressembler à ce qui suit :
  
![Schéma de déploiement approximatif pour SAP HANA sans connexion de site à site](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations"></a>Opérations
### <a name="backup-and-restore-operations-on-azure-vms"></a>Opérations de sauvegarde et restauration sur Machines virtuelles Azure
Les capacités de SAP HANA en matière de sauvegarde et de restauration sont décrites dans ces documents :

- [Vue d’ensemble de la sauvegarde SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Sauvegarde SAP HANA au niveau des fichiers](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Sauvegarde SAP HANA à partir de captures instantanées de stockage](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)



### <a name="start-and-restart-of-vms-containing-sap-hana"></a>Démarrage et redémarrage des machines virtuelles contenant SAP HANA
L’une des forces du cloud public Azure est le fait que vous êtes facturé uniquement pour les minutes de calcul que vous dépensez. Cela signifie que si vous arrêtez une machine virtuelle sur laquelle SAP HANA s’exécute, seuls les coûts de stockage vous sont facturés pendant ce temps-là. Quand vous redémarrez la machine virtuelle avec SAP HANA, elle a les mêmes adresses IP (si vous l’avez déployée avec des adresses IP statiques). 


### <a name="saprouter-enabling-sap-remote-support"></a>Prise en charge à distance de SAP grâce à SAPRouter
Si vous avez une connexion de site à site entre vos emplacements locaux et Azure, et que vous exécutez déjà des composants SAP, il est très probable que vous exécutez déjà SAProuter. Dans ce cas, vous n’avez aucune opération à effectuer sur les instances SAP HANA que vous déployez dans Azure, hormis mettre à jour l’adresse IP privée et statique de la machine virtuelle qui héberge HANA dans la configuration de SAPRouter, et d’adapter le groupe de sécurité réseau du sous-réseau qui héberge la machine virtuelle HANA (autorisation du trafic sur le port TCP/IP 3299).

Si vous déployez SAP HANA, que vous vous connectez à Azure par le biais d’Internet et que vous n’avez pas installé de routeur SAP sur le réseau virtuel qui exécute une machine virtuelle avec SAP HANA, vous devez installer le SAPRouter sur une machine virtuelle distincte sur le sous-réseau de gestion, comme illustré ici :


![Schéma de déploiement approximatif pour SAP HANA sans connexion de site à site et SAPRouter](media/hana-vm-operations/hana-simple-networking3.PNG)

Vous devez installer SAPRouter sur une machine virtuelle distincte, et non sur votre machine virtuelle Jumpbox. La machine virtuelle distincte a besoin d’une adresse IP statique. Pour pouvoir connecter SAPRouter au SAPRouter hébergé par SAP (équivalent de l’instance de SAPRouter que vous installez sur la machine virtuelle), vous devez contacter SAP pour obtenir une adresse IP que vous configurerez sur votre instance de SAPRouter. Le seul port nécessaire est le port TCP 3299.
Pour plus d’informations sur la façon de configurer et de gérer des connexions de support à distance par le biais de SAPRouter, consultez cette [source SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Haute disponibilité avec SAP HANA sur les machines virtuelles Azure natives
Si vous exécutez SUSE Linux 12 SP1 ou une version plus récente, vous pouvez établir un cluster Pacemaker avec des appareils STONITH afin de définir une configuration SAP HANA qui utilise la réplication synchrone avec la réplication de système HANA et le basculement automatique. La procédure de configuration est décrite dans l’article [Haute disponibilité de SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

 










