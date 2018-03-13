---
title: "Configurer la récupération d’urgence des machines virtuelles Hyper-V locales (sans VMM) dans Azure avec Azure Site Recovery | Microsoft Docs"
description: "Découvrez comment configurer la récupération d’urgence de machines virtuelles Hyper-V locales (sans VMM) dans Azure avec le service Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/14/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: e7ddb3046b0725b3afcea2ed6a533388a89cf306
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurer la récupération d’urgence de machines virtuelles Hyper-V locales vers Azure

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence en gérant et en coordonnant la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel vous montre comment configurer la récupération d’urgence de machines virtuelles Hyper-V locales vers Azure. Le didacticiel s’applique aux machines virtuelles Hyper-V qui ne sont pas gérées par System Center Virtual Machine Manager (VMM). Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Sélectionner la source et la cible de réplication.
> * Configurer l’environnement de réplication source, y compris les composants Site Recovery locaux, et l’environnement de réplication cible.
> * Créer une stratégie de réplication.
> * Activer la réplication pour une machine virtuelle.

Il s’agit du troisième didacticiel d’une série. Ce didacticiel suppose que vous avez déjà effectué les tâches des didacticiels précédents :

1. [Préparer Azure](tutorial-prepare-azure.md)
2. [Préparer un serveur Hyper-V local](tutorial-prepare-on-premises-hyper-v.md)

Avant de commencer, [examinez l’architecture](concepts-hyper-v-to-azure-architecture.md) de ce scénario de récupération d’urgence.

## <a name="select-a-replication-goal"></a>Sélectionner un objectif de réplication


1. Dans **Tous les services** > **Coffres Recovery Services**, sélectionnez le coffre que nous avons préparé dans le didacticiel précédent, **ContosoVMVault**.
2. Dans **Prise en main**, cliquez sur **Site Recovery**. Cliquez ensuite sur **Préparer l’infrastructure**
3. Dans **Objectif de protection** > **Où se trouvent vos machines**, sélectionnez **Local**.
4. Dans **Où voulez-vous répliquer vos machines**, sélectionnez **Dans Azure**.
5. Dans **Vos machines sont-elles virtualisées**, sélectionnez **Non**. Cliquez ensuite sur **OK**.

    ![Objectif de réplication](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Pour configurer l’environnement source, vous ajoutez des hôtes Hyper-V sur un site Hyper-V, téléchargez et installez le fournisseur Azure Site Recovery et l’agent Azure Recovery Services, et inscrivez le site Hyper-V dans le coffre. 

1. Dans **Préparer l’infrastructure**, cliquez sur **Source**.
2. Cliquez sur **+Site Hyper-V** et spécifiez le nom du site créé dans le didacticiel précédent, **ContosoHyperVSite**.
3. Cliquez sur **+Serveur Hyper-V**.
4. Téléchargez le fichier de configuration du fournisseur.
5. Téléchargez la clé d’inscription du coffre. Vous en aurez besoin lorsque vous exécuterez le programme d’installation du fournisseur. Une fois générée, la clé reste valide pendant 5 jours.

    ![Télécharger le fournisseur](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Installer le fournisseur

Exécutez le fichier de configuration de fournisseur (AzureSiteRecoveryProvider.exe) sur chaque hôte Hyper-V ajouté au site **ContosoHyperVSite**. Le programme d’installation installe le fournisseur Azure Site Recovery et l’agent Recovery Services sur chaque hôte Hyper-V.

1. Dans l’Assistant Installation du fournisseur Azure Site Recovery > **Microsoft Update**, choisissez d’utiliser Microsoft Update pour rechercher les mises à jour du fournisseur.
2. Dans **Installation**, acceptez l’emplacement d’installation par défaut pour le fournisseur et l’agent, et cliquez sur **Installer**.
3. Après l’installation, dans l’Assistant Inscription de Microsoft Azure Site Recovery > **Paramètres de coffre**, cliquez sur **Parcourir** et, dans **Fichier de clé**, sélectionnez le fichier de clé de coffre que vous avez téléchargé. 
4. Spécifiez l’abonnement Azure Site Recovery, le nom du coffre (**ContosoVMVault**) et le site Hyper-V (**ContosoHyperVSite**) auquel appartient le serveur Hyper-V.
5. Dans **Paramètres de proxy**, sélectionnez **Se connecter directement à Azure Site Recovery sans proxy**.
6. Dans **Inscription**, une fois que le serveur est inscrit dans le coffre, cliquez sur **Terminer**.

Les métadonnées du serveur Hyper-V sont récupérées par Azure Site Recovery et le serveur s’affiche dans **Infrastructure Site Recovery** > **Hôtes Hyper-V**. Ce processus peut prendre jusqu’à 30 minutes.


## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez et vérifiez les ressources cibles. 

1. Cliquez sur **Préparer l’infrastructure** > **Cible**.
2. Sélectionnez l’abonnement et le groupe de ressources **ContosoRG** où créer les machines virtuelles Azure après le basculement.
3. Sélectionnez le modèle de déploiement **Resource Manager**.

Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.


## <a name="set-up-a-replication-policy"></a>Configurer une stratégie de réplication

> [!NOTE]
> Concernant Hyper-V pour les stratégies de réplication Azure, l’option de fréquence de copie de 15 minutes passe à 5 minutes, et des paramètres de fréquence de copie de 30 secondes. Les stratégies de réplication utilisant une fréquence de copie de 15 minutes utiliseront automatiquement une fréquence de copie de 5 minutes. Les options de fréquence de copie de 5 minutes et de 30 secondes offrent de meilleures performances de réplication et de meilleurs objectifs de point de récupération par rapport à une fréquence de copie de 15 minutes, avec un impact minimal sur le volume de transfert de données et l’utilisation de la bande passante.

1. Cliquez sur **Préparer l’infrastructure** > **Paramètres de réplication** > **+Créer et associer**.
2. Dans **Créer et associer une stratégie**, indiquez le nom de la stratégie, **ContosoReplicationPolicy**.
3. Laissez les paramètres par défaut et cliquez sur **OK**.
    - **Fréquence de copie** indique que les données delta (après la réplication initiale) sont répliquées toutes les cinq minutes.
    - **Rétention des points de récupération** indique que la fenêtre de rétention de chaque point de récupération est de deux heures.
    - **Fréquence des instantanés de cohérence des applications** indique que les points de récupération contenant des instantanés de cohérence des applications sont créés toutes les heures.
    - **Heure de début de la réplication initiale** indique que la réplication initiale démarre immédiatement.
4. Après avoir créé la stratégie, cliquez sur **OK**. Quand vous créez une stratégie, elle est automatiquement associée au site Hyper-V spécifié (**ContosoHyperVSite**)

    ![Stratégie de réplication](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>Activer la réplication


1. Dans **Répliquer l’application**, cliquez sur **Source**. 
2. Dans **Source**, sélectionnez le site **ContosoHyperVSite**. Cliquez ensuite sur **OK**.
3. Dans **Cible**, vérifiez qu’Azure est la cible, et vérifiez l’abonnement du coffre et le modèle de déploiement **Resource Manager**.
4. Sélectionnez le compte de stockage **contosovmsacct1910171607** créé dans le didacticiel précédent pour les données répliquées et le réseau **ContosoASRnet** où trouver les machines virtuelles Azure après le basculement.
5. Dans **Machines virtuelles** > **Sélectionner**, sélectionnez les machines virtuelles à répliquer. Cliquez ensuite sur **OK**.

 Vous pouvez suivre la progression de l’action **Activer la protection** dans **Travaux** > **Travaux Site Recovery**. Lorsque le travail de **finalisation de la protection** est terminé, la réplication initiale est également terminée et la machine virtuelle est prête à être basculée.

## <a name="next-steps"></a>Étapes suivantes
[Exécuter une simulation de récupération d’urgence](tutorial-dr-drill-azure.md)
