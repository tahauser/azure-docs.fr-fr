---
title: "Activer la sauvegarde des machines virtuelles Azure lors de la création | Microsoft Docs"
description: "Consultez les étapes pour activer la sauvegarde de machine virtuelle Azure pendant le processus de création."
services: backup, virtual-machines
documentationcenter: 
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 4041fc555fe4b61d10f84236dcae5156c6282fd3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Activer la sauvegarde lors de la création de machines virtuelles Azure 

Le service de sauvegarde Azure fournit une interface pour créer et configurer des sauvegardes dans le cloud. Protégez vos données en effectuant des sauvegardes, appelées points de récupération, à intervalles réguliers. La sauvegarde Azure crée des points de récupération pouvant être stockés dans des coffres de récupération géo-redondants. Cet article explique comment activer la sauvegarde lors de la création d’une machine virtuelle (VM) dans le portail Azure.  

## <a name="log-in-to-azure"></a>Connexion à Azure 

Si vous n’êtes pas connecté à votre compte, connectez-vous sur le [portail Azure](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Créez une machine virtuelle avec la sauvegarde configurée 

1. Dans le coin supérieur gauche du portail Azure, cliquez sur **Nouveau**. 

2. Sélectionnez **Calcul**, puis sélectionnez une image de la machine virtuelle.   

3. Saisissez les informations de la machine virtuelle. Le nom d’utilisateur et le mot de passe que vous avez fournis vous serviront pour vous connecter à la machine virtuelle. Lorsque vous avez terminé, cliquez sur **OK**. 

4. Choisissez la taille de la machine virtuelle.  

5. Sous **Paramètres > Sauvegarde**, cliquez sur **Activé** pour afficher les paramètres de configuration de sauvegarde. Vous pouvez accepter les valeurs par défaut et cliquer sur **OK** sur la page de paramètres pour passer à la page Résumé et créer la machine virtuelle. Si vous souhaitez modifier les valeurs, suivez les étapes suivantes.  

6. Créez ou sélectionnez un coffre Recovery Services, qui contient les sauvegardes de la machine virtuelle. Si vous créez un coffre Recovery Services, vous pouvez choisir un groupe de ressources pour le coffre.  

    ![Page de création de configuration de sauvegarde dans une machine virtuelle](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Le groupe de ressources pour le coffre Recovery Services peut être différent de celui du groupe de ressources pour la machine virtuelle.  
    > 
    > 

7. Par défaut, une stratégie de sauvegarde est sélectionnée pour vous pour protéger rapidement la machine virtuelle. Une stratégie de sauvegarde spécifie la fréquence de prise d’instantanés de sauvegarde et la durée pendant laquelle ces copies de sauvegarde sont conservées. Vous pouvez accepter la stratégie par défaut, ou créer ou sélectionner une autre stratégie de sauvegarde. Pour modifier la stratégie de sauvegarde, sélectionnez **Stratégie de sauvegarde** et modifiez les valeurs de la stratégie.  

8. Lorsque vous êtes satisfait avec les valeurs de configuration de la sauvegarde, cliquez sur **OK** dans la page Paramètres.  

9. Sur la page Résumé, une fois la validation réussie, cliquez sur **Créer** pour créer une machine virtuelle qui utilise les paramètres de sauvegarde configurés. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>Lancer une sauvegarde après la création de la machine virtuelle 

Même si la stratégie de sauvegarde a été créée, il est conseillé de créer une sauvegarde initiale. Pour afficher les informations de sauvegarde pour la machine virtuelle une fois le modèle de création de machine virtuelle terminé, cliquez sur **Sauvegarder** à partir du paramètre **Operations** dans le menu de gauche. Vous pouvez utiliser cela pour déclencher une sauvegarde à la demande, restaurer une machine virtuelle complète ou tous les disques, restaurer des fichiers à partir de la sauvegarde de la machine virtuelle ou modifier la stratégie de sauvegarde associée à la machine virtuelle.  

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ) 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>Quelles images de machine virtuelle activent-elles la sauvegarde au moment de la création de machines virtuelles ? 

Les images principales suivantes publiées par Microsoft sont prises en charge pour l’activation de la sauvegarde lors de la création de machines virtuelles. Pour les autres machines virtuelles, vous pouvez activer la sauvegarde une fois la machine virtuelle créée. Pour en savoir plus, consultez [Activer la sauvegarde une fois la machine virtuelle créée.](quick-backup-vm-portal.md) 

- **Windows** - Windows Server 2016 Datacenter, Windows Server 2016 Datacenter core, Windows Server 2012 Datacenter, Windows Server 2012 R2 Datacenter, Windows Server 2008 R2 SP1 
- **Ubuntu** - Ubuntu Server 1710, Ubuntu Server 1704, UUbuntu Server 1604(LTS), Ubuntu Server 1404(LTS) 
- **Redhat** - RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** : SUSE Linux Enterprise Server 11 SP4, 12 SP2, 12 SP3 
- **Debian** - Debian 8, Debian 9 
- **CentOS** - CentOS 6.9, CentOS 7.3 
- **Oracle Linux** - Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>Le coût de la sauvegarde est-il inclus dans le coût de la machine virtuelle ? 

Non, les coûts de sauvegarde sont séparés, ou distincts, des coûts des machines virtuelles. Pour plus d’informations sur la tarification des sauvegardes, consultez le [site de tarification de sauvegarde](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Quelles autorisations sont-elles requises pour activer la sauvegarde sur une machine virtuelle ? 

Si vous êtes un contributeur de machines virtuelles, vous pouvez activer la sauvegarde sur la machine virtuelle. Si vous utilisez un rôle personnalisé, vous devez disposer des autorisations suivantes pour activer la sauvegarde sur la machine virtuelle. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Si votre coffre Recovery Services et votre machine virtuelle ont des groupes de ressources différents, assurez-vous que vous disposez des autorisations d’écriture dans le groupe de ressources du coffre Recovery Services.  

## <a name="next-steps"></a>Étapes suivantes 

Vous avez protégé votre machine virtuelle. Vous pouvez maintenant consulter les articles suivants pour en savoir plus sur les tâches de gestion de machine virtuelle et la restauration des machines virtuelles. 

- [Gestion et surveillance de vos machines virtuelles](backup-azure-manage-vms.md) 
- [Restauration des machines virtuelles](backup-azure-arm-restore-vms.md) 
