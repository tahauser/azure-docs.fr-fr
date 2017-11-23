---
title: Sauvegarder des charges de travail DPM sur le portail Azure Classic | Microsoft Docs
description: "Présentation de la sauvegarde de serveurs DPM à l'aide du service Azure Backup"
services: backup
documentationcenter: 
author: Nkolli1
manager: shreeshd
editor: 
keywords: System Center Data Protection Manager, Data Protection Manager, sauvegarde DPM
ms.assetid: 8f23972b-d167-4231-b331-e198db3b18b4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: nkolli;giridham;markgal
ms.openlocfilehash: 7f13739c2d3f7fba700b649f27e02913481ca5e5
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Préparation de la sauvegarde des charges de travail dans Azure avec DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Azure Backup Server (Classic)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (Classic)](backup-azure-dpm-introduction-classic.md)
>
>

Cet article présente l'utilisation de Microsoft Azure Backup pour protéger vos serveurs System Center Data Protection Manager (DPM) et vos charges de travail. En le lisant, vous comprendrez :

* le fonctionnement de la sauvegarde du serveur Azure DPM ;
* les conditions requises pour une expérience de sauvegarde fluide ;
* les erreurs classiques rencontrées et comment les gérer ;
* Scénarios pris en charge

System Center DPM sauvegarde les données des fichiers et des applications. Les données sauvegardées dans DPM peuvent être stockées sur bande, sur disque, ou sauvegardées dans Azure avec Microsoft Azure Backup. DPM interagit avec Azure Backup comme suit :

* **DPM déployé comme un serveur physique ou une machine virtuelle en local** : si DPM est déployé comme un serveur physique ou comme une machine virtuelle Hyper-V en local, vous pouvez sauvegarder les données dans un coffre Azure Backup en plus d'une sauvegarde sur disque et sur bande.
* **DPM déployé comme une machine virtuelle Azure** : depuis la mise à jour 3 de System Center 2012 R2, DPM peut être déployé comme une machine virtuelle Azure. Si DPM est déployé comme une machine virtuelle Azure, vous pouvez sauvegarder les données sur des disques Azure connectés à la machine virtuelle DPM Azure, ou décharger le stockage de données en les sauvegardant dans un coffre Azure Backup.

## <a name="why-backup-your-dpm-servers"></a>Pourquoi sauvegarder vos serveurs DPM ?
Les avantages commerciaux de l'utilisation d'Azure Backup pour la sauvegarde de serveurs DPM sont les suivants :

* Pour le déploiement DPM en local, vous pouvez utiliser la sauvegarde Azure au lieu d'un déploiement à long terme sur bande.
* Pour les déploiements DPM dans Azure, Azure Backup vous permet de décharger le stockage du disque Azure et ainsi d'évoluer en stockant les données plus anciennes dans Azure Backup et les nouvelles données sur un disque.

## <a name="how-does-dpm-server-backup-work"></a>Comment fonctionne la sauvegarde d'un serveur DPM ?
Pour sauvegarder une machine virtuelle, il est d’abord nécessaire de capturer un instantané des données. Le service Azure Backup lance le travail de sauvegarde à l’heure planifiée et déclenche l’extension de sauvegarde pour capturer un instantané. L’extension de sauvegarde se coordonne avec le service VSS in-guest pour assurer la cohérence et appelle l’API d’instantané d’objet blob du service Azure Storage une fois que la cohérence a été atteinte. Cela permet d’obtenir un instantané cohérent des disques de la machine virtuelle sans avoir à l’arrêter.

Une fois l’instantané capturé, les données sont transférées par le service Azure Backup dans le coffre de sauvegarde. Le service se charge d'identifier et de transférer uniquement les blocs qui ont été modifiés depuis la dernière sauvegarde, ce qui garantit l'efficacité du stockage des sauvegardes et les performances du réseau. Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé. Ce point de récupération est visible dans le portail Azure Classic.

> [!NOTE]
> Pour les machines virtuelles Linux, seule une sauvegarde cohérente au niveau des fichiers est possible.
>
>

## <a name="prerequisites"></a>Conditions préalables
Préparer Azure Backup pour sauvegarder des données DPM comme suit :

1. **Créez un coffre de sauvegarde**. Si vous n’avez pas créé de coffre de sauvegarde dans votre abonnement, consultez la version de cet article pour le Portail Azure : [Préparation de la sauvegarde des charges de travail dans Azure avec DPM](backup-azure-dpm-introduction.md).

  > [!IMPORTANT]
  > Depuis mars 2017, vous ne pouvez plus utiliser le portail Classic pour créer des coffres de sauvegarde.
  > Vous pouvez désormais mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services. Pour en savoir plus, consultez l’article [Mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft vous recommande de mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services.<br/> À compter du 30 novembre 2017, vous ne pouvez pas vous servir de PowerShell pour créer des coffres de sauvegarde. **D’ici au 30 novembre 2017** :
  >- tous les coffres de sauvegarde restants seront automatiquement mis à niveau vers des coffres Recovery Services.
  >- Vous ne pourrez plus accéder à vos données de sauvegarde depuis le portail Classic. Au lieu de cela, vous devrez utiliser le portail Azure pour accéder à ces données au sein de coffres Recovery Services.
  >

2. **Télécharger les informations d'identification de l'archivage** : dans Azure Backup, téléchargez dans l'archivage le certificat de gestion que vous avez créé.
3. **Installer l'Agent Azure Backup et inscrire le serveur** : à partir d'Azure Backup, installez l'agent sur chaque serveur DPM et inscrivez le serveur DPM dans le coffre de sauvegarde.

[!INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[!INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## <a name="requirements-and-limitations"></a>Spécifications (et limitations)
* DPM peut s'exécuter en tant que serveur physique ou machine virtuelle Hyper-V installée sur System Center 2012 SP1 ou System Center 2012 R2. DPM peut également s'exécuter en tant que machine virtuelle Azure sur System Center 2012 R2 avec au moins le correctif cumulatif 3 pour DPM 2012 R2, ou en tant que machine virtuelle Windows dans VMWare sur System Center 2012 R2 avec au moins le correctif cumulatif 5.
* Si vous exécutez DPM avec System Center 2012 SP1, vous devez installer le correctif cumulatif 2 pour System Center Data Protection Manager SP1. Cette opération est nécessaire avant d'installer l'agent Azure Backup.
* Windows PowerShell et .Net Framework 4.5 doivent être installés sur le serveur DPM.
* DPM permet de sauvegarder la plupart des charges de travail dans Azure Backup. Pour une liste complète des éléments pris en charge par Azure Backup, consultez la liste ci-dessous.
* Des données stockées dans Azure Backup ne peuvent pas être récupérées avec l'option « Copie sur bande ».
* Vous devez posséder un compte Azure avec la fonctionnalité Azure Backup activée. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. En savoir plus sur la [tarification d'Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* L'utilisation d'Azure Backup nécessite l'installation de l'agent Azure Backup sur les serveurs que vous souhaitez sauvegarder. Chaque serveur doit disposer d’au moins 10 % de la taille des données en cours de sauvegarde en tant que stockage local libre. Par exemple, la sauvegarde de 100 Go de données nécessite un minimum de 10 Go d'espace libre dans l'emplacement temporaire. La valeur minimale est de 10 %, mais il est recommander d’utiliser 15 % de l'espace de stockage local pour l'emplacement du cache.
* Les données seront stockées dans le coffre Azure. Il n’existe aucune limite à la quantité de données que vous pouvez sauvegarder dans un coffre Azure Backup, mais la taille d’une source de données (par exemple, une machine virtuelle ou une base de données) ne doit pas dépasser 54 400 Go.

Les types de fichiers suivants sont pris en charge pour une sauvegarde vers Azure :

* Chiffré (sauvegardes complètes uniquement)
* Compressé (sauvegardes incrémentielles prises en charge)
* Partiellement alloué (sauvegardes incrémentielles prises en charge)
* Compressé et partiellement alloué (traité comme partiellement alloué)

Et les types suivants ne sont pas pris en charge :

* Les serveurs sur des systèmes de fichiers respectant la casse ne sont pas pris en charge.
* Liens physiques (ignorés)
* Points d'analyse (ignorés)
* Chiffré et compressé (ignoré)
* Chiffré et partiellement alloué (ignoré)
* Flux compressé
* Flux partiellement alloué

> [!NOTE]
> À partir de System Center 2012 DPM avec SP1, vous pouvez sauvegarder dans Azure des charges de travail protégées par DPM grâce à Microsoft Azure Backup.
>
>
