---
title: "Migrer des machines virtuelles IaaS Azure entre des régions Azure | Microsoft Docs"
description: "Utilisez Site Recovery pour migrer des machines virtuelles IaaS Azure d’une région Azure vers une autre."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: raynew
ms.openlocfilehash: f645150aa7b93ebbd98899cc8fdd495dfeaf3076
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrer des machines virtuelles IaaS Azure entre différentes régions Azure avec Azure Site Recovery

Bienvenue dans Azure Site Recovery ! Utilisez cet article si vous souhaitez migrer des machines virtuelles Azure entre régions Azure.
>[!NOTE]
>
> Pour la réplication de machines virtuelles Azure sur une autre région dans des situations de migration et de récupération d’urgence, consultez [ce document](../site-recovery-azure-to-azure.md). La réplication Site Recovery pour les machines virtuelles Azure est actuellement en préversion.


## <a name="prerequisites"></a>Prérequis
Voici ce dont vous avez besoin pour ce déploiement :

* **Machines virtuelles IaaS**: machines virtuelles à migrer. Vous migrez ces machines virtuelles en les traitant comme des machines physiques.

## <a name="deployment-steps"></a>Étapes du déploiement
Cette section décrit les étapes de déploiement dans le nouveau portail Azure.

1. [Créer un coffre](../site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Activez la réplication]../(site-recovery-azure-to-azure.md) pour les machines virtuelles que vous souhaitez migrer et choisissez Azure comme source.
  >[!NOTE]
  >
  > Actuellement, la réplication native des machines virtuelles Azure utilisant des disques managés n’est pas prise en charge. Vous pouvez utiliser l’option « Physique vers Azure » dans [ce document](../site-recovery-vmware-to-azure.md) pour migrer les machines virtuelles avec des disques managés.
3. [Exécutez un basculement](../site-recovery-failover.md). Une fois la réplication initiale terminée, vous pouvez exécuter le basculement d’une région Azure sur une autre. Vous pouvez également créer un plan de récupération et exécuter un basculement pour migrer plusieurs machines virtuelles d’une région sur l’autre. [Découvrez d’autres informations](../site-recovery-create-recovery-plans.md) sur les plans de récupération.

## <a name="next-steps"></a>Étapes suivantes
Découvrez les autres scénarios de réplication dans [Qu’est-ce que le service Azure Site Recovery ?](../site-recovery-overview.md)
