---
title: Migrer des machines sur site vers Azure avec Azure Site Recovery | Microsoft Docs
description: "Cet article explique comment migrer des machines sur site vers Azure à l’aide d’Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/27/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: e268a6d53a9f0b001ad0da8c9ce7ea1a9046960c
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrer des machines sur site vers Azure

Le service [Azure Site Recovery](../site-recovery-overview.md) gère et orchestre la réplication, le basculement et la restauration automatique des machines sur site et des machines virtuelles Azure.

Ce didacticiel vous montre comment migrer des machines virtuelles sur site et des serveurs physiques vers Azure à l’aide de Site Recovery. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer les prérequis pour le déploiement
> * Créer un coffre Recovery Services pour Site Recovery
> * Déployer des serveurs d’administration sur site
> * Configurer une stratégie de réplication et activer la réplication
> * Exécuter une simulation de récupération d’urgence pour vérifier que tout fonctionne
> * Exécuter un basculement unique vers Azure

## <a name="overview"></a>Vue d'ensemble

Vous migrez une machine en activant la réplication pour celle-ci puis en effectuant un basculement vers Azure.


## <a name="prerequisites"></a>Composants requis

Voici ce que vous devez faire pour ce didacticiel.

- [Préparer](../tutorial-prepare-azure.md) des ressources Azure, notamment un abonnement Azure, un réseau virtuel Azure et un compte de stockage.
- [Préparer](../tutorial-prepare-on-premises-vmware.md) des serveurs VMware et des machines virtuelles sur site.
- Notez que les appareils exportés par les pilotes paravirtualisés ne sont pas pris en charge.


## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

[!INCLUDE [site-recovery-create-vault](../../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Sélectionner un objectif de protection

Sélectionnez les éléments à répliquer et l’emplacement de la réplication.
1. Cliquez sur **Coffres Recovery Services** > coffre.
2. Dans le menu Ressource, cliquez sur **Site Recovery** > **Préparer l’infrastructure** > **Objectif de protection**.
3. Dans **Objectif de protection**, sélectionnez :
    - **VMware** : sélectionnez **Vers Azure** > **Oui, avec hyperviseur vSphere VMWare**.
    - **Machine physique** : sélectionnez **Vers Azure** > **Non virtualisé / Autre**.
    - **Hyper-V** : sélectionnez **Vers Azure** > **Oui, avec Hyper-V**.


## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

- [Configurez](../tutorial-vmware-to-azure.md#set-up-the-source-environment) l’environnement source pour les machines virtuelles VMware.
- [Configurez](../tutorial-physical-to-azure.md#set-up-the-source-environment) l’environnement source pour les serveurs physiques.
- [Configurez](../tutorial-hyper-v-to-azure.md#set-up-the-source-environment) l’environnement source pour les machines virtuelles Hyper-V.

## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez et vérifiez les ressources cibles.

1. Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez l’abonnement Azure à utiliser.
2. Spécifiez le modèle de déploiement cible.
3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes de stockage Azure compatibles.

## <a name="create-a-replication-policy"></a>Créer une stratégie de réplication

- [Configurez une stratégie de réplication](../tutorial-vmware-to-azure.md#create-a-replication-policy) pour les machines virtuelles VMware.


## <a name="enable-replication"></a>Activer la réplication

- [Activez la réplication](../tutorial-vmware-to-azure.md#enable-replication) pour les machines virtuelles VMware.


## <a name="run-a-test-migration"></a>Exécuter un test de migration

Exécutez un [test de basculement](../tutorial-dr-drill-azure.md) vers Azure afin de vérifier que tout fonctionne bien.


## <a name="migrate-to-azure"></a>Migrer vers Azure

Exécutez un basculement pour les machines que vous souhaitez migrer.

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine > **Basculement**.
2. Dans **Basculement**, sélectionnez un **point de récupération** vers lequel basculer. Sélectionnez le point de récupération le plus récent.
3. Le paramètre de clé de chiffrement ne s’applique pas à ce scénario.
4. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.
5. Vérifiez que la machine virtuelle Azure s’affiche dans Azure comme prévu.
6. Dans **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle > **Terminer la migration**. Cette opération termine le processus de migration, interrompt la réplication pour la machine virtuelle et arrête la facturation Site Recovery pour la machine virtuelle.

    ![Terminer la migration](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **N’annulez pas un basculement en cours** : la réplication de la machine virtuelle est arrêtée avant que le basculement démarre. Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.

Dans certains scénarios, le basculement nécessite un traitement supplémentaire qui dure environ huit à dix minutes. Vous constaterez peut-être des délais de basculement plus longs pour les serveurs physiques, les machines virtuelles VMware Linux, les machines virtuelles VMware pour lesquelles le service DHCP n’est pas activé, et les machines virtuelles VMware qui ne disposent pas des pilotes de démarrage suivants : storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Réplication des machines virtuelles Azure vers une autre région après migration vers Azure](site-recovery-azure-to-azure-after-migration.md)
