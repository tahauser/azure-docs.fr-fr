---
title: "Sauvegarde Azure : Restaurer des machines virtuelles à l’aide du portail Azure | Microsoft Docs"
description: "Restaurer une machine virtuelle Azure à partir d’un point de récupération à l’aide du portail Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "restauration de sauvegarde ; restauration ; point de récupération ;"
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: 84fb2cc08e97541d2d9d327ca2b6865ff9a6fe20
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Utiliser le portail Azure pour restaurer des machines virtuelles
Protégez vos données en prenant des instantanés de vos données à des intervalles définis. Ces instantanés sont considérés comme des points de récupération stockés dans des coffres Recovery Services. Lorsqu’il est nécessaire de réparer ou de générer de nouveau une machine virtuelle, sa restauration peut s’effectuer à partir des points de récupération enregistrés. Lorsque vous restaurez un point de récupération, vous pouvez :

* Créer une machine virtuelle en tant que représentation jusqu’à une date et une heure de votre machine virtuelle sauvegardée.
* Restaurer des disques et utiliser le modèle fourni avec le processus pour personnaliser la machine virtuelle restaurée, ou effectuer une récupération de fichier individuel. 

Cet article explique comment restaurer une machine virtuelle sur une nouvelle machine virtuelle ou restaurer tous les disques de secours. Pour la récupération de fichier individuel, consultez [Récupérer des fichiers à partir d’une sauvegarde de machine virtuelle Azure](backup-azure-restore-files-from-vm.md).

![Trois méthodes pour restaurer à partir d’une sauvegarde de machine virtuelle](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure comporte deux modèles de déploiement pour la création et l’utilisation de ressources : [Azure Resource Manager et Classic](../azure-resource-manager/resource-manager-deployment-model.md). Cet article fournit les informations et les procédures utilisées pour restaurer des machines virtuelles déployées à l’aide du modèle Resource Manager.
>
>

La restauration d’une machine virtuelle ou de tous les disques à partir de la sauvegarde de machine virtuelle implique deux étapes :

* Sélectionnez un point pour la restauration.
* Sélectionnez le type de restauration, créez une machine virtuelle ou restaurez des disques, et spécifiez les paramètres requis. 

## <a name="select-a-restore-point-for-restore"></a>Sélectionner un point pour la restauration
1. Connectez-vous au [Portail Azure](http://portal.azure.com/).

2. Dans le menu Azure, sélectionnez **Parcourir**. Dans la liste des services, tapez **Recovery Services**. La liste des services s’ajuste en fonction de ce que vous tapez. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

    ![Coffre Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    La liste des coffres de l’abonnement s’affiche.

    ![Liste des coffres Recovery Services](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. Dans la liste, sélectionnez le coffre associé à la machine virtuelle que vous souhaitez restaurer. Lorsque vous sélectionnez le coffre, son tableau de bord s’affiche.

    ![Coffre Recovery Services sélectionné](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. Sur la vignette **Éléments de sauvegarde** du tableau de bord du coffre, sélectionnez **Machines virtuelles Azure**.

    ![Tableau de bord du coffre](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    Le panneau **Éléments de sauvegarde** s’ouvre et affiche la liste des machines virtuelles Azure.

    ![Liste des machines virtuelles du coffre](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. Dans la liste, sélectionnez une machine virtuelle pour ouvrir le tableau de bord. Le tableau de bord de la machine virtuelle s’ouvre sur la zone de surveillance qui contient la vignette **Points de restauration**.

    ![Points de restauration](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. Dans le menu du tableau de bord de la machine virtuelle, sélectionnez **Restaurer**.

    ![Bouton Restaurer](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    Le panneau **Restauration** s’ouvre.

    ![Panneau Restauration](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. Sur le panneau **Restauration**, sélectionnez **Point de restauration**. Le panneau **Sélectionner le point de restauration** s’ouvre.

    ![Sélectionner le point de restauration](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    Par défaut, la boîte de dialogue affiche tous les points de restauration des 30 derniers jours. Utilisez le **filtre** pour modifier la plage d’heures des points de restauration affichés. Par défaut, les points de restauration de toutes les cohérences sont affichés. Modifiez le filtre **Tous les points de restauration** afin de sélectionner une cohérence spécifique des points de restauration. Pour plus d’informations sur chaque type de point de restauration, consultez [Cohérence des données](backup-azure-vms-introduction.md#data-consistency).

    Options de cohérence de point de restauration :

     * Points de restauration cohérents d’incident
     * Points de restauration cohérents d’application
     * Points de restauration cohérents de système de fichiers
     * Tous les points de restauration

8. Choisissez un point de restauration, puis sélectionnez **OK**.

    ![Options de point de restauration](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    Le panneau **Restaurer** indique que le point de restauration est défini.

9. Si vous n’en êtes pas encore là, accédez au panneau **Restaurer** . Vérifiez qu’un [point de restauration est sélectionné](#select-restore-point-for-restore), puis choisissez **Configuration de la restauration**. Le panneau **Configuration de la restauration** s’ouvre.

## <a name="choose-a-vm-restore-configuration"></a>Choisir une configuration de restauration de machine virtuelle
Après avoir sélectionné le point de restauration, choisissez une configuration de restauration de machine virtuelle. Pour configurer la machine virtuelle restaurée, vous pouvez utiliser le portail Azure ou PowerShell.

1. Si vous n’en êtes pas encore là, accédez au panneau **Restaurer** . Vérifiez qu’un [point de restauration est sélectionné](#select-restore-point-for-restore), puis choisissez **Configuration de la restauration**. Le panneau **Configuration de la restauration** s’ouvre.

    ![Assistant Configuration de restauration](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. Dans le panneau **Restaurer la configuration**, vous avez deux possibilités :

   * **Créer une machine virtuelle**

   * **Restaurer des disques**

Le portail fournit une option **Création rapide** pour la machine virtuelle restaurée. Pour personnaliser la configuration de la machine virtuelle ou les noms des ressources créées dans le cadre de la création d’un choix de machines virtuelles, utilisez PowerShell ou le portail pour restaurer des disques sauvegardés. Utilisez les commandes PowerShell pour les associer à votre choix de configuration de machine virtuelle. Vous pouvez également utiliser le modèle fourni avec les disques restaurés pour personnaliser la machine virtuelle restaurée. Pour plus d’informations sur la façon de restaurer une machine virtuelle possédant plusieurs cartes réseau ou sous un équilibrage de charge, voir [Restaurer une machine virtuelle avec des configurations réseau spéciales](#restore-vms-with-special-network-configurations). Si vous utilisez des [licences HUB](../virtual-machines/windows/hybrid-use-benefit-licensing.md), restaurez des disques et utilisez PowerShell/modèle comme indiqué dans cet article pour créer la machine virtuelle. Veillez à spécifier **LicenseType** comme « Windows_Server » lorsque vous créez la machine virtuelle pour profiter des avantages HUB sur la machine virtuelle restaurée. 
 
## <a name="create-a-new-vm-from-a-restore-point"></a>Créer une machine virtuelle à partir d’un point de restauration
1. Si ce n’est pas encore fait, [sélectionnez un point de restauration](#restore-a vm-with-special-network-configurations) avant de procéder à la création d’une machine virtuelle à partir d’un point de restauration. Une fois le point de restauration sélectionné, sur le panneau **Configuration de la restauration**, entrez ou sélectionnez des valeurs pour chacun des champs suivants :

    a. **Type de restauration**. Création d’une machine virtuelle

    b. **Nom de machine virtuelle**. Donnez un nom à la machine virtuelle. Le nom doit être unique pour le groupe de ressources (pour une machine virtuelle déployée à l'aide d’Azure Resource Manager) ou le service cloud (pour une machine virtuelle classique). Vous ne pouvez pas remplacer la machine virtuelle si elle existe déjà dans l’abonnement.

    c. **Groupe de ressources**. Utilisez un groupe de ressources existant ou créez-en un. Si vous restaurez une machine virtuelle classique, utilisez ce champ pour spécifier le nom d’un nouveau service cloud. Si vous créez un nouveau groupe de ressources/service cloud, le nom doit être globalement unique. En général, le nom du service cloud est associé à une URL publique. Par exemple : [cloudservice]. cloudapp.net. Si vous essayez d’utiliser un nom de service cloud/groupe de ressources cloud déjà utilisé, Azure attribue au service cloud/groupe de ressources le même nom que la machine virtuelle. Azure affiche les groupes de ressources/services cloud et les machines virtuelles qui ne sont associées à aucun groupe d’affinités. Pour en savoir plus, consultez [Comment migrer des groupes d’affinités vers un réseau virtuel régional](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Réseau virtuel**. Sélectionnez le réseau virtuel lorsque vous créez la machine virtuelle. Le champ indique tous les réseaux virtuels associés à l’abonnement. Le groupe de ressources de la machine virtuelle s’affiche entre parenthèses.

    e. **Sous-réseau**. Si le réseau virtuel a des sous-réseaux, le premier sous-réseau est sélectionné par défaut. En présence de sous-réseaux supplémentaires, sélectionnez le sous-réseau souhaité.

    f. **Compte de stockage**. Ce menu répertorie les comptes de stockage au même emplacement que le coffre Recovery Services. Les comptes de stockage redondant dans une zone ne sont pas pris en charge. S’il n’existe aucun compte de stockage avec le même emplacement que le coffre Recovery Services, vous devez en créer un avant de commencer l’opération de restauration. Le type de réplication du compte de stockage est indiqué entre parenthèses.

    ![Assistant Configuration de restauration](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

    > [!NOTE]
    > * Si vous restaurez une machine virtuelle déployée à l'aide de Resource Manager, vous devez identifier un réseau virtuel. Un réseau virtuel est facultatif pour une machine virtuelle classique.

    > * Si vous restaurez des machines virtuelles avec des disques gérés, vérifiez que le chiffrement du service de stockage Azure n’est pas activé sur le compte de stockage sélectionné au cours de la durée de vie de celui-ci.

    > * Selon le type de stockage du compte de stockage sélectionné (Premium ou Standard), tous les disques restaurés seront des disques Premium ou Standard. Actuellement, le mode mixte de disques n’est pas pris en charge lors de la restauration.
    >
    >

2. Sur le panneau **Configuration de la restauration**, sélectionnez **OK** pour finaliser la configuration de la restauration. Sur le panneau **Restaurer**, sélectionnez **Restaurer** pour déclencher l’opération de restauration.

## <a name="restore-backed-up-disks"></a>Restaurer des disques sauvegardés
Pour personnaliser la machine virtuelle que vous souhaitez créer à partir des disques sauvegardés d’une façon différente de celle qui est spécifiée dans le panneau de **configuration de la restauration**, sélectionnez **Restaurer les disques** comme valeur de **Restaurer le type**. Ce choix exige un compte de stockage sur lequel copier les disques à partir des sauvegardes. Lorsque vous choisissez un compte de stockage, sélectionnez un compte qui partage le même emplacement que le coffre Recovery Services. Les comptes de stockage redondant dans une zone ne sont pas pris en charge. S’il n’existe aucun compte de stockage avec le même emplacement que le coffre Recovery Services, vous devez en créer un avant de commencer l’opération de restauration. Le type de réplication du compte de stockage est indiqué entre parenthèses.

Une fois l’opération de restauration terminée, vous pouvez :

* [Utiliser le modèle pour personnaliser la machine virtuelle restaurée](#use-templates-to-customize-restore-vm)
* [Utiliser les disques restaurés pour les attacher à une machine virtuelle existante](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Créer une machine virtuelle à l’aide de PowerShell à partir de disques restaurés](./backup-azure-vms-automation.md#restore-an-azure-vm)

Sur le panneau **Configuration de la restauration**, sélectionnez **OK** pour finaliser la configuration de la restauration. Sur le panneau **Restaurer**, sélectionnez **Restaurer** pour déclencher l’opération de restauration.

![Configuration de la récupération terminée](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Suivi de l’opération de restauration
Après avoir déclenché l’opération de restauration, le service de sauvegarde crée une tâche pour le suivi de l’opération de restauration. Par ailleurs, le service de sauvegarde crée et affiche temporairement la notification dans la zone **Notifications** du portail. Si vous ne voyez pas la notification, sélectionnez le symbole **Notifications** pour afficher vos notifications.

![Restauration déclenchée](./media/backup-azure-arm-restore-vms/restore-notification.png)

Pour voir l’opération en cours de traitement, ou l’afficher lorsqu’elle est terminée, ouvrez la liste des **tâches de sauvegarde**.

1. Dans le menu Azure, sélectionnez **Parcourir** et, dans la liste des services, tapez **Recovery Services**. La liste des services s’ajuste en fonction de ce que vous tapez. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

    ![Ouvrir le coffre Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    La liste des coffres de l’abonnement s’affiche.

    ![Liste des coffres Recovery Services](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. Dans la liste, sélectionnez le coffre associé à la machine virtuelle que vous avez restaurée. Lorsque vous sélectionnez le coffre, son tableau de bord s’affiche.

3. Sur la vignette **Travaux de sauvegarde** du tableau de bord du coffre, sélectionnez **Machines virtuelles Azure** pour afficher les travaux associés au coffre.

    ![Tableau de bord du coffre](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    Le panneau **Travaux de sauvegarde** s’ouvre et affiche la liste des travaux.

    ![Liste des machines virtuelles d’un coffre](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-a-restored-vm"></a>Utiliser des modèles pour personnaliser une machine virtuelle restaurée
À la [fin de l’opération de restauration des disques](#Track-the-restore-operation), utilisez le modèle qui a été généré dans le cadre de l’opération de restauration pour créer une machine virtuelle avec une configuration différente de la configuration de sauvegarde. Vous pouvez également l’utiliser pour personnaliser les noms des ressources qui ont été créés pendant le processus de création d’une machine virtuelle à partir d’un point de restauration. 

> [!NOTE]
> Les modèles sont ajoutés dans le cadre de la restauration des disques pour les points de récupération effectuée après le 1er mars 2017. Ils sont applicables aux machines virtuelles avec disque non géré. La prise en charge des machines virtuelles avec disque géré est prévue dans une prochaine version. 
>
>

Pour obtenir le modèle qui a été généré dans le cadre de l’option de restauration des disques :

1. Accédez aux détails de la tâche correspondant à l’opération de restauration.

2. Dans la fenêtre **Détails du travail de restauration**, sélectionnez **Déployer un modèle** pour lancer le déploiement du modèle. 

     ![Détail du travail de restauration](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
3. Sur le panneau **Déployer un modèle**, utilisez la procédure de déploiement pour [modifier et déployer le modèle](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), ou ajoutez des personnalisations en [créant un modèle](../azure-resource-manager/resource-group-authoring-templates.md) avant d’effectuer le déploiement. 

   ![Charger un déploiement de modèle](./media/backup-azure-arm-restore-vms/loading-template.png)
   
4. Après avoir entré les valeurs requises, acceptez les **Conditions générales**, puis sélectionnez **Acheter**.

   ![Envoyer un déploiement de modèle](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>Étapes post-restauration
* Si vous utilisez une distribution Linux basée sur cloud-init telle qu’Ubuntu, le mot de passe est bloqué après la restauration pour des raisons de sécurité. Utilisez l’extension VMAccess sur la machine virtuelle restaurée pour [réinitialiser le mot de passe](../virtual-machines/linux/reset-password.md). Nous vous recommandons d’utiliser des clés SSH sur ces distributions pour éviter de réinitialiser le mot de passe après la restauration.
* Les extensions présentes au cours de la configuration de sauvegarde sont installées, mais pas activées. Si vous rencontrez un problème, réinstallez les extensions. 
* Si la machine virtuelle sauvegardée a une IP statique, la machine virtuelle restaurée possède une adresse IP dynamique après la restauration pour éviter tout conflit lorsque vous créez une machine virtuelle restaurée. Découvrez comment [ajouter une adresse IP statique à une machine virtuelle restaurée](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* Une machine virtuelle restaurée n’a aucune valeur de disponibilité définie. Nous recommandons d’utiliser l’option de restauration des disques et [d’ajouter un groupe à haute disponibilité](../virtual-machines/windows/tutorial-availability-sets.md) lorsque vous créez une machine virtuelle depuis PowerShell ou de modèles à l’aide de disques restaurés. 


## <a name="backup-for-restored-vms"></a>Sauvegarde de machines virtuelles restaurées
Si vous avez restauré une machine virtuelle sur le même groupe de ressources avec le même nom que la machine virtuelle sauvegardée d’origine, la sauvegarde se poursuit sur la machine virtuelle après la restauration. Si vous avez restauré la machine virtuelle sur un groupe de ressources différent, ou que vous avez spécifié un nom différent pour la machine virtuelle restaurée, la machine virtuelle est traitée comme une nouvelle machine virtuelle. Vous devez configurer la sauvegarde pour la machine virtuelle restaurée.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Restaurer une machine virtuelle en cas de défaillance du centre de données Azure
Azure Backup permet de restaurer des machines virtuelles sauvegardées sur le centre de données associé en cas de défaillance du centre de données principal sur lequel s’exécutent les machines virtuelles et si vous avez configuré le coffre de sauvegarde pour qu’il soit géo-redondant. Pour ces scénarios, sélectionnez un compte de stockage situé dans un centre de données associé. Le reste du processus de restauration ne change pas. Azure Backup utilise le service de calcul du centre de données géo-redondant associé pour créer la machine virtuelle restaurée. Pour plus d’informations, consultez [Résilience du centre de données Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Restaurer les machines virtuelles d’un contrôleur de domaine
Azure Backup prend en charge la sauvegarde de machines virtuelles d’un contrôleur de domaine. Vous devez toutefois faire preuve de prudence au cours du processus de restauration. Le processus de restauration correct dépend de la structure du domaine. Dans le cas le plus simple, vous disposez d’un seul contrôleur de domaine dans un domaine unique. Plus communément pour les charges de production, vous disposez d’un domaine unique avec plusieurs contrôleurs de domaine, dont certains sont peut-être locaux. Enfin, vous disposez peut-être d’une forêt avec plusieurs domaines. 

Du point de vue d’Active Directory, la machine virtuelle Azure est semblable à toute autre machine virtuelle sur un hyperviseur moderne pris en charge. La principale différence avec les hyperviseurs locaux est qu’aucune console de machine virtuelle n’est disponible dans Azure. Une console est nécessaire pour certains scénarios tels que la récupération à l’aide d’une sauvegarde de type Récupération complète (BMR, Bare Metal Recovery). Toutefois, la restauration d’une machine virtuelle à partir du coffre de sauvegarde consiste en un remplacement total de la récupération complète. Le mode DSRM (Directory Restore Mode) étant également disponible, tous les scénarios de récupération Active Directory sont viables. Pour plus d’informations, voir les [considérations en matière de sauvegarde et de restauration pour les contrôleurs de domaine virtualisés](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) et la [planification de la récupération de forêt Active Directory](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Contrôleur de domaine unique dans un seul domaine
La machine virtuelle peut être restaurée (comme toute autre machine virtuelle) depuis le portail Azure ou à l’aide de PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Contrôleurs de domaine multiples dans un seul domaine
Lorsque d’autres contrôleurs de domaine du même domaine sont accessibles sur le réseau, le contrôleur de domaine peut être restauré comme n’importe quelle machine virtuelle. S’il s’agit du dernier contrôleur de domaine dans le domaine, ou si une récupération dans un réseau isolé est effectuée, il convient de suivre une procédure de récupération de forêt.

### <a name="multiple-domains-in-one-forest"></a>Domaines multiples dans une seule forêt
Lorsque d’autres contrôleurs de domaine du même domaine sont accessibles sur le réseau, le contrôleur de domaine peut être restauré comme n’importe quelle machine virtuelle. Dans tous les autres cas, une récupération de forêt est recommandée.

## <a name="restore-vms-with-special-network-configurations"></a>Restaurer des machines virtuelles avec des configurations réseau spéciales
Il est possible de sauvegarder et de restaurer des machines virtuelles avec les configurations réseau spéciales suivantes. Toutefois, ces configurations requièrent une attention particulière au cours du processus de restauration :

* Machines virtuelles sous des équilibreurs de charge (internes et externes)
* Machines virtuelles avec plusieurs adresses IP réservées
* Machines virtuelles avec plusieurs cartes d'interface réseau

> [!IMPORTANT]
> Lorsque vous créez la configuration réseau spéciale pour les machines virtuelles, vous devez utiliser PowerShell pour créer des machines virtuelles à partir des disques restaurés.
>
>

Pour pouvoir recréer entièrement les machines virtuelles après les avoir restaurées sur le disque, procédez comme suit :

1. Restaurez les disques à partir d’un coffre Recovery Services à l’aide de [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

2. Créez la configuration de machine virtuelle requise pour l’équilibreur de charge/plusieurs cartes réseau/plusieurs adresses IP réservées à l’aide des applets de commande PowerShell. Utilisez-la pour créer la machine virtuelle avec la configuration souhaitée :

   a. Créer une machine virtuelle dans le service cloud avec un [équilibreur de charge interne](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Créer une machine virtuelle pour vous connecter à [l’équilibreur de charge accessible sur Internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/).

   c. Créer une machine virtuelle avec [plusieurs cartes d’interface réseau](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/).

   d. Créer une machine virtuelle avec [plusieurs adresses IP réservées](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous pouvez restaurer vos machines virtuelles, consultez l’article de résolution des problèmes pour en savoir plus sur les erreurs courantes relatives aux machines virtuelles. En outre, consultez l’article sur la gestion des tâches avec vos machines virtuelles.

* [Résolution des erreurs](backup-azure-vms-troubleshoot.md#restore)
* [Gestion des machines virtuelles](backup-azure-manage-vms.md)
