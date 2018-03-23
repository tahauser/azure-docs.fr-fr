---
title: Disponibilité de SAP HANA au sein d’une région Azure | Microsoft Docs
description: Opérations de SAP HANA sur les machines virtuelles Azure natives
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/5/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28fe9bc7c8cb1d59df404b7dd7429def54648a18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Disponibilité de SAP HANA au sein d’une région Azure
Dans cette section, plusieurs scénarios de disponibilité au sein d’une région Azure sont présentés. Azure dispose de nombreuses régions, éparpillées partout dans le monde. Pour voir la liste des régions Azure, consultez l’article [Régions Azure](https://azure.microsoft.com/regions/). Pour le déploiement de SAP HANA sur des machines virtuelles au sein d’une région Azure, Microsoft offre le déploiement d’une machine virtuelle unique avec une instance HANA. Pour une disponibilité accrue, vous pouvez aussi déployer deux machines virtuelles avec deux instances HANA au sein d’un [groupe à haute disponibilité Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) qui utilise la réplication de système HANA pour des raisons de disponibilité. Azure a une préversion publique de ses [Zones de disponibilité Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Nous ne parlerons pas tout de suite en détail de ces zones de disponibilité. Nous aborderons cela dit quelques notions générales sur les différences d’utilisation des groupes à haute disponibilité et des zones de disponibilité.

Quelle est la différence entre des groupes à haute disponibilité et les zones de disponibilité ? Pour les régions Azure dans lesquelles les zones de disponibilité seront offertes, elles disposent de plusieurs centres de données, indépendant en termes de source d’alimentation, de refroidissement et de réseau. La raison pour laquelle nous offrons différentes zones au sein d’une seule région Azure est de vous permettre de déployer des applications sur deux ou trois zones de disponibilité offertes. En imaginant que les problèmes d’alimentation et/ou de réseau n’affectent qu’une seule infrastructure de zone de disponibilité, le déploiement de votre application au sein d’une région Azure devrait toujours entièrement opérationnel. Avec peut-être quelques diminutions des fonctionnalités, car certaines machines virtuelles d’une zone pourraient être perdues. Mais celles des deux autres zones seraient toujours opérationnelles. 
 
Un groupe à haute disponibilité Azure est une fonctionnalité de regroupement logique que vous pouvez utiliser dans Azure pour vous assurer que les ressources de machine virtuelle que vous y incluez sont isolées les unes des autres lors de leur déploiement dans un centre de données Azure. Azure veille à ce que les machines virtuelles que vous placez dans un groupe à haute disponibilité s’exécutent sur plusieurs serveurs physiques, racks de calcul, unités de stockage et commutateurs réseau. D’autres documentations Azure y font référence en tant que placements dans différents [Mise à jour et domaines d’erreur](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). En règle générale, ces placements se trouvent dans un centre de données Azure. En imaginant que les problèmes d’alimentation et/ou de réseau affectent le centre de données que vous déployez, toutes vos capacités dans une région Azure seraient aussi affectées.

Le placement des centres de données représentant les zones de disponibilité Azure est un compromis entre une latence réseau entre des services déployés dans différentes zones acceptées par la plupart des applications, et une distance certaine entre les centres de données. Pour que les catastrophes naturelles n’affectent idéalement pas l’alimentation, le réseau et l’infrastructure de toutes vos zones de disponibilité dans cette région. Toutefois, comme nous l’avons déjà vu avec des catastrophes naturelles d’envergure, les zones de disponibilité peuvent ne pas être en mesure de fournir autant de disponibilité au sein d’une région que l’on souhaite. Souvenez-vous de l’ouragan Maria qui a frappé l’île de Puerto Rico le 20 août 2017. Il a causé un black-out presque total sur l’ensemble de l’île.   
  


## <a name="single-vm-scenario"></a>Scénario de machine virtuelle unique
Dans ce scénario, vous avez créé une machine virtuelle Azure pour une instance SAP HANA. Vous avez utilisé le stockage Premium Azure pour héberger le disque du système d’exploitation et tous les disques de données. Le contrat SLA de disponibilité de 99,9 % d’Azure et les contrats SLA d’autres composants Azure vous suffisent à réaliser vos contrats SLA de disponibilité pour vos clients. Dans ce scénario, vous n’avez pas besoin de tirer profit d’un groupe à haute disponibilité pour des machines virtuelles qui exécutent le gestionnaire de bases de données. Dans ce scénario, vous vous appuyez sur deux fonctionnalités :

- Redémarrage automatique de machine virtuelle Azure (aussi appelé Azure Service Healing)
- Redémarrage automatique SAP HANA

Le redémarrage automatique de machine virtuelle Azure, ou « Service Healing », est une fonctionnalité Azure qui fonctionne sur deux niveaux :

- L’hôte serveur Azure vérifie l’intégrité d’une machine virtuelle hébergée sur le serveur hôte
- Le contrôleur de structure Azure surveille l’intégrité et la disponibilité du serveur hôte

Pour chaque machine virtuelle hébergée sur un serveur hôte Azure, une fonctionnalité d’analyse de l’intégrité surveille l’intégrité de la ou des machines virtuelles hébergées. Si une machine virtuelle passe en état défaillant, un redémarrage de la machine virtuelle peut être initié par l’agent hôte Azure qui vérifie l’intégrité de la machine virtuelle. Le contrôleur de structure Azure vérifie l’intégrité de l’hôte en analysant plusieurs paramètres différents qui indiquent des problèmes avec le matériel hôte, mais il vérifie aussi l’accessibilité de l’hôte via le réseau. Une indication de problèmes avec l’hôte peut amener à prendre des mesures telles que :

- Redémarrer l’hôte et les machines virtuelles qu’il exécutait, dans le cas où l’hôte signale un état défaillant
- Redémarrer l’hôte et redémarrez les machines virtuelles qu’il hébergeait à la base sur un hôte intègre, dans le cas où l’hôte n’est pas dans un état intègre après redémarrage. Dans ce cas, l’hôte est marqué comme défaillant et n’est pas utilisé pour d’autres déploiements tant qu’il n’est pas réglé ou remplacé.
- Redémarrage immédiat des machines virtuelles sur un hôte intègre dans les cas où l’hôte défaillant rencontre des difficultés lors de son redémarrage. 

Avec la surveillance d’hôte et de machine virtuelle fournie par Azure, les machines virtuelles Azure qui subissent des problèmes d’hôte sont automatiquement redémarrées sur un hôte Azure intègre. 

La deuxième fonctionnalité sur laquelle vous pouvez vous appuyer dans un tel scénario est le redémarrage automatique du service HANA qui s’exécute dans une machine virtuelle après qu’elle ait été redémarrée. Le [service de redémarrage automatique HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) peut être configuré via les services de surveillance des différents services HANA.

Ce scénario de machine virtuelle unique peut être amélioré en ajoutant un nœud de basculement froid à une configuration SAP HANA. Ou un [hôte de basculement automatique](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html), comme il est appelé dans la documentation SAP HANA. Cette configuration est logique dans des situations de déploiements locaux, où le matériel de serveur est limité et où vous dédiez un seul nœud de serveur comme nœud d’hôte de basculement automatique pour un ensemble d’hôtes de production. Toutefois, pour des cas comme Azure, où l’infrastructure sous-jacente fournit un serveur cible intègre pour un redémarrage réussi d’une machine virtuelle, le déploiement du scénario d’hôte de basculement automatique SAP HANA n’est pas logique. 

Par conséquent, nous ne disposons d’aucune architecture de référence qui prévoit un nœud de veille pour l’hôte de basculement automatique SAP HANA. Cela vaut également pour les configurations de mise à l’échelle SAP HANA.


## <a name="availability-scenarios-involving-two-different-vms"></a>Scénarios de disponibilité impliquant deux machines virtuelles différentes
L’utilisation de deux machines virtuelles Azure au sein de vos groupes à haute disponibilité Azure vous permet d’augmenter le temps entre ces deux machines virtuelles, si elles sont placées dans un groupe à haute disponibilité Azure au sein d’une région Azure. Cette configuration de base ressemble aux visuels montrés ici : ![Deux machines virtuelles avec toutes les couches](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Afin d’illustrer les différents scénarios de disponibilité, quelques couches ci-dessus sont coupées et les visuels limités aux couches des machines virtuelles, des hôtes, des groupes à haute disponibilité et des régions Azure. Les réseaux virtuels Azure, les groupes de ressources et les abonnements n’ont aucune importance dans les scénarios décrits.

### <a name="replicating-backups-to-second-virtual-machine"></a>Réplication de sauvegardes vers une seconde machine virtuelle
L’un des configurations les plus rudimentaires consiste à envoyer des sauvegardes d’une machine virtuelle Azure à une autre, en particulier les sauvegardes de fichier journal. Vous pouvez choisir n’importe quel type de stockage Azure. Vous êtes chargé de scripter la copie des sauvegardes planifiées de la première machine virtuelle vers la seconde. S’il est nécessaire d’utiliser les instances de la seconde machine virtuelle, vous devez restaurer les sauvegardes différentielles/incrémentielles et de fichier journal complètes à l’endroit requis. L’architecture ressemble à : ![Deux machines virtuelles avec réplication de stockage](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Cette configuration n’est pas adaptée à des temps de RPO et de RTO. Les temps de RTO, plus particulièrement, souffriraient en raison du besoin de restauration complet pour terminer la base de données avec les sauvegardes copiées. Toutefois, cette configuration est utilisable pour récupérer d’une suppression de données involontaire sur les instances principales. Avec ce type de configuration, vous êtes en mesure de restaurer à un certain point dans le temps, d’extraire les données et d’importer les données supprimées dans votre instance principale en tout temps. Ainsi, il est peut-être logique d’utiliser une méthode de copie de sauvegarde de ce genre, avec d’autres fonctionnalités de haute disponibilité. Pendant la copie des sauvegardes, vous pourriez rencontrer une machine virtuelle plus petite que les instances SAP HANA principales exécutent. Mais n’oubliez pas que des plus petites machines virtuelles ont un nombre de disques durs joints plus réduit. Consultez [Tailles des machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) pour connaître les limites des types de machines virtuelles individuelles.

### <a name="using-sap-hana-system-replication-without-automatic-failover"></a>Utilisation de la réplication de système SAP HANA sans basculement automatique
Pour les scénarios suivants, vous allez utiliser la réplication de système SAP HANA. La documentation de SAP est consultable en commençant par l’articule [Réplication de système](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Entre deux machines virtuelles Azure dans une seule région Azure, il y a deux configurations différentes qui comportent quelques différences concernant l’objectif de délai de récupération. En général, les scénarios sans basculement automatique ne sont pas cohérents au sein d’une région Azure. Pour cause : dans la plupart des situations d’échec dans l’infrastructure Azure, Azure Service Healing va redémarrer la machine virtuelle principale sur un autre hôte. Il n’existe que quelques cas extrêmes où une configuration de ce genre peut être utile en termes de scénarios d’échec. Ou certains cas que vous, en tant que client, souhaitez réaliser, surtout lorsqu’il s’agit d’efficacité.

#### <a name="using-hana-system-replication-without-auto-failover-and-without-data-pre-load"></a>Utilisation de la réplication de système HANA sans basculement automatique ni données pré-chargées 
Il s’agit d’un scénario où vous utilisez la réplication de système SAP HANA dans le but de déplacer des données de façon asynchrone pour obtenir un objectif de point de récupération (RPO) égal à 0. D’un autre côté, vous disposez d’un objectif de délai de récupération (RTO) assez long pour ne pas avoir besoin de basculer ou de pré-charger les données dans le cache d’une instance HANA. Dans ce cas, vous avez la possibilité de diminuer les coûts dans votre configuration :

- Vous pouvez exécuter une autre instance SAP HANA dans la deuxième machine virtuelle, et qui utilise la majeure partie de la mémoire de la machine virtuelle. En général, il s’agit d’une instance qui pourrait être mise hors service en cas de basculement sur la deuxième machine virtuelle, afin que les données répliquées puissent être chargées dans le cache de l’instance HANA ciblée dans la deuxième machine virtuelle.
- Vous pouvez utiliser une taille de machine virtuelle réduite comme deuxième machine virtuelle. En cas de basculement, vous auriez un train d’avance sur le basculement manuel, où vous redimensionnerez la machine virtuelle pour qu’elle corresponde à la machine virtuelle source. Le scénario ressemble à ceci :

![Deux machines virtuelles avec réplication de stockage](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Même sans données pré-chargées dans la cible de réplication du système HANA, il vous faut au moins 64 Go de mémoire, et assez de mémoire pour conserver les données de stockage de lignes de l’instance cible.

#### <a name="using-hana-system-replication-without-auto-failover-and-with-data-pre-load"></a>Utilisation de la réplication de système HANA sans basculement automatique et avec des données pré-chargées
La différence avec le scénario précédent réside dans le fait que les données répliquées dans l’instance HANA de la deuxième machine virtuelle sont pré-chargées. Cela supprime les deux avantages dont vous pouvez profiter avec le scénario sans données pré-chargées. Dans ce cas, vous ne pouvez pas exécuter d’autres systèmes SAP HANA sur la deuxième machine virtuelle, ni utiliser une taille de machine virtuelle réduite. Par conséquent, cette configuration est rarement implémentée avec les clients.


### <a name="using-sap-hana-system-replication-with-automatic-failover"></a>Utilisation de la réplication de système SAP HANA avec basculement automatique

La configuration de disponibilité standard au sein d’une région (la plupart des clients implémentent avec SAP HANA) est une configuration dans laquelle les deux machines virtuelles Azure exécutées sur un serveur SLES Linux disposent d’un cluster de basculement défini. Le cluster Linux du serveur SLES est basé sur l’infrastructure [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker), conjointement avec un appareil [STONITH](http://linux-ha.org/wiki/STONITH). Côté SAP HANA, le mode de réplication utilisé est synchronisé et un basculement automatique est configuré. Dans la deuxième machine virtuelle, l’instance SAP HANA sert de nœud de secours et reçoit un flux synchrone d’enregistrements de modification en provenance de l’instance SAP HANA principale. Tandis que les transactions sont acceptées par l’application au nœud HANA principal, ce dernier attend la confirmation de réception de l’enregistrement de la part nœud HANA secondaire avant de confirmer la réception à l’application. SAP HANA dispose de deux modes de réplication synchrone différents. Pour en savoir plus et connaître les différences entre les deux modes de réplication synchrone, lisez l’article [Modes de réplication pour la réplication de système SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

La configuration générale ressemble à ceci :

![Deux machines virtuelles avec réplication de stockage et basculement](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Cette solution est choisie car elle vous permet d’obtenir un objectif de point de récupération égal à 0 et un objectif de délai de récupération extrêmement faible. Configurez la connectivité du client SAP HANA de façon à ce que les clients SAP HANA utilise l’adresse IP virtuelle pour se connecter à la configuration de réplication de système HANA. Cela évite de reconfigurer l’application en cas de basculement sur le nœud secondaire. Dans cette solution, les références SKU de machine virtuelle Azure pour le serveur principal ou secondaire doivent être identiques.  


## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin d’un guide pas à pas pour installer une telle configuration dans Azure, lisez les articles :

- [Installer la réplication de système SAP HANA sur des machines virtuelles Azure](sap-hana-high-availability.md)
- [Votre SAP sur Azure - Partie 4 - Haute disponibilité pour SAP HANA avec la réplication de système](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Si vous avez besoin d’en savoir plus sur la disponibilité SAP HANA dans l’ensemble des régions Azure, consultez :

- [Disponibilité de SAP HANA dans l’ensemble des régions Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

