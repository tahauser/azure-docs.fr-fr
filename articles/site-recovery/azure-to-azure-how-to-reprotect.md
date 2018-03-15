---
title: "Reprotéger les machines virtuelles Azure basculées vers la région Azure principale avec Azure Site Recovery | Microsoft Docs"
description: "Décrit comment reprotéger les machines virtuelles Azure dans une région secondaire, après basculement à partir d’une région principale, à l’aide d’Azure Site Recovery."
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: rajanaki
ms.openlocfilehash: d24376c57c468a562fc6d6dd52b4e9b01b53c3da
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Reprotéger les machines virtuelles Azure basculées vers la région principale


>[!NOTE]
>
> La réplication Site Recovery pour les machines virtuelles Azure est en préversion.



Lorsque vous [basculez](site-recovery-failover.md) des machines virtuelles Azure d’une région vers une autre avec [Azure Site Recovery](site-recovery-overview.md), le démarrage des machines virtuelles dans la région secondaire se fait dans un état non protégé. Si les machines virtuelles rebasculent vers la région principale, vous devez effectuer les opérations suivantes :

- Reprotégez les machines virtuelles dans la région secondaire, afin qu’elles commencent à se répliquer dans la région principale. 
- Après l’exécution de la reprotection et de la réplication des machines virtuelles, vous pouvez les basculer de la région secondaire vers la région principale.

> [!WARNING]
> Si vous avez [migré](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration) des machines de la région principale vers la région secondaire, déplacé la machine virtuelle vers un autre groupe de ressources ou supprimé la machine virtuelle Azure, vous ne pouvez pas reprotéger la machine virtuelle ou la rebasculer.


## <a name="prerequisites"></a>Prérequis
1. Le basculement de machine virtuelle de la région principale vers la région secondaire doit être validé.
2. Le site cible principal doit être disponible et vous devez être en mesure d’accéder aux ressources ou de créer des ressources dans cette région.

## <a name="reprotect-a-vm"></a>Reprotéger une machine virtuelle

1. Dans **Coffre** > **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle ayant échoué, puis sélectionnez **Reprotéger**. La direction de reprotection doit afficher secondaire à principal. 

  ![Reprotéger](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Vérifiez les informations Groupe de ressources, Réseau, Stockage et Groupes à haute disponibilité. Cliquez ensuite sur **OK**. Si des ressources sont marquées comme nouvelles, elles seront créées dans le cadre du processus de reprotection.
3. Le travail de reprotection amorce le site cible avec les données les plus récentes. Une fois que cela se termine, la réplication différentielle a lieu. Ensuite, vous pouvez rebasculer vers le site principal. Vous pouvez sélectionner le compte de stockage ou le réseau que vous voulez utiliser pendant la reprotection à l’aide de l’option Personnaliser.

  ![Option Personnaliser](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Personnaliser les paramètres de reprotection

Vous pouvez personnaliser les propriétés suivantes de la machine virtuelle cible durant une reprotection.

![Personnaliser](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriété |Notes  |
|---------|---------|
|Groupe de ressources cible     | Modifiez le groupe de ressources cible dans lequel la machine virtuelle est créée. Dans le cadre de la reprotection, la machine virtuelle cible est supprimée. Vous pouvez choisir un nouveau groupe de ressources sous lequel créer la machine virtuelle après le basculement.        |
|Réseau virtuel cible     | Le réseau cible ne peut pas être modifié lors de la tâche de reprotection. Pour modifier le réseau, refaites le mappage réseau.         |
|Stockage cible     | Vous pouvez modifier le compte de stockage que la machine virtuelle utilise après le basculement.         |
|Stockage du cache     | Vous pouvez spécifier un compte de stockage de cache à utiliser lors de la réplication. Par défaut, un nouveau compte de stockage de cache est créé, s’il n’existe pas.         |
|Groupe à haute disponibilité     |Si la machine virtuelle dans la région secondaire fait partie d’un groupe à haute disponibilité, vous pouvez choisir un groupe à haute disponibilité pour la machine virtuelle cible dans la région principale. Par défaut, Site Recovery tente de trouver le groupe à haute disponibilité dans la région principale et de l’utiliser. Au cours de la personnalisation, vous pouvez spécifier un groupe à haute disponibilité.         |


### <a name="what-happens-during-reprotection"></a>Que se passe-t-il durant la reprotection ?

Par défaut les événements suivants se produisent :

1. Un compte de stockage de cache est créé dans la région principale
2. Si le compte de stockage cible (le compte de stockage d’origine dans la région principale) n’existe pas, un nouveau est créé. Le nom de compte de stockage affecté est le nom du compte de stockage utilisé par la machine virtuelle secondaire, suivi du suffixe « asr ».
3. Si le groupe à haute disponibilité cible n’existe pas, un nouveau est créé dans le cadre du travail de reprotection si nécessaire. Si vous avez personnalisé les paramètres de la reprotection, le groupe sélectionné est utilisé.

Lorsque vous déclenchez un travail de reprotection et que la cible de que machine virtuelle existe, les événements suivants se produisent :

1. Les composants nécessaires sont créés pendant la reprotection. S’ils existent déjà, ils sont réutilisés.
2. La machine virtuelle côté cible est désactivée si elle est en cours d’exécution.
3. Le disque de la machine virtuelle côté cible est copié dans un conteneur par Site Recovery, en tant qu’objet blob de valeur initiale.
4. La machine virtuelle du côté cible est supprimée.
5. L’objet blob d’amorçage est utilisé par la machine virtuelle côté source (secondaire) pour la réplication. Cela garantit que seuls les deltas sont répliqués.
6. Les principales modifications entre le disque source et l’objet blob d’amorçage sont synchronisées. Cela peut prendre du temps.
7. Une fois le travail de reprotection terminé, la réplication delta commence à créer un point de récupération conformément à la stratégie de réplication.
8. Une fois la tâche de reprotection réussie, la machine virtuelle passe à l’état protégé.

## <a name="next-steps"></a>Étapes suivantes

Une fois la machine virtuelle protégée, vous pouvez commencer un basculement. Le basculement arrête la machine virtuelle dans la région secondaire et crée et démarre la machine virtuelle dans la région principale, avec un léger temps d’indisponibilité. Nous vous recommandons de choisir une période en conséquence, et d’effectuer un test de basculement avant d’initialiser un basculement complet vers le site principal. [En savoir plus](site-recovery-failover.md) sur le basculement.

