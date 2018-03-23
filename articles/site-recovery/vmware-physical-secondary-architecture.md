---
title: Architecture de réplication d’un serveur physique/VMware dans Azure Site Recovery | Microsoft Docs
description: Cet article fournit une vue d’ensemble des composants et de l’architecture utilisés lors de la réplication de serveurs physiques Windows/Linux et de machines virtuelles VMware locaux vers un site secondaire VMware avec Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2018
ms.author: raynew
ms.openlocfilehash: 97a990aa3ed9043280888900d8fc7b604b6c22b5
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="vmware-vmphysical-server-to-vmware-replication-architecture"></a>Architecture de réplication de serveur physique/machine virtuelle VMware vers VMware

Cet article décrit l’architecture et les processus utilisés quand vous répliquez, basculez et récupérez des machines virtuelles VMware locales ou des serveurs physiques Windows/Linux vers un site secondaire VMware en utilisant [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Composants architecturaux

**Zone** | **Composant** | **Détails**
--- | --- | ---
**Microsoft Azure** | Vous déployez ce scénario à l’aide d’InMage Scout. | Pour obtenir InMage Scout, vous avez besoin d’un abonnement Azure.<br/><br/> Après avoir créé un coffre Recovery Services, téléchargez InMage Scout et installez les dernières mises à jour pour configurer le déploiement.
**Serveur de traitement** | Situé dans le site principal | Vous déployez le serveur de traitement pour gérer la mise en cache, la compression et l’optimisation des données.<br/><br/> Il gère aussi l’installation Push de l’Agent unifié sur les machines à protéger.
**Serveur de configuration** | Situé sur le site secondaire | Le serveur de configuration gère, configure et surveille votre déploiement via le site web de gestion ou la console vContinuum.
**Serveur vContinuum** | facultatif. Installé au même emplacement que le serveur de configuration. | Il intègre une console qui vous permet de gérer et surveiller votre environnement protégé.
**Serveur cible maître** | Situé sur le site secondaire | Le serveur cible maître stocke les données répliquées. Il reçoit les données du serveur de traitement, crée une machine de réplication sur le site secondaire et stocke les points de rétention des données.<br/><br/> Le nombre de serveurs cibles maîtres dont vous avez besoin dépend du nombre de machines que vous protégez.<br/><br/> Si vous voulez effectuer une restauration sur le site principal, vous avez là aussi besoin d’un serveur cible maître. L’Agent unifié est installé sur ce serveur.
**Serveur VMware ESX/ESXi et vCenter** |  Les machines virtuelles sont hébergées sur des hôtes ESX/ESXi. Les hôtes sont gérés avec un serveur vCenter | Vous avez besoin d’une infrastructure VMware pour répliquer des machines virtuelles VMware.
**Machines virtuelles/serveurs physiques** |  L’Agent unifié installé sur les machines virtuelles VMware et les serveurs physiques que vous souhaitez répliquer. | Il joue le rôle de fournisseur de communication entre tous les composants.

### <a name="replication-process"></a>Processus de réplication

1. Vous configurez les serveurs de composants dans chaque site (configuration, processus, cible maître) et installez l’Agent unifié sur les ordinateurs que vous souhaitez répliquer.
2. Après la réplication initiale, l’agent de chaque machine envoie les modifications de réplication différentielle au serveur de traitement.
3. Le serveur de traitement optimise ces données et les transfère vers le serveur cible maître du site secondaire. Le serveur de configuration gère le processus de réplication.

**Figure 6 : réplication de VMware vers VMware**

![VMware vers VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Étapes suivantes

[Configurez](vmware-physical-secondary-disaster-recovery.md) la récupération d’urgence des machines virtuelles VMware et des serveurs physiques vers un site secondaire.
