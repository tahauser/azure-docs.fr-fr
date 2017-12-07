---
title: "Vue d’ensemble des coffres Recovery Services | Microsoft Docs"
description: "Vue d’ensemble et comparaison entre les coffres Recovery Services et les coffres de sauvegarde Azure."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.assetid: 38d4078b-ebc8-41ff-9bc8-47acf256dc80
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/15/2017
ms.author: markgal;arunak;sogup
ms.openlocfilehash: ad685744595ab86def8d226d7c2fb5455af98da4
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="recovery-services-vaults-overview"></a>Vue d’ensemble des coffres Recovery Services

Cet article décrit les fonctionnalités d’un coffre Recovery Services. Un coffre Recovery Services est une entité de stockage dans Azure qui héberge des données. Les données sont généralement des copies de données ou des informations de configuration pour des machines virtuelles, des charges de travail, des serveurs ou des stations de travail. Vous pouvez utiliser des coffres Recovery Services afin de stocker des données de sauvegarde pour divers services Azure tels que des machines virtuelles IaaS (Windows ou Linux) et des bases de données SQL Azure. Les coffres Recovery Services prennent en charge System Center DPM, Windows Server, le serveur de sauvegarde Azure et bien plus. Les coffres Recovery Services facilitent l’organisation de vos données de sauvegarde, tout en réduisant le temps de gestion. 

Dans un abonnement Azure, vous pouvez créer jusqu’à 25 coffres Recovery Services par région.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Comparaison entre les coffres Recovery Services et les coffres de sauvegarde

Si vous possédez toujours des coffres de sauvegarde, ils sont automatiquement mis à niveau vers des coffres Recovery Services. Depuis novembre 2017, tous les coffres de sauvegarde ont été mis à niveau vers des coffres Recovery Services. 

Les coffres Recovery Services sont basés sur le modèle Azure Resource Manager d’Azure, tandis que les coffres de sauvegarde étaient basés sur le modèle Azure Service Manager. Lorsque vous mettez à niveau un coffre de sauvegarde vers un coffre Recovery Services, les données de sauvegarde restent intactes pendant et après le processus de mise à niveau. Les coffres Recovery Services fournissent des fonctionnalités non disponibles pour les coffres de sauvegarde, telles que :

- **Davantage de fonctionnalités pour sécuriser des données sauvegarde** : avec les coffres Recovery Services, le module Sauvegarde Microsoft Azure fournit des fonctionnalités de sécurité pour protéger les sauvegardes cloud. Ces fonctionnalités de sécurité vous garantissent de pouvoir sécuriser vos sauvegardes et récupérer en toute sécurité des données même si des serveurs de production et de sauvegarde sont compromis. [En savoir plus](backup-azure-security-feature.md)

- **Surveillance centrale de votre environnement informatique hybride** : avec les coffres Recovery Services, vous pouvez surveiller non seulement vos [machines virtuelles IaaS Azure](backup-azure-manage-vms.md), mais aussi votre [ressources locales](backup-azure-manage-windows-server.md#manage-backup-items) à partir d’un portail centralisé. [En savoir plus](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Contrôle d’accès en fonction du rôle (RBAC)** : le RBAC offre un contrôle précis de la gestion des accès dans Azure. [Azure offre différents rôles intégrés](../active-directory/role-based-access-built-in-roles.md), et Sauvegarde Microsoft Azure comprend trois [rôles intégrés pour gérer les points de récupération](backup-rbac-rs-vault.md). Les coffres Recovery Services sont compatibles avec RBAC, ce qui limite les accès en sauvegarde et en restauration à l’ensemble défini des rôles d’utilisateur. [En savoir plus](backup-rbac-rs-vault.md)

- **Protéger toutes les configurations des machines virtuelles Azure** : les coffres Recovery Services protègent les machines virtuelles basées sur Resource Manager, notamment les disques Premium, les disques gérés et les machines virtuelles chiffrées. La mise à niveau d’un coffre de sauvegarde vers un coffre Recovery Services vous donne la possibilité de mettre à niveau vos machines virtuelles basées sur Service Manager vers des machines virtuelles basées sur Resource Manager. Lors de la mise à niveau du coffre, vous pouvez conserver vos points de récupération de machines virtuelles basées sur Service Manager et configurer la protection pour de machines virtuelles (compatibles avec Resource Manager) mises à niveau. [En savoir plus](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauration instantanée de machines virtuelles IaaS** : les coffres Recovery Services vous permettent de restaurer des fichiers et dossiers d’une machine virtuelle IaaS sans restaurer la machine virtuelle entière, ce qui permet d’accélérer les temps de restauration. La restauration instantanée de machines virtuelles IaaS est disponible pour les machines virtuelles Windows et Linux. [En savoir plus](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Gestion des coffres Recovery Services dans le portail
La création et la gestion de coffres Recovery Services dans le portail Azure sont faciles car le service de sauvegarde est intégré dans le menu Paramètres d’Azure. Cette intégration signifie que vous pouvez créer ou gérer un coffre Recovery Services *dans le contexte du service cible*. Par exemple, pour afficher les points de récupération pour une machine virtuelle, sélectionnez celle-ci, puis cliquez sur **Sauvegarde** dans le menu Paramètres. Les informations de sauvegarde spécifiques de cette machine virtuelle s’affichent. Dans l’exemple suivant, **ContosoVM** est le nom de la machine virtuelle. **ContosoVM-demovault** est le nom du coffre Recovery Services. Vous n’avez pas besoin de mémoriser le nom du coffre Recovery Services qui stocke les points de récupération, car vous pouvez accéder à ces informations à partir de la machine virtuelle.  

![coffre recovery services, détails, machine virtuelle](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context.png)

Si plusieurs serveurs sont protégés à l’aide du même coffre Recovery Services, il peut être plus logique d’examiner celui-ci. Vous pouvez rechercher tous les coffres de Recovery Services dans l’abonnement, puis choisir dans la liste.

Les sections suivantes contiennent des liens vers des articles qui expliquent comment utiliser un coffre Recovery Services dans le cadre de chaque type d’activité.

### <a name="back-up-data"></a>Sauvegarder des données
- [Sauvegarder une machine virtuelle Azure](backup-azure-vms-first-look-arm.md)
- [Sauvegarder des stations de travail Windows Server ou Windows](backup-try-azure-backup-in-10-mins.md)
- [Sauvegarder des charges de travail DPM sur Azure](backup-azure-dpm-introduction.md)
- [Préparer la sauvegarde des charges de travail à l’aide du serveur de sauvegarde Azure](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Gérer les points de récupération
- [Gérer les sauvegardes de machines virtuelles Azure](backup-azure-manage-vms.md)
- [Gestion des fichiers et dossiers](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Restaurer des données à partir du coffre
- [Récupérer des fichiers individuels d’une machine virtuelle Azure](backup-azure-restore-files-from-vm.md)
- [Restaurer une machine virtuelle Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Sécuriser le coffre
- [Sécurisation des données de sauvegarde cloud dans des coffres Recovery Services](backup-azure-security-feature.md)



## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants pour :</br>
[Sauvegarder une machine virtuelle IaaS](backup-azure-arm-vms-prepare.md)</br>
[Sauvegarder un serveur de sauvegarde Azure](backup-azure-microsoft-azure-backup.md)</br>
[Sauvegarder un serveur Windows Server](backup-configure-vault.md)
