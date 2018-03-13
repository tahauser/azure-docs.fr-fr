---
title: Migrer des machines sur site vers Azure avec Azure Site Recovery | Microsoft Docs
description: "Cet article explique comment migrer des machines sur site vers Azure à l’aide d’Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 656ba02401d9ba610d0ebe33a683164af0b871f0
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrer des machines sur site vers Azure

Outre l’utilisation du service [Azure Site Recovery](site-recovery-overview.md) pour gérer et orchestrer la récupération d’urgence des ordinateurs locaux et des machines virtuelles Azure dans le cadre de la continuité d’activité et de la récupération d’urgence, vous pouvez utiliser Site Recovery pour gérer la migration des machines locales sur Azure.


Ce didacticiel vous montre comment migrer des machines virtuelles locales et des serveurs physiques vers Azure. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Sélectionner un objectif de réplication
> * Configurer l’environnement cible et source
> * Configurer une stratégie de réplication
> * Activer la réplication
> * Exécutez un test de migration afin de vérifier que tout fonctionne bien.
> * Exécuter un basculement unique vers Azure

Il s’agit du troisième didacticiel d’une série. Ce didacticiel suppose que vous avez déjà effectué les tâches des didacticiels précédents :

1. [Préparer Azure](tutorial-prepare-azure.md)
2. Préparez les serveurs [VMware](vmware-azure-tutorial-prepare-on-premises.md) locaux ou les serveurs Hyper-V.

Avant de commencer, il est utile d’examiner les architectures [VMware](vmware-azure-architecture.md) et [Hyper-V](hyper-v-azure-architecture.md) pour la récupération d’urgence.


## <a name="prerequisites"></a>Prérequis

Les appareils exportés par les pilotes paravirtualisés ne sont pas pris en charge.


## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

1. Connectez-vous au [portail Azure](https://portal.azure.com) > **Recovery Services**.
2. Cliquez sur **Créer une ressource** > **Surveillance + gestion** > **Backup and Site Recovery**.
3. Dans **Nom**, indiquez le nom convivial **ContosoVMVault**. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié.
4. Créez un groupe de ressources **ContosoRG**.
5. Spécifiez une région Azure. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Tarification de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Pour accéder rapidement au coffre à partir du tableau de bord, cliquez sur **Épingler au tableau de bord**, puis sur **Créer**.

   ![Nouveau coffre](./media/migrate-tutorial-on-premises-azure/onprem-to-azure-vault.png)

Le nouveau coffre est ajouté à la zone **Tableau de bord** dans **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.



## <a name="select-a-replication-goal"></a>Sélectionner un objectif de réplication

Sélectionnez les éléments à répliquer et l’emplacement de la réplication.
1. Cliquez sur **Coffres Recovery Services** > coffre.
2. Dans le menu Ressource, cliquez sur **Site Recovery** > **Préparer l’infrastructure** > **Objectif de protection**.
3. Dans **Objectif de protection**, sélectionnez les composants à migrer.
    - **VMware** : sélectionnez **Vers Azure** > **Oui, avec hyperviseur vSphere VMWare**.
    - **Machine physique** : sélectionnez **Vers Azure** > **Non virtualisé / Autre**.
    - **Hyper-V** : sélectionnez **Vers Azure** > **Oui, avec Hyper-V**. Si des machines virtuelles Hyper-V sont gérées par VMM, sélectionnez **Oui**.


## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

- [Configurez](vmware-azure-tutorial.md#set-up-the-source-environment) l’environnement source pour les machines virtuelles VMware.
- [Configurez](physical-azure-disaster-recovery.md#set-up-the-source-environment) l’environnement source pour les serveurs physiques.
- [Configurez](hyper-v-azure-tutorial.md#set-up-the-source-environment) l’environnement source pour les machines virtuelles Hyper-V.

## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez et vérifiez les ressources cibles.

1. Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez l’abonnement Azure à utiliser.
2. Spécifiez le modèle de déploiement Resource Manager.
3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.

## <a name="set-up-a-replication-policy"></a>Configurer une stratégie de réplication

- [Configurez une stratégie de réplication](vmware-azure-tutorial.md#create-a-replication-policy) pour les machines virtuelles VMware.
- [Configurez une stratégie de réplication](physical-azure-disaster-recovery.md#create-a-replication-policy) pour les serveurs physiques.
- [Configurez une stratégie de réplication](hyper-v-azure-tutorial.md#set-up-a-replication-policy) pour les machines virtuelles Hyper-V.


## <a name="enable-replication"></a>Activer la réplication

- [Activez la réplication](vmware-azure-tutorial.md#enable-replication) pour les machines virtuelles VMware.
- [Activez la réplication](physical-azure-disaster-recovery.md#enable-replication) des serveurs physiques.
- Activez la réplication des machines virtuelles Hyper-V [avec](hyper-v-vmm-azure-tutorial.md#enable-replication) ou [sans VMM](hyper-v-azure-tutorial.md#enable-replication).


## <a name="run-a-test-migration"></a>Exécuter un test de migration

Exécutez un [test de basculement](tutorial-dr-drill-azure.md) vers Azure afin de vérifier que tout fonctionne bien.


## <a name="migrate-to-azure"></a>Migrer vers Azure

Exécutez un basculement pour les machines que vous souhaitez migrer.

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine > **Basculement**.
2. Dans **Basculement**, sélectionnez un **point de récupération** vers lequel basculer. Sélectionnez le point de récupération le plus récent.
3. Le paramètre de clé de chiffrement ne s’applique pas à ce scénario.
4. Sélectionnez **Arrêter la machine avant de commencer le basculement**. Site Recovery tenter d’arrêter les machines virtuelles sources avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.
5. Vérifiez que la machine virtuelle Azure s’affiche dans Azure comme prévu.
6. Dans **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle > **Terminer la migration**. Cette opération termine le processus de migration, interrompt la réplication pour la machine virtuelle et arrête la facturation Site Recovery pour la machine virtuelle.

    ![Terminer la migration](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **N’annulez pas un basculement en cours** : la réplication de la machine virtuelle est arrêtée avant que le basculement démarre. Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.

Dans certains scénarios, le basculement nécessite un traitement supplémentaire qui dure environ huit à dix minutes. Vous constaterez peut-être des délais de basculement plus longs pour les serveurs physiques, les machines virtuelles VMware Linux, les machines virtuelles VMware pour lesquelles le service DHCP n’est pas activé, et les machines virtuelles VMware qui ne disposent pas des pilotes de démarrage suivants : storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez migré des machines virtuelles locales vers des machines virtuelles Azure. Vous pouvez maintenant configurer la récupération d’urgence pour les machines virtuelles Azure.

> [!div class="nextstepaction"]
> [Configurez la récupération d’urgence](azure-to-azure-replicate-after-migration.md) pour des machines virtuelles Azure après la migration à partir d’un site local.
