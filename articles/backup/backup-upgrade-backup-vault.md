---
title: "Mise à niveau d’un coffre de sauvegarde vers un coffre Recovery Services de la sauvegarde Azure | Microsoft Docs"
description: "Mise à niveau d’un coffre de sauvegarde vers un coffre Recovery Services pour bénéficier de nouvelles fonctionnalités comme la sauvegarde de machines virtuelles Resource Manager, la sécurité renforcée, la sauvegarde de machines virtuelles VMware et la sauvegarde de l’état du système pour les serveurs Windows"
services: backup
documentationcenter: 
author: trinadhk
manager: vijayts
editor: 
keyword: backup vault; upgrade vault; recovery services vault
ms.assetid: d037a8bf-49f2-4578-974a-3471d87ca278
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: trinadhk, sogup
ms.openlocfilehash: 708983fc2c5264d1213bdb32b665dcccc5ca9df9
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2017
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Coffre de sauvegarde mis à niveau vers un coffre Recovery Services
Cet article fournit une vue d’ensemble des prestations du coffre Recovery Services, répond aux questions fréquentes sur la mise à niveau d’un coffre de sauvegarde existant vers un coffre Recovery Services et indique les étapes postérieures à la mise à niveau. Un coffre Recovery Services est l’équivalent Azure Resource Manager d’un coffre de sauvegarde qui héberge vos données de sauvegarde. Les données sont généralement des copies de données ou des informations de configuration pour des machines virtuelles, des charges de travail, des stations de travail ou des serveurs locaux ou dans Azure.

## <a name="what-is-a-recovery-services-vault"></a>Présentation d’un coffre Recovery Services
Un coffre Recovery Services est une entité de stockage en ligne dans Azure qui permet de conserver des données telles que des copies de sauvegarde, des points de récupération et des stratégies de sauvegarde. Vous pouvez utiliser des coffres Recovery Services afin de stocker des données de sauvegarde pour divers services Azure tels que des machines virtuelles IaaS (Windows ou Linux) et des bases de données SQL Azure. Les coffres Recovery Services prennent en charge System Center DPM, Windows Server, le serveur de sauvegarde Azure et bien plus. Les coffres Recovery Services facilitent l’organisation de vos données de sauvegarde, tout en réduisant le temps de gestion.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Comparaison entre les coffres Recovery Services et les coffres de sauvegarde
Les coffres Recovery Services sont basés sur le modèle Azure Resource Manager d’Azure, tandis que les coffres de sauvegarde sont basés sur le modèle Azure Service Manager. Lorsque vous mettez à niveau un coffre de sauvegarde vers un coffre Recovery Services, les données de sauvegarde restent intactes pendant et après le processus de mise à niveau. Les coffres Recovery Services fournissent des fonctionnalités non disponibles pour les coffres de sauvegarde, telles que :

- **Davantage de fonctionnalités pour sécuriser des données sauvegarde** : avec les coffres Recovery Services, le module Sauvegarde Microsoft Azure fournit des fonctionnalités de sécurité pour protéger les sauvegardes cloud. Ces fonctionnalités de sécurité vous garantissent de pouvoir sécuriser vos sauvegardes, et récupérer en toute sécurité des données à partir de sauvegardes cloud, même si des serveurs de production et de sauvegarde sont compromis. [En savoir plus](backup-azure-security-feature.md)

- **Surveillance centrale de votre environnement informatique hybride** : avec les coffres Recovery Services, vous pouvez surveiller non seulement vos [machines virtuelles IaaS Azure](backup-azure-manage-vms.md), mais aussi votre [ressources locales](backup-azure-manage-windows-server.md#manage-backup-items) à partir d’un portail centralisé. [En savoir plus](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Contrôle d’accès en fonction du rôle (RBAC)** : le RBAC offre un contrôle précis de la gestion des accès dans Azure. [Azure offre différents rôles intégrés](../active-directory/role-based-access-built-in-roles.md), et Sauvegarde Microsoft Azure comprend trois [rôles intégrés pour gérer les points de récupération](backup-rbac-rs-vault.md). Les coffres Recovery Services sont compatibles avec RBAC, ce qui limite les accès en sauvegarde et en restauration à l’ensemble défini des rôles d’utilisateur. [En savoir plus](backup-rbac-rs-vault.md)

- **Protéger toutes les configurations des machines virtuelles Azure** : les coffres Recovery Services protègent les machines virtuelles basées sur Resource Manager, notamment les disques Premium, les disques gérés et les machines virtuelles chiffrées. La mise à niveau d’un coffre de sauvegarde vers un coffre Recovery Services vous donne la possibilité de mettre à niveau vos machines virtuelles basées sur Service Manager vers des machines virtuelles basées sur Resource Manager. Lors de la mise à niveau du coffre, vous pouvez conserver vos points de récupération de machines virtuelles basées sur Service Manager et configurer la protection pour de machines virtuelles (compatibles avec Resource Manager) mises à niveau. [En savoir plus](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauration instantanée de machines virtuelles IaaS** : les coffres Recovery Services vous permettent de restaurer des fichiers et dossiers d’une machine virtuelle IaaS sans restaurer la machine virtuelle entière, ce qui permet d’accélérer les temps de restauration. La restauration instantanée de machines virtuelles IaaS est disponible pour les machines virtuelles Windows et Linux. [En savoir plus](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Si vous avez enregistré des éléments dans un coffre de sauvegarde avec une version de l’agent MARS antérieure à 2.0.9083.0, [téléchargez la dernière version de l’agent MARS]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) pour pouvoir bénéficier de toutes les fonctionnalités de coffre Recovery Services. 
> 

## <a name="managing-your-recovery-services-vaults"></a>Accéder à vos coffres Recovery Services
Les écrans suivants montrent un nouveau coffre Recovery Services, mis à niveau à partir du coffre de sauvegarde, dans le portail Azure. Le coffre mis à niveau est inclus dans un groupe de ressources par défaut nommé « Default-RecoveryServices-ResourceGroup-geo ». Par exemple, si votre coffre de sauvegarde se trouvait dans l’Ouest des États-Unis, il est inclus dans un groupe de ressources par défaut nommé Default-RecoveryServices-ResourceGroup-westus.
> [!NOTE]
> Pour les clients soumis à la norme CPS, le groupe de ressources n’est pas modifié après la mise à niveau du coffre et reste donc intact.

Le premier écran présente le tableau de bord du coffre qui affiche les entités clés pour le coffre.
![Exemple de coffre Recovery Services mis à niveau à partir d’un coffre de sauvegarde](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Le deuxième écran présente les liens d’aide disponibles pour vous aider à commencer à utiliser le coffre Recovery Services.

![liens d’aide dans le panneau Démarrage rapide](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Étapes post-mise à niveau
Les coffres Recovery Services prennent en charge la spécification d’informations de fuseau horaire dans la stratégie de sauvegarde. Une fois le coffre mis à niveau, accédez à Stratégies de sauvegarde dans le menu de paramètres du coffre et mettez à jour les informations de fuseau horaire pour chacune des stratégies configurées dans le coffre. Cet écran montre déjà l’heure de planification de sauvegarde spécifiée en fonction du fuseau horaire local utilisé quand vous avez créé la stratégie. 

## <a name="enhanced-security"></a>Sécurité améliorée
Quand un coffre de sauvegarde est mis à niveau vers un coffre Recovery Services, les paramètres de sécurité de ce coffre sont activés automatiquement. Quand les paramètres de sécurité sont actifs, certaines opérations telles que la suppression des sauvegardes ou la modification d’une phrase secrète nécessitent un code PIN [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md). Pour plus d’informations sur la sécurité améliorée, consultez l’article [Fonctionnalités de sécurité pour la protection des sauvegardes hybrides](backup-azure-security-feature.md). Quand la sécurité améliorée est activée, les données sont conservées jusqu’à 14 jours après que les informations relatives au point de récupération ont été supprimées du coffre. Les clients sont facturés pour le stockage de ces données de sécurité. La rétention des données de sécurité s’applique aux points de récupération définis pour l’agent de sauvegarde Azure, le serveur de sauvegarde Azure et System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Collecter des données sur votre coffre
Une fois que vous avez effectué la mise à niveau vers un coffre Recovery Services, configurez des rapports pour la sauvegarde Azure (pour les machines virtuelles IaaS et l’agent Microsoft Azure Recovery Services) et utilisez Power BI pour accéder aux rapports. Pour plus d’informations sur la collecte de données, consultez l’article [Configurer les rapports de la Sauvegarde Azure](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Le plan de mise à niveau affecte-t-il mes sauvegardes en cours ?**</br>
Non. Vos sauvegardes en cours se poursuivent sans interruption pendant et après la mise à niveau.

**Quelles sont les implications de cette migration pour mes outils existants ?**</br>
Vous devez mettre à jour votre automatisation ou vos outils existants vers le modèle de déploiement Resource Manager pour vous assurer qu’ils continueront à fonctionner après la mise à niveau. Consultez les références de cmdlets PowerShell pour le [Modèle de déploiement Resource Manager](backup-client-automation.md).

**Puis-je restaurer après la mise à niveau ?**</br>
Non. La restauration n’est pas prise en charge une fois la mise à niveau des ressources opérée avec succès.

**Puis-je afficher mon coffre Azure Classic après mise à niveau ?**</br>
Non. Vous ne pouvez pas afficher ou gérer votre coffre classique après mise à niveau. Vous pouvez utiliser le nouveau portail Azure uniquement pour toutes les actions de gestion sur le coffre.

**Pourquoi ne puis-je pas voir les serveurs protégés par l’agent MARS dans mon coffre mis à niveau ?**</br>
Vous devez installer la dernière version de l’agent MARS pour pouvoir voir tous les serveurs protégés par l’agent MARS dans votre coffre. Vous pouvez télécharger la dernière version de l’agent [ici]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**Je ne peux pas voir la stratégie de sauvegarde pour les serveurs protégés par l’agent MARS après la mise à niveau**</br>
Il se peut que la stratégie de sauvegarde du coffre soit obsolète et qu’il soit par conséquent impossible de la synchroniser avec le coffre mis à niveau. Mettez à jour la stratégie pour pouvoir continuer à voir vos stratégies dans le coffre mis à niveau.
Pour mettre à jour la stratégie, accédez à l’agent MARS et mettez à jour la stratégie de sauvegarde configurée.

**Pourquoi ne puis-je pas mettre à jour ma stratégie de sauvegarde après la mise à niveau ?**</br>
Ce cas se présente quand vous utilisez un agent de sauvegarde obsolète et que vous sélectionnez une période de rétention minimale inférieure à la valeur minimale autorisée. Quand un coffre de sauvegarde est mis à niveau vers un coffre Recovery Services, les paramètres de sécurité de ce coffre sont activés automatiquement. Pour qu’il y ait toujours un nombre valide de points de récupération disponibles, il existe une période de rétention minimale à respecter selon la fonctionnalité de sécurité. Pour plus d’informations, cliquez [ici](backup-azure-security-feature.md).
Vous devez également mettre à jour vos agents de sauvegarde Azure vers la version la plus récente pour pouvoir bénéficier des toutes dernières fonctionnalités de la sauvegarde Azure.

**J’ai mis à jour mon agent, mais je ne peux toujours pas voir les objets synchronisés, même plusieurs jours après la mise à niveau**</br>
Vérifiez si vous avez enregistré la même machine pour plusieurs coffres. Assurez-vous de consulter le coffre qui correspond à l’inscription de l’agent MARS. Pour identifier le coffre qui correspond à l’inscription de votre agent MARS, ouvrez le Registre Windows et vérifiez la valeur de la clé ServiceResourceName sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config. Le coffre inscrit auprès de cet agent MARS y figure. Si la clé ServiceResourceName n’est pas visible dans votre système, contactez-nous en ayant à portée de main la valeur des clés ResourceId et MachineId keys sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config et nous vous aiderons à résoudre le problème.

**Pourquoi ne puis-je pas voir les informations relatives aux travaux de mes ressources après la mise à niveau ?**</br>
La surveillance des sauvegardes (agent MARS et IaaS) est une nouvelle fonctionnalité que vous obtenez quand vous mettez à niveau votre coffre de sauvegarde vers un coffre Recovery Services. La synchronisation des informations de surveillance avec le service prend jusqu’à 12 heures.

**Comment signaler un problème ?**</br>
Si une partie de la mise à niveau du coffre échoue, notez l’OperationId mentionné dans l’erreur. Le Support technique de Microsoft travaillera de manière proactive pour résoudre le problème. Vous pouvez contacter le Support ou nous envoyer un e-mail à l’adresse rsvaultupgrade@service.microsoft.com avec votre ID d’abonnement, le nom du coffre et l’OperationId. Nous tenterons de résoudre le problème le plus rapidement possible. Ne retentez l’opération que sauf si vous y êtes explicitement invité par Microsoft.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants pour :</br>
[Sauvegarder une machine virtuelle IaaS](backup-azure-arm-vms-prepare.md)</br>
[Sauvegarder un serveur de sauvegarde Azure](backup-azure-microsoft-azure-backup.md)</br>
[Sauvegarder un serveur Windows Server](backup-configure-vault.md)
