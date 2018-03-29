---
title: Mise à niveau vers la pile de sauvegarde de machine virtuelle Azure V2 | Microsoft Docs
description: Processus de mise à niveau et questions fréquentes (FAQ) pour la pile de sauvegarde de machine virtuelle V2
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk, sogup
ms.openlocfilehash: 6d214072bccb8b2b42828ee003dcf349985b4f43
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="upgrade-to-vm-backup-stack-v2"></a>Mise à niveau vers la pile de sauvegarde de machine virtuelle V2
La mise à niveau de la pile de sauvegarde de machine virtuelle V2 fournit les améliorations de fonctionnalités suivantes :
* Possibilité d’afficher un instantané pris dans le cadre d’une tâche de sauvegarde pour qu’il puisse être récupéré sans attendre la fin du transfert des données.
Cela réduit le temps d’attente pour la copie de l’instantané dans le coffre avant de déclencher la restauration. De plus, cela supprime l’exigence de stockage supplémentaire pour la sauvegarde des machines virtuelles Premium excepté pour la première sauvegarde.  

* Réduction des temps de sauvegarde et de restauration en conservant les instantanés localement pendant 7 jours. 

* Prise en charge de tailles de disque jusqu’à 4 To.  

* Possibilité d’utiliser des comptes de stockage d’origine (même si la machine virtuelle a des disques répartis entre des comptes de stockage) lors de l’exécution d’une restauration d’une machine virtuelle non gérée. Cela accélère les restaurations pour de nombreuses configurations de machine virtuelle. 
    > [!NOTE] 
    > Cela ne revient pas au même que de remplacer la machine virtuelle d’origine. 
    > 
    >

## <a name="what-is-changing-in-the-new-stack"></a>Changements de la nouvelle pile
Aujourd’hui, la tâche de sauvegarde consiste en deux phases :
1.  Prise d’un instantané de la machine virtuelle. 
2.  Transfert de l’instantané de la machine virtuelle vers le coffre de sauvegarde Azure. 

Un point de récupération est considéré comme créé seulement après l’exécution des étapes 1 et 2. Dans le cadre de la nouvelle pile, un point de récupération est créé dès que l’instantané est terminé. Vous pouvez également effectuer une restauration à partir de ce point de récupération en utilisant le même flux de restauration. Vous pouvez identifier ce point de récupération dans le portail Azure en utilisant « snapshot » (instantané) comme type de point de récupération. Une fois l’instantané transféré vers le coffre, le type de point de récupération devient « Snapshot and Vault » (Instantané et coffre). 

![Tâche de sauvegarde dans la pile de sauvegarde de machine virtuelle V2](./media/backup-azure-vms/instant-rp-flow.jpg) 

Par défaut, les instantanés sont conservés pendant sept jours. Cela permet une exécution plus rapide de la restauration à partir de ces instantanés en réduisant le temps nécessaire pour copier des données du coffre vers le compte de stockage client. 

## <a name="considerations-before-upgrade"></a>Considérations à prendre en compte avant une mise à niveau
* Il s’agit d’une mise à niveau unidirectionnelle de la pile de sauvegarde de machine virtuelle. Par conséquent, toutes les futures sauvegardes passeront dans ce flux. Étant donné qu’**il est activé au niveau d’un abonnement, toutes les machines virtuelles passeront par ce flux**. Tous les ajouts de nouvelles fonctionnalités reposeront sur la même pile. La possibilité de contrôler cette option au niveau de la stratégie sera disponible dans de futures versions. 
* Pour les machines virtuelles disposant de disques Premium, pendant la première sauvegarde, vérifiez qu’un espace de stockage équivalant à la taille de la machine virtuelle est disponible dans le compte de stockage jusqu’à la fin de la première sauvegarde. 
* Étant donné que les instantanés sont stockés localement pour optimiser la création des points de récupération et également accélérer la restauration, vous verrez des coûts de stockage correspondant aux instantanés pendant la période de sept jours.
* Si vous effectuez une restauration à partir du point de récupération d’instantané pour une machine virtuelle Premium, vous verrez un emplacement de stockage temporaire utilisé pendant la création de la machine virtuelle dans le cadre de la restauration. 

## <a name="how-to-upgrade"></a>Comment mettre à niveau ?
### <a name="the-azure-portal"></a>Le portail Azure
Si vous utilisez le portail Azure, une notification s’affiche sur le tableau de bord du coffre concernant la prise en charge des disques volumineux et les améliorations de la vitesse de sauvegarde et de restauration.

![Tâche de sauvegarde dans la pile de sauvegarde de machine virtuelle V2](./media/backup-azure-vms/instant-rp-banner.png) 

Pour ouvrir un écran pour la mise à niveau vers la nouvelle pile, sélectionnez la bannière. 

![Tâche de sauvegarde dans la pile de sauvegarde de machine virtuelle V2](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Exécutez les applets de commande suivantes à partir d’un terminal PowerShell avec élévation des privilèges :
1.  Connectez-vous à votre compte Azure. 

```
PS C:> Login-AzureRmAccount
```

2.  Sélectionnez l’abonnement que vous voulez inscrire pour la préversion :

```
PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
```

3.  Inscrivez cet abonnement pour une préversion privée :

```
PS C:>  Register-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

## <a name="verify-whether-the-upgrade-is-complete"></a>Vérifier si la mise à niveau est terminée
À partir d’un terminal PowerShell avec élévation des privilèges, exécutez l’applet de commande suivante :

```
Get-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

Si la sortie indique « Registered », votre abonnement est mis à niveau vers la pile de sauvegarde de machine virtuelle V2. 



