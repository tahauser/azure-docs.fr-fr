---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1f4bd0d81d16997a9d627f918d77cb8e770a5b53
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Comprendre les redémarrages des machines virtuelles : maintenance et temps d’arrêt
Il existe trois scénarios pouvant affecter une machine virtuelle dans Azure : maintenance matérielle non planifiée, temps d’arrêt imprévu et maintenance planifiée.

* Les **événements de maintenance matérielle non planifiée** surviennent lorsque la plateforme Azure prédit qu’une défaillance du matériel ou d’un composant de la plateforme, associé à une machine physique, est sur le point de se produire. Lorsque la plateforme prédit une défaillance, elle génère un événement de maintenance matérielle non prévue afin de réduire l’impact sur les machines virtuelles hébergées sur ce matériel. Azure utilise la technologie de Migration dynamique pour migrer les machines virtuelles depuis le matériel défectueux vers une machine physique intègre. La migration dynamique est une opération de préservation des machines virtuelles qui interrompt la machine virtuelle pendant une courte période. La mémoire, les fichiers ouverts et les connexions réseau sont conservés, mais les performances peuvent être réduites avant et/ou après l’événement. Dans les cas où la migration dynamique ne peut pas être utilisée, la machine virtuelle subit des temps d’arrêt imprévus, comme décrit ci-dessous.


* Les **temps d’arrêt imprévus** surviennent rarement lorsque l’infrastructure physique ou matérielle sous-jacente de votre machine virtuelle est victime d’une défaillance. Cela comprend les défaillances du réseau local, du disque local ou au niveau du rack. Lorsqu’une défaillance de ce type est détectée, la plateforme Azure migre automatiquement (répare) votre machine virtuelle vers une machine physique saine dans le même centre de données. Lors de la procédure de réparation, les machines virtuelles subissent des temps d’arrêt (redémarrage) et, dans certains cas, une perte du lecteur temporaire. Le système d’exploitation attaché et les disques de données sont toujours conservés. 

  Les machines virtuelles peuvent éventuellement subir un temps d’arrêt en cas de panne ou de catastrophe qui affectent l’ensemble d’un centre de données ou d’une région. Dans ce genre de scénarios, Azure offre des options de protection pour les [zones de disponibilité](../articles/availability-zones/az-overview.md) et les [régions jumelées](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* Les **événements de maintenance planifiés** sont des mises à jour périodiques effectuées par Microsoft sur la plateforme sous-jacente Azure afin d’améliorer la fiabilité, les performances et la sécurité de l’infrastructure de la plateforme hébergeant vos machines virtuelles. La plupart de ces mises à jour se déroulent sans aucune incidence sur les machines virtuelles ou les services cloud (voir [Maintenance de conservation des machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). La plateforme Azure s’efforce d’utiliser la maintenance de conservation des machines virtuelles lorsque cela est possible. Toutefois, dans quelques rares cas, ces mises à jour nécessitent le redémarrage de votre machine virtuelle afin d’appliquer les mises à jour requises sur l’infrastructure sous-jacente. Dans ce cas, vous pouvez effectuer la maintenance planifiée Azure via l’opération de maintenance-redéploiement, en lançant la maintenance pour les machines virtuelles dans la période de temps adaptée. Pour plus d’informations, consultez [Maintenance planifiée pour les machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Pour réduire l'effet des interruptions de service dues à un ou plusieurs de ces événements, nous vous recommandons les meilleures pratiques suivantes pour la haute disponibilité de vos machines virtuelles :

* [Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance]
* [Utilisation de disques gérés pour les machines virtuelles dans le groupe à haute disponibilité]
* [Utilisation d’événements planifiés pour répondre de façon proactive aux événements qui impactent les machines virtuelles] (https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events))
* [Configuration de chaque couche application dans des groupes à haute disponibilité séparés]
* [Combinaison de l’équilibrage de charge et des groupes à haute disponibilité]
* [Utiliser les zones de disponibilité pour se protéger contre les défaillances au niveau du centre de données]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance
Pour assurer la redondance de votre application, nous vous recommandons de regrouper au moins deux machines virtuelles dans un groupe à haute disponibilité. Cette configuration au sein d’un centre de données assure la disponibilité d’au moins une des machines virtuelles pendant un événement de maintenance planifié ou non, avec le niveau de 99,95 % stipulé dans le contrat de niveau de service (SLA) Azure. Pour plus d’informations, consultez le [SLA pour Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Évitez de laisser une instance unique de machine virtuelle dans un groupe à haute disponibilité. Avec cette configuration, les machines virtuelles ne sont pas sous la garantie du SLA et connaissent des interruptions de service au cours des événements de maintenance planifiés, sauf lorsqu’une machine virtuelle unique utilise le [stockage Premium Azure](../articles/virtual-machines/windows/premium-storage.md). Pour les machines virtuelles uniques utilisant le stockage Premium, le SLA Azure s’applique.

Chaque machine virtuelle de votre groupe à haute disponibilité se voit attribuer un **domaine de mise à jour** et un **domaine d’erreur** par la plateforme Azure sous-jacente. Pour un groupe à haute disponibilité donné, cinq domaines de mise à jour non configurables par l’utilisateur sont affectés par défaut (les déploiements Resource Manager peuvent ensuite être étendus à 20 domaines de mise à jour) pour indiquer les groupes de machines virtuelles et les équipements physiques sous-jacents qui peuvent être redémarrés simultanément. Dans le cas où un seul groupe à haute disponibilité comprend plus de cinq machines virtuelles, la sixième machine est placée dans le même domaine de mise à jour que la première, la septième dans le même que la deuxième, etc. Le redémarrage des domaines de mise à jour peut ne pas suivre un ordre séquentiel au cours de la maintenance planifiée, mais un seul domaine de mise à jour peut être redémarré à la fois. Un domaine de mise à jour redémarré bénéficie de 30 minutes pour récupérer avant que la maintenance ne soit lancée sur un autre domaine de mise à jour.

Les domaines d’erreur définissent le groupe de machines virtuelles partageant une source d’alimentation et un commutateur réseau communs. Par défaut, les machines virtuelles configurées dans votre groupe à haute disponibilité sont réparties entre trois domaines d’erreur au maximum pour les déploiements Resource Manager (deux domaines d’erreur pour les déploiements Classic). Le fait de placer vos machines virtuelles dans un groupe à haute disponibilité ne protège pas vos applications des défaillances du système d’exploitation ou propres aux applications, mais limite l’effet des défaillances des équipements physiques, des pannes du serveur et des coupures d’électricité.

<!--Image reference-->
   ![Schéma conceptuel de la configuration du domaine de mise à jour et du domaine d’erreur](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Utilisation de disques gérés pour les machines virtuelles dans le groupe à haute disponibilité
Si vous utilisez actuellement des machines virtuelles avec des disques non gérés, nous vous recommandons fortement de [convertir les machines virtuelles du groupe à haute disponibilité pour utiliser les disques gérés](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

Les [disques gérés](../articles/virtual-machines/windows/managed-disks-overview.md) augmentent la fiabilité des groupes à haute disponibilité en garantissant que les disques des machines virtuelles d’un groupe à haute disponibilité sont suffisamment isolés les uns des autres, ceci pour éviter les points de défaillance uniques. Ceci se fait en plaçant automatiquement les disques dans des domaines d’erreur de stockage différents (clusters de stockage) et en les alignant avec le domaine d’erreur de machine virtuelle. Si un domaine d’erreur de stockage est mis en échec en raison d’une défaillance matérielle ou logicielle, seule l’instance de machine virtuelle possédant des disques sur le domaine d’erreur de stockage est mise en échec.
![Domaines d’erreurs de disques managés](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Le nombre de domaines d’erreur des groupes à haute disponibilité gérés varie selon la région : de deux à trois par région. Le tableau suivant présente le nombre de domaines d’erreur par région

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

Si vous prévoyez d’utiliser des machines virtuelles avec des [disques non gérés](../articles/virtual-machines/windows/about-disks-and-vhds.md#types-of-disks), suivez les meilleures pratiques ci-dessous pour les comptes de stockage sur lesquels les disques durs virtuels (VHD) d’ordinateurs virtuels sont stockés en tant [qu’objets blob de pages](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Conservez tous les disques (système d’exploitation et données) associés à une machine virtuelle dans le même compte de stockage.**
2. **Examinez les [limites](../articles/storage/common/storage-scalability-targets.md) sur le nombre de disques non gérés dans un compte de stockage** avant d’ajouter plus de disques durs virtuels à un compte de stockage.
3. **Utilisez un compte de stockage distinct pour chaque machine virtuelle d’un groupe à haute disponibilité.** Ne partagez pas de comptes de stockage avec plusieurs machines virtuelles d’un même groupe à haute disponibilité. Il est acceptable pour les machines virtuelles de différents groupes à haute disponibilité de partager des comptes de stockage si les meilleures pratiques ci-dessus sont suivies ![FDs de disques non gérés](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Configuration de chaque couche application dans des groupes à haute disponibilité séparés
Si vos machines virtuelles sont presque identiques et ont la même fonction au sein de votre application, nous vous recommandons de configurer un groupe à haute disponibilité pour chaque couche de votre application.  Si vous placez deux couches différentes dans le même groupe à haute disponibilité, toutes les machines virtuelles de la même couche application peuvent être redémarrées en même temps. En configurant deux machines virtuelles ou plus dans un groupe à haute disponibilité par couche, vous vous assurez qu’au moins une machine de chaque couche reste disponible.

Par exemple, vous pouvez rassembler dans un seul groupe à haute disponibilité toutes les machines virtuelles du composant frontal de votre application exécutant IIS, Apache ou Nginx. Assurez-vous que seules les machines virtuelles frontales sont placées dans le même groupe à haute disponibilité. Assurez-vous également que seules les machines virtuelles de la couche de données sont placées dans leur propre groupe à haute disponibilité, au même titre que vos machines virtuelles répliquées SQL Server ou vos machines MySQL.

<!--Image reference-->
   ![Couches de l'application](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Combinaison de l’équilibrage de charge et des groupes à haute disponibilité
Combinez [l’équilibrage de charge Azure](../articles/load-balancer/load-balancer-overview.md) avec un groupe à haute disponibilité pour une meilleure résilience de votre application. L'équilibrage de charge Azure répartit le trafic entre plusieurs machines virtuelles. L'équilibrage de charge Azure est compris pour nos machine virtuelles de niveau Standard. Certains niveaux de machines virtuelles n’intègrent pas Azure Load Balancer. Pour plus d’informations sur l’équilibrage de charge des machines virtuelles, consultez [Équilibrage de charge des machines virtuelles](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Si l’équilibrage de charge n’est pas configuré pour équilibrer le trafic entre plusieurs machines virtuelles, tout événement de maintenance planifié affecte l’unique machine virtuelle en charge du trafic, entraînant ainsi une interruption de votre couche Application. Placer plusieurs machines virtuelles de la même couche dans le même équilibrage de charge et groupe à haute disponibilité permet de toujours avoir au moins une instance disponible pour le trafic.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Utiliser les zones de disponibilité pour se protéger contre les défaillances au niveau du centre de données

Les [Zones de disponibilité](../articles/availability-zones/az-overview.md) (version préliminaire) sont une alternative aux groupes à haute disponibilité, et élargissent le niveau de contrôle dont vous disposez pour maintenir la disponibilité des applications et des données sur vos machines virtuelles. Une zone de disponibilité est une zone physiquement séparée au sein d’une région Azure. On distingue trois zones de disponibilité par région Azure prise en charge. Chaque zone dispose d’une source d’alimentation, d’un réseau et d’un refroidissement. Elles sont toutes séparées logiquement des autres zones de disponibilité de la même région Azure. En concevant vos solutions pour répliquer des machines virtuelles dans des zones, vous pouvez protéger vos applications et vos données de la perte d’un centre de données. Si une zone est compromise, les applications et données répliquées sont instantanément rendues disponibles dans une autre zone. 

![Zones de disponibilité](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

[!INCLUDE [availability-zones-preview-statement.md](availability-zones-preview-statement.md)]

Apprenez-en davantage sur le déploiement d’une machine virtuelle [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) dans une zone de disponibilité.


<!-- Link references -->
[Configuration de plusieurs machines virtuelles dans un groupe à haute disponibilité pour assurer la redondance]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configuration de chaque couche application dans des groupes à haute disponibilité séparés]: #configure-each-application-tier-into-separate-availability-sets
[Combinaison de l’équilibrage de charge et des groupes à haute disponibilité]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Utilisation de disques gérés pour les machines virtuelles dans le groupe à haute disponibilité]: #use-managed-disks-for-vms-in-an-availability-set
[Utiliser les zones de disponibilité pour se protéger contre les défaillances au niveau du centre de données]: #use-availability-zones-to-protect-from-datacenter-level-failures
