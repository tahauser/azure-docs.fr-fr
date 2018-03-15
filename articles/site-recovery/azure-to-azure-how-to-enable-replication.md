---
title: "Configurer la réplication pour les machines virtuelles Azure dans Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit comment configurer la réplication pour les machines virtuelles Azure, d’une région Azure à l’autre à l’aide de Site Recovery."
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2017
ms.author: asgang
ms.openlocfilehash: 5a0e1f638d68131c3c9f1cac36d63795b87e8f41
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Répliquer des machines virtuelles Azure vers une autre région Azure


>[!NOTE]
>
> La réplication Site Recovery pour les machines virtuelles Azure est actuellement en préversion.

Cet article décrit comment activer la réplication de machines virtuelles de Azure, d’une région Azure à l’autre.

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous avez déjà configuré Site Recovery pour ce scénario, comme décrit dans le [didacticiel Azure sur Azure](azure-to-azure-tutorial-enable-replication.md). Assurez-vous que vous avez préparé les conditions préalables et créé le coffre Recovery Services.



## <a name="enable-replication"></a>Activer la réplication

Activez la réplication. Cette procédure suppose que la région principale Azure est Asie de l’Est et que la région secondaire est Asie du Sud-Est.

1. Dans le coffre, cliquez sur **+ Répliquer**.
2. Notez les champs suivants :
    - **Source** : le point d’origine des machines virtuelles, qui dans ce cas est **Azure**.
    - **Emplacement source :** La région Azure où vous souhaitez protéger vos machines virtuelles. Dans notre exemple, l’emplacement source est « Asie de l’Est »
    - **Modèle de déploiement :** Le modèle de déploiement Azure des machines sources.
    - **Groupe de ressources :** Le groupe de ressources auquel appartiennent vos machines virtuelles sources. Toutes les machines virtuelles du groupe de ressources sélectionné sont répertoriées pour la protection à l’étape suivante.

    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Dans **Machines virtuelles > Sélectionner les machines virtuelles**, cliquez sur chaque machine virtuelle à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.
    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. Dans **Paramètres**, vous pouvez éventuellement configurer les paramètres du site cible :

    - **Emplacement cible :** L’emplacement où vos données de machines virtuelles sources sont répliquées. En fonction de l’emplacement des machines virtuelles sélectionné, Site Recovery vous fournit la liste des régions cibles appropriées. Nous vous recommandons de conserver le même emplacement cible que l’emplacement du coffre Recovery Services.
    - **Groupe de ressources cible :** Le groupe de ressources auquel appartiennent toutes vos machines virtuelles répliquées. Par défaut, Azure Site Recovery crée un groupe de ressources dans la région cible avec un nom comportant le suffixe « asr ». Si le groupe de ressources créé par Azure Site Recovery existe déjà, il est réutilisé. Vous pouvez également choisir de le personnaliser, comme indiqué dans la section ci-dessous.
    - **Réseau virtuel cible :** par défaut, Site Recovery crée un réseau virtuel dans la région cible avec un nom ayant le suffixe « asr ». Il est mappé à votre réseau source et utilisé pour toute protection ultérieure. [En savoir plus](site-recovery-network-mapping-azure-to-azure.md) sur le mappage réseau.
    - **Comptes de stockage cibles :** par défaut, Site Recovery crée un compte de stockage cible avec la même configuration que celle du compte de stockage de machines virtuelles source. Si le compte de stockage existe déjà, il est réutilisé.
    - **Comptes de stockage de cache :** Site Recovery a besoin d’un compte de stockage supplémentaire appelé « stockage de cache » dans la région source. Toutes les modifications effectuées sur les machines virtuelles sources sont suivies et envoyées au compte de stockage de cache avant leur réplication vers l’emplacement cible.
    - **Groupe à haute disponibilité :** par défaut, Azure Site Recovery crée un groupe à haute disponibilité dans la région cible avec un nom comportant le suffixe « asr ». Si le groupe à haute disponibilité créé par Azure Site Recovery existe déjà, il est réutilisé.
    - **Stratégie de réplication :** elle définit les paramètres de l’historique de rétention des points de récupération et la fréquence des captures instantanées de cohérence des applications. Par défaut, Azure Site Recovery crée une stratégie de réplication avec les paramètres par défaut de « 24 heures » pour la rétention des points de récupération, et de « 60 minutes » pour la fréquence des captures instantanées de cohérence des applications.

    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Personnaliser les ressources cibles

Vous pouvez modifier les paramètres de cible par défaut utilisés par Site Recovery.

1. Cliquez sur **Personnaliser :** pour modifier les paramètres par défaut :
    - Dans **Groupe de ressources cible**, sélectionnez le groupe de ressources dans la liste de tous les groupes de ressources à l’emplacement cible de l’abonnement.
    - Dans **Réseau virtuel cible**, sélectionnez le réseau à partir d’une liste de tous les réseaux virtuels dans l’emplacement cible.
    - Dans **Groupe à haute disponibilité**, vous pouvez ajouter les paramètres de groupe à haute disponibilité de la machine virtuelle, s’ils font partie d’un groupe à haute disponibilité dans la zone source.
    - Dans **Comptes de stockage cible**, sélectionnez le compte que vous souhaitez utiliser.

        ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Cliquez sur **Créer une ressource cible** > **Activer la réplication**.
3. Une fois que les machines virtuelles sont activées pour la réplication, vous pouvez vérifier leur état d’intégrité sous **Éléments répliqués**

>[!NOTE]
>Pendant la réplication initiale, l’état des peut prendre un certain temps à s’actualiser à défaut de progression. Cliquez sur le bouton **Actualiser** pour obtenir le dernier état.
>

# <a name="next-steps"></a>Étapes suivantes

[En savoir plus](site-recovery-test-failover-to-azure.md) sur l’exécution d’un test de basculement.

