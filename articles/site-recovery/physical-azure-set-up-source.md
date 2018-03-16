---
title: "Configurer l’environnement source (serveurs physiques sur Azure) | Microsoft Docs"
description: "Cet article décrit comment configurer votre environnement local pour lancer la réplication des serveurs physiques exécutant Windows ou Linux dans Azure."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 96004a70547c4bfb3a1a3bfadecb1304e4910b52
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Configurer l’environnement source (serveurs physiques sur Azure)

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

    ![Sélectionner des objectifs](./media/physical-azure-set-up-source/choose-goals.png)
3. Dans la zone **Objectif de protection**, sélectionnez **To Azure** (Vers Azure) et **Non virtualisé/Autre**, puis cliquez sur **OK**.

    ![Sélectionner des objectifs](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

1. Dans **Préparer la source**, si vous n’avez pas de serveur de configuration, cliquez sur **+Serveur de configuration** afin d’en ajouter un.

  ![Configurer la source](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Dans le panneau **Ajouter un serveur**, vérifiez que **Serveur de configuration** s’affiche dans **Type de serveur**.
4. Téléchargez le fichier d’installation unifiée de Site Recovery.
5. Téléchargez la clé d’inscription du coffre. Vous avez besoin de la clé d’inscription lorsque vous exécutez le programme d’installation unifiée. Une fois générée, la clé reste valide pendant 5 jours.

    ![Configurer la source](./media/physical-azure-set-up-source/set-source2.png)
6. Sur la machine qui vous sert de serveur de configuration, exécutez le **programme d’installation unifiée Azure Site Recovery** afin d’installer le serveur de configuration, le serveur de processus et le serveur cible maître.

#### <a name="run-azure-site-recovery-unified-setup"></a>Exécuter le programme d’installation unifiée Azure Site Recovery

> [!TIP]
> L’inscription du serveur de configuration échoue si l’horloge système de vos ordinateurs diffère de l’heure locale de plus de cinq minutes. Synchronisez votre horloge système avec un [serveur de temps](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) avant de commencer l’installation.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Le serveur de configuration peut être installé via une ligne de commande. Pour plus d’informations, consultez [Installation du serveur de configuration à l’aide des outils en ligne de commande](http://aka.ms/installconfigsrv).


## <a name="common-issues"></a>Problèmes courants

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Étapes suivantes

L’étape suivante consiste en la [configuration de votre environnement cible](physical-azure-set-up-target.md) dans Azure.
