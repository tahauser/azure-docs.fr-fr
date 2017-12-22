---
title: "Configurer l’environnement source (VMware vers Azure) | Microsoft Docs"
description: "Cet article décrit la procédure de configuration de votre environnement local de manière à lancer la réplication de machines virtuelles VMware dans Azure."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/23/2017
ms.author: anoopkv
ms.openlocfilehash: 32a3f7498d3c8891178818436e33221f91ae2f8f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Configurer l’environnement source (VMware vers Azure)
> [!div class="op_single_selector"]
> * [VMware vers Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Physique vers Azure](./site-recovery-set-up-physical-to-azure.md)

Cet article décrit la procédure de configuration de votre environnement local de manière à lancer la réplication de machines virtuelles exécutées sur VMware dans Azure.

## <a name="prerequisites"></a>Composants requis

Cet article suppose que vous avez déjà créé les éléments suivants :
- Un coffre Recovery Services dans le [portail Azure](http://portal.azure.com "portail Azure").
- Un compte dédié dans votre serveur VMware vCenter qui peut être utilisé pour la [découverte automatique](./site-recovery-vmware-to-azure.md).
- Une machine virtuelle sur laquelle installer le serveur de configuration.

## <a name="configuration-server-minimum-requirements"></a>Configuration minimale requise du serveur
Le tableau suivant présente la configuration minimale requise pour le matériel, le logiciel et le réseau pour un serveur de configuration.

> [!IMPORTANT]
> Quand vous déployez un serveur de configuration pour protéger des machines virtuelles VMware, nous vous recommandons de le déployer en tant que machine virtuelle **hautement disponible**.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Les serveurs proxy HTTPS ne sont pas pris en charge par le serveur de configuration.

## <a name="choose-your-protection-goals"></a>Sélectionner vos objectifs en matière de protection

1. Dans le portail Azure, accédez au panneau des coffres **Recovery Services**, puis sélectionnez votre coffre.
2. Dans le menu Ressource du coffre, sélectionnez **Prise en main** > **Site Recovery** > **Étape 1 : préparer l’infrastructure** > **Objectif de protection**.

    ![Sélectionner des objectifs](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. Dans **Objectif de la protection**, sélectionnez **Vers Azure** puis choisissez **Oui, avec hyperviseur vSphere VMware**. Cliquez ensuite sur **OK**.

    ![Sélectionner des objectifs](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source
La configuration de l’environnement implique deux activités principales :

- Installer et inscrire un serveur de configuration avec Site Recovery.
- Découvrir vos machines virtuelles locales par la connexion de Site Recovery à vos hôtes VMware vCenter ou vSphere EXSi locaux.

### <a name="step-1-install-and-register-a-configuration-server"></a>Étape 1 : Installer et inscrire un serveur de configuration

1. Cliquez sur **Étape 1 : Préparer l’infrastructure** > **Source**. Dans **Préparer la source**, si vous n’avez pas de serveur de configuration, cliquez sur **+Serveur de configuration** afin d’en ajouter un.

    ![Configurer la source](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. Dans le panneau **Ajouter un serveur**, vérifiez que **Serveur de configuration** s’affiche dans **Type de serveur**.
4. Téléchargez le fichier d’installation unifiée de Site Recovery.
5. Téléchargez la clé d’inscription du coffre. Vous avez besoin de la clé d’inscription lorsque vous exécutez le programme d’installation unifiée. Une fois générée, la clé reste valide pendant 5 jours.

    ![Configurer la source](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. Sur la machine qui vous sert de serveur de configuration, exécutez le **programme d’installation unifiée Azure Site Recovery** afin d’installer le serveur de configuration, le serveur de processus et le serveur cible maître.

#### <a name="run-azure-site-recovery-unified-setup"></a>Exécuter le programme d’installation unifiée Azure Site Recovery

> [!TIP]
> L’inscription du serveur de configuration échoue si l’horloge système de vos ordinateurs diffère de l’heure locale de plus de cinq minutes. Synchronisez votre horloge système avec un [serveur de temps](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) avant de commencer l’installation.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Le serveur de configuration peut être installé via la ligne de commande. Pour plus d’informations, consultez [Installation du serveur de configuration à l’aide des outils en ligne de commande](http://aka.ms/installconfigsrv).

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>Ajouter le compte VMware pour la découverte automatique

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>Étape 2 : Ajouter un serveur vCenter
Pour permettre à Azure Site Recovery de détecter les machines virtuelles en cours d’exécution dans votre environnement local, vous devez connecter votre serveur VMware vCenter ou vos hôtes ESXi vSphere à Site Recovery.

Sélectionnez **+vCenter** pour connecter un serveur VMware vCenter ou un ordinateur hôte VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Problèmes courants
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Étapes suivantes
[Configurez votre environnement cible](./site-recovery-prepare-target-vmware-to-azure.md) dans Azure.
