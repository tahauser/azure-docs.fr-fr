---
title: "Basculer et restaurer automatiquement des machines virtuelles Azure répliquées vers une région Azure secondaire avec Azure Site Recovery (préversion)"
description: "Découvrez comment basculer et restaurer automatiquement la réplication de machines virtuelles Azure vers une région Azure secondaire avec Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/07/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b45d1287444d200727550a81ce72a19a417fe510
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions-preview"></a>Basculer et restaurer automatiquement des machines virtuelles Azure entre des régions Azure (préversion)

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel explique comment basculer une seule machine virtuelle Azure vers une région Azure secondaire. Après avoir effectué le basculement, vous effectuez la restauration automatique vers la région primaire quand celle-ci est disponible. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Basculer la machine virtuelle Azure
> * Reprotéger la machine virtuelle Azure secondaire, afin qu’elle soit répliquée vers la région primaire
> * Restaurer automatiquement la machine virtuelle secondaire
> * Reprotéger la machine virtuelle principale vers la région secondaire

## <a name="prerequisites"></a>Prérequis

- Assurez-vous d’avoir effectué une [simulation de récupération d’urgence](azure-to-azure-tutorial-dr-drill.md) pour vérifier que tout fonctionne comme prévu.
- Vérifiez les propriétés de la machine virtuelle avant d’exécuter le test de basculement. La machine virtuelle doit satisfaire aux [exigences Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="run-a-failover-to-the-secondary-region"></a>Exécuter un basculement vers la région secondaire

1. Dans **Éléments répliqués**, sélectionnez la machine virtuelle que vous souhaitez basculer > **Basculement**.

   ![Basculement](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Dans **Basculement**, sélectionnez un **point de récupération** vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :

   * **Dernier** (par défaut) : cette option traite toutes les données dans le service Site Recovery et fournit l’objectif de point de récupération (RPO) le plus bas.
   * **Dernier point traité** : cette option rétablit la machine virtuelle vers le dernier point de récupération ayant été traité par le service Site Recovery.
   * **Personnalisé** : utilisez cette option pour effectuer un basculement vers un point de récupération spécifique. Cette option est utile pour effectuer un test de basculement.

3. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt.

4. Suivre la progression du basculement sur la page **Tâches**.

5. Après le basculement, validez la machine virtuelle en vous y connectant. Si vous souhaitez accéder à un autre point de récupération pour la machine virtuelle, utilisez l’option **Changer le point de récupération**.

6. Si vous êtes satisfait de la machine vers laquelle est effectué le basculement, vous pouvez **Valider** le basculement.
   La validation supprime tous les points de récupération disponibles avec le service. L’option **Modifier le point de récupération** n’est plus disponible.

## <a name="reprotect-the-secondary-vm"></a>Reprotéger la machine virtuelle secondaire

Après avoir basculé la machine virtuelle, vous devez la reprotéger afin qu’elle soit répliquée vers la région principale.

1. Vérifiez que la machine virtuelle se trouve dans l’état **Basculement validé** et que la région primaire est disponible ; vous pouvez alors y créer des ressources et accéder à celles-ci.
2. Dans **Coffre** > **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle ayant été basculée et sélectionnez **Reprotéger**.

   ![Cliquer avec le bouton droit pour reprotéger](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Notez que la direction de la protection, à savoir depuis la région secondaire vers la région primaire, est déjà sélectionnée.
3. Vérifiez les informations **Groupe de ressources, Réseau, Stockage et Groupes à haute disponibilité**. Les ressources éventuellement marquées (nouvelles) sont créées dans le cadre de l’opération de reprotection.
4. Cliquez sur **OK** pour déclencher un travail de reprotection. Ce travail amorce le site cible avec les données les plus récentes. Ensuite, il réplique les deltas vers la région primaire. La machine virtuelle est désormais dans un état protégé.

## <a name="fail-back-to-the-primary-region"></a>Effectuer une restauration automatique vers la région primaire

Une fois les machines virtuelles reprotégées, vous pouvez effectuer une restauration automatique vers la région primaire en fonction de vos besoins. Pour ce faire, suivez les instructions de [basculement](#run-a-failover).
