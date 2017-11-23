---
title: " Supprimer un coffre Recovery Services dans Azure | Microsoft Docs "
description: "Comment supprimer un coffre Azure Backup et Recovery Services. Un coffre de sauvegarde peut être appelé un coffre cloud Azure ou un coffre de récupération Azure. Résolution des problèmes lorsque vous ne pouvez pas supprimer un coffre de sauvegarde dans le portail Classic ou le portail Azure."
services: service-name
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 5fa08157-2612-4020-bd90-f9e3c3bc1806
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/10/2017
ms.author: markgal;trinadhk
ms.openlocfilehash: b07b9e01a5a8d8a5189b130fb5a9baeef7a43f4f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="delete-a-recovery-services-vault"></a>Supprimer un coffre Recovery Services
Le service Sauvegarde Azure comporte deux types de coffres : le coffre de sauvegarde et le coffre Recovery Services. Le coffre de sauvegarde a été créé en premier. Ensuite, le coffre Recovery Services est venu prendre en charge les déploiements Resource Manager étendus. Étant donné les capacités étendues et les dépendances des informations qui doivent être stockées dans le coffre, la suppression d’un coffre Backup ou Recovery Services peut prêter à confusion. Cet article explique comment supprimer le coffre dans le portail Classic et le portail Azure.  

| **Type de déploiement** | **Portail** | **Nom du coffre** |
| --- | --- | --- |
| Classique |Classique |Archivage de sauvegarde |
| Gestionnaire de ressources |Microsoft Azure |Coffre Recovery Services |

> [!NOTE]
> Les coffres de sauvegarde ne peuvent pas protéger les solutions déployées par le biais de Resource Manager. Toutefois, vous pouvez utiliser un coffre Recovery Services pour protéger les serveurs et les machines virtuelles déployés de façon classique.  
>

> [!IMPORTANT]
> Vous pouvez désormais mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services. Pour en savoir plus, consultez l’article [Mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft vous recommande de mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services.<br/> À partir du **30 novembre 2017**, vous ne pouvez pas vous servir de PowerShell pour créer des coffres de sauvegarde. <br/> **D’ici au 30 novembre 2017** :
>- Les coffres de sauvegarde restants seront automatiquement mis à niveau vers des coffres Recovery Services.
>- Vous ne pourrez plus accéder à vos données de sauvegarde depuis le portail Classic. Au lieu de cela, vous devrez utiliser le portail Azure pour accéder à ces données au sein de coffres Recovery Services.
>

Dans cet article, nous utilisons le terme de « coffre » pour faire référence de manière générique au coffre de sauvegarde ou au coffre Recovery Services. Nous utilisons les termes officiels, coffre de sauvegarde ou coffre Recovery Services lorsqu’il est nécessaire de distinguer les coffres.

## <a name="deleting-a-recovery-services-vault"></a>Supprimer un coffre Recovery Services
Supprimer un coffre Recovery Services est un processus en une seule étape, *à condition que le coffre ne contienne aucune ressource*. Avant de pouvoir supprimer un coffre Recovery Services, vous devez retirer ou supprimer toutes les ressources qu’il contient. Si vous essayez de supprimer un coffre qui contient des ressources, vous obtenez une erreur ressemblant à l’image suivante :

![Erreur de suppression du coffre](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Le fait de cliquer sur **Réessayer** génère la même erreur jusqu’à ce que vous effaciez les ressources du coffre. Si ce message d’erreur persiste, cliquez sur **Annuler** et procédez comme suit pour supprimer les ressources dans le coffre.

### <a name="removing-the-items-from-a-vault-protecting-a-vm"></a>Suppression des éléments d’un coffre protégeant une machine virtuelle
Si le coffre Recovery Services est déjà ouvert, passez à la deuxième étape.

1. Ouvrez le Portail Azure et, depuis le tableau de bord, ouvrez le coffre à supprimer.

   Si le coffre Recovery Services n’est pas épinglé au tableau de bord, dans le menu Hub, cliquez sur **Plus de services** et, dans la liste des ressources, tapez **Recovery Services**. Au fur et à mesure de la saisie, la liste est filtrée. Cliquez sur **Coffres Recovery Services**.

   ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   La liste des coffres Recovery Services est affichée. Dans la liste, sélectionnez le coffre à supprimer.

   ![choisir le coffre à partir de la liste](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. Dans la vue du coffre, examinez le volet **Essentials** . Pour supprimer un coffre, il ne doit y avoir aucun élément protégé. Si vous voyez un nombre différent de zéro, sous **Éléments de sauvegarde** ou **Serveurs de gestion des sauvegardes**, vous devez supprimer ces éléments avant de pouvoir supprimer le coffre.

    ![Examiner le volet Essentials pour identifier les éléments protégés](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    Les machines virtuelles et fichiers/dossiers sont considérés comme des éléments de sauvegarde et sont répertoriés dans la zone **Éléments de sauvegarde** du volet Essentials. Un serveur DPM est indiqué dans la zone **Serveurs de gestion des sauvegardes** du volet Essentials. **Éléments répliqués** appartiennent au service Azure Site Recovery.
3. Pour commencer à supprimer les éléments protégés du coffre, recherchez les éléments dans le coffre. Dans le tableau de bord du coffre, cliquez sur **Paramètres**, puis sur **Éléments de sauvegarde** pour ouvrir ce panneau.

    ![choisir le coffre à partir de la liste](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    Le panneau **Éléments de sauvegarde** comporte des listes séparées selon le type d’élément : Machines virtuelles Azure ou Fichiers-Dossiers (voir l’image). La liste Type d’élément par défaut affichée est Machines virtuelles Azure. Pour afficher la liste des éléments Fichiers-Dossiers dans le coffre, sélectionnez **Fichiers-Dossiers** dans le menu déroulant.
4. Avant de pouvoir supprimer un élément dans le coffre protégeant une machine virtuelle, vous devez arrêter la tâche de sauvegarde de l’élément et supprimer les données du point de récupération. Pour chaque élément dans le coffre, procédez comme suit :

    a. Dans le panneau **Éléments de sauvegarde**, cliquez avec le bouton droit sur l’élément et, à partir du menu contextuel, sélectionnez **Arrêter la sauvegarde**.

    ![arrêter la tâche de sauvegarde](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    Le panneau Arrêter la sauvegarde s’ouvre.

    b. Sur le panneau **Arrêter la sauvegarde**, à partir du menu **Choisir une option**, sélectionnez **Supprimer les données de sauvegarde** > tapez le nom de l’élément > et cliquez sur **Arrêter la sauvegarde**.

    Tapez le nom de l’élément pour confirmer que vous souhaitez le supprimer. Le bouton **Arrêter la sauvegarde** s’active après avoir vérifié l’élément. Si vous ne voyez pas de boîte de dialogue permettant de taper le nom de l’élément de sauvegarde, vous avez choisi l’option **Conserver les données de sauvegarde** .

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Si vous le souhaitez, vous pouvez indiquer une raison expliquant pourquoi vous supprimez les données, et ajouter des commentaires. Après avoir cliqué sur **Arrêter la sauvegarde**, laissez la tâche de suppression se terminer avant d’essayer de supprimer le coffre. Pour vérifier que la tâche est terminée, vérifiez les messages Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Une fois la tâche terminée, vous recevrez un message indiquant que le processus de sauvegarde a été arrêté et que les données de sauvegarde ont été supprimées pour cet élément.

    c. Après la suppression d’un élément de la liste, dans le menu **Éléments de sauvegarde**, cliquez sur **Actualiser** pour voir les éléments restants dans le coffre.

      ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/empty-items-list.png)

      Lorsque la liste est vide, faites défiler jusqu’au volet **Essentials** dans le panneau du coffre de sauvegarde. Il ne doit pas contenir **d’Éléments de sauvegarde**, de **Serveurs de gestion des sauvegardes** ou **d’Éléments répliqués** répertoriés. Si des éléments apparaissent toujours dans le coffre, retournez à l’étape trois et choisissez une autre liste Type d’élément.  
5. Une fois que la barre d’outils du coffre est vide, cliquez sur **Supprimer**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-vault.png)
6. Pour confirmer la suppression du coffre, cliquez sur **Oui**.

    Le coffre est supprimé et le portail revient au menu de service **Nouveau** .

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>Que se passe-t-il si j’ai arrêté le processus de sauvegarde mais que j’ai conservé les données ?
Si vous avez arrêté le processus de sauvegarde, mais que vous avez accidentellement *conservé* les données, vous devez supprimer les données de sauvegarde avant de pouvoir supprimer le coffre. Pour supprimer les données de sauvegarde :

1. Dans le panneau **Éléments de sauvegarde**, cliquez avec le bouton droit sur l’élément et, à partir du menu contextuel, cliquez sur **Supprimer les données de sauvegarde**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    Le panneau **Supprimer les données de sauvegarde** s’ouvre.
2. Dans le panneau **Supprimer les données de sauvegarde**, tapez le nom de l’élément, puis cliquez sur **Supprimer**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Une fois que vous avez supprimé les données, revenez à l’étape 4c et poursuivez le processus.

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>Supprimer un coffre utilisé pour protéger un serveur DPM
Avant de pouvoir supprimer un coffre utilisé pour protéger un serveur DPM, vous devez effacer tous les points de récupération qui ont été créés, puis désinscrire le serveur du coffre.

Pour supprimer les données associées à un groupe de protection :

1. Dans la console Administrateur DPM, cliquez sur **Protection** > sélectionnez un groupe de protection > sélectionnez le membre du groupe de protection et > dans la barre d’outils, cliquez sur **Supprimer**.

  Sélectionnez le membre du groupe de protection pour activer le bouton **Supprimer** dans la barre d’outils. Dans l’exemple, le membre est **dummyvm9**. Pour sélectionner plusieurs membres du groupe de protection, maintenez la touche Ctrl enfoncée tout en cliquant sur les membres.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    La boîte de dialogue **Arrêter la protection** s’ouvre.
2. Dans la boîte de dialogue **Arrêter la protection**, sélectionnez **Supprimer les données protégées**, puis cliquez sur **Arrêter la protection**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    Pour supprimer un coffre, vous devez effacer ou supprimer le coffre de données protégées. Selon le nombre de points de récupération et la quantité de données figurant dans le groupe de protection, la suppression des données peut prendre quelques secondes à plusieurs minutes. La boîte de dialogue **Arrêter la protection** affiche l’état une fois la tâche terminée.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. Continuez ce processus pour tous les membres de tous les groupes de protection.

    Supprimez toutes les données protégées et tous les groupes de protection.
4. Après avoir supprimé tous les membres du groupe de protection, basculez vers le Portail Azure. Ouvrez le tableau de bord du coffre et assurez-vous qu’il ne contient pas **d’Éléments de sauvegarde**, de **Serveurs de gestion des sauvegardes** ou **d’Éléments répliqués**. Dans la barre d’outils du coffre, cliquez sur **Supprimer**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/delete-vault.png)

    Si des serveurs de gestion des sauvegardes sont inscrits dans le coffre, vous ne pouvez pas supprimer le coffre, même si ce dernier ne contient aucune donnée. Si vous avez supprimé les serveurs de gestion des sauvegardes associés au coffre, mais qu’il y a des serveurs répertoriés dans le volet **Essentials**, voir [Rechercher les serveurs de gestion des sauvegardes inscrits dans le coffre](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault).
5. Pour confirmer la suppression du coffre, cliquez sur **Oui**.

    Le coffre est supprimé et le portail revient au menu de service **Nouveau** .

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>Supprimer un coffre utilisé pour protéger un serveur de production
Avant de pouvoir supprimer un coffre utilisé pour protéger un serveur de production, vous devez supprimer ou désinscrire le serveur du coffre.

Pour supprimer le serveur de production associé au coffre :

1. Dans le Portail Azure, ouvrez le tableau de bord du coffre et cliquez sur **Paramètres** > **Infrastructure de sauvegarde** > **Serveurs de production**.

    ![ouvrir le panneau serveurs de production](./media/backup-azure-delete-vault/delete-production-server.png)

    Le panneau **Serveurs de production** s’ouvre et répertorie tous les serveurs de production figurant dans le coffre.

    ![liste des serveurs de production](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. Sur le panneau **Serveurs de production**, cliquez avec le bouton droit sur le serveur, puis cliquez sur **Supprimer**.

    ![supprimer le serveur de production ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    Le panneau **Supprimer** s’ouvre.

    ![supprimer le serveur de production ](./media/backup-azure-delete-vault/delete-blade.png)
3. Sur le panneau **Supprimer**, confirmez le nom du serveur, puis cliquez sur **Supprimer**. Vous devez nommer le serveur correctement pour activer le bouton **Supprimer**.

    Une fois que le coffre est supprimé, vous recevez un message confirmant cette action. Après la suppression de tous les serveurs du coffre, revenez au volet Essentials du tableau de bord du coffre.
4. Dans le tableau de bord du coffre, assurez-vous qu’il n’y a pas **d’Éléments de sauvegarde**, de **Serveurs de gestion des sauvegardes** ou **d’Éléments répliqués**. Dans la barre d’outils du coffre, cliquez sur **Supprimer**.
5. Pour confirmer la suppression du coffre, cliquez sur **Oui**.

    Le coffre est supprimé et le portail revient au menu de service **Nouveau** .

## <a name="delete-a-backup-vault-in-classic-portal"></a>Supprimer un coffre de sauvegarde dans le portail Classic
Les instructions suivantes permettent de supprimer un coffre de sauvegarde dans le portail Classic. Avant de pouvoir supprimer le coffre de sauvegarde, vous devez supprimer les points de récupération, ou les éléments sauvegardés, et supprimer les serveurs inscrits. Les serveurs inscrits sont les serveurs Windows Server, stations de travail ou machines virtuelles qui ont été inscrits dans le coffre.

1. Ouvrez le [portail Classic](https://manage.windowsazure.com).

2. Dans la liste des coffres de sauvegarde, sélectionnez le coffre à supprimer.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/classic-portal-delete-vault-open-vault.png)

    Le tableau de bord du coffre s’ouvre. Observez le nombre de serveurs Windows et/ou de machines virtuelles Azure associés au coffre. Observez également le stockage total consommé dans Azure. Arrêtez tous les travaux de sauvegarde et supprimez toutes les données avant de supprimer le coffre.

3. Cliquez sur l’onglet **Éléments protégés**, puis sur **Arrêter la protection**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/classic-portal-delete-vault-stop-protect.png)

    La boîte de dialogue **Arrêter la protection de « votre coffre »** s’affiche.
4. Dans la boîte de dialogue **Arrêter la protection de « votre coffre »**, cochez **Supprimer les données de sauvegarde associées**, puis cliquez sur ![coche](./media/backup-azure-delete-vault/checkmark.png). <br/>
    Si vous le souhaitez, vous pouvez indiquer une raison pour l’arrêt de la protection et fournir des commentaires.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/classic-portal-delete-vault-verify-stop-protect.png)

    Après avoir supprimé les éléments du coffre, celui-ci est vide.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/classic-portal-delete-vault-post-delete-data.png)
5. Dans la liste des onglets, cliquez sur **Éléments inscrits**. Le menu déroulant **Type** vous permet de choisir le type de serveur inscrit dans le coffre. Le type peut être Windows Server ou Machine virtuelle Azure. Dans l’exemple suivant, sélectionnez la machine virtuelle enregistrée dans le coffre, puis cliquez sur **Annuler l’inscription**.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/classic-portal-unregister.png)

  Si vous souhaitez supprimer l’inscription d’un serveur Windows, à partir du menu déroulant **Type**, sélectionnez **Windows Server**, cliquez sur la ![coche](./media/backup-azure-delete-vault/checkmark.png) pour actualiser l’écran, puis cliquez sur **Supprimer**. <br/>

  ![Sélectionnez Windows Server](./media/backup-azure-delete-vault/select-windows-server.png)

6. Dans la liste des onglets, cliquez sur **Tableau de bord** pour ouvrir cet onglet. Vérifiez qu’il n’y a pas de serveurs enregistrés ou de machines virtuelles Azure protégées dans le cloud. Vérifiez également qu’il n’y a aucune donnée dans le stockage. Cliquez sur **Supprimer** pour supprimer le coffre.

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/classic-portal-list-of-tabs-dashboard.png)

    L’écran de confirmation Supprimer un coffre de sauvegarde s’ouvre alors. Sélectionnez un motif de suppression du coffre, puis cliquez sur  ![coche](./media/backup-azure-delete-vault/checkmark.png). <br/>

    ![Supprimer les données de sauvegarde](./media/backup-azure-delete-vault/classic-portal-delete-vault-confirmation-1.png)

    Le coffre est supprimé, et vous revenez au tableau de bord du portail Classic.

### <a name="find-the-backup-management-servers-registered-to-the-vault"></a>Rechercher les serveurs de gestion des sauvegardes inscrits dans le coffre
Si vous avez inscrit plusieurs serveurs dans un coffre, il peut être difficile de se les rappeler. Pour voir les serveurs inscrits dans le coffre et les supprimer :

1. Ouvrez le tableau de bord du coffre.
2. Dans le volet **Essentials**, cliquez sur **Paramètres** pour ouvrir ce panneau.

    ![ouvrir le panneau paramètres](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. Dans le **panneau Paramètres**, cliquez sur **Infrastructure de sauvegarde**.
4. Dans le panneau **Infrastructure de sauvegarde**, cliquez sur **Serveurs de gestion des sauvegardes**. Le panneau Serveurs de gestion des sauvegardes s’ouvre.

    ![liste des serveurs de gestion des sauvegardes](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Pour supprimer un serveur de la liste, cliquez avec le bouton droit sur le nom du serveur, puis cliquez sur **Supprimer**.
    Le panneau **Supprimer** s’ouvre.
6. Sur le panneau **Supprimer** , indiquez le nom du serveur. S’il s’agit d’un nom long, vous pouvez le copier-coller à partir de la liste Serveurs de gestion des sauvegardes. Ensuite, cliquez sur **Supprimer**.  
