---
title: "Réplication Hyper-V sur une architecture de site secondaire dans Azure Site Recovery | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble de l’architecture utilisée pour la réplication de machines virtuelles Hyper-V locales sur un site System Center VMM secondaire avec Azure Site Recovery."
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: raynew
ms.openlocfilehash: a6e20b3f1f804617f78413413509d1b5bd476d23
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="hyper-v-replication-to-a-secondary-site"></a>Réplication Hyper-V vers un site secondaire

Cet article décrit les composants et les processus impliqués dans la réplication des machines virtuelles Hyper-V locales dans des clouds System Center Virtual Machine Manager (VMM) sur un site VMM secondaire à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.


## <a name="architectural-components"></a>Composants architecturaux

Le tableau et le graphique suivants fournissent une vue d’ensemble des composants utilisés pour la réplication Hyper-V vers un site secondaire.

**Composant** | **Prérequis** | **Détails**
--- | --- | ---
**Microsoft Azure** | Abonnement Azure | Vous créez un coffre Recovery Services dans l’abonnement Azure pour orchestrer et gérer la réplication entre les emplacements VMM.
**Serveur VMM** | Vous avez besoin d’un emplacement VMM principal et secondaire. | Nous recommandons l’utilisation d’un serveur VMM dans le site principal et un autre dans le site secondaire.
**Serveur Hyper-V** |  Un ou plusieurs serveurs hôtes Hyper-V dans les clouds VMM principaux et secondaires. | Les données sont répliquées entre les serveurs hôtes Hyper-V principal et secondaire via une liaison LAN ou VPN, en utilisant Kerberos ou une authentification par certificat.  
**Machines virtuelles Hyper-V** | Sur le serveur hôte Hyper-V. | Le serveur hôte source doit avoir au moins une machine virtuelle que vous souhaitez répliquer.

**Architecture local vers local**

![Local vers local](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Processus de réplication

1. Au moment où la réplication initiale est déclenchée, un [instantané des machines virtuelles Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) a lieu.
2. Les disques durs virtuels sur la machine virtuelle sont répliqués un par un vers l’emplacement secondaire.
3. Si des changements se produisent sur les disques pendant l’exécution de la réplication initiale, 
4. Quand la réplication initiale se termine, la réplication différentielle commence. Le dispositif de suivi de réplication des réplicas Hyper-V assure le suivi des modifications sous forme de journaux de réplication Hyper-V (.hrl). Ces fichiers journaux se trouvent dans le même dossier que les disques. À chaque disque correspond un fichier .hrl, qui est envoyé à l’emplacement secondaire. L’instantané et les fichiers journaux consomment des ressources disque pendant la réplication initiale.
5. Les modifications d’ordre différentiel dans le fichier journal sont synchronisées et fusionnées sur le disque parent.


## <a name="failover-and-failback-process"></a>Processus de basculement et de restauration automatique

- Vous pouvez basculer une seule machine ou créer des plans de récupération pour orchestrer le basculement de plusieurs machines.
- Vous pouvez effectuer un basculement planifié ou non planifié entre des sites locaux. Si vous exécutez un basculement planifié, les machines virtuelles sources sont arrêtées pour éviter toute perte de données.
    - Si vous effectuez un basculement non planifié vers un site secondaire, une fois cette opération effectuée, les machines de l’emplacement secondaire ne sont pas protégées.
    - Si vous avez lancé un basculement planifié, une fois l’opération terminée, les machines de l’emplacement secondaire sont protégées.
- Une fois que le basculement initial s’exécute, validez-le pour accéder à la charge de travail à partir de la machine virtuelle de réplication.
- Quand l’emplacement principal est à nouveau disponible, vous pouvez effectuer une restauration automatique.
    - Vous initiez la réplication inverse, pour démarrer la réplication depuis le site secondaire vers le site principal. La réplication inverse affecte aux machines virtuelles l’état protégé, mais l’emplacement actif est toujours le centre de données secondaire.
    - Pour placer le site principal à l’emplacement actif, lancez un basculement planifié depuis le site secondaire vers le site principal, puis une autre réplication inverse.



## <a name="next-steps"></a>Étapes suivantes


Suivre [le didacticiel](hyper-v-vmm-disaster-recovery.md) montrant comment activer la réplication Hyper-V entre des clouds VMM.
