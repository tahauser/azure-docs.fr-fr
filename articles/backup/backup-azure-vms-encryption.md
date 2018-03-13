---
title: "Sauvegarder et restaurer les machines virtuelles chiffrées à l’aide de Sauvegarde Azure"
description: "Cet article présente l’expérience de sauvegarde et de restauration de machines virtuelles chiffrées à l’aide d’Azure Disk Encryption (ADE)."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/13/2017
ms.author: pajosh;markgal;trinadhk; sogup
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4a1a3c5eb5550967e0a0e045ec508a86cd80ee03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Sauvegarder et restaurer des machines virtuelles chiffrées avec Sauvegarde Azure
Cet article présente les étapes pour sauvegarder et restaurer des machines virtuelles à l’aide de Sauvegarde Azure. Il fournit également des détails sur les scénarios pris en charge, les composants requis et les étapes de dépannage en cas d’erreur.

## <a name="supported-scenarios"></a>Scénarios pris en charge

 * La sauvegarde et la restauration de machines virtuelles chiffrées sont prises en charge uniquement pour les machines virtuelles qui utilisent le modèle de déploiement Azure Resource Manager. Ces opérations ne sont pas prises en charge pour les machines virtuelles qui utilisent le modèle de déploiement classique. <br>
 * La sauvegarde et la restauration de machines virtuelles chiffrées sont prises en charge pour les machines virtuelles Windows et Linux qui utilisent Azure Disk Encryption. Disk Encryption utilise la fonctionnalité standard BitLocker Windows et la fonctionnalité dm-crypt de Linux pour assurer le chiffrement des disques. <br>
 
 Le tableau suivant montre les scénarios pris en charge par les machines virtuelles chiffrées avec une clé de chiffrement BitLocker (BEK) uniquement et chiffrées avec une clé de chiffrement de clé (KEK) :
 
 
   |  | Machines virtuelles avec BEK et KEK | Machines virtuelles BEK uniquement |
   | --- | --- | --- |
   | **Machines virtuelles non gérées**  | OUI | OUI  |
   | **Machines virtuelles gérées**  | OUI | OUI  |

## <a name="prerequisites"></a>Prérequis
* La machine virtuelle a été chiffrée à l’aide d’[Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* Un coffre Recovery Services a été créé et la réplication du stockage a été définie en suivant les étapes de la rubrique [Préparation de l’environnement pour la sauvegarde](backup-azure-arm-vms-prepare.md).

* Des [autorisations d’accès au coffre de clés](#provide-permissions-to-azure-backup) contenant des clés et secrets pour les machines virtuelles chiffrées ont été accordées à Sauvegarde Azure.

## <a name="backup-encrypted-vm"></a>Machine virtuelle de sauvegarde chiffrée
Utilisez les étapes suivantes pour définir un objectif de sauvegarde, définir une stratégie, configurer des éléments et déclencher une sauvegarde.

### <a name="configure-backup"></a>Configurer une sauvegarde
1. Si l’un de vos coffres Recovery Services est déjà ouvert, passez à l’étape suivante. Si vous n’avez aucun coffre Recovery Services ouvert, mais que vous vous trouvez dans le portail Azure, sélectionnez **Tous les services**.

   a. Dans la liste des ressources, tapez **Recovery Services**.

   b. Au fur et à mesure de la saisie, la liste est filtrée. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

      ![Coffre Recovery Services](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. La liste des archivages de Recovery Services s’affiche. Sélectionnez un coffre dans la liste.

     Le tableau de bord de l’archivage sélectionné s'ouvre.
2. Dans la liste d’éléments qui s’affiche sous le coffre, sélectionnez **Sauvegarder** pour démarrer la sauvegarde de la machine virtuelle chiffrée.

      ![Panneau Sauvegarde](./media/backup-azure-vms-encryption/select-backup.png)
3. Sur la vignette **Sauvegarde**, sélectionnez **Objectif de sauvegarde**.

      ![Panneau Scénario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. Sous **Où s'exécute votre charge de travail ?**, sélectionnez **Azure**. Sous **Que voulez-vous sauvegarder ?**, sélectionnez **Machine virtuelle**. Sélectionnez ensuite **OK**.

   ![Ouvrir le panneau Scénario](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. Sous **Choisir une stratégie de sauvegarde**, sélectionnez la stratégie de sauvegarde à appliquer au coffre. Sélectionnez ensuite **OK**.

      ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Une liste des détails de la stratégie par défaut apparaît. Pour créer une stratégie, sélectionnez **Créer** dans la liste déroulante. Lorsque vous cliquez sur **OK**, la stratégie de sauvegarde est associée au coffre.

6. Sélectionnez les machines virtuelles chiffrées à associer à la stratégie spécifiée, puis cliquez sur **OK**.

      ![Sélectionner des machines virtuelles chiffrées](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Cette page affiche un message sur les coffres de clés associés aux machines virtuelles chiffrées que vous avez sélectionnées. Sauvegarde Azure requiert l’accès en lecture seule aux clés et clés secrètes dans le coffre de clés. Il utilise ces autorisations pour sauvegarder les clés et les clés secrètes, ainsi que les machines virtuelles associées.<br>
Si vous êtes un **utilisateur membre**, l’activation du processus de sauvegarde permet d’accéder facilement au coffre de clés pour sauvegarder les machines virtuelles chiffrées sans intervention de l’utilisateur.

   ![Message Machines virtuelles chiffrées](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   Si vous êtes un **utilisateur invité**, pour que les sauvegardes fonctionnent, vous devez fournir des autorisations d’accès au coffre de clés au service de sauvegarde. Vous pouvez fournir ces autorisations en suivant les [étapes mentionnées dans la section suivante](#provide-permissions-to-backup).

   ![Message Machines virtuelles chiffrées](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)
 
    Maintenant que vous avez défini tous les paramètres du coffre, sélectionnez **Activer la sauvegarde** en bas de la page. L’option **Activer la sauvegarde** déploie la stratégie dans le coffre et les machines virtuelles.
  
8. La prochaine phase de préparation est l’installation de l’Agent de machine virtuelle ou s’assurer que l’Agent de machine virtuelle est installé. Pour faire de même, suivez les étapes de l’article [Préparation de l’environnement pour la sauvegarde](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Déclencher une tâche de sauvegarde
Suivez les étapes de l’article [Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services](backup-azure-arm-vms.md) pour déclencher un travail de sauvegarde.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Continuer les sauvegardes des machines virtuelles déjà sauvegardées avec le chiffrement activé  
Si vous avez déjà des machines virtuelles en cours de sauvegarde dans un coffre Recovery Services et que leur chiffrement sera activé ultérieurement, vous devez accorder des autorisations à Sauvegarde Azure pour qu’il puisse accéder au coffre de clés et que les sauvegardes continuent. Vous pouvez fournir ces autorisations en suivant le [les étapes de la section suivante](#provide-permissions-to-azure-backup). Ou vous pouvez suivre les étapes PowerShell dans la section « Activer la sauvegarde » de la [documentation PowerShell](backup-azure-vms-automation.md). 

## <a name="provide-permissions-to-backup"></a>Fournir des autorisations à Sauvegarde Azure
Utilisez les étapes suivantes pour fournir à Azure Sauvegarde Azure des autorisations d’accès appropriées au coffre de clés et lui permettre d’effectuer la sauvegarde des machines virtuelles chiffrée.
1. Sélectionnez **Tous les services**, puis recherchez **Coffres de clés**.

    ![Coffres de clés](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. Dans la liste des coffres de clés, sélectionnez celui qui est associé aux machines virtuelles chiffrées et qui doit être sauvegardé.

     ![Sélection de coffre de clés](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. Sélectionnez des **Stratégies d’accès** puis choisissez **Ajouter un nouveau**.

    ![Ajouter un nouveau](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. Choisissez **Sélectionner le principal** puis tapez **Service de gestion de sauvegarde** dans la zone de recherche. 

    ![Rechercher le service de sauvegarde](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. Sélectionnez **Service de gestion de sauvegarde** puis choisissez **Sélectionner**.

    ![Sélection du service de sauvegarde](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. Sous **Configurer à partir du modèle (facultatif)**, sélectionnez **Sauvegarde Azure**. Les autorisations requises sont préremplies dans les listes déroulantes **Autorisations de clé** et **Autorisations du secret**. Si votre machine virtuelle est chiffrée à l’aide de **BEK uniquement**, seules les autorisations du secret sont nécessaires, vous devez donc supprimer la sélection pour les **autorisations de clé**.

    ![Sélection de Sauvegarde Azure](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. Sélectionnez **OK**. Notez que le **service de gestion de sauvegarde** est ajouté aux **stratégies d’accès**. 

    ![Stratégies d’accès](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. Sélectionnez **Enregistrer** pour accorder les autorisations requises à Sauvegarde Azure.

    ![Stratégie d’accès Azure Backup](./media/backup-azure-vms-encryption/save-access-policy.png)

Une fois les autorisations fournies, vous pouvez activer la sauvegarde pour les machines virtuelles chiffrées.

## <a name="restore-an-encrypted-vm"></a>Restaurer une machine virtuelle chiffrée
Pour restaurer une machine virtuelle chiffrée, restaurez tout d’abord les disques en suivant les étapes de la section « Restaurer les disques sauvegardés » de la rubrique [Choisir une configuration de restauration de machine virtuelle](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Ensuite, vous pouvez utiliser l’une des options suivantes :

* Suivez les étapes PowerShell de la rubrique [Créer une machine virtuelle à partir de disques restaurés](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) pour créer une machine virtuelle complète à partir de disques restaurés.
* Ou [utilisez des modèles pour personnaliser une machine virtuelle restaurée](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) afin de créer des machines virtuelles à partir de disques restaurés. Les modèles peuvent uniquement être utilisés pour les points de restauration créés après le 26 avril 2017.

## <a name="troubleshooting-errors"></a>Résolution des erreurs
| Opération | Détails de l’erreur | Résolution : |
| --- | --- | --- |
|Sauvegarde | Sauvegarde Azure ne dispose pas des autorisations d’accès suffisantes au coffre de clés pour la sauvegarde de machines virtuelles chiffrées. | Vous devez accorder ces autorisations à Sauvegarde Azure en suivant les [étapes de la section précédente](#provide-permissions-to-azure-backup). Vous pouvez également suivre les étapes PowerShell dans la section « Activer la protection » de la documentation PowerShell sous [Utilisez les applets de commande AzureRM.RecoveryServices.Backup pour sauvegarder des machines virtuelles](backup-azure-vms-automation.md#back-up-azure-vms). |  
| Restore |Vous ne pouvez pas restaurer cette machine virtuelle chiffrée car le coffre de clés associé à cette machine virtuelle n’existe pas. |Créez un coffre de clés en suivant les instructions de l’article [Bien démarrer avec Azure Key Vault](../key-vault/key-vault-get-started.md). Consultez [Restaurer la clé et le secret de coffre de clés à l’aide de Sauvegarde Azure](backup-azure-restore-key-secret.md) pour restaurer une clé et une clé secrète si celles-ci n’existent pas. |
| Restore |Vous ne pouvez pas restaurer cette machine virtuelle chiffrée car la clé et la clé secrète associées à cette machine virtuelle n’existent pas. |Consultez [Restaurer la clé et le secret de coffre de clés à l’aide de Sauvegarde Azure](backup-azure-restore-key-secret.md) pour restaurer une clé et une clé secrète si celles-ci n’existent pas. |
| Restore |Sauvegarde Azure n’a pas l’autorisation d’accéder aux ressources de votre abonnement. |Comme mentionné précédemment, restaurez tout d’abord les disques en suivant les étapes de la section « Restaurer les disques sauvegardés » de la rubrique [Choisir une configuration de restauration de machine virtuelle](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Ensuite, utilisez PowerShell pour [créer une machine virtuelle à partir de disques restaurés](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
