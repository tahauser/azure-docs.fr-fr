---
title: "Gérer les serveurs et les coffres Azure Backup à l’aide du modèle de déploiement classique | Microsoft Docs"
description: "Ce didacticiel vous apprend à gérer les serveurs et les coffres Azure Backup."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: f175eb12-0905-437f-91fd-eaee03ab6e81
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: markgal;
ms.openlocfilehash: ecc2eb996546810dad169dc25175ab10ebb164aa
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="manage-azure-backup-vaults-and-servers-using-the-classic-deployment-model"></a>Gérer les serveurs et les coffres Azure Backup à l’aide du modèle de déploiement classique
> [!div class="op_single_selector"]
> * [Gestionnaire de ressources](backup-azure-manage-windows-server.md)
> * [Classique](backup-azure-manage-windows-server-classic.md)
>
>

Cet article offre une vue d’ensemble des tâches de gestion des sauvegardes disponibles via le portail Azure Classic et l’agent Microsoft Azure Backup.

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

> [!IMPORTANT]
> Vous pouvez désormais mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services. Pour en savoir plus, consultez l’article [Mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft vous recommande de mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services.<br/> À compter du 30 novembre 2017, vous ne pouvez pas vous servir de PowerShell pour créer des coffres de sauvegarde. **D’ici au 30 novembre 2017** :
>- tous les coffres de sauvegarde restants seront automatiquement mis à niveau vers des coffres Recovery Services.
>- Vous ne pourrez plus accéder à vos données de sauvegarde depuis le portail Classic. Au lieu de cela, vous devrez utiliser le portail Azure pour accéder à ces données au sein de coffres Recovery Services.
>

## <a name="management-portal-tasks"></a>Tâches du portail de gestion
1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).
2. Cliquez sur **Recovery Services**, puis sur le nom du coffre de sauvegarde pour afficher la page de démarrage rapide.

    ![Recovery Services](./media/backup-azure-manage-windows-server-classic/rs-left-nav.png)

Sélectionnez les options situées en haut de la page de démarrage rapide pour afficher les tâches de gestion disponibles.

![Gérer les onglets](./media/backup-azure-manage-windows-server-classic/qs-page.png)

### <a name="dashboard"></a>Tableau de bord
Sélectionnez **Tableau de bord** pour afficher l’aperçu de l’utilisation du serveur. La **vue d’ensemble de l’utilisation** inclut :

* Le nombre de serveurs Windows inscrits auprès du cloud
* Le nombre de machines virtuelles Azure protégées dans le cloud
* Le stockage total consommé dans Azure
* L’état des travaux récents

En bas du tableau de bord, vous pouvez réaliser les tâches suivantes :

* **Gérer un certificat** : si un certificat a été utilisé pour enregistrer le serveur, utilisez cette tâche pour mettre à jour le certificat. Si vous utilisez des informations d'identification de coffre, n'utilisiez pas **Manage certificate**.
* **Supprimer** : permet de supprimer le coffre de sauvegarde actuel. Si un coffre de sauvegarde n'est plus utilisé, vous pouvez le supprimer pour libérer de l'espace de stockage. **Supprimer** est disponible uniquement une fois que tous les serveurs inscrits ont été supprimés du coffre.

![Tâches du tableau de bord Backup](./media/backup-azure-manage-windows-server-classic/dashboard-tasks.png)

## <a name="registered-items"></a>Éléments inscrits
Sélectionnez **Éléments inscrits** pour afficher le nom des serveurs inscrits auprès de ce coffre.

![Éléments inscrits](./media/backup-azure-manage-windows-server-classic/registered-items.png)

Le filtre **Type** permet de filtrer les valeurs par défaut sur une machine virtuelle Azure. Sélectionnez **Windows Server** dans le menu déroulant pour afficher le nom des serveurs inscrits auprès de ce coffre.

Exécutez ensuite les tâches suivantes :

* **Autoriser la réinscription** : quand cette option est sélectionnée pour un serveur, vous pouvez utiliser **l’Assistant Inscription** dans l’agent de Sauvegarde Microsoft Azure local pour inscrire à nouveau le serveur auprès du coffre de sauvegarde. Vous devrez peut-être effectuer cette nouvelle inscription en raison d'une erreur dans le certificat ou de la nécessité de régénérer un serveur.
* **Supprimer** : permet de supprimer un serveur du coffre de sauvegarde. Toutes les données stockées associées au serveur sont immédiatement supprimées.

    ![Tâches d’éléments inscrits](./media/backup-azure-manage-windows-server-classic/registered-items-tasks.png)

## <a name="protected-items"></a>Éléments protégés
Sélectionnez **Éléments protégés** pour afficher les éléments sauvegardés à partir des serveurs.

![Éléments protégés](./media/backup-azure-manage-windows-server-classic/protected-items.png)

## <a name="configure"></a>Configuration
Dans l’onglet **Configurer** , vous pouvez sélectionner l’option de redondance de stockage appropriée. Nous vous recommandons de sélectionner l’option de redondance de stockage juste après avoir créé un coffre et avant d’y inscrire des ordinateurs.

> [!WARNING]
> Une fois qu’un élément a été inscrit dans l’archivage, l’option de redondance de stockage est verrouillée et ne peut pas être modifiée.
>
>

![Configuration](./media/backup-azure-manage-windows-server-classic/configure.png)

Consultez cet article pour plus d’informations sur la [redondance du stockage](../storage/common/storage-redundancy.md).

## <a name="microsoft-azure-backup-agent-tasks"></a>Tâches de l’agent Microsoft Azure Backup
### <a name="console"></a>Console
Ouvrez l’ **agent Microsoft Azure Backup** (vous pouvez le trouver en recherchant *Microsoft Azure Backup*sur votre ordinateur).

![Agent Backup](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

Dans les **Actions** disponibles à droite de la console de l’agent de sauvegarde, vous pouvez effectuer les tâches de gestion suivantes :

* Inscription du serveur
* Planification d’une sauvegarde
* Sauvegarder maintenant
* Modifier les propriétés

![Actions de la console de l’agent](./media/backup-azure-manage-windows-server-classic/console-actions.png)

> [!NOTE]
> Pour **Récupérer des données**, consultez [Restauration de fichiers sur un serveur Windows ou un ordinateur client Windows](backup-azure-restore-windows-server.md).
>
>

### <a name="modify-an-existing-backup"></a>Modifier une sauvegarde existante
1. Dans l’agent Microsoft Azure Backup, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
2. Dans **l’Assistant Planification de sauvegarde**, conservez l’option **Modifier les éléments ou les dates/heures de sauvegarde** sélectionnée, puis cliquez sur **Suivant**.

    ![Modifier une sauvegarde planifiée](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
3. Si vous souhaitez ajouter ou modifier des éléments, cliquez sur **Ajouter des éléments** dans l’écran **Sélectionner les éléments à sauvegarder**.

    Vous pouvez également définir les **Paramètres d’exclusion** sur cette page de l’Assistant. Si vous souhaitez exclure des fichiers ou des types de fichiers, lisez la procédure permettant d’ajouter des [paramètres d’exclusion](#exclusion-settings).
4. Sélectionnez les fichiers et les dossiers que vous souhaitez sauvegarder, puis cliquez sur **OK**.

    ![Sélectionner les éléments à sauvegarder](./media/backup-azure-manage-windows-server-classic/add-items-modify.png)
5. Spécifiez la **planification de sauvegarde**, puis cliquez sur **Suivant**.

    Vous pouvez planifier des sauvegardes quotidiennes (au maximum 3 fois par jour) ou hebdomadaires.

    ![Spécifier votre planification de sauvegarde](./media/backup-azure-manage-windows-server-classic/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > La définition de la planification de sauvegarde est décrite en détail dans cet [article](backup-azure-backup-cloud-as-tape.md).
   >
   >
6. Sélectionnez la **Stratégie de rétention** pour la copie de sauvegarde et cliquez sur **Suivant**.

    ![Sélectionner votre stratégie de rétention](./media/backup-azure-manage-windows-server-classic/select-retention-policy-modify.png)
7. Dans l’écran **Confirmation**, passez en revue les informations, puis cliquez sur **Terminer**.
8. Dès que l’Assistant a terminé la création de la **planification de sauvegarde**, cliquez sur **Fermer**.

    Après la modification de la protection, vous pouvez confirmer le déclenchement correct des sauvegardes en accédant à l’onglet **Travaux** et en vérifiant que les modifications sont répercutées dans les tâches de sauvegarde.

### <a name="enable-network-throttling"></a>Activation de la limitation du réseau
L’agent Azure Backup contient un onglet Limitation qui vous permet de contrôler l’utilisation de la bande passante réseau pendant le transfert de données. Cela peut s’avérer utile si vous avez besoin de sauvegarder des données pendant les heures de travail, mais ne souhaitez pas que le processus de sauvegarde interfère avec le reste du trafic internet. La limitation du transfert de données s’applique aux activités de sauvegarde et de restauration.  

Pour activer la limitation :

1. Dans **l’agent de Sauvegarde**, cliquez sur **Modifier les propriétés**.
2. Cochez la case **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde** .

    ![Limitation du réseau](./media/backup-azure-manage-windows-server-classic/throttling-dialog.png)
3. Une fois que vous avez activé la limitation, spécifiez la bande passante autorisée pour le transfert des données de sauvegarde durant les **Heures de travail** et les **Heures chômées**.

    Les valeurs de bande passante, qui démarrent à 512 kilo-octets par seconde, peuvent aller jusqu’à 1 023 mégaoctets par seconde. Vous pouvez également définir le début et la fin des **Heures de travail**et identifier les jours de la semaine considérés comme des jours de travail. Les intervalles de temps situés en dehors des heures de travail sont considérés comme des périodes (heures) chômées.
4. Cliquez sur **OK**.

## <a name="exclusion-settings"></a>Paramètres d’exclusion
1. Ouvrez l’ **agent Microsoft Azure Backup** (vous pouvez le trouver en recherchant *Microsoft Azure Backup*sur votre ordinateur).

    ![Ouvrir l’agent Backup](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)
2. Dans l’agent Microsoft Azure Backup, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)
3. Dans l’Assistant Planification de sauvegarde, conservez l’option **Modifier les éléments ou les dates/heures de sauvegarde** sélectionnée, puis cliquez sur **Suivant**.

    ![Modifier une planification](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)
4. Cliquez sur **Paramètres d’exclusion**.

    ![Sélectionner des éléments à exclure](./media/backup-azure-manage-windows-server-classic/exclusion-settings.png)
5. Cliquez sur **Ajouter une exclusion**.

    ![Ajouter des exclusions](./media/backup-azure-manage-windows-server-classic/add-exclusion.png)
6. Sélectionnez l’emplacement, puis cliquez sur **OK**.

    ![Sélectionnez un emplacement pour l’exclusion](./media/backup-azure-manage-windows-server-classic/exclusion-location.png)
7. Ajoutez l’extension de fichier dans le champ **Type de fichier** .

    ![Exclure par type de fichier](./media/backup-azure-manage-windows-server-classic/exclude-file-type.png)

    Ajout d’une extension .mp3

    ![Exemple de type de fichier](./media/backup-azure-manage-windows-server-classic/exclude-mp3.png)

    Pour ajouter une autre extension, cliquez sur **Ajouter une exclusion** et entrez une autre extension de type de fichier (ajout d’une extension .jpeg).

    ![Autre exemple de type de fichier](./media/backup-azure-manage-windows-server-classic/exclude-jpg.png)
8. Lorsque vous avez ajouté toutes les extensions, cliquez sur **OK**.
9. Suivez les instructions de l’Assistant Planification de sauvegarde en cliquant sur **Suivant** jusqu’à la **page de Confirmation**, puis cliquez sur **Terminer**.

    ![Confirmation de l’exclusion](./media/backup-azure-manage-windows-server-classic/finish-exclusions.png)

## <a name="next-steps"></a>Étapes suivantes
* [Restaurer un serveur Windows Server ou un client Windows à partir d’Azure](backup-azure-restore-windows-server.md)
* Pour en savoir plus sur Azure Backup, consultez la [vue d’ensemble d’Azure Backup](backup-introduction-to-azure-backup.md)
* Consultez le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933)
