---
title: "Restaurer une machine virtuelle à partir d’une sauvegarde | Microsoft Docs"
description: "Découvrez comment restaurer une machine virtuelle Azure à partir d’un point de récupération"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "restauration de sauvegarde ; restauration ; point de récupération ;"
ms.assetid: fed877b3-b496-49fb-99e4-653be7c23e3a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: trinadhk; jimpark;
ms.openlocfilehash: 5f6e5dd9d4fb96376762300856b594d772d84af8
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="restore-virtual-machines-in-azure"></a>Restauration de machines virtuelles dans Azure
> [!div class="op_single_selector"]
> * [Restaurer des machines virtuelles dans le portail Azure](backup-azure-arm-restore-vms.md)
> * [Restaurer des machines virtuelles dans le portail classique](backup-azure-restore-vms.md)
>
>

Pour restaurer une machine virtuelle sur une nouvelle machine virtuelle à partir des sauvegardes stockées dans le coffre de sauvegarde Azure, procédez comme suit.

> [!IMPORTANT]
> Vous pouvez désormais mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services. Pour en savoir plus, consultez l’article [Mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft vous recommande de mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services. Après <br/> **À compter du 30 novembre 2017**, vous ne pouvez plus vous servir de PowerShell pour créer des coffres de sauvegarde. <br/> **D’ici au 30 novembre 2017** :
>- tous les coffres de sauvegarde restants seront automatiquement mis à niveau vers des coffres Recovery Services.
>- Vous ne pourrez plus accéder à vos données de sauvegarde depuis le portail Classic. Au lieu de cela, vous devrez utiliser le portail Azure pour accéder à ces données au sein de coffres Recovery Services.
>

## <a name="restore-workflow"></a>Flux de travail de restauration
### <a name="step-1-choose-an-item-to-restore"></a>Étape 1 : Choix d’un élément à restaurer
1. Accédez à l’onglet **Éléments protégés** et sélectionnez la machine virtuelle que vous voulez restaurer sur une nouvelle machine virtuelle.

    ![Éléments protégés](./media/backup-azure-restore-vms/protected-items.png)

    La colonne **Point de récupération** de la page **Éléments protégés** vous indique le nombre de points de récupération pour une machine virtuelle. La colonne **Point de récupération le plus récent** indique l’heure de la sauvegarde la plus récente à partir de laquelle une machine virtuelle peut être restaurée.
2. Cliquez sur **Restaurer** pour ouvrir l’Assistant **Restaurer un élément**.

    ![Restaurer un élément](./media/backup-azure-restore-vms/restore-item.png)

### <a name="step-2-pick-a-recovery-point"></a>Étape 2 : Choix d’un point de récupération
1. Dans l’écran **Sélectionner un point de récupération** , vous pouvez effectuer une restauration à partir du point de récupération le plus récent ou d’un point antérieur dans le temps. L’option par défaut sélectionnée à l’ouverture de l’Assistant est *Point de récupération le plus récent*.

    ![Sélectionner un point de récupération](./media/backup-azure-restore-vms/select-recovery-point.png)
2. Pour sélectionner un point antérieur dans le temps, choisissez l’option **Sélectionner une date** dans la liste déroulante, puis sélectionnez une date dans le contrôle Calendrier en cliquant sur **l’icône de calendrier**. Dans le contrôle, toutes les dates associées à des points de récupération sont indiquées par une couleur de remplissage gris clair et peuvent être sélectionnées par l’utilisateur.

    ![Sélectionner une date](./media/backup-azure-restore-vms/select-date.png)

    Quand vous cliquez sur une date dans le contrôle Calendrier, les points de récupération disponibles pour cette date apparaissent dans le tableau de points de récupération ci-dessous. La colonne **Heure** indique l’heure à laquelle l’instantané a été capturé. La colonne **Type** affiche la [cohérence](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) du point de récupération. L’en-tête du tableau indique entre parenthèses le nombre de points de récupération disponibles ce jour-là.

    ![Points de récupération](./media/backup-azure-restore-vms/recovery-points.png)
3. Sélectionnez le point de récupération dans le tableau **Points de récupération** , puis cliquez sur la flèche Suivant pour accéder à l’écran suivant.

### <a name="step-3-specify-a-destination-location"></a>Étape 3 : Spécification d’un emplacement de destination
1. Dans l’écran **Sélectionner l’instance à restaurer** , spécifiez les détails de l’emplacement où restaurer la machine virtuelle.

   * Spécifiez le nom de la machine virtuelle : dans un service cloud donné, le nom de la machine virtuelle doit être unique. Nous ne prenons pas en charge l’écrasement d’une machine virtuelle existante.
   * Sélectionnez un service cloud pour la machine virtuelle : ce champ est obligatoire pour la création d’une machine virtuelle. Vous pouvez utiliser un service cloud existant ou en créer un.

        Quel que soit le nom du service cloud choisi, il doit être globalement unique. En général, le nom du service cloud est associé à une URL publique sous la forme [cloudservice].cloudapp.net. Azure ne vous autorisera pas à créer un service cloud si le nom est déjà utilisé. Si vous choisissez de créer un service cloud, le nom de la machine virtuelle lui sera attribué. Par conséquent, le nom de la machine virtuelle choisi doit être assez unique pour être appliqué au service cloud associé.

        Nous affichons uniquement les services cloud et réseaux virtuels qui ne sont pas associés à des groupes d’affinités dans les détails de l’instance à restaurer. [En savoir plus](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)
2. Sélectionnez un compte de stockage pour la machine virtuelle : ce champ est obligatoire pour la création de la machine virtuelle. Vous pouvez sélectionner un compte de stockage existant dans la même région que l’archivage de sauvegarde Azure. Nous ne prenons pas en charge les comptes de stockage redondants dans une zone ou de type Premium.

    S’il n’existe aucun compte de stockage avec la configuration prise en charge, créez-en un avant de commencer l’opération de restauration.

    ![Sélectionner un réseau virtuel](./media/backup-azure-restore-vms/restore-sa.png)
3. Sélectionnez un réseau virtuel : le réseau virtuel de la machine virtuelle doit être sélectionné au moment de la création de la machine virtuelle. L’interface utilisateur de restauration affiche tous les réseaux virtuels de l’abonnement qui peuvent être utilisés. Il n’est pas obligatoire de sélectionner un réseau virtuel pour la machine virtuelle restaurée, car vous pourrez vous connecter à la machine virtuelle restaurée sur Internet même si le réseau virtuel n’est pas appliqué.

    Si le service cloud sélectionné est associé à un réseau virtuel, vous ne pouvez pas modifier le réseau virtuel.

    ![Sélectionner un réseau virtuel](./media/backup-azure-restore-vms/restore-cs-vnet.png)
4. Sélectionnez un sous-réseau : si le réseau virtuel possède des sous-réseaux, le premier sous-réseau est sélectionné par défaut. Choisissez le sous-réseau de votre choix parmi les options de liste déroulante. Pour plus d’informations sur les sous-réseaux, accédez à la [page d’accueil du portail](https://manage.windowsazure.com/)d’extension de réseaux, accédez à **Réseaux virtuels** , puis sélectionnez un réseau virtuel et explorez les détails des sous-réseaux en cliquant sur Configurer.

    ![Sélectionner un sous-réseau](./media/backup-azure-restore-vms/select-subnet.png)
5. Cliquez sur l’icône **Envoyer** de l’Assistant pour envoyer les détails et créer un travail de restauration.

## <a name="track-the-restore-operation"></a>Suivi de l’opération de restauration
Une fois que vous avez entré toutes les informations dans l’Assistant Restauration et que vous les avez envoyées, Azure Backup essaie de créer un travail pour effectuer le suivi de l’opération de restauration.

![Création d’un travail de restauration](./media/backup-azure-restore-vms/create-restore-job.png)

Si le travail est correctement créé, une notification toast vous en informe. Vous pouvez obtenir plus de détails en cliquant sur le bouton **Afficher le travail** qui vous renvoie vers l’onglet **Travaux**.

![Travail de restauration créé](./media/backup-azure-restore-vms/restore-job-created.png)

Une fois l’opération de restauration terminée, elle est marquée comme terminée sous l’onglet **Travaux** .

![Travail de restauration terminé](./media/backup-azure-restore-vms/restore-job-complete.png)

Après la restauration de la machine virtuelle, vous devrez peut-être réinstaller les extensions de la machine virtuelle d’origine et [modifier les points de terminaison](../virtual-machines/windows/classic/setup-endpoints.md) de la machine virtuelle dans le portail Azure.

## <a name="post-restore-steps"></a>Étapes post-restauration
Si vous utilisez une distribution Linux basée sur cloud-init telle qu’Ubuntu, le mot de passe sera bloqué après la restauration pour des raisons de sécurité. Pour [réinitialiser le mot de passe](../virtual-machines/linux/classic/reset-access.md), utilisez l’extension VMAccess sur la machine virtuelle restaurée. Nous vous recommandons d’utiliser des clés SSH sur ces distributions pour éviter de réinitialiser le mot de passe après la restauration.

## <a name="backup-for-restored-vms"></a>Sauvegarde de machines virtuelles restaurées
Si vous avez restauré une machine virtuelle sur le même service cloud avec le même nom que la machine virtuelle sauvegardée d’origine, la sauvegarde se poursuit sur la machine virtuelle après la restauration. Si vous avez restauré une machine virtuelle sur un autre service cloud ou si vous avez spécifié un autre nom pour la machine virtuelle restaurée, celle-ci sera traitée comme une machine virtuelle nouvelle et vous devrez configurer la sauvegarde pour la machine virtuelle restaurée.

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Restauration d’une machine virtuelle en cas de défaillance du centre de données Azure
Azure Backup permet de restaurer des machines virtuelles sauvegardées sur le centre de données associé en cas de défaillance du centre de données principal sur lequel s’exécutent les machines virtuelles et si vous avez configuré le coffre de sauvegarde pour qu’il soit géo-redondant. Le cas échéant, vous devez sélectionner un compte de stockage qui est présent dans le centre de données associé et le reste du processus de restauration reste le même. Azure Backup utilise le service de calcul du centre de données géo-redondant associé pour créer la machine virtuelle restaurée. En savoir plus sur la [résilience des centres de données Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-domain-controller-vms"></a>Restauration de machines virtuelles d’un contrôleur de domaine
Azure Backup prend en charge la sauvegarde de machines virtuelles d’un contrôleur de domaine. Vous devez toutefois faire preuve de prudence au cours du processus de restauration. Le processus de restauration correct dépend de la structure du domaine. Dans le cas le plus simple, vous disposez d’un seul contrôleur de domaine dans un domaine unique. Plus communément pour les charges de production, vous disposez d’un domaine unique avec plusieurs contrôleurs de domaine, dont certains sont peut-être locaux. Enfin, vous disposez peut-être d’une forêt avec plusieurs domaines.

Du point de vue d’Active Directory, la machine virtuelle Azure est semblable à toute autre machine virtuelle sur un hyperviseur moderne pris en charge. La principale différence avec les hyperviseurs locaux est qu’aucune console de machine virtuelle n’est disponible dans Azure. Une console est nécessaire pour certains scénarios tels que la récupération à l’aide d’une sauvegarde de type Récupération complète (BMR, Bare Metal Recovery). Toutefois, la restauration d’une machine virtuelle à partir du coffre de sauvegarde consiste en un remplacement total de la récupération complète. Le mode DSRM (Active Directory Restore Mode) étant également disponible, tous les scénarios de récupération Active Directory sont viables. Pour plus d’informations, voir les [considérations en matière de sauvegarde et de restauration pour les contrôleurs de domaine virtualisés](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) et la [planification de la récupération de forêt Active Directory](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Contrôleur de domaine unique dans un seul domaine
La machine virtuelle peut être restaurée (comme toute autre machine virtuelle) depuis le portail Azure ou à l’aide de PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Contrôleurs de domaine multiples dans un seul domaine
Lorsque d’autres contrôleurs de domaine du même domaine sont accessibles sur le réseau, le contrôleur de domaine peut être restauré comme n’importe quelle machine virtuelle. S’il s’agit du dernier contrôleur de domaine dans le domaine, ou si une récupération dans un réseau isolé est effectuée, il convient de suivre une procédure de récupération de forêt.

### <a name="multiple-domains-in-one-forest"></a>Domaines multiples dans une seule forêt
Lorsque d’autres contrôleurs de domaine du même domaine sont accessibles sur le réseau, le contrôleur de domaine peut être restauré comme n’importe quelle machine virtuelle. Toutefois, dans tous les autres cas, une récupération de forêt est recommandée.

<!--- WK: this following original supportability statement is incorrect, taking it out.
The challenge arises because DSRM mode is not present in Azure. So to restore such a VM, you cannot use the Azure portal. The only supported restore mechanism is disk-based restore using PowerShell.

> [!WARNING]
> For Domain Controller VMs in a multi-DC environment, do not use the Azure portal for restore! Only PowerShell based restore is supported
>
>

Read more about the [USN rollback problem](https://technet.microsoft.com/library/dd363553) and the strategies suggested to fix it.
--->

## <a name="restoring-vms-with-special-network-configurations"></a>Restauration de machines virtuelles avec des configurations de réseau spéciales
Azure Backup prend en charge la sauvegarde pour les configurations de réseau spéciales de machines virtuelles.

* Machines virtuelles sous un équilibreur de charge (interne et externe)
* Machines virtuelles avec plusieurs adresses IP réservées
* Machines virtuelles avec plusieurs cartes d'interface réseau

Les considérations suivantes doivent être prises en compte lors de la restauration de ces configurations.

> [!TIP]
> Utilisez le flux de restauration basé sur PowerShell pour recréer la configuration de réseau spéciale de machines virtuelles restaurées.
>
>

### <a name="restoring-from-the-ui"></a>Restauration à partir de l'interface utilisateur :
Lors de la restauration à partir de l'interface utilisateur, **choisissez toujours un service cloud**. Veuillez noter que, le portail n'utilisant que des paramètres obligatoires pendant le processus de restauration, les machines virtuelles restaurées à l'aide de l'interface utilisateur perdent la configuration de réseau spéciale dont ils disposent. En d'autres termes, les machines virtuelles restaurées seront normales sans configuration d'un équilibreur de charge ou de plusieurs cartes réseau ou plusieurs adresses IP réservées.

### <a name="restoring-from-powershell"></a>Restauration de PowerShell :
PowerShell ne peut restaurer que des disques de machine virtuelle à partir d'une sauvegarde et non créer la machine virtuelle. Cela est utile lors de la restauration de machines virtuelles qui nécessitent les configurations de réseau spéciales mentionnées ci-dessus.

Pour pouvoir recréer entièrement des disques de machine virtuelle restaurés, procédez comme suit :

1. Restaurez les disques à partir de l’archivage de sauvegarde à l’aide [d’Azure Backup PowerShell](backup-azure-vms-classic-automation.md#restore-an-azure-vm)
2. Créer la configuration de machine virtuelle requise pour l'équilibreur de charge/plusieurs cartes réseau/plusieurs adresse IP réservée à l'aide des applets de commande PowerShell et utilisez-la pour créer la machine virtuelle avec la configuration souhaitée.

   * Créer une machine virtuelle dans le service cloud avec un [équilibreur de charge interne ](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * Créer une machine virtuelle pour vous connecter à [l’équilibreur de charge accessible sur Internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * Créer une machine virtuelle avec [plusieurs cartes d’interface réseau](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * Créer des machines virtuelles avec [plusieurs adresses IP réservées](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>Étapes suivantes
* [Résolution des erreurs](backup-azure-vms-troubleshoot.md#restore)
* [Gestion des machines virtuelles](backup-azure-manage-vms.md)
