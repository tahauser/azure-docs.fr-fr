---
title: "Répliquer des applications (Azure vers Azure) | Microsoft Docs"
description: "Cet article décrit comment configurer la réplication de machines virtuelles en cours d’exécution dans une région Azure vers une autre région Azure."
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
ms.date: 12/08/2017
ms.author: asgang
ms.openlocfilehash: 209ec47388ee7291f8107df022e0c2bb202ba6b5
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Répliquer des machines virtuelles Azure vers une autre région Azure



>[!NOTE]
>
> La réplication Site Recovery pour les machines virtuelles Azure est actuellement en préversion.

Cet article décrit comment configurer la réplication des machines virtuelles d’une région Azure vers une autre.

## <a name="prerequisites"></a>Prérequis

* Cet article part du principe que vous connaissez déjà Site Recovery et le coffre Recovery Services. Vous devez avoir un « coffre Recovery services » précréé.

    >[!NOTE]
    >
    > Nous vous recommandons de créer le « coffre Recovery services » à l’emplacement où vous souhaitez répliquer vos machines virtuelles. Par exemple, si votre emplacement cible est la région « États-Unis du Centre », créez le coffre dans « États-Unis du Centre ».

* Si vous utilisez des règles de groupe de sécurité réseau ou un proxy de pare-feu pour contrôler l’accès à la connectivité Internet sortante sur les machines virtuelles Azure, veillez à inclure dans la liste verte les URL ou les adresses IP nécessaires. Pour plus d’informations, consultez le [document d’aide à la mise en réseau](./site-recovery-azure-to-azure-networking-guidance.md).

* Si vous avez une connexion ExpressRoute ou VPN entre l’infrastructure locale et l’emplacement source dans Azure, suivez les [instructions relatives à la configuration des connexions ExpressRoute / VPN entre Azure et l’infrastructure locale dans Site Recovery](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration).

* Pour pouvoir activer la réplication d’une machine virtuelle Azure, votre compte d’utilisateur Azure doit disposer de certaines [autorisations](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

* Votre abonnement Azure doit être activé pour créer des machines virtuelles à l’emplacement cible que vous souhaitez utiliser en tant que région de récupération d’urgence. Vous pouvez contacter le support pour activer le quota nécessaire.

## <a name="enable-replication-from-azure-site-recovery-vault"></a>Activer la réplication à partir du coffre Azure Site Recovery
Pour cette illustration, nous allons répliquer des machines virtuelles en cours d’exécution dans la région Azure « Asie de l’Est » vers la région « Asie du Sud-Est ». La procédure comporte trois étapes :

 Cliquez sur **+Répliquer** dans le coffre pour activer la réplication des machines virtuelles.

1. **Source :** fait référence au point d’origine des machines (en l’occurrence, **Azure**).

2. **Emplacement source :** il s’agit de la région Azure où vous souhaitez protéger vos machines virtuelles. Dans notre exemple, l’emplacement source est « Asie de l’Est »

3. **Modèle de déploiement :** fait référence au modèle de déploiement Azure des machines sources. Vous pouvez sélectionner Classique ou Resource Manager pour que les machines appartenant à ce modèle spécifique soient répertoriées pour la protection à l’étape suivante.

      >[!NOTE]
      >
      > Vous pouvez uniquement répliquer une machine virtuelle classique et la récupérer en tant que machine virtuelle classique. Vous ne pouvez pas la récupérer en tant que machine virtuelle Resource Manager.

4. **Groupe de ressources :** il s’agit du groupe de ressources auquel appartiennent vos machines virtuelles sources. Toutes les machines virtuelles du groupe de ressources sélectionné seront répertoriées pour la protection à l’étape suivante.

    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

Dans **Machines virtuelles > Sélectionner les machines virtuelles**, cliquez sur chaque machine à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Puis cliquez sur OK.
    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)


Dans la section Paramètres, vous pouvez configurer les propriétés du site cible

1. **Emplacement cible :** il s’agit de l’emplacement où vos données de machines virtuelles sources sont répliquées. En fonction de l’emplacement des machines virtuelles sélectionné, Site Recovery vous fournit la liste des régions cibles appropriées.

    > [!TIP]
    > Nous vous recommandons de conserver le même emplacement cible que votre coffre Recovery services.

2. **Groupe de ressources cible :** il s’agit du groupe de ressources auquel appartiennent toutes vos machines virtuelles répliquées. Par défaut, Azure Site Recovery crée un groupe de ressources dans la région cible avec un nom comportant le suffixe « asr ». Si le groupe de ressources créé par Azure Site Recovery existe déjà, il sera réutilisé. Vous pouvez également choisir de le personnaliser, comme indiqué dans la section ci-dessous.    
3. **Réseau virtuel cible :** par défaut, Azure Site Recovery crée un réseau virtuel dans la région cible avec un nom ayant le suffixe « asr ». Il sera mappé à votre réseau source et utilisé pour toute protection ultérieure.

    > [!NOTE]
    > Pour en savoir plus sur le mappage réseau, [consultez les détails sur la mise en réseau](site-recovery-network-mapping-azure-to-azure.md).

4. **Comptes de stockage cibles :** par défaut, Azure Site Recovery crée un compte de stockage cible avec la même configuration que celle du compte de stockage de machines virtuelles source. Dans le cas où le compte de stockage créé par Azure Site Recovery existe déjà, il est réutilisé.

5. **Comptes de stockage de cache :** Azure Site Recovery a besoin d’un compte de stockage supplémentaire appelé « stockage de cache » dans la région source. Toutes les modifications effectuées sur les machines virtuelles sources sont suivies et envoyées au compte de stockage de cache avant leur réplication vers l’emplacement cible.

6. **Groupe à haute disponibilité :** par défaut, Azure Site Recovery crée un groupe à haute disponibilité dans la région cible avec un nom comportant le suffixe « asr ». Si le groupe à haute disponibilité créé par Azure Site Recovery existe déjà, il est réutilisé.

7.  **Stratégie de réplication :** elle définit les paramètres de l’historique de rétention des points de récupération et la fréquence des captures instantanées de cohérence des applications. Par défaut, Azure Site Recovery crée une stratégie de réplication avec les paramètres par défaut de « 24 heures » pour la rétention des points de récupération, et de « 60 minutes » pour la fréquence des captures instantanées de cohérence des applications.

    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Personnaliser les ressources cibles

Si vous souhaitez changer les valeurs par défaut utilisées par Azure Site Recovery, vous pouvez le faire pour répondre à vos besoins.

1. **Personnaliser :** cliquez sur ce bouton pour changer les valeurs par défaut utilisées par Azure Site Recovery.

2. **Groupe de ressources cible :** vous pouvez sélectionner le groupe de ressources dans la liste de tous les groupes de ressources existants à l’emplacement cible dans l’abonnement.

3. **Réseau virtuel cible :** vous pouvez obtenir la liste de tous les réseaux virtuels à l’emplacement cible.

4. **Groupe à haute disponibilité :** vous pouvez uniquement ajouter des paramètres de groupe à haute disponibilité aux machines virtuelles qui font partie de la disponibilité dans la région source.

5. **Comptes de stockage cibles :**

![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/customize.PNG) Cliquez sur **Créer la ressource cible** et Activer la réplication.


Une fois les machines virtuelles protégées, vous pouvez vérifier leur état d’intégrité sous **Éléments répliqués**.

>[!NOTE]
>Pendant la réplication initiale, il est possible que l’actualisation de l’état prenne un peu de temps et que la progression ne s’affiche pas immédiatement. Vous pouvez cliquer sur le bouton Actualiser en haut du panneau pour afficher l’état le plus récent.
>

![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)


## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus](site-recovery-test-failover-to-azure.md) sur l’exécution d’un test de basculement.
- [En savoir plus](site-recovery-failover.md) sur les différents types de basculement et leur exécution.
- En savoir plus sur l’[utilisation des plans de récupération](site-recovery-create-recovery-plans.md) afin de réduire l’objectif de délai de récupération (RTO).
- En savoir plus sur la [reprotection des machines virtuelles Azure](site-recovery-how-to-reprotect.md) après le basculement.
