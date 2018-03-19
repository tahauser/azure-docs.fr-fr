---
title: Restauration automatique dans Azure Site Recovery | Microsoft Docs
description: "Présente le service Azure Site Recovery et récapitule les différents scénarios de déploiement associés."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: overview
ms.date: 03/08/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 57b191a5dbdea487a0105c6af2866cdc037864e2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="about-site-recovery"></a>À propos de Site Recovery

Bienvenue dans le service Azure Site Recovery. Cet article propose une vue d’ensemble rapide du service.

En tant qu’organisation, vous devez adopter une stratégie de récupération d’urgence et de continuité de l’activité (BCDR) qui assure la sécurité de vos données et l’exécution de vos applications et charges de travail lorsque des interruptions planifiées et non planifiées se produisent.

Azure Recovery Services contribue à votre stratégie BCDR :

- **Service Site Recovery** : Site Recovery permet d’assurer la continuité de l’activité en maintenant l’exécution des charges de travail et applications métier lors des interruptions. Site Recovery réplique les charges de travail s’exécutant sur des machines virtuelles et physiques depuis un site principal vers un emplacement secondaire. En cas d’interruption au niveau de votre site principal, vous basculez vers l’emplacement secondaire, depuis lequel vous pouvez accéder aux applications. Quand l’emplacement principal est de nouveau fonctionnel, vous pouvez effectuer une restauration automatique vers celui-ci.  
- **Service Sauvegarde** : le service [Sauvegarde Azure](https://docs.microsoft.com/azure/backup/) assure la sécurité de vos données. En sauvegardant ces données sur Azure, elles sont récupérables.

Site Recovery peut gérer la réplication pour :

- Les machines virtuelles Azure qui répliquent des données entre des régions Azure.
- Les machines virtuelles et les serveurs physiques locaux qui répliquent des données sur Azure ou sur un site secondaire.


## <a name="what-does-site-recovery-provide"></a>À quoi sert Site Recovery ?


**Fonctionnalité** | **Détails**
--- | ---
**Solution BCDR simple** | Grâce à Site Recovery, vous pouvez configurer et gérer la réplication, le basculement et la restauration automatique à partir d’un seul emplacement dans le portail Azure.
**Réplication de machines virtuelles Azure** | Vous pouvez configurer la récupération d’urgence de machines virtuelles Azure d’une région primaire vers une région secondaire.
**Réplication de machines virtuelles locales** | Vous pouvez répliquer des machines virtuelles et des serveurs physiques locaux sur Azure ou vers un centre de données local secondaire. La réplication sur Azure réduit le coût et la complexité qu’implique la maintenance d’un centre de données secondaire.
**Réplication de charges de travail** | Répliquez n’importe quelle charge de travail en cours d’exécution sur des machines virtuelles Azure, des machines virtuelles Hyper-V locales et VMware, et des serveurs physiques Windows/Linux pris en charge.
**Résilience des données** | Site Recovery orchestre la réplication sans intercepter les données d’une application. Lorsque vous effectuez une réplication vers Azure, les données sont stockées dans le stockage Azure, avec la résilience que cela implique. Lors du basculement, les machines virtuelles Azure sont créées à partir des données répliquées.
**Cibles RTO et RPO** | Maintenez les objectifs de délai de récupération (RTO) et les objectifs de point de récupération (RPO) au sein des limites fixées par l’organisation. Site Recovery fournit une réplication continue pour les machines virtuelles Azure et VMware. Les machines virtuelles Hyper-V bénéficient d’une fréquence de réplication de 30 secondes seulement. Vous pouvez réduire davantage les RTO en les intégrant à [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Maintenir la cohérence des applications en cas de basculement** | Vous pouvez effectuer une réplication à l’aide des points de récupération avec des captures instantanées cohérentes au niveau des applications. Ces captures récupèrent les données des disques, l’ensemble des données en mémoire et toutes les transactions en cours de traitement.
**Tests sans interruption** | Vous pouvez facilement exécuter la marche à suivre en cas de récupération d’urgence et ce, sans affecter une réplication en cours.
**Basculements flexibles** | Vous pouvez exécuter des basculements planifiés pour les interruptions attendues, sans perte de données, ou des basculements non planifiés avec une perte de données minimale (en fonction de la fréquence de réplication) pour les incidents inattendus. Vous pouvez effectuer en toute simplicité une restauration automatique vers votre site principal dès qu’il est à nouveau disponible.
**Plans de récupération personnalisés** | À l’aide de plans de récupération, vous pouvez personnaliser et séquencer le basculement et la récupération d’applications multiniveaux s’exécutant sur plusieurs machines virtuelles. Vous regroupez des machines dans un plan de récupération et ajoutez éventuellement des scripts et des actions manuelles. Les plans de récupération peuvent être intégrés à des runbooks Azure Automation.
**Intégration BCDR** | Site Recovery s’intègre à d’autres technologies BCDR. Par exemple, vous pouvez utiliser Site Recovery pour protéger le serveur principal SQL Server de charges de travail d’entreprise, avec la prise en charge native de SQL Server AlwaysOn pour gérer le basculement des groupes de disponibilité.
**Intégration Azure Automation** | La bibliothèque Azure Automation diversifiée fournit des scripts spécifiques à l’application prêts pour la production, qui peuvent être téléchargés et intégrés au service Site Recovery.
**Intégration réseau** | Site Recovery s’intègre à Azure pour permettre une gestion simple du réseau d’application, y compris la réservation d’adresses IP, la configuration des équilibrages de charges et l’intégration d’Azure Traffic Manager pour des commutations réseau efficaces.


## <a name="what-can-i-replicate"></a>Que puis-je répliquer ?

**Pris en charge** | **Détails**
--- | ---
**Scénarios de réplication** | Répliquez des machines virtuelles Azure d’une région Azure vers une autre.<br/><br/>  Répliquez des machines virtuelles VMware locales, des machines virtuelles Hyper-V et des serveurs physiques (Windows et Linux) vers Azure.<br/><br/> Répliquer des machines virtuelles VMware locales, des machines virtuelles Hyper-V gérées par System Center VMM et des serveurs physiques vers un site secondaire.
**Régions** | Consultez les [régions prises en charge](https://azure.microsoft.com/regions/services/) pour Site Recovery. |
**Machines répliquées** | Passez en revue la configuration requise pour la réplication de [machines virtuelles Azure](azure-to-azure-support-matrix.md#support-for-replicated-machine-os-versions), de [machines virtuelles locales VMware et de serveurs physiques](vmware-physical-azure-support-matrix.md#replicated-machines),et de [machines virtuelles locales Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms).
**Hôtes/serveurs VMware** | Les machines virtuelles VMware que vous souhaitez répliquer peuvent se trouver sur les [hôtes et serveurs de virtualisation pris en charge](vmware-physical-azure-support-matrix.md).
**Charges de travail** | Vous pouvez répliquer toutes les charges de travail qui s’exécutent sur une machine prenant en charge la réplication. En outre, l’équipe de Site Recovery a effectué des tests pour un [certain nombre d’applications](site-recovery-workload.md#workload-summary).



## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [prise en charge de la charge de travail](site-recovery-workload.md).
* Prise en main de [la réplication d’une machine virtuelle Azure entre des régions](azure-to-azure-quickstart.md). 
