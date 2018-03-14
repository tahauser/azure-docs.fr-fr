---
title: " Gérer un serveur de processus en cours d’exécution dans Azure (Resource Manager) | Microsoft Docs"
description: Cet article explique comment configurer un serveur de processus de restauration automatique (Resource Manager) dans Azure.
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
ms.date: 12/19/2017
ms.author: anoopkv
ms.openlocfilehash: 1b75acb13ac4c8990f99f7454a6de5483f6ca2f1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>Gérer un serveur de processus en cours d’exécution dans Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [Classic ](./site-recovery-vmware-setup-azure-ps-classic.md)

Lors de la restauration automatique, il est recommandé de déployer le serveur de processus dans Azure s’il existe une latence élevée entre le réseau virtuel Azure et votre réseau local. Cet article explique comment installer, configurer et gérer les serveurs de processus en cours d’exécution dans Azure.

> [!NOTE]
> Consultez cet article si vous avez utilisé **Resource Manager** comme modèle de déploiement pour les machines virtuelles lors du basculement. Si vous avez utilisé le modèle de déploiement **Classic**, suivez les étapes de la rubrique [Guide pratique pour installer et configurer un serveur de processus de restauration automatique (Classic)](./site-recovery-vmware-setup-azure-ps-classic.md)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Déployer un serveur de processus dans Azure
1. Sous Coffre > **Infrastructure Site Recovery** (sous le titre « Gérer ») > **Serveurs de configuration** (sous le titre « Pour VMware et les machines physiques »), sélectionnez le serveur de configuration.
2. Dans la page des détails du serveur de configuration qui s’ouvre, cliquez sur « +Serveur de processus »

  ![Galerie Ajouter un serveur de processus](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  Sur la page **Ajouter un serveur de processus**, sélectionnez les valeurs suivantes

  ![Élément de la galerie Ajouter un serveur de processus](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**Nom du champ**|**Valeur**|
|-|-|
|Indique où vous souhaitez déployer votre serveur de processus|Sélectionnez la valeur **Déployer un serveur de processus de restauration automatique dans Azure** |
|Abonnement|Sélectionnez l’abonnement Azure dans lequel vous avez basculé les machines virtuelles|
|Groupe de ressources|Vous pouvez créer un groupe de ressources afin d’y déployer ce serveur de processus, ou choisir de déployer le serveur de processus dans un groupe de ressources existant|
|Lieu|Sélectionnez le centre de données Azure dans lequel les machines virtuelles ont été basculées|
|Réseau Azure|Sélectionnez le réseau virtuel Azure (VNet) dans lequel les machines virtuelles ont été basculées. Si vous avez basculé des machines virtuelles dans plusieurs réseaux virtuels Azure, vous devez déployer un serveur de processus pour chaque réseau virtuel|

4. Spécifiez les autres propriétés du serveur de processus

  ![Récapitulatif Ajouter un serveur de processus](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**Nom du champ**|**Valeur**|
|-|-|
|Nom du serveur|Nom d’affichage et nom d’hôte de la machine virtuelle de votre serveur processus|
| User Name|Nom d’utilisateur qui devient un administrateur sur cette machine virtuelle|
|Compte de stockage|Nom du compte de stockage où est placé le disque virtuel de la machine virtuelle|
|Sous-réseau|Sous-réseau du réseau virtuel Azure auquel les machines virtuelles sont connectées|
| Adresse IP|Adresse IP que le serveur de processus est supposé utiliser au démarrage|
5. Cliquez sur le bouton OK pour commencer le déploiement de la machine virtuelle du serveur de processus.

> [!NOTE]
> Pour pouvoir utiliser ce serveur de processus pour la restauration automatique, vous devez l’inscrire auprès du serveur de configuration local.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Inscription du serveur de processus (en cours d’exécution dans Azure) auprès d’un serveur de Configuration (en cours d’exécution en local)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Mise à niveau du serveur de processus avec la version la plus récente.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Désinscription du serveur de processus (en cours d’exécution dans Azure) d’un serveur de Configuration (en cours d’exécution en local)

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
