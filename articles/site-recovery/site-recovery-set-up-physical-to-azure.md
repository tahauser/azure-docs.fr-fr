---
title: "Configurer l’environnement source (serveurs physiques sur Azure) | Microsoft Docs"
description: "Cet article décrit comment configurer votre environnement local pour lancer la réplication des serveurs physiques exécutant Windows ou Linux dans Azure."
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
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: 6c29ac32cb868cfdd72479add551cece266a5f9d
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Configurer l’environnement source (serveurs physiques sur Azure)
> [!div class="op_single_selector"]
> * [VMware vers Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Physique vers Azure](./site-recovery-set-up-physical-to-azure.md)

Cet article décrit comment configurer votre environnement local pour lancer la réplication des serveurs physiques exécutant Windows ou Linux dans Azure.

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous disposez déjà des éléments suivants :
1. Un coffre Recovery Services dans le [portail Azure](http://portal.azure.com "portail Azure").
3. Un ordinateur physique sur lequel installer le serveur de configuration.

### <a name="configuration-server-minimum-requirements"></a>Configuration minimale requise du serveur
Le tableau suivant présente la configuration minimale requise pour le matériel, le logiciel et le réseau pour un serveur de configuration.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Les serveurs proxy HTTPS ne sont pas pris en charge par le serveur de configuration.

## <a name="choose-your-protection-goals"></a>Sélectionner vos objectifs en matière de protection

1. Dans le portail Azure, accédez au panneau des coffres **Recovery Services**, puis sélectionnez votre coffre.
2. Dans le menu **Ressource** du coffre, cliquez sur **Prise en main** > **Site Recovery** > **Étape 1 : préparer l’infrastructure** > **Objectif de protection**.

    ![Sélectionner des objectifs](./media/site-recovery-set-up-physical-to-azure/choose-goals.png)
3. Dans la zone **Objectif de protection**, sélectionnez **To Azure** (Vers Azure) et **Non virtualisé/Autre**, puis cliquez sur **OK**.

    ![Sélectionner des objectifs](./media/site-recovery-set-up-physical-to-azure/physical-protection-goal.PNG)

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

1. Dans **Préparer la source**, si vous n’avez pas de serveur de configuration, cliquez sur **+Serveur de configuration** afin d’en ajouter un.

  ![Configurer la source](./media/site-recovery-set-up-physical-to-azure/plus-config-srv.png)
2. Dans le panneau **Ajouter un serveur**, vérifiez que **Serveur de configuration** s’affiche dans **Type de serveur**.
4. Téléchargez le fichier d’installation unifiée de Site Recovery.
5. Téléchargez la clé d’inscription du coffre. Vous avez besoin de la clé d’inscription lorsque vous exécutez le programme d’installation unifiée. Une fois générée, la clé reste valide pendant 5 jours.

    ![Configurer la source](./media/site-recovery-set-up-physical-to-azure/set-source2.png)
6. Sur la machine qui vous sert de serveur de configuration, exécutez le **programme d’installation unifiée Azure Site Recovery** afin d’installer le serveur de configuration, le serveur de processus et le serveur cible maître.

#### <a name="run-azure-site-recovery-unified-setup"></a>Exécuter le programme d’installation unifiée Azure Site Recovery

> [!TIP]
> L’inscription du serveur de configuration échoue si l’horloge système de vos ordinateurs diffère de l’heure locale de plus de cinq minutes. Synchronisez votre horloge système avec un [serveur de temps](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) avant de commencer l’installation.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Le serveur de configuration peut être installé via une ligne de commande. Pour plus d’informations, consultez [Installation du serveur de configuration à l’aide des outils en ligne de commande](http://aka.ms/installconfigsrv).


## <a name="common-issues"></a>Problèmes courants

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>étapes suivantes

L’étape suivante consiste en la [configuration de votre environnement cible](./site-recovery-prepare-target-physical-to-azure.md) dans Azure.
