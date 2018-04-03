---
title: FAQ sur la sauvegarde des fichiers Azure
description: Cet article fournit des détails sur la façon de protéger vos partages de fichiers Azure.
services: backup
keywords: N’ajoutez pas ou ne modifiez pas de mots clés sans consulter votre expert SEO.
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: 850d4d1e2ef6a13fcd8a072e6da210d558c7769b
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="questions-about-backing-up-azure-files"></a>Questions sur la sauvegarde des fichiers Azure
Cet article répond aux questions courantes sur la sauvegarde des fichiers Azure. Certaines réponses comportent des liens vers les articles présentant des informations complètes. Vous pouvez également publier des questions sur le service Azure Backup dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Pour analyser rapidement les sections de cet article, utilisez les liens sur la droite, en dessous de **Dans cet article**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configuration de la tâche de sauvegarde pour les fichiers Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares-br"></a>Pourquoi ne puis-je pas voir certains des comptes de stockage que je souhaite protéger et contenant des partages de fichiers Azure valides ? <br/>
Dans la préversion, la sauvegarde pour les partages de fichiers Azure ne prend pas en charge tous les types de comptes de stockage. Reportez-vous à la liste [ici](troubleshoot-azure-files.md#preview-boundaries) pour afficher la liste des comptes de stockage pris en charge.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Pourquoi ne puis-je pas voir certains de mes partages de fichiers Azure dans le compte de stockage lorsque j’essaie de configurer la sauvegarde ? <br/>
Vérifiez si le partage de fichiers Azure est déjà protégé dans le même coffre Recovery Services ou s’il a été supprimé récemment.

### <a name="why-cant-i-protect-file-shares-connected-to-a-sync-group-in-azure-file-sync-br"></a>Pourquoi ne puis-je pas protéger les partages de fichiers connectés à un groupe de synchronisation dans Azure File Sync ? <br/>
La protection des partages de fichiers Azure connectés à des groupes de synchronisation est en préversion limitée. Écrivez à [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) avec votre ID d’abonnement pour l’accès demandé. 

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>Dans quelles zones géographiques puis-je sauvegarder des partages de fichiers Azure ? <br/>
La sauvegarde pour les partages de fichiers Azure est actuellement en préversion et disponible uniquement dans les zones géographiques suivantes : 
-   Sud-Est de l’Australie (ASE) 
- Sud du Brésil (BRS)
- Centre du Canada (CNC)
-   Est du Canada (CE)
-   Centre des États-Unis (CUS)
-   Est de l’Asie (EA)
-   Est d’Australie (AE) 
-   Est des États-Unis (EUS)
-   Est des États-Unis 2 (EUS2)
-   Centre de l’Inde (INC) 
-   Centre-Nord des États-Unis (NCUS) 
-   Europe du Nord (NE) 
-   Centre-Sud des États-Unis (SCUS) 
-   Asie du Sud-Est (SEA)
-   Sud du Royaume-Uni (UKS) 
-   Ouest du Royaume-Uni (UKW) 
-   Europe de l’Ouest (WE) 
-   Ouest des États-Unis (WUS)
-   Centre-Ouest des États-Unis (WCUS)
-   Ouest des États-Unis 2 (WUS 2)

Écrivez à [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) si vous souhaitez l’utiliser dans une région qui n’est pas spécifiée ci-dessus.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vaultbr"></a>Combien de partages de fichiers Azure puis-je protéger dans un coffre ?<br/>
Dans la préversion, vous pouvez protéger les partages de fichiers Azure d’un maximum de 25 comptes de stockage par coffre. Vous pouvez également protéger jusqu’à 200 partages de fichiers Azure dans un seul coffre. 

## <a name="backup"></a>Sauvegarde

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Combien de sauvegardes à la demande puis-je prendre pour chaque partage de fichiers ? <br/>
À tout moment, vous pouvez disposer de 200 instantanés pour un partage de fichiers. La limite inclut des instantanés pris par Azure Backup, comme défini dans votre stratégie. Si vos sauvegardes échouent une fois la limite atteinte, supprimez des points de restauration à la demande pour réussir les prochaines sauvegardes.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>Après avoir activé les réseaux virtuels sur mon compte de stockage, la sauvegarde des partages de fichiers du compte commence à échouer. Pourquoi ?
La sauvegarde de partages de fichiers Azure ne prend pas en charge les comptes de stockage ayant activé les réseaux virtuels. Désactivez les réseaux virtuels dans les comptes de stockage afin de réussir les sauvegardes. 

## <a name="restore"></a>Restore

### <a name="can-i-recover-from-a-deleted-azure-file-share-br"></a>Puis-je effectuer une restauration d’un partage de fichiers Azure supprimé ? <br/>
Lorsqu’un partage de fichiers Azure est supprimé, la liste des sauvegardes qui seront également supprimées est affichée et une confirmation est demandée. Un partage de fichiers Azure supprimé ne peut pas être restauré.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share-br"></a>Puis-je effectuer une restauration à partir de sauvegardes après avoir arrêté la protection sur un partage de fichiers Azure ? <br/>
Oui. Si vous avez choisi l’option **Conserver les données de sauvegarde** lors de l’arrêt de la protection, vous pouvez restaurer à partir de tous les points de restauration existants.

## <a name="manage-backup"></a>Gérer la sauvegarde

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Puis-je accéder aux instantanés effectués par les sauvegardes Azure et les monter ? <br/>
Tous les instantanés pris par Azure Backup sont accessibles par l’option Voir les instantanés dans le portail, PowerShell ou CLI. Pour en savoir plus sur les instantanés de partage de fichiers Azure, consultez [Vue d’ensemble des instantanés de partage pour Azure Files (préversion)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Quelle est la durée de rétention maximale configurable pour les sauvegardes ? <br/>
La sauvegarde pour les partages de fichiers Azure vous permet de conserver vos sauvegardes quotidiennes pendant 120 jours.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share-br"></a>Que se passe-t-il lorsque je modifie la stratégie de sauvegarde pour un partage de fichiers Azure ? <br/>
Lorsqu’une nouvelle stratégie est appliquée sur les partages de fichiers, le planning et la rétention de la nouvelle stratégie sont suivis. Si la rétention est étendue, les points de récupération existants sont marqués comme à conserver afin qu’ils soient conformes à la nouvelle stratégie. Si la rétention est réduite, ils sont marqués comme à nettoyer lors de la prochaine tâche de nettoyage et sont ensuite supprimés.

## <a name="see-also"></a>Voir aussi
Cette information concerne uniquement la sauvegarde des fichiers Azure, pour en savoir plus sur les autres zones de Azure Backup, consultez certaines de ces autres FAQ relatives à la sauvegarde :
-  [FAQ du coffre Recovery Services](backup-azure-backup-faq.md)
-  [FAQ sur la sauvegarde de la machine virtuelle Azure](backup-azure-vm-backup-faq.md)
-  [FAQ sur l’Agent Azure Backup](backup-azure-file-folder-backup-faq.md)