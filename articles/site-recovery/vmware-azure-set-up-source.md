---
title: Configurer l’environnement source pour la réplication VMware sur Azure à l’aide de Azure Site Recovery | Microsoft Docs
description: Cet article décrit la procédure de configuration de votre environnement local de manière à répliquer des machines virtuelles VMware dans Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: b2c564e8d49e39d9cdc09d3fe168388d579de70e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Configurer l’environnement source pour la réplication VMware vers Azure

Cet article décrit la procédure de configuration de votre environnement local de manière à répliquer des machines virtuelles VMware vers Azure. Il comprend des étapes pour la sélection de votre scénario de réplication, la configuration d’un ordinateur local en tant que le serveur de configuration Site Recovery et la détection automatique des machines virtuelles locales. 

## <a name="prerequisites"></a>Prérequis


Cet article suppose que vous avez déjà effectué les opérations suivantes :
- [Configurer les ressources](tutorial-prepare-azure.md) dans le [portail Azure](http://portal.azure.com).
- [Installer localement VMware](vmware-azure-tutorial-prepare-on-premises.md), y compris un compte dédié pour la détection automatique.



## <a name="choose-your-protection-goals"></a>Sélectionner vos objectifs en matière de protection

1. Dans le portail Azure, accédez au panneau des coffres **Recovery Services**, puis sélectionnez votre coffre.
2. Dans le menu Ressource du coffre, sélectionnez **Prise en main** > **Site Recovery** > **Étape 1 : préparer l’infrastructure** > **Objectif de protection**.

    ![Sélectionner des objectifs](./media/vmware-azure-set-up-source/choose-goals.png)
3. Dans **Objectif de la protection**, sélectionnez **Vers Azure** puis choisissez **Oui, avec hyperviseur vSphere VMware**. Cliquez ensuite sur **OK**.

    ![Sélectionner des objectifs](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Configurer le serveur de configuration

Vous configurez le serveur de configuration en tant que machine virtuelle VMware locale et utilisez un modèle OVF (Open Virtualization Format). [En savoir plus](concepts-vmware-to-azure-architecture.md) sur les composants qui vont être installés sur la machine virtuelle VMware. 

1. Prenez connaissance des [conditions préalables](vmware-azure-deploy-configuration-server.md#prerequisites) pour le déploiement du serveur de configuration.
2. [Vérifiez les chiffres de capacité](vmware-azure-deploy-configuration-server.md#capacity-planning) pour le déploiement.
3. [Téléchargez](vmware-azure-deploy-configuration-server.md#download-the-template) et [importez](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) le modèle OVF (how-to-deploy-configuration-server.md) pour configurer une machine virtuelle VMware locale exécutée sur le serveur de configuration.
4. Activez la machine virtuelle VMware et [inscrivez-la](vmware-azure-deploy-configuration-server.md#register-the-configuration-server) dans le coffre Recovery Services.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Ajouter le compte VMware pour la découverte automatique

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Se connecter au serveur VMware

Pour permettre à Azure Site Recovery de détecter les machines virtuelles en cours d’exécution dans votre environnement local, vous devez connecter votre serveur VMware vCenter ou vos hôtes ESXi vSphere à Site Recovery.

Sélectionnez **+vCenter** pour connecter un serveur VMware vCenter ou un ordinateur hôte VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Problèmes courants
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Étapes suivantes
[Configurez votre environnement cible](./vmware-azure-set-up-target.md) dans Azure.
