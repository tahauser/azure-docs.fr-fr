---
title: "Comment reprotéger à partir de machines virtuelles rebasculées vers la région Azure primaire | Microsoft Docs"
description: "Après un basculement de machines virtuelles d’une région Azure vers une autre, vous pouvez utiliser Azure Site Recovery pour protéger les machines dans le sens inverse. Découvrez comment effectuer une reprotection avant un rebasculement."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: rajanaki
ms.openlocfilehash: 3e614b6c3c8358585f3b502f301cc659d2088e2f
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="reprotect-from-failed-over-azure-region-back-to-primary-region"></a>Reprotection à partir d’une région Azure rebasculée vers la région primaire



>[!NOTE]
>
> La récupération Site Recovery pour les machines virtuelles Azure est actuellement en préversion.


## <a name="overview"></a>Vue d'ensemble
Lorsque vous [basculez](site-recovery-failover.md) les machines virtuelles d’une région Azure vers une autre, les machines virtuelles sont dans un état non protégé. Si vous souhaitez ramener les machines virtuelles à la région primaire, vous devez les protéger avant de les rebasculer. La façon d’opérer le basculement ne change pas, quelle que soit la direction du basculement. De même, après activation de la protection des machines virtuelles, il n’existe aucune différence entre la reprotection après basculement ou après restauration automatique.
Pour expliquer les flux de travail de reprotection et pour éviter toute confusion, nous allons utiliser comme site principal des machines protégées la région Asie-Pacifique, et comme site de récupération des machines la région Asie du Sud-Est. Pendant le basculement, les machines virtuelles démarreront dans la région Asie du Sud-Est. Avant la restauration automatique, vous devez reprotéger les machines virtuelles de l’Asie du Sud-est vers l’Asie-Pacifique. Cet article décrit la procédure à suivre pour reprotéger.

> [!WARNING]
> Si vous avez [procédé à la migration](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), déplacé la machine virtuelle vers un autre groupe de ressources ou supprimé la machine virtuelle Azure, vous ne pouvez pas reprotéger ou restaurer automatiquement la machine virtuelle.

Une fois la reprotection terminée et les machines virtuelles protégées en cours de réplication, vous pouvez lancer un basculement sur les machines virtuelles afin de les ramener dans la région Asie-Pacifique.

Publiez vos commentaires ou vos questions en bas de cet article ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Prérequis
1. Les machines virtuelles doivent avoir été validées.
2. Le site cible, en l’occurrence la région Azure Asie-Pacifique, doit être disponible et en mesure d’accéder aux ressources ou de créer des ressources dans cette région.

## <a name="steps-to-reprotect"></a>Procédure de reprotection

Voici la procédure à suivre pour reprotéger une machine virtuelle à l’aide des paramètres par défaut.

1. Dans **Coffre** > **Éléments répliqués**, cliquez avec le bouton droit de la souris sur la machine virtuelle ayant basculé et sélectionnez **Reprotéger**. Vous pouvez également cliquer sur la machine et sélectionner **Reprotéger** à partir des boutons de commande.

![Cliquer avec le bouton droit pour reprotéger](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Dans le panneau, notez que la direction de la protection, **Asie du Sud-est vers Asie-Pacifique**, est déjà sélectionnée.

![Panneau Reprotéger](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Vérifiez les informations **Groupe de ressources, Réseau, Stockage et Groupes à haute disponibilité**, puis cliquez sur OK. Si des ressources sont marquées (nouvelles), elles sont créées pendant la reprotection.

Le travail de reprotection amorce tout d’abord le site cible (en l’occurrence Asie du Sud-est) avec les dernières données, puis réplique les deltas avant que vous opériez le rebasculement vers la région Asie du Sud-Est.

### <a name="reprotect-customization"></a>Personnalisation de la reprotection
Si vous souhaitez choisir le compte de stockage ou le réseau d’extraction durant une reprotection, vous pouvez le faire à l’aide de l’option Personnaliser fournie dans le panneau Reprotéger.

![Option Personnaliser](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Vous pouvez personnaliser les propriétés suivantes de la machine virtuelle cible durant une reprotection.

![Panneau Personnaliser](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriété |Remarques  |
|---------|---------|
|Groupe de ressources cible     | Vous pouvez choisir de modifier le groupe de ressources cible dans lequel créer la machine virtuelle. Dans le cadre d’une reprotection, la machine virtuelle cible est supprimée. Vous pouvez donc choisir un nouveau groupe de ressources sous lequel créer la machine virtuelle après le basculement.         |
|Réseau virtuel cible     | Le réseau ne peut pas être modifié pendant le travail de reprotection. Pour modifier le réseau, refaites le mappage réseau.         |
|Stockage cible     | Vous pouvez modifier le compte de stockage sur lequel créer la machine virtuelle après basculement.         |
|Stockage du cache     | Vous pouvez spécifier un compte de stockage de cache à utiliser lors de la réplication. Si poursuivez avec les valeurs par défaut, un nouveau compte de stockage de cache est créé s’il n’en existe pas.         |
|Groupe à haute disponibilité     |Si la machine virtuelle en Asie-Pacifique fait partie d’un groupe à haute disponibilité, vous pouvez choisir un groupe à haute disponibilité pour la machine virtuelle cible en Asie du Sud-est. Le comportement par défaut consiste à chercher le groupe à haute disponibilité d’Asie du Sud-est et à tenter de l’utiliser. Au cours de personnalisation, vous pouvez spécifier un nouveau groupe à haute disponibilité.         |


### <a name="what-happens-during-reprotect"></a>Que se passe-t-il durant la reprotection ?

Tout comme après la première activation de la protection, voici les artefacts qui sont créés si vous utilisez les paramètres par défaut.
1. Un compte de stockage de cache est créé dans la région Asie-Pacifique.
2. Si le compte de stockage cible (compte de stockage d’origine de la machine virtuelle d’Asie du Sud-est) n’existe pas, un nouveau compte est créé. Son nom est celui du compte de stockage de la machine virtuelle d’Asie-Pacifique, suivi du suffixe « asr ».
3. Si le groupe à haute disponibilité cible n’existe pas, le comportement par défaut consiste à détecter la nécessité de créer un groupe à haute disponibilité, puis à le créer dans le cadre de la reprotection. Si vous avez personnalisé la reprotection, le groupe à haute disponibilité sélectionné est utilisé.
4.

Voici la liste des étapes qui se produisent lorsque vous déclenchez un travail de reprotection. Ceci est dans le cas où la machine virtuelle côté cible existe.

1. Les artefacts nécessaires sont créés pendant la reprotection. S’ils existent déjà, ils sont réutilisés.
2. La machine virtuelle côté cible (Asie du Sud-est) est tout d’abord mise hors tension si elle est en cours d’exécution.
3. Le disque de la machine virtuelle côté cible est copié par Azure Site Recovery dans un conteneur en tant qu’objet blob d’amorçage.
4. La machine virtuelle côté cible est ensuite supprimée.
5. L’objet blob d’amorçage est utilisé par la machine virtuelle côté source (Asie-Pacifique) pour la réplication. Cela garantit que seuls les deltas sont répliqués.
6. Les principales modifications entre le disque source et l’objet blob d’amorçage sont synchronisées. Cela peut prendre du temps.
7. Une fois le travail de reprotection terminé, la réplication delta commence à créer un point de récupération conformément à la stratégie.

> [!NOTE]
> Vous ne pouvez pas protéger à un niveau plan de récupération. Vous pouvez uniquement reprotéger à un niveau par machine virtuelle.

Une fois la reprotection réussie, la machine virtuelle passe à l’état protégé.

## <a name="next-steps"></a>Étapes suivantes

Une fois que la machine virtuelle est à l’état protégé, vous pouvez commencer un basculement. Le basculement a pour effet d’arrêter la machine virtuelle dans la région Azure Asie-Pacifique, ainsi que de créer puis démarrer la machine virtuelle de la région Asie du Sud-est. L’application sera donc indisponible quelques instants. Par conséquent, choisissez d’opérer le basculement à un moment votre application peut tolérer un temps d’arrêt. Avant d’initier un basculement, Il est recommandé d’effectuer un test de basculement de la machine virtuelle pour s’assurer qu’il se produit correctement.

-   [Étapes pour lancer un test de basculement de la machine virtuelle](site-recovery-test-failover-to-azure.md)

-   [Étapes pour lancer un basculement de la machine virtuelle](site-recovery-failover.md)
