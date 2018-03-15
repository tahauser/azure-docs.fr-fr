---
title: Sauvegarde Azure - Forum aux questions | Microsoft Docs
description: "Réponses aux questions courantes sur : les fonctionnalités de la sauvegarde Azure, y compris les coffres Recovery Services ce qu’il peut sauvegarder, son fonctionnement, son chiffrement, et ses limites. "
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "sauvegarde et récupération d’urgence ; service de sauvegarde"
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/21/2017
ms.author: markgal;arunak;trinadhk;sogup;
ms.openlocfilehash: 2e829ae90aa0366964f96b3c43f88503c7f3e894
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="questions-about-the-azure-backup-service"></a>Questions sur le service de sauvegarde Azure
Cet article répond aux questions courantes sur les composants de la Sauvegarde Azure. Certaines réponses comportent des liens vers les articles présentant des informations complètes. Vous pouvez poser des questions sur la sauvegarde Microsoft Azure en cliquant sur **Commentaires** (à droite). Les commentaires sont regroupés à la fin de cet article. Un compte Livefyre est nécessaire pour pouvoir déposer un commentaire. Vous pouvez également publier des questions sur le service Azure Backup dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Pour analyser rapidement les sections de cet article, utilisez les liens sur la droite, en dessous de **Dans cet article**.


## <a name="recovery-services-vault"></a>Coffre Recovery Services

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>Le nombre de coffres pouvant être créés dans chaque abonnement Azure est-il limité ? <br/>
Oui. À compter de janvier 2018, vous pouvez créer jusqu’à 25 coffres Recovery Services par région de sauvegarde Azure prise en charge et par abonnement. Au-delà, créez un autre abonnement.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>Le nombre de serveurs/ordinateurs pouvant être inscrits dans chaque coffre est-il limité ? <br/>
Vous pouvez inscrire jusqu’à 200 machines virtuelles Azure par coffre. Si vous utilisez l’agent MAB, vous pouvez inscrire jusqu’à 50 agents MAB par coffre. Et vous pouvez inscrire 50 serveurs MAB/DPM dans un coffre.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Si mon organisation possède un coffre, comment isoler les données d’un serveur de celles d’un autre serveur lors de la restauration des données ?<br/>
Tous les serveurs inscrits dans le même coffre sont en mesure de récupérer les données sauvegardées par d’autres serveurs *qui utilisent la même phrase secrète*. Si vous avez des serveurs dont vous souhaitez isoler les données de sauvegarde des autres serveurs de votre organisation, utilisez une phrase secrète désignée pour ces serveurs. Par exemple, les serveurs des ressources humaines peuvent utiliser une phrase secrète de chiffrement, les serveurs de comptabilité peuvent en utiliser une autre et les serveurs de stockage une troisième.

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>Puis-je « migrer » mon coffre ou mes données de sauvegarde entre des abonnements ? <br/>
Non. Le coffre est créé au niveau de l’abonnement et ne peut pas être ensuite réaffecté à un autre abonnement.

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-still-supported-br"></a>Les coffres Recovery Services sont basés sur Resource Manager. Les coffres Backup sont-ils toujours pris en charge ? <br/>
Les coffres Backup ont été convertis en coffres Recovery Services. Si vous n’avez pas converti le coffre Backup en coffre Recovery Services, la conversion a été effectuée automatiquement. 

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Puis-je migrer un coffre Azure Backup dans un coffre Recovery Services ? <br/>
Tous les coffres Backup ont été convertis en coffres Recovery Services. Si vous n’avez pas converti le coffre Backup en coffre Recovery Services, la conversion a été effectuée automatiquement.

## <a name="azure-backup-agent"></a>Agent Azure Backup
Liste détaillée des questions présentes dans le [Forum aux questions sur la sauvegarde de fichiers-dossiers Azure](backup-azure-file-folder-backup-faq.md)

## <a name="azure-vm-backup"></a>Sauvegarde des machines virtuelles Azure
Liste détaillée des questions présentes dans le [Forum aux questions sur la sauvegarde des machines virtuelles Azure](backup-azure-vm-backup-faq.md)

## <a name="back-up-vmware-servers"></a>Sauvegarder des serveurs VMware

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Puis-je sauvegarder des serveurs VMware vCenter dans Azure ?

Oui. Vous pouvez utiliser Azure Backup Server pour sauvegarder VMware vCenter et ESXi dans Azure. Pour en savoir plus sur la version de VMware prise en charge, consultez l’article [Matrice de protection d’Azure Backup Server](backup-mabs-protection-matrix.md). Pour un guide pas à pas, consultez [Utiliser Azure Backup Server pour sauvegarder un serveur VMware](backup-azure-backup-server-vmware.md).


## <a name="azure-backup-server-and-system-center-data-protection-manager"></a>Microsoft Azure Backup et System Center Data Protection Manager
### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Puis-je utiliser Azure Backup Server pour créer une sauvegarde de la récupération complète (BMR) pour un serveur physique ? <br/>
Oui.

### <a name="can-i-register-my-dpm-server-to-multiple-vaults-br"></a>Puis-je enregistrer mon serveur DPM auprès de plusieurs coffres ? <br/>
Non. Un serveur DPM ou MABS peut être inscrit auprès d’un seul coffre.

### <a name="which-version-of-system-center-data-protection-manager-is-supported-br"></a>Quelle version de System Center Data Protection Manager est prise en charge ? <br/>
Nous vous recommandons d’installer le [dernier](http://aka.ms/azurebackup_agent) agent de sauvegarde Azure sur le dernier correctif cumulatif de System Center Data Protection Manager (DPM). À compter d’août 2016, le correctif cumulatif 11 correspond à la dernière mise à jour.

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-system-center-dpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>J’ai installé l’agent Azure Backup pour protéger mes fichiers et dossiers. Puis-je à présent installer System Center DPM pour utiliser l’agent de sauvegarde Azure afin de protéger les charges de travail d’application/de machine virtuelle locales dans Azure ? <br/>
Pour utiliser Azure Backup avec System Center Data Protection Manager (DPM), installez d’abord DPM, puis installez l’agent Azure Backup. L’installation des composants Azure Backup dans cet ordre garantit le fonctionnement de l’agent Azure Backup avec DPM. L’installation de l’agent Azure Backup avant l’installation de DPM n’est pas conseillée ou prise en charge.


## <a name="how-azure-backup-works"></a>Fonctionnement de la sauvegarde Azure
### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Si j’annule une opération de sauvegarde en cours, les données de sauvegarde transférées sont-elles supprimées ? <br/>
Non. Toutes les données transférées dans le coffre avant l’annulation de l’opération de sauvegarde sont conservées. Azure Backup utilise un mécanisme de point de contrôle pour ajouter occasionnellement des points de contrôle aux données de sauvegarde pendant la sauvegarde. En présence de points de contrôle dans les données de sauvegarde le processus de sauvegarde suivant est en mesure de valider l’intégrité des fichiers. La prochaine tâche de sauvegarde sera incrémentielle par rapport aux données précédemment sauvegardées. Les sauvegardes incrémentielles transfèrent uniquement les données nouvelles ou modifiées, ce qui équivaut à une meilleure utilisation de la bande passante.

Si vous annulez une tâche de sauvegarde pour une machine virtuelle Azure, toutes les données transférées sont ignorées. La tâche de sauvegarde suivante transfère des données incrémentielles à partir de la dernière sauvegarde réussie.

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>Existe-t-il des limites concernant le moment et le nombre de fois durant lesquels une opération de sauvegarde peut être planifiée chaque jour ?<br/>
Oui. Vous pouvez exécuter les opérations de sauvegarde sur Windows Server ou des stations de travail Windows jusqu’à trois fois/jour. Vous pouvez exécuter des tâches de sauvegarde sur System Center DPM jusqu’à deux fois par jour. Vous pouvez exécuter une tâche de sauvegarde pour les machines virtuelles IaaS une fois par jour. Vous pouvez utiliser la stratégie de planification pour Windows Server ou la station de travail Windows pour spécifier des planifications quotidiennes ou hebdomadaires. Avec System Center DPM, vous pouvez spécifier des planifications quotidiennes, hebdomadaires, mensuelles et annuelles.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Pourquoi la taille des données transférées dans le coffre Recovery Services est-elle plus réduite que celle des données que j’ai sauvegardées ?<br/>
 Toutes les données sauvegardées à partir de l’agent Azure Backup, de SCDPM ou d’Azure Backup Server sont compressées et chiffrées avant d’être transférées. Une fois la compression et le chiffrement appliqués, les données du coffre Recovery Services sont réduites de 30 à 40 %.

## <a name="what-can-i-back-up"></a>Que puis-je sauvegarder ?
### <a name="which-operating-systems-do-azure-backup-support-br"></a>Quels systèmes d’exploitation la sauvegarde Azure prend-elle en charge ? <br/>
La sauvegarde Azure prend en charge les systèmes d’exploitation suivants pour la sauvegarde des dossiers et des fichiers, ainsi que des applications de charge de travail protégées à l’aide du serveur de sauvegarde Azure et de System Center Data Protection Manager (DPM).

| Système d’exploitation | Plateforme | SKU |
|:--- | --- |:--- |
| Windows 8 et derniers Service Packs |64 bits |Entreprise, Professionnel |
| Windows 7 et derniers Service Packs |64 bits |Édition Intégrale, Entreprise, Professionnel, Édition Familiale Premium, Édition Familiale Basique, Édition Starter |
| Windows 8.1 et derniers Service Packs |64 bits |Entreprise, Professionnel |
| Windows 10 |64 bits |Entreprise, Professionnel, Familiale |
| Windows Server 2016 |64 bits |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 et derniers Service Packs |64 bits |Standard, Datacenter, Foundation |
| Windows Server 2012 et derniers Service Packs |64 bits |Datacenter, Foundation, Standard |
| Windows Storage Server 2016 et derniers Service Packs |64 bits |Standard, Workgroup | 
| Windows Storage Server 2012 R2 et derniers Service Packs |64 bits |Standard, Workgroup |
| Windows Storage Server 2012 et derniers Service Packs |64 bits |Standard, Workgroup |
| Windows Server 2012 R2 et derniers Service Packs |64 bits |Essential |
| Windows Server 2008 R2 SP1 |64 bits |Standard, Entreprise, Datacenter, Foundation |

**Pour la sauvegarde de machines virtuelles Azure :**

* **Linux**: Azure Backup prend en charge [une liste de distributions approuvées par Azure](../virtual-machines/linux/endorsed-distros.md) , à l’exception de CoreOS Linux.  D’autres distributions « Bring-Your-Own-Linux » fonctionnent également tant que l’agent de machine virtuelle est disponible sur la machine virtuelle et que Python est pris en charge.
* **Windows Server** : les versions antérieures à Windows Server 2008 R2 ne sont pas prises en charge.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>La taille de chaque source de données sauvegardée est-elle limitée ? <br/>
Il n’existe aucune limite pour la quantité de données que vous pouvez sauvegarder dans un coffre. Azure Backup limite la taille maximale de la source de données ; cependant, ces limites sont importantes. Depuis août 2015, la taille maximale de la source de données du système d’exploitation pris en charge est :

| N° | Système d’exploitation | Taille maximale de la source de données |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 ou version ultérieure |54 400 Go |
| 2 |Windows 8 ou version ultérieure |54 400 Go |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1 700 Go |
| 4 |Windows 7 |1 700 Go |

Le tableau suivant explique comment la taille de chaque source de données est déterminée.

| Source de données | Détails |
|:---:|:--- |
| Volume |Quantité de données sauvegardées à partir d’un volume unique ou d’une machine client |
| Machine virtuelle Hyper-V |Somme des données de tous les disques durs virtuels de la machine virtuelle en cours de sauvegarde |
| Base de données Microsoft SQL Server |Taille d’une base de données SQL unique en cours de sauvegarde |
| Microsoft SharePoint |Somme des bases de données de contenu et de configuration dans une batterie de serveurs SharePoint en cours de sauvegarde |
| Microsoft Exchange |Somme de toutes les bases de données Exchange sur un serveur Exchange en cours de sauvegarde |
| État système/récupération complète |Chaque copie individuelle de l’état système/récupération complète de l’ordinateur en cours de sauvegarde |

Pour la sauvegarde des machines virtuelles Azure, chaque machine virtuelle peut avoir jusqu’à 16 disques de données et chaque disque de données est d’une taille inférieure ou égale à 1 024 Go. <br>
Nous disposons d’une [préversion privée](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a?redir=0) qui prend en charge les disques jusqu’à 4 To. 

## <a name="retention-policy-and-recovery-points"></a>Stratégie de rétention et points de récupération
### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>Existe-t-il une différence entre les stratégies de rétention de DPM et celle d’un serveur/client Windows (par exemple, sur Windows Server sans DPM) ?<br/>
Non, DPM et le serveur/client Windows Server répondent tous les deux à des stratégies quotidienne, hebdomadaire, mensuelle et annuelle de rétention.

### <a name="can-i-configure-my-retention-policies-selectively--ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>Puis-je configurer mes stratégies de rétention de manière sélective (par exemple, configurer des stratégies hebdomadaires et quotidiennes, mais pas annuelles et mensuelles) ?<br/>
Oui, la structure de rétention Azure Backup vous permet une flexibilité complète dans la définition de la stratégie de rétention selon vos besoins.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>Puis-je « planifier une sauvegarde » à 18 h 00 et spécifier des stratégies de rétention à une autre heure ?<br/>
Non. Les stratégies de rétention ne peuvent être appliquées que sur les points de sauvegarde. Dans l’image qui suit, la stratégie de rétention est spécifiée sur les sauvegardes effectuées à minuit et à 18 h 00. <br/>

![Planification de sauvegarde et rétention](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Si une sauvegarde est conservée pendant longtemps, faut-il plus de temps pour récupérer un point de données plus ancien ? <br/>
Non, le délai de récupération reste identique pour les points de données récents ou anciens. Chaque point de récupération se comporte comme un point complet.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Si chaque point de récupération est un point complet, cela a-t-il un impact sur la quantité totale de stockage de sauvegarde facturable ?<br/>
Les produits avec points de rétention à long terme stockent les données de sauvegarde en tant que points complets. Toutefois, même si ces points *occupent* de l’espace de stockage, ils sont plus faciles et plus rapides à récupérer. Les copies incrémentielles *occupent moins d’espace de stockage* , mais vous devez restaurer une chaîne de données, ce qui rallonge le temps de récupération. L’architecture de stockage d’Azure Backup vous offre le meilleur des deux en stockant les données de manière optimale pour des restaurations rapides et des coûts de stockage faibles. Cette approche de stockage de données garantit que votre bande passante entrante et sortante est utilisée de façon efficace. La quantité de stockage de données et le temps nécessaire pour récupérer les données sont tous les deux réduits au minimum. En savoir plus sur l’efficacité des [sauvegardes incrémentielles](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>Le nombre de points de récupération pouvant être créés est-il limité ?<br/>
Vous pouvez créer jusqu’à 9 999 points de récupération par instance protégée. Une instance protégée est un ordinateur, un serveur (physique ou virtuel) ou une charge de travail configurés pour sauvegarder des données dans Azure. Pour plus d’informations, consultez [Sauvegarde et rétention](./backup-introduction-to-azure-backup.md#backup-and-retention) et [Qu’est-ce qu’une instance protégée ?](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance).

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Combien de récupérations puis-je effectuer sur les données sauvegardées dans Azure ?<br/>
Il n’existe aucune limite concernant le nombre de récupérations dans Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>En cas de restauration de données, dois-je payer pour le trafic de sortie à partir d’Azure ? <br/>
Non. Vos récupérations sont gratuites et le trafic sortant ne vous est pas facturé.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Que se passe-t-il lorsque je modifie ma stratégie de sauvegarde ?
Lorsqu’une nouvelle stratégie est appliquée, le planning et la rétention de la nouvelle stratégie sont suivis. Si la rétention est étendue, les points de récupération existants sont marqués comme à conserver afin qu’ils soient conformes à la nouvelle stratégie. Si la rétention est réduite, ils sont marqués comme à nettoyer lors de la prochaine tâche de nettoyage et sont ensuite supprimés.

## <a name="azure-backup-encryption"></a>Chiffrement de sauvegarde Azure
### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Les données envoyées à Azure sont-elles chiffrées ? <br/>
Oui. Les données sont chiffrées sur l’ordinateur client/serveur/SCDPM local avec AES256 et sont envoyées via une connexion HTTPS sécurisée.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Les données de sauvegarde sont-elles également chiffrées dans Azure ?<br/>
Oui. Les données envoyées à Azure restent chiffrées (au repos). Microsoft ne déchiffre les données de sauvegarde à aucun moment. Lorsque vous sauvegardez une machine virtuelle Azure, Azure Backup s’appuie sur le chiffrement de la machine virtuelle. Par exemple, si votre machine virtuelle est chiffrée à l’aide d’Azure Disk Encryption, ou d’une autre technologie de chiffrement, Azure Backup utilise ce chiffrement pour sécuriser vos données.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>Quelle est la longueur minimale de la clé de chiffrement utilisée pour chiffrer les données de sauvegarde ? <br/>
La clé de chiffrement doit comporter au moins 16 caractères lorsque vous utilisez l’agent de sauvegarde Azure. Pour les machines virtuelles Azure, il n’existe aucune limite à la longueur des clés utilisées par Azure KeyVault. 

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>Que se passe-t-il si j’ai égaré la clé de chiffrement ? Puis-je récupérer des données (ou) Microsoft peut-il récupérer les données ? <br/>
La clé utilisée pour chiffrer les données de sauvegarde est présente uniquement localement chez le client. Microsoft ne conserve pas de copie dans Azure et n’a pas accès à la clé. Si le client égare la clé, Microsoft ne peut pas récupérer les données de sauvegarde.
