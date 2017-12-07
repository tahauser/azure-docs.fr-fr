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
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 805582d89ee906e21fbe588e895ce0fe3a926a66
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrer des machines virtuelles IaaS Azure entre différentes régions Azure avec Azure Site Recovery

Utilisez cet article si vous souhaitez migrer des machines virtuelles Azure entre des régions Azure à l’aide du service [Site Recovery](../site-recovery-overview.md).

## <a name="prerequisites"></a>Prérequis

Machines virtuelles IaaS que vous souhaitez migrer.

## <a name="deployment-steps"></a>Étapes du déploiement

1. [Créer un coffre](azure-to-azure-tutorial-enable-replication.md#create-a-vault).
2. [Activez la réplication](azure-to-azure-tutorial-enable-replication.md#enable-replication) pour les machines virtuelles que vous voulez migrer et choisissez Azure comme source. La réplication native des machines virtuelles Azure utilisant des disques managés n’est pas prise en charge.
3. [Exécutez un basculement](../site-recovery-failover.md). Une fois la réplication initiale terminée, vous pouvez exécuter le basculement d’une région Azure sur une autre. Vous pouvez également créer un plan de récupération et exécuter un basculement pour migrer plusieurs machines virtuelles d’une région sur l’autre. [Découvrez d’autres informations](../site-recovery-create-recovery-plans.md) sur les plans de récupération.

## <a name="next-steps"></a>Étapes suivantes
Découvrez les autres scénarios de réplication dans [Qu’est-ce que le service Azure Site Recovery ?](../site-recovery-overview.md)
