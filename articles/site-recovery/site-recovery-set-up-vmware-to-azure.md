---
title: "Configurer l’environnement source (VMware vers Azure) | Microsoft Docs"
description: "Cet article décrit la procédure de configuration de votre environnement local de manière à lancer la réplication de machines virtuelles VMware dans Azure."
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: 2b6b0e5cc95f28b5596e7e898f5f035e3647d9c5
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Configurer l’environnement source (VMware vers Azure)
> [!div class="op_single_selector"]
> * [VMware vers Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Physique vers Azure](./site-recovery-set-up-physical-to-azure.md)

Cet article décrit la procédure de configuration de votre environnement local source de manière à répliquer les machines virtuelles exécutées sur VMware dans Azure. Il comprend des étapes pour la sélection de votre scénario de réplication, la configuration d’un ordinateur local en tant que le serveur de configuration Site Recovery et la détection automatique des machines virtuelles locales. 

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous avez déjà effectué les opérations suivantes :
- [Configurer les ressources](tutorial-prepare-azure.md) dans le [portail Azure](http://portal.azure.com).
- [Installer localement VMware](tutorial-prepare-on-premises-vmware.md), y compris un compte dédié pour la détection automatique.



## <a name="choose-your-protection-goals"></a>Sélectionner vos objectifs en matière de protection

1. Dans le portail Azure, accédez au panneau des coffres **Recovery Services**, puis sélectionnez votre coffre.
2. Dans le menu Ressource du coffre, sélectionnez **Prise en main** > **Site Recovery** > **Étape 1 : préparer l’infrastructure** > **Objectif de protection**.

    ![Sélectionner des objectifs](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. Dans **Objectif de la protection**, sélectionnez **Vers Azure** puis choisissez **Oui, avec hyperviseur vSphere VMware**. Cliquez ensuite sur **OK**.

    ![Sélectionner des objectifs](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Configurer le serveur de configuration

Vous configurez le serveur de configuration en tant que machine virtuelle VMware locale et utilisez un modèle OVF (Open Virtualization Format). [En savoir plus](concepts-vmware-to-azure-architecture.md) sur les composants qui vont être installés sur la machine virtuelle VMware. 

1. Prenez connaissance des [conditions préalables](how-to-deploy-configuration-server.md#prerequisites) pour le déploiement du serveur de configuration. [Vérifiez les chiffres de capacité](how-to-deploy-configuration-server.md#capacity-planning) pour le déploiement.
2. [Téléchargez](how-to-deploy-configuration-server.md#download-the-template) et [importez](how-to-deploy-configuration-server.md#import-the-template-in-vmware) le modèle OVF (how-to-deploy-configuration-server.md) pour configurer une machine virtuelle VMware locale exécutée sur le serveur de configuration.
3. Activez la machine virtuelle VMware et [inscrivez-la](how-to-deploy-configuration-server.md#register-the-configuration-server) dans le coffre Recovery Services.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Ajouter le compte VMware pour la découverte automatique

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Se connecter au serveur VMware

Pour permettre à Azure Site Recovery de détecter les machines virtuelles en cours d’exécution dans votre environnement local, vous devez connecter votre serveur VMware vCenter ou vos hôtes ESXi vSphere à Site Recovery.

Sélectionnez **+vCenter** pour connecter un serveur VMware vCenter ou un ordinateur hôte VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Problèmes courants
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Étapes suivantes
[Configurez votre environnement cible](./site-recovery-prepare-target-vmware-to-azure.md) dans Azure.
