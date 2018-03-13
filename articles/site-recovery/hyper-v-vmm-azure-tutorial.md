---
title: "Configurer la récupération d’urgence dans Azure des machines virtuelles Hyper-V locales hébergées dans des clouds VMM, avec Azure Site Recovery | Microsoft Docs"
description: "Découvrez comment configurer la récupération d’urgence dans Azure des machines virtuelles Hyper-V locales hébergées dans des clouds System Center VMM, avec le service Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 99477757c89fe2df7ae24b7ffe95c8fb7f470c93
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurer la récupération d’urgence dans Azure de machines virtuelles Hyper-V locales hébergées dans des clouds VMM

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel vous montre comment configurer la récupération d’urgence de machines virtuelles Hyper-V locales vers Azure. Le didacticiel s’applique aux machines virtuelles Hyper-V qui sont gérées par System Center Virtual Machine Manager (VMM). Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Sélectionner la source et la cible de réplication.
> * Configurer l’environnement de réplication source, y compris les composants Site Recovery locaux, et l’environnement de réplication cible.
> * Configurer le mappage réseau afin de mapper les réseaux de machines virtuelles VMM et les réseaux virtuels Azure.
> * Créer une stratégie de réplication
> * Activer la réplication pour une machine virtuelle

Il s’agit du troisième didacticiel d’une série. Ce didacticiel suppose que vous avez déjà effectué les tâches des didacticiels précédents :

1. [Préparer Azure](tutorial-prepare-azure.md)
2. [Préparer un serveur Hyper-V local](tutorial-prepare-on-premises-hyper-v.md)

Avant de commencer, [examinez l’architecture](concepts-hyper-v-to-azure-architecture.md) de ce scénario de récupération d’urgence.



## <a name="select-a-replication-goal"></a>Sélectionner un objectif de réplication

1. Dans **Tous les services** > **Coffres Recovery Services**, cliquez sur le nom du coffre utilisé dans ces didacticiels **ContosoVMVault**.
2. Dans **Prise en main**, cliquez sur **Site Recovery**. Cliquez ensuite sur **Préparer l’infrastructure**
3. Dans **Objectif de protection** > **Où se trouvent vos machines**, sélectionnez **Local**.
4. Dans **Où voulez-vous répliquer vos machines**, sélectionnez **Dans Azure**.
5. Dans **Vos machines sont-elles virtualisées**, sélectionnez **Oui, avec Hyper-V**.
6. Dans **Utilisez-vous System Center VMM**, sélectionnez **Oui**. Cliquez ensuite sur **OK**.

    ![Objectif de réplication](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)



## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Lorsque vous configurez l’environnement source, vous installez le fournisseur Azure Site Recovery et l’agent Azure Recovery Services, et inscrivez les serveurs locaux dans le coffre. 

1. Dans **Préparer l’infrastructure**, cliquez sur **Source**.
2. Dans **Préparer la source**, cliquez sur **+ VMM** pour ajouter un serveur VMM. Dans **Ajouter un serveur**, vérifiez que **Serveur System Center VMM** s’affiche dans **Type de serveur**.
3. Téléchargez le programme d’installation du fournisseur Microsoft Azure Site Recovery.
4. Téléchargez la clé d’inscription du coffre. Vous en aurez besoin lorsque vous exécuterez le programme d’installation du fournisseur. Une fois générée, la clé reste valide pendant 5 jours.
5. Téléchargez l’agent Recovery Services.

    ![Download](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installer le fournisseur sur le serveur VMM

1. Dans l’Assistant Installation du fournisseur Azure Site Recovery > **Microsoft Update**, choisissez d’utiliser Microsoft Update pour rechercher les mises à jour du fournisseur.
2. Dans **Installation**, acceptez l’emplacement d’installation par défaut du fournisseur et cliquez sur **Installer**. 
3. Après l’installation, dans l’Assistant Inscription de Microsoft Azure Site Recovery > **Paramètres de coffre**, cliquez sur **Parcourir** et, dans **Fichier de clé**, sélectionnez le fichier de clé de coffre que vous avez téléchargé.
4. Spécifiez l’abonnement Azure Site Recovery et le nom du coffre (**ContosoVMVault**). Spécifiez un nom convivial pour le serveur VMM afin de l’identifier dans le coffre.
5. Dans **Paramètres de proxy**, sélectionnez **Se connecter directement à Azure Site Recovery sans proxy**.
6. Acceptez l’emplacement par défaut du certificat utilisé pour chiffrer les données. Les données chiffrées sont déchiffrées pendant le basculement.
7. Dans **Synchroniser les métadonnées du cloud**, sélectionnez **Synchronisation des métadonnées du cloud dans le portail Site Recovery**. Cette action se produit une seule fois sur chaque serveur. Cliquez ensuite sur **S’inscrire**.
8. Une fois que le serveur est inscrit dans le coffre, cliquez sur **Terminer**.

Une fois l’inscription terminée, les métadonnées du serveur sont récupérées par Azure Site Recovery et le serveur VMM s’affiche dans **Infrastructure Site Recovery**.

### <a name="install-the-recovery-services-agent"></a>Installer l’agent Azure Recovery Services

Installez l’agent sur chaque hôte Hyper-V contenant les machines virtuelles à répliquer.

1. Dans l’Assistant Installation de l’agent Microsoft Azure Recovery Services > **Vérification des prérequis**, cliquez sur **Suivant**. Tous les prérequis manquants sont automatiquement installés.
2. Dans **Paramètres d’installation**, acceptez l’emplacement d’installation et l’emplacement du cache. Le lecteur de cache doit comporter au moins 5 Go d’espace de stockage. Nous recommandons un lecteur avec au moins 600 Go d’espace libre. Cliquez ensuite sur **Installer**.
3. Dans **Installation**, lorsque l’installation est terminée, cliquez sur **Fermer** pour quitter l’Assistant.

    ![Installer l’agent](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

1. Cliquez sur **Préparer l’infrastructure** > **Cible**.
2. Sélectionnez l’abonnement et le groupe de ressources (**ContosoRG**) où créer les machines virtuelles Azure après le basculement.
3. Sélectionnez le modèle de déploiement **Resource Manager**.

Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.


## <a name="configure-network-mapping"></a>Configurer le mappage réseau

1. Dans **Infrastructure Site Recovery** > **Mappages réseau** > **Mappage réseau**, cliquez sur l’icône**+Mappage réseau**.
2. Dans la zone **Ajouter un mappage réseau**, sélectionnez le serveur VMM source. Sélectionnez **Azure** comme cible.
3. Vérifiez l’abonnement et le modèle de déploiement après le basculement.
4. Dans **Réseau Source**, sélectionnez le réseau de machines virtuelles locales sources.
5. Dans **Réseau cible**, choisissez le réseau Azure dans lequel les machines virtuelles Azure de réplica se trouveront, une fois créées après le basculement. Cliquez ensuite sur **OK**.

    ![Mappage réseau](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurer une stratégie de réplication

1. Cliquez sur **Préparer l’infrastructure** > **Paramètres de réplication** > **+Créer et associer**.
2. Dans **Créer et associer une stratégie**, indiquez le nom de la stratégie, **ContosoReplicationPolicy**.
3. Laissez les paramètres par défaut et cliquez sur **OK**.
    - **Fréquence de copie** indique que les données delta (après la réplication initiale) sont répliquées toutes les cinq minutes.
    - **Rétention des points de récupération** indique que la fenêtre de rétention de chaque point de récupération est de deux heures.
    - **Fréquence des instantanés de cohérence des applications** indique que les points de récupération contenant des instantanés de cohérence des applications sont créés toutes les heures.
    - **Heure de début de la réplication initiale** indique que la réplication initiale démarre immédiatement.
    - **Chiffrer les données stockées sur Azure** : le paramètre par défaut **Désactivé** indique que les données au repos dans Azure ne sont pas chiffrées.
4. Après avoir créé la stratégie, cliquez sur **OK**. Lorsque vous créez une stratégie, elle est automatiquement associée au cloud VMM.

## <a name="enable-replication"></a>Activer la réplication

1. Dans **Répliquer l’application**, cliquez sur **Source**. 
2. Dans **Source**, sélectionnez le cloud VMM. Cliquez ensuite sur **OK**.
3. Dans **Cible**, vérifiez qu’Azure est la cible, vérifiez l’abonnement du coffre et sélectionnez le modèle de déploiement **Resource Manager**.
4. Sélectionnez le compte de stockage **contosovmsacct1910171607** et le réseau Azure **ContosoASRnet**.
5. Dans **Machines virtuelles** > **Sélectionner**, sélectionnez les machines virtuelles à répliquer. Cliquez ensuite sur **OK**.

 Vous pouvez suivre la progression de l’action **Activer la protection** dans **Travaux** > **Travaux Site Recovery**. Lorsque le travail de **finalisation de la protection** est terminé, la réplication initiale est également terminée et la machine virtuelle est prête à être basculée.


## <a name="next-steps"></a>Étapes suivantes
[Exécuter une simulation de récupération d’urgence](tutorial-dr-drill-azure.md)
