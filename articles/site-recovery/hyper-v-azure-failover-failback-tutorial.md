---
title: "Basculer et restaurer automatiquement des machines virtuelles Hyper-V répliquées sur Azure avec Site Recovery | Microsoft Docs"
description: "Découvrez comment basculer des machines virtuelles Hyper-V vers Azure et restaurer sur le site local avec Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/8/2018
ms.author: raynew
ms.openlocfilehash: 7863feb29fbb04f643aa3b7e1984209f44cdbe9a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Basculer et restaurer automatiquement des machines virtuelles Hyper-V répliquées sur Azure

Ce didacticiel explique comment basculer une machine virtuelle Hyper-V vers Azure. Une fois que vous avez procédé au basculement, vous restaurez automatiquement sur votre site local quand il est disponible. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Vérifier la conformité des propriétés des machines virtuelles Hyper-V aux spécifications d’Azure
> * Effectuer un basculement vers Azure
> * Reprotéger des machines virtuelles Azure pour le site local
> * Restaurer automatiquement depuis Azure vers un site local
> * Reprotéger des machines virtuelles locales pour redémarrer la réplication vers Azure

Il s’agit du cinquième didacticiel d’une série. Ce didacticiel suppose que vous avez déjà effectué les tâches des didacticiels précédents.

1. [Préparer Azure](tutorial-prepare-azure.md)
2. [Préparer des machines virtuelles VMware locales](tutorial-prepare-on-premises-hyper-v.md)
3. Configurer la récupération d’urgence pour les [machines virtuelles Hyper-V](tutorial-hyper-v-to-azure.md) ou pour les [machines virtuelles Hyper-V gérées par les clouds System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Exécuter une simulation de récupération d’urgence](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Préparer le basculement et la restauration automatique

Assurez-vous qu’il n’existe aucun instantané sur la machine virtuelle et que l’ordinateur virtuel local est arrêté pendant la reprotection. Cela permet de garantir la cohérence des données pendant la réplication. N’activez pas la machine virtuelle une fois la reprotection terminée. 

Le basculement et la restauration automatique comportent quatre étapes :

1. **Basculer vers Azure** : basculer les machines du site local vers Azure.
2. **Reprotéger des machines virtuelles Azure** : reprotégez les machines virtuelles Azure afin qu’elles soient répliquées sur les machines virtuelles Hyper-V locales.
3. **Basculer sur un site local** : effectuez un basculement à partir d’Azure vers votre site local, lorsqu’il est disponible.
4. **Reprotéger les machines virtuelles locales** : une fois les données restaurées automatiquement, reprotégez les machines virtuelles locales pour débuter leur réplication vers Azure.

## <a name="verify-vm-properties"></a>Vérifier les propriétés de la machine virtuelle

Vérifiez les propriétés de la machine virtuelle et que la machine virtuelle est conforme aux [conditions requises pour Azure](hyper-v-azure-support-matrix.md#replicated-vms).

1. Dans **Éléments protégés**, cliquez sur **Éléments répliqués** > <VM-name>.

2. Dans le volet **Élément répliqué**, examinez les informations de la machine virtuelle, son état d’intégrité et ses derniers points de récupération disponibles. Cliquez sur **Propriétés** pour obtenir plus de détails.
     - Dans **Calcul et réseau**, vous pouvez modifier les paramètres du réseau et de la machine virtuelle, notamment le réseau/sous-réseau dans lequel la machine virtuelle Azure. Les disques managés ne sont pas pris en charge pour la restauration automatique à partir d’Azure vers Hyper-V.
   se trouve après le basculement et l’adresse IP qui sera assignée à ce dernier.
    - Des informations sur les disques de données et du système d’exploitation de la machine virtuelle s’affichent dans **Disques** .

## <a name="fail-over-to-azure"></a>Basculer vers Azure

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle > **Basculer**.
2. Dans **Basculement** sélectionnez le point de récupération **le plus récent**. 
3. Sélectionnez **Arrêter la machine avant de commencer le basculement**. Site Recovery tente d’arrêter les machines virtuelles source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.
4. Après avoir vérifié le basculement, cliquez sur **Valider**. Ceci supprime tous les points de récupération disponibles.

> [!WARNING]
> **N’annulez pas un basculement en cours** : avant que le basculement soit démarré, la réplication de la machine virtuelle est arrêtée. Si vous annulez un basculement en cours, il s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.

## <a name="reprotect-azure-vms"></a>Reprotéger les machines virtuelles Azure

1. Dans **AzureVMVault** > **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle ayant été basculée et sélectionnez **Reprotéger**.
2. Vérifiez que la direction de la protection est définie sur **Azure vers un site local**.
3. La machine virtuelle locale est désactivée au cours de la reprotection, afin de garantir la cohérence des données. Ne l’activez pas une fois la reprotection terminée.
4. Après le processus de reprotection, la machine virtuelle démarre la réplication à partir d’Azure vers le site local.



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>Basculer depuis Azure et reprotéger la machine virtuelle locale

Basculez depuis Azure sur le site local et démarrez la réplication des machines virtuelles depuis le site local vers Azure.

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle > **Basculement planifié**.
2. Dans **Confirmer le basculement planifié**, vérifiez le sens du basculement (depuis Azure), et sélectionnez les emplacements source et cible.
3. Sélectionnez **Synchroniser les données avant le basculement (synchroniser seulement les modifications d’ordre différentiel)**. Cette option réduit le temps d’arrêt de machine virtuelle, car il synchronise la machine virtuelle sans l’arrêter.
4. Lancez le basculement. Vous pouvez suivre la progression du basculement sur l’onglet **Tâches** .
5. Quand la synchronisation initiale des données est terminée et que vous êtes prêt à arrêter les machines virtuelles Azure, cliquez sur **Tâches** > Nom de la tâche de basculement planifié > **Terminer le basculement**. Ceci arrête la machine virtuelle Azure, transfère les dernières modifications locales et démarre la machine virtuelle locale.
6. Connectez-vous à la machine virtuelle locale pour vérifier qu’elle est disponible comme prévu.
7. La machine virtuelle locale est maintenant dans l’état **Validation en attente**. Cliquez sur **Valider**. Ceci supprime les machines virtuelles Azure et leurs disques, et prépare la machine virtuelle locale pour la réplication inverse.
Pour démarrer la réplication de la machine virtuelle locale sur Azure, activez **Réplication inverse**. Cela déclenche la réplication des modifications delta qui se sont produites depuis que la machine virtuelle Azure a été désactivée.  
