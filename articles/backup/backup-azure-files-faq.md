---
title: FAQ sur la sauvegarde des fichiers Azure
description: "Cet article fournit des détails sur la façon de protéger vos fichiers Azure dans Azure. Ce résumé s’affiche dans les résultats de recherche."
services: backup
keywords: "N’ajoutez pas ou ne modifiez pas de mots clés sans consulter votre expert SEO."
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: d37e119709bc9d4643fcaa9512b5209d4139515e
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="questions-about-backing-up-azure-files"></a>Questions sur la sauvegarde des fichiers Azure
Cet article répond aux questions courantes sur la sauvegarde des fichiers Azure. Certaines réponses comportent des liens vers les articles présentant des informations complètes. Vous pouvez également publier des questions sur le service Azure Backup dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Pour analyser rapidement les sections de cet article, utilisez les liens sur la droite, en dessous de **Dans cet article**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configuration de la tâche de sauvegarde pour les fichiers Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-file-shares-br"></a>Pourquoi ne puis-je pas voir certains des comptes de stockage que je souhaite protéger et contenant des partages de fichiers valides ? <br/>
Sauvegarde de fichiers Azure est actuellement en version préliminaire et seuls les comptes de stockage pris en charge peuvent être configurés pour la sauvegarde. Assurez-vous que le compte de stockage que vous cherchez est bien pris en charge.

### <a name="why-cant-i-see-some-of-my-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Pourquoi ne puis-je pas voir certains de mes partages de fichiers dans le compte de stockage lorsque j’essaie de configurer la sauvegarde ? <br/>
Vérifiez si le partage de fichiers est déjà protégé dans le même coffre Recovery Services. Assurez-vous que le partage de fichiers que vous souhaitez protéger n’a pas été supprimé récemment.

### <a name="why-cant-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>Pourquoi ne puis-je pas protéger les partages de fichiers connectés à un groupe de synchronisation dans Azure Files Sync ? <br/>
La protection des partages de fichiers Azure connectés à des groupes de synchronisation est en version préliminaire limitée. Écrivez à [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) avec votre ID d’abonnement pour l’accès demandé. 

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>Dans quelles zones géographiques puis-je sauvegarder des partages de fichiers Azure ? <br/>
La sauvegarde pour les partages de fichiers Azure est actuellement en préversion et disponible uniquement dans les zones géographiques suivantes. 
-   Centre du Canada (CNC)
-   Est du Canada (CE) 
-   Centre des États-Unis (CUS)
-   Est de l’Asie (EA)
-   Est d’Australie (AE) 
-   Centre de l’Inde (INC) 
-   Centre-Nord des États-Unis (NCUS) 
-   Sud du Royaume-Uni (UKS) 
-   Ouest du Royaume-Uni (UKW) 
-   Centre-Ouest des États-Unis (WCUS)
-   Ouest des États-Unis 2 (WUS 2)

La sauvegarde pour les partages de fichiers Azure sera disponible dans les zones géographiques suivantes à partir du *23 février*.
-   Sud-Est de l’Australie (ASE) 
-   Sud du Brésil (BRS) 
-   Est des États-Unis (EUS) 
-   Est des États-Unis 2 (EUS2) 
-   Europe du Nord (NE) 
-   Centre-Sud des États-Unis (SCUS) 
-   Asie du Sud-Est (SEA)
-   Europe de l’Ouest (WE) 
-   Ouest des États-Unis (WUS)  

Écrivez à [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) si vous souhaitez l’utiliser dans une région qui n’est pas spécifiée ci-dessus.

### <a name="how-many-file-shares-can-i-protect-in-a-vaultbr"></a>Combien de partages de fichiers puis-je protéger dans un coffre ?<br/>
Dans la préversion, vous pouvez protéger les partages de fichiers d’un maximum de 25 comptes de stockage par coffre. Vous pouvez également protéger jusqu’à 200 partages de fichiers dans un seul coffre. 

## <a name="backup"></a>Sauvegarde

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Combien de sauvegardes à la demande puis-je prendre pour chaque partage de fichiers ? <br/>
À tout moment, vous pouvez disposer d’un maximum de 200 captures instantanées pour un partage de fichiers, y compris celles effectuées par Azure Backup comme défini par votre stratégie. Si vos sauvegardes échouent car la limite est atteinte, supprimez des points de restauration à la demande.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>Après avoir activé les réseaux virtuels sur mon compte de stockage, la sauvegarde des partages de fichiers du compte commence à échouer. Pourquoi ?
Actuellement, la sauvegarde de fichiers Azure n’est pas prise en charge pour les comptes de stockage ayant activé les réseaux virtuels. Désactivez les réseaux virtuels dans les comptes de stockage que vous souhaitez sauvegarder. 

## <a name="restore"></a>Restore

### <a name="can-i-recover-from-a-deleted-file-share-br"></a>Puis-je effectuer une restauration d’un partage de fichiers supprimé ? <br/>
Lorsque vous tentez de supprimer un partage de fichiers, la liste des sauvegardes qui seront également supprimées si vous continuez s’affiche et une confirmation vous est demandée. Vous ne pouvez pas restaurer un partage de fichiers supprimé.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-a-file-share-br"></a>Puis-je effectuer une restauration à partir de sauvegardes après avoir arrêté la protection sur un partage de fichiers ? <br/>
Oui. Si vous avez choisi l’option **Conserver les données de sauvegarde** lors de l’arrêt de la protection, vous pouvez restaurer à partir de tous les points de restauration existants.

## <a name="manage-backup"></a>Gérer la sauvegarde

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Puis-je accéder aux instantanés effectués par les sauvegardes Azure et les monter ? <br/>
Tous les instantanés pris par Azure Backup sont accessibles par l’option Voir les instantanés dans le portail, PowerShell ou CLI. Vous pouvez les monter à l’aide de la procédure détaillée ici.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Quelle est la durée de rétention maximale configurable pour les sauvegardes ? <br/>
La sauvegarde pour les partages de fichiers Azure vous permet de conserver vos sauvegardes quotidiennes pendant 120 jours.

### <a name="what-happens-when-i-change-the-backup-policy-for-a-file-share-br"></a>Que se passe-t-il lorsque je modifie la stratégie de sauvegarde pour un partage de fichiers ? <br/>
Lorsqu’une nouvelle stratégie est appliquée sur les partages de fichiers, le planning et la rétention de la nouvelle stratégie sont suivis. Si la rétention est étendue, les points de récupération existants sont marqués comme à conserver afin qu’ils soient conformes à la nouvelle stratégie. Si la rétention est réduite, ils sont marqués comme à nettoyer lors de la prochaine tâche de nettoyage et sont ensuite supprimés.


## <a name="see-also"></a>Voir aussi
Cette information concerne uniquement la sauvegarde des fichiers Azure, pour en savoir plus sur les autres zones de Azure Backup, consultez certaines de ces autres FAQ relatives à la sauvegarde :
-  [FAQ du coffre Recovery Services](backup-azure-backup-faq.md)
-  [FAQ sur la sauvegarde de la machine virtuelle Azure](backup-azure-vm-backup-faq.md)
-  [FAQ sur l’Agent Azure Backup](backup-azure-file-folder-backup-faq.md)
