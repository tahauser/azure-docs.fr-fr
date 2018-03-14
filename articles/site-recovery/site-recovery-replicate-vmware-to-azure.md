---
title: "Répliquer des applications (VMware vers Azure) | Microsoft Docs"
description: "Cet article décrit la procédure de configuration de la réplication de machines virtuelles exécutées sur VMware dans Azure."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: asgang
ms.openlocfilehash: 1b63515970f81b1cab679287d84707d531fd102a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="replicate-applications-running-on-vmware-virtual-machines-to-azure"></a>Répliquer des applications s’exécutant sur des machines virtuelles VMware dans Azure



Cet article décrit la procédure de configuration de la réplication de machines virtuelles exécutées sur VMware dans Azure.
## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous avez :

1.  [Configuré un environnement source local](site-recovery-set-up-vmware-to-azure.md) ;
2.  [Configuré un environnement cible dans Azure](site-recovery-prepare-target-vmware-to-azure.md).


## <a name="enable-replication"></a>Activer la réplication
### <a name="before-you-start"></a>Avant de commencer
Lors de la réplication de machines virtuelles VMware :

* Votre compte d’utilisateur Azure doit disposer de certaines [autorisations](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) pour activer la réplication d’une machine virtuelle dans Azure.
* Les machines virtuelles VMware sont découvertes toutes les 15 minutes, et il peut s’écouler 15 minutes ou plus entre leur découverte et leur affichage dans le portail Azure. De même, la découverte peut prendre 15 minutes ou plus lorsque vous ajoutez un serveur vCenter ou un hôte vSphere.
* La mise à jour avec les modifications de l’environnement sur la machine virtuelle (par exemple, l’installation d’outils VMware) peut aussi prendre plus de 15 minutes.
* Vous pouvez vérifier l’heure de la dernière découverte de machines virtuelles VMware dans le champ **Dernier contact à** correspondant au serveur vCenter ou à l’hôte vSphere dans la page **Serveurs de configuration**.
* Pour ajouter des machines à répliquer sans attendre la découverte planifiée, mettez en surbrillance le serveur de configuration (sans cliquer dessus) et cliquez sur le bouton **Actualiser**.
* Lorsque vous activez la réplication, si la machine est prête, le serveur de processus installe automatiquement le service Mobilité sur ce dernier.


### <a name="enable-replication-as-follows"></a>Activez la réplication comme suit

1. Cliquez sur **Étape 2 : Répliquer l’application** > **Source**. Après avoir activé la réplication pour la première fois, cliquez sur l’option **+Répliquer** dans le coffre pour activer la réplication des autres machines.
2. Dans la page **Source** > **Source**, sélectionnez le serveur de configuration.
3. Dans la zone **Type de machine**, sélectionnez **Machines virtuelles** ou **Machines physiques**.
4. Dans la zone **Hyperviseur vCenter/vSphere**, sélectionnez le serveur vCenter qui gère l’hôte vSphere, ou sélectionnez l’hôte. Ce paramètre n’est pas utile si vous répliquez des machines physiques.
5. Sélectionnez le serveur de processus, qui est le nom du serveur de configuration si vous n’avez pas créé de serveur de processus supplémentaire. Cliquez ensuite sur **OK**.

    ![Activer la source de réplication](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. Dans **Cible**, sélectionnez l’abonnement et le groupe de ressources dans lesquels vous voulez créer les machines virtuelles basculées. Choisissez le modèle de déploiement que vous souhaitez utiliser dans Azure (classique ou Azure Resource Manager) pour les machines virtuelles basculées.

7. Sélectionnez le compte de stockage Azure que vous souhaitez utiliser pour les données de réplication. 

    > [!NOTE]

    >   * Vous pouvez sélectionner un compte Standard Storage ou Premium Storage. Si vous sélectionnez un compte Premium, vous devez spécifier un compte de stockage standard supplémentaire pour les journaux de réplication en cours. Les comptes doivent se trouver dans la même région que le coffre Recovery Services.
    >   * Si vous souhaitez utiliser un compte de stockage différent, vous pouvez en [créer un](../storage/common/storage-create-storage-account.md). Pour créer un compte de stockage à l’aide du Gestionnaire des ressources, cliquez sur **Créer**. Si vous souhaitez créer un compte de stockage en suivant le modèle classique, vous pouvez le faire dans le portail Azure.

8. Sélectionnez le sous-réseau et le réseau Azure auxquels les machines virtuelles Azure se connectent lorsqu’elles sont démarrées après le basculement. Ce réseau doit se trouver dans la même région que le coffre Recovery Services. Sélectionnez **Effectuez maintenant la configuration pour les machines sélectionnées** pour appliquer le paramètre réseau à l’ensemble des machines que vous sélectionnez à des fins de protection. Sélectionnez **Configurer ultérieurement** pour sélectionner le réseau Azure pour chaque machine. Si vous n’avez pas de réseau, vous devez en [créer un](#set-up-an-azure-network). Pour créer un réseau à l’aide du Gestionnaire des ressources, cliquez sur **Créer**. Si vous souhaitez créer un réseau en suivant le modèle classique, utilisez le [portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Le cas échéant, sélectionnez un sous-réseau, puis cliquez sur **OK**.

    ![Activer le paramètre de cible de réplication](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. Dans **Machines virtuelles** > **Sélectionner les machines virtuelles**, sélectionnez chaque machine à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.

    ![Activer la réplication de certaines machines virtuelles](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. Dans **Propriétés** > **Configurer les propriétés**, sélectionnez le compte que le serveur de processus doit utiliser pour installer automatiquement le service Mobilité sur la machine.  
11. Par défaut, tous les disques sont répliqués. Pour exclure les disques de la réplication, cliquez sur **Tous les disques**, puis désélectionnez ceux qui ne doivent pas être répliqués.  Cliquez ensuite sur **OK**. Vous pouvez opter pour une définition ultérieure des propriétés. [Apprenez-en davantage](site-recovery-exclude-disk.md) sur l’exclusion de disques.

    ![Activer les propriétés de configuration de la réplication](./media/site-recovery-vmware-to-azure/enable-replication6.png)

12. Dans **Paramètres de réplication** > **Configurer les paramètres de réplication**, vérifiez que la stratégie de réplication correcte est sélectionnée. Vous pouvez modifier les paramètres de la stratégie de réplication dans **Paramètres** > **Stratégies de réplication** > (nom de la stratégie) > **Modifier les paramètres**. Les modifications que vous appliquez à une stratégie s’appliquent également à la réplication et aux nouvelles machines.
13. Cochez la case **Cohérence multimachine virtuelle** pour regrouper les machines dans un groupe de réplication. Nommez le groupe, puis cliquez sur **OK**. 

    > [!NOTE]

    >    * Toutes les machines d’un groupe de réplication sont répliquées ensemble et elles ont des points de récupération cohérents après incident et avec les applications lorsqu’elles basculent.
    >    * Rassemblez les machines virtuelles et les serveurs physiques afin qu’ils reflètent vos charges de travail. L’activation de la cohérence multimachine virtuelle peut affecter les performances de la charge de travail. Utilisez-la uniquement si les machines exécutent la même charge de travail et si vous avez besoin de cohérence.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication7.png)
14. Cliquez sur **Activer la réplication**. Vous pouvez suivre la progression du travail **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.

> [!NOTE]
> Si la machine est prête pour l’installation Push, le composant du service Mobilité est installé dès que la protection est activée. Une fois le composant installé sur la machine, une tâche de protection démarre et échoue. Après cet échec, vous devez redémarrer manuellement chaque machine. Après le redémarrage, la tâche de protection recommence et la réplication initiale se produit.
>
>

## <a name="view-and-manage-vm-properties"></a>Afficher et gérer les propriétés des machines virtuelles

Vérifiez ensuite les propriétés de la machine source. N’oubliez pas que le nom de la machine virtuelle Azure doit respecter la [configuration requise pour les machines virtuelles Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Cliquez sur **Paramètres** > **Éléments répliqués** > et sélectionnez la machine. La page **Bases** affiche des informations sur les paramètres et l’état de la machine.
2. Dans **Propriétés**, vous pouvez afficher les informations sur la réplication et le basculement de la machine virtuelle.
3. Dans **Calcul et réseau** > **Propriétés de calcul**, vous pouvez spécifier la taille de la cible et le nom de la machine virtuelle Azure. Si besoin, modifiez ce nom afin de respecter les exigences d’Azure.

    ![Propriétés Calcul et réseau](./media/site-recovery-vmware-to-azure/vmproperties.png)

4.  Vous pouvez sélectionner un [groupe de ressources](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) dont l’ordinateur fera partie pendant le post-basculement. Vous pouvez changer ce paramètre avant le basculement. Après le basculement, si vous migrez l’ordinateur vers un autre groupe de ressources, les paramètres de protection de l’ordinateur ne sont plus adaptés.
5. Vous pouvez sélectionner un basculement de [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) si votre machine doit faire partie d’un post-basculement. Quand vous sélectionnez un groupe à haute disponibilité, prenez note de ce qui suit :

    * Seuls les groupes à haute disponibilité appartenant au groupe de ressources spécifié sont répertoriés.  
    * Les machines avec différents réseaux virtuels ne peuvent pas faire partie du même groupe à haute disponibilité.
    * Seules les machines virtuelles de même taille peuvent faire partie d’un groupe à haute disponibilité.
5. Vous pouvez également afficher et ajouter des informations sur le réseau cible, le sous-réseau et l’adresse IP affectés à la machine virtuelle Azure.
6. Les disques de données et du système d’exploitation de la machine virtuelle à répliquer s’affichent dans **Disques**.

### <a name="network-adapters-and-ip-addressing"></a>Cartes réseau et adressage IP

- Vous pouvez définir l’adresse IP cible. Si vous ne fournissez pas d’adresse IP, la machine ayant basculé utilise le service DHCP. Si vous définissez une adresse qui n’est pas disponible au moment du basculement, ce dernier échoue. Si l’adresse est disponible sur le réseau de test de basculement, vous pouvez utiliser la même adresse IP cible pour le test de basculement.
- Le nombre de cartes réseau est déterminé par la taille spécifiée pour la machine virtuelle cible, comme suit :
    - Si le nombre de cartes réseau sur la machine source est inférieur ou égal au nombre de cartes autorisé pour la taille de la machine cible, la cible présente le même nombre de cartes que la source.
    - Si le nombre de cartes de la machine virtuelle source dépasse la taille cible autorisée, la taille cible maximale est utilisée.
    Par exemple, si une machine source présente deux cartes réseau et que la taille de la machine cible en accepte quatre, la machine cible présente deux cartes. Si la machine source présente deux cartes, mais que la taille cible n’en prend en charge qu’une seule, la machine cible n’a qu’une seule carte.
    - Si la machine virtuelle possède plusieurs cartes réseau, elles se connectent toutes au même réseau. La première qui s’affiche dans la liste devient la carte réseau *par défaut* dans la machine virtuelle Azure.

### <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

Les clients Microsoft Software Assurance peuvent utiliser Azure Hybrid User Benefit afin de réduire les coûts de licences pour les machines Windows Server qui sont migrées vers Azure ou qui utilisent Azure pour la récupération d’urgence. Si vous êtes autorisé à utiliser Azure Hybrid Use Benefit, vous pouvez spécifier que la machine virtuelle qui doit en profiter est celle créée par Azure Site Recovery en cas de basculement. Pour ce faire :
- Accédez à la section des propriétés Calcul et Réseau de la machine virtuelle répliquée.
- Répondez à la question qui vous demande si vous avez une licence Windows Server qui vous permet de profiter d’Azure Hybrid Use Benefit.
- Cochez la case pour confirmer que vous possédez une licence Windows Server éligible avec Software Assurance vous permettant d’appliquer Hybrid Use Benefit sur la machine qui sera créée pendant le basculement.
- Enregistrez les paramètres de la machine répliquée.

En savoir plus sur l’offre [Azure Hybrid Use Benefit](https://aka.ms/azure-hybrid-use-benefit-pricing).

## <a name="common-issues"></a>Problèmes courants

* La taille de chaque disque doit être inférieure à 1 To.
* Le disque du système d’exploitation doit être un disque de base, et non un disque dynamique.
* Pour les machines virtuelles de génération 2/activées UEFI, le système d’exploitation doit être de la famille Windows, et la taille du disque de démarrage doit être inférieure à 300 Go.

## <a name="next-steps"></a>Étapes suivantes

Une fois que la protection est effectuée et que la machine a atteint un état protégé, vous pouvez essayer un [basculement](site-recovery-failover.md) pour vérifier si votre application s’affiche dans Azure ou non.

Si vous souhaitez désactiver la protection, consultez [Nettoyer les paramètres d’inscription et de protection](site-recovery-manage-registration-and-protection.md).
