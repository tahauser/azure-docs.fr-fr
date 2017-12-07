---
title: "Répliquer des machines virtuelles Azure vers une région secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Cet article explique comment répliquer des machines virtuelles Azure s’exécutant dans une région Azure vers une autre région Azure à l’aide du service Azure Site Recovery."
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
ms.date: 11/29/2017
ms.author: asgang
ms.openlocfilehash: 114390712f5e5bf62ec515db62469e09361b8f85
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Répliquer des machines virtuelles Azure vers une autre région Azure


Cet article explique comment répliquer des machines virtuelles Azure dans une région Azure vers une région Azure secondaire à l’aide du service Azure Site Recovery.

>[!NOTE]
>
> La réplication de machines virtuelles Azure avec Site Recovery est actuellement en préversion.

## <a name="prerequisites"></a>Prérequis

* Vous devez disposer d’un coffre Recovery Services. Nous vous recommandons de créer le coffre dans la région cible vers laquelle vous souhaitez répliquer vos machines virtuelles.
* Si vous utilisez des règles de groupe de sécurité réseau ou un proxy de pare-feu pour contrôler l’accès à la connectivité Internet sortante sur les machines virtuelles Azure, veillez à autoriser les URL ou les adresses IP nécessaires. [En savoir plus](./site-recovery-azure-to-azure-networking-guidance.md).
* Si vous avez une connexion ExpressRoute ou VPN entre l’infrastructure locale et l’emplacement source dans Azure, [découvrez comment les configurer](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration).
* Afin d’activer la réplication d’une machine virtuelle Azure, votre compte d’utilisateur Azure doit disposer [d’autorisations spécifiques](../site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).
Votre abonnement Azure doit être activé pour créer des machines virtuelles à l’emplacement cible que vous souhaitez utiliser en tant que région de récupération d’urgence. Contactez le support pour activer le quota requis.

## <a name="enable-replication"></a>Activer la réplication

Dans cette procédure, les régions Asie de l’Est et Asie du Sud-Est sont respectivement utilisées comme emplacement source et cible.

1. Cliquez sur **+Répliquer** dans le coffre pour activer la réplication des machines virtuelles.
2. Vérifiez que **Source :** a la valeur **Azure**.
3. Définissez **Emplacement source** sur Asie de l’Est.
4. Dans **Modèle de déploiement**, sélectionnez **Classique** ou **Resource Manager**.
5. Dans **Groupe de ressources**, sélectionnez le groupe auquel appartiennent vos machines virtuelles sources. Toutes les machines virtuelles sous le groupe de ressources sélectionné sont répertoriées.

    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

6. Dans **Machines virtuelles > Sélectionner les machines virtuelles**, cliquez sur chaque machine virtuelle à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)
    
7. Sous **Paramètres** > **Emplacement cible**, spécifiez la destination de la réplication des données des machines virtuelles sources. Site Recovery fournit une liste des régions cibles appropriées, en fonction de la région des machines virtuelles sélectionnées.
8. Site Recovery définit les paramètres cibles par défaut. Vous pouvez les modifier selon les besoins.

    - **Groupe de ressources cible**. Par défaut, Site Recovery crée un groupe de ressources dans la région cible avec le suffixe « asr ». Si le groupe de ressources créé existe déjà, il est réutilisé.
    - **Réseau virtuel cible**. Par défaut, Site Recovery crée un réseau virtuel dans la région cible, avec le suffixe « asr ». Ce réseau est mappé à votre réseau source. [En savoir plus](site-recovery-network-mapping-azure-to-azure.md) sur le mappage réseau.
    - **Comptes de stockage cibles**. Par défaut, Site Recovery crée un compte de stockage cible qui correspond à la configuration du stockage des machines virtuelles sources. Si le compte créé existe déjà, il est réutilisé.
    - **Comptes de stockage de cache**. Azure Site Recovery crée un compte de stockage de cache supplémentaire (région source). Toutes les modifications sur les machines virtuelles sources sont suivies et envoyées au compte de stockage de cache avant la réplication vers l’emplacement cible.
    - **Groupe à haute disponibilité**. Par défaut, Site Recovery crée un groupe à haute disponibilité dans la région cible, avec un suffixe « asr ». Si le groupe à haute disponibilité créé existe déjà, il est réutilisé.
    - **Stratégie de réplication**. Site Recovery définit les paramètres de l’historique de rétention des points de récupération et la fréquence des captures instantanées de cohérence des applications. Par défaut, Site Recovery crée une stratégie de réplication avec des paramètres par défaut de « 24 heures » pour la rétention des points de récupération et « 60 minutes » pour la fréquence des captures instantanées de cohérence des applications.

    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
9. Cliquez sur **Activer la réplication** pour commencer à protéger les machines virtuelles.

## <a name="customize-target-resources"></a>Personnaliser les ressources cibles

1. Modifiez les valeurs par défaut cibles suivantes de votre choix :

    - **Groupe de ressources cible**. Sélectionnez un groupe de ressources dans la liste de tous les groupes de ressources à l’emplacement cible, au sein de l’abonnement.
    - **Réseau virtuel cible**. Effectuez votre choix dans la liste de tous les réseaux virtuels dans l’emplacement cible.
    - **Groupe à haute disponibilité** Vous ne pouvez ajouter des paramètres de groupe à haute disponibilité qu’aux machines virtuelles situées dans un groupe se trouvant dans la région source.
    - **Comptes de stockage cibles** : ajoutez un compte qui est disponible.

        ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Cliquez sur **Créer une ressource cible** > **Activer la réplication**. Pendant la réplication initiale, l’état des machines virtuelles peut prendre un certain temps à s’actualiser. Cliquez sur **Actualiser** pour obtenir l’état le plus récent.

    ![Activer la réplication](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)
    
3. Une fois les machines virtuelles protégées, vérifiez leur intégrité dans **Éléments répliqués**.



## <a name="next-steps"></a>Étapes suivantes
[Découvrez](../azure-to-azure-tutorial-dr-drill.md) comment exécuter un test de basculement.

