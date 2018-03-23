---
title: Sauvegarder l’état du système Windows vers Azure | Microsoft Docs
description: Découvrez comment sauvegarder l’état du système des ordinateurs Windows Server et/ou Windows vers Azure.
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: carmonm
editor: ''
keywords: procédure de sauvegarde ; comment sauvegarder ; sauvegarder des fichiers et des dossiers
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: saurse;markgal
ms.openlocfilehash: df2565be7c543be061c3297f4d87aeae5eae4b8e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Sauvegarder l’état du système Windows dans un déploiement Resource Manager
Cet article explique comment sauvegarder l’état du système Windows Server vers Azure. Il s’agit d’un didacticiel destiné à vous présenter les notions de base.

Si vous souhaitez en savoir plus sur Sauvegarde Azure, lisez cette [présentation](backup-introduction-to-azure-backup.md).

Si vous ne disposez pas d’un abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) pour accéder à n’importe quel service Azure.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services
Pour sauvegarder l’état du système Windows Server, vous devez créer un archivage de Recovery Services dans la région où vous souhaitez stocker les données. Vous devez également spécifier le mode de réplication de votre stockage.

### <a name="to-create-a-recovery-services-vault"></a>Pour créer un coffre Recovery Services
1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide de votre abonnement Azure.
2. Dans le menu Hub, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Recovery Services** et cliquez sur **Coffres Recovery Services**.

    ![Créer un coffre Recovery Services - Étape 1](./media/backup-azure-system-state/open-rs-vault-list.png) <br/>

    Si l’abonnement inclut des coffres Recovery Services, ces derniers sont répertoriés.
3. Dans le menu **Coffres Recovery Services**, cliquez sur **Ajouter**.

    ![Créer un coffre Recovery Services - Étape 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Le panneau du coffre Recovery Services s’affiche et vous invite à renseigner les champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement**.

    ![Créer un coffre Recovery Services - Étape 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Sous **Nom**, entrez un nom convivial permettant d’identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Tapez un nom contenant entre 2 et 50 caractères. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.

5. Dans la section **Abonnement**, utilisez le menu déroulant pour choisir l’abonnement Azure. Si vous n’utilisez qu’un seul abonnement, ce dernier s’affiche et vous pouvez passer directement à l’étape suivante. Si vous n’êtes pas sûr de l’abonnement à utiliser, utilisez l’abonnement par défaut (ou suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel est associé à plusieurs abonnements Azure.

6. Dans la section **Groupe de ressources** :

    * Sélectionnez **Créer** si vous voulez créer un groupe de ressources.
    Ou
    * sélectionnez **Utiliser existant** et cliquez sur le menu déroulant pour afficher la liste des groupes de ressources disponibles.

  Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Cliquez sur **Emplacement** pour sélectionner la région géographique du coffre. Ce choix définit la région géographique où vos données de sauvegarde sont envoyées.

8. En bas du panneau du coffre Recovery Services, cliquez sur **créer**.

    La création du coffre Recovery Services peut prendre plusieurs minutes. Surveillez les notifications d'état dans l'angle supérieur droit du portail. Une fois votre archivage créé, il apparaît dans la liste des archivages de Recovery Services. Si vous ne voyez pas votre coffre après quelques minutes, cliquez sur **Actualiser**.

    ![Bouton Actualiser](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Une fois que votre coffre apparaît dans la liste des coffres Recovery Services, vous êtes prêt à définir la redondance du stockage.

### <a name="set-storage-redundancy-for-the-vault"></a>Définir la redondance du stockage pour un coffre
Lorsque vous créez un coffre Recovery Services, vérifiez que la redondance du stockage est configurée comme vous le souhaitez.

1. Dans le panneau **Coffres Recovery Services**, cliquez sur le nouveau coffre.

    ![Sélectionnez le nouveau coffre dans la liste des coffres Recovery Services.](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Lorsque vous sélectionnez le coffre, le panneau **Coffre Recovery Services** se réduit et le panneau Paramètres (*qui porte le nom du coffre en haut*) ainsi que le panneau des détails du coffre s’ouvrent.

    ![Afficher la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. Dans le panneau Paramètres du nouveau coffre, utilisez le curseur vertical pour faire défiler l’écran jusqu'à la section Gestion, puis cliquez sur **Infrastructure de sauvegarde**.
    Le panneau Infrastructure de sauvegarde s’ouvre.
3. Dans le panneau Infrastructure de sauvegarde, cliquez sur **Configuration de la sauvegarde** pour ouvrir le panneau **Configuration de la sauvegarde**.

    ![Définir la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Choisissez l’option de réplication de stockage à appliquer à votre archivage.

    ![Options de configuration du stockage](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Par défaut, votre archivage utilise un stockage géo-redondant. Si vous utilisez Azure comme principal point de terminaison du stockage de sauvegarde, laissez cette option **inchangée**. Sinon, choisissez l’option **Localement redondant**, qui réduit les coûts de stockage Azure. Pour en savoir plus sur les options de stockage [géo-redondant](../storage/common/storage-redundancy.md#geo-redundant-storage) et [localement redondant](../storage/common/storage-redundancy.md#locally-redundant-storage), consultez la [présentation de la redondance du stockage](../storage/common/storage-redundancy.md).

Une fois votre coffre créé, vous devez le configurer pour la sauvegarde de l’état du système Windows.

## <a name="configure-the-vault"></a>configuration du coffre
1. Dans le panneau du coffre Recovery Services que vous venez de créer, accédez à la section Prise en main, puis cliquez sur **Sauvegarde** ; dans le panneau **Prise en main de la sauvegarde**, sélectionnez **Objectif de la sauvegarde**.

    ![Ouvrir le panneau Objectif de sauvegarde](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Le panneau **Objectif de la sauvegarde** s’ouvre.

    ![Ouvrir le panneau Objectif de sauvegarde](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Dans la liste déroulante **Où s’exécute votre charge de travail ?**, sélectionnez **Local**.

    En effet, vous devez choisir l’option **Local**, car votre ordinateur Windows Server ou Windows est une machine physique, qui ne se trouve donc pas dans Azure.

3. Dans le menu **Que voulez-vous sauvegarder ?**, sélectionnez **État du système** et cliquez sur **OK**.

    ![Configuration des fichiers et dossiers](./media/backup-azure-system-state/backup-goal-system-state.png)

    Lorsque vous cliquez sur OK, une coche apparaît en regard de la zone **Objectif de la sauvegarde** et le volet **Préparer l’infrastructure** s’ouvre.

    ![Objectif de sauvegarde configuré, début de préparation de l’infrastructure](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. Dans le panneau **Préparer l’infrastructure**, cliquez sur **Télécharger l’agent pour Windows Server ou pour le client Windows**.

    ![Télécharger l’agent pour Windows Server ou Windows Client](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Si vous utilisez Windows Server Essentials, puis choisissez de télécharger l’agent associé à ce produit, un menu contextuel vous invite à exécuter ou enregistrer le fichier MARSAgentInstaller.exe.

    ![Boîte de dialogue MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. Dans le menu contextuel de téléchargement, cliquez sur **Enregistrer**.

    Par défaut, le fichier **MARSagentinstaller.exe** est enregistré dans le dossier Téléchargements. Une fois l’exécution du programme d’installation terminée, une fenêtre contextuelle s’affiche, vous demandant si vous voulez lancer ce programme ou ouvrir le dossier.

    ![Télécharger l’agent pour Windows Server ou Windows Client](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Vous n’avez pas besoin d’installer l’agent à cette étape ; Vous pouvez installer l’agent après avoir téléchargé les informations d’identification du coffre.

6. Dans le panneau **Préparer l’infrastructure**, cliquez sur **Télécharger**.

    ![Télécharger les informations d’identification du coffre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    Les informations d’identification du coffre sont téléchargées dans le dossier Téléchargements. Une fois cette opération terminée, une fenêtre contextuelle s’affiche, vous demandant si vous voulez ouvrir ou enregistrer ces informations. Cliquez sur **Enregistrer**. Si vous cliquez sur **Ouvrir** par erreur, attendez que la boîte de dialogue qui s’affiche annonce l’échec de la tentative d’ouverture des informations d’identification du coffre. En effet, vous ne pouvez pas les ouvrir. Passez à l'étape suivante. Les informations d’identification du coffre se trouvent dans le dossier Téléchargements.   

    ![Fin du téléchargement des informations d’identification du coffre](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
> [!NOTE]
> Les informations d’identification du coffre doivent être enregistrées uniquement à un emplacement proche du serveur Windows sur lequel vous souhaitez utiliser l’agent. 
>

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Installer et inscrire l’agent

> [!NOTE]
> La fonction de sauvegarde via le portail Azure n’est pas disponible pour le moment. Utilisez l’agent Microsoft Azure Recovery Services pour sauvegarder l’état du système Windows Server.
>

1. Recherchez et double-cliquez sur **MARSagentinstaller.exe** dans le dossier Téléchargements (ou tout autre emplacement d’enregistrement).

    Le programme d’installation affiche différents messages au fur et à mesure qu’il extrait, installe et enregistre l’agent Recovery Services.

    ![Informations d’identification de programme d’installation de l’agent Recovery Services exécuté](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Exécutez l’Assistant Installation de l’Agent Microsoft Azure Recovery Services. Pour terminer l’Assistant, vous devez :

   * Choisir un emplacement pour le dossier d’installation et de cache.
   * Fournir les informations relatives au serveur proxy, si vous en utilisez un pour vous connecter à Internet.
   * Fournir votre nom d’utilisateur et votre mot de passe si vous utilisez un proxy authentifié.
   * Fournir les informations d’identification de l’archivage téléchargées.
   * Enregistrer la phrase secrète de chiffrement dans un emplacement sécurisé.

     > [!NOTE]
     > En cas de perte ou d’oubli de la phrase secrète, Microsoft ne pourra pas vous aider à récupérer les données de sauvegarde. Enregistrez le fichier dans un emplacement sécurisé. Il est nécessaire pour restaurer une sauvegarde.
     >
     >

L’agent est désormais installé et votre ordinateur est inscrit dans le coffre. Vous êtes prêt à configurer et à planifier votre sauvegarde.

## <a name="back-up-windows-server-system-state"></a>Sauvegarder l’état du système Windows Server 
La sauvegarde initiale comprend deux tâches :

* Planifier la sauvegarde
* Sauvegarder l’état du système pour la première fois

Pour effectuer la sauvegarde initiale, utilisez l’agent Microsoft Azure Recovery Services.

### <a name="to-schedule-the-backup-job"></a>Pour planifier un travail de sauvegarde

1. Ouvrez l’agent Microsoft Azure Recovery Services. Vous pouvez le trouver en recherchant **Sauvegarde Microsoft Azure**sur votre ordinateur.

    ![Lancer l’agent Azure Recovery Services](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. Dans l’agent Recovery Services, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Sur la page Mise en route de l’Assistant Planifier la sauvegarde, cliquez sur **Suivant**.

4. Sur la page Sélectionner les éléments à sauvegarder, cliquez sur **Ajouter des éléments**.

5. Sélectionnez **État du système**, puis cliquez sur **OK**.

6. Cliquez sur **Suivant**.

7. Sélectionnez la fréquence de sauvegarde requise et la stratégie de rétention pour les sauvegardes de l’état du système dans les pages suivantes. 

8. Sur la page Confirmation, passez en revue les informations, puis cliquez sur **Terminer**.

9. Lorsque l’Assistant a terminé la création de la planification de la sauvegarde, cliquez sur **Fermer**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Pour sauvegarder l’état du système Windows Server pour la première fois

1. Vérifiez qu’aucune mise à jour de Windows Server nécessitant un redémarrage n’est en attente.

2. Dans l’agent Recovery Services, cliquez sur **Sauvegarder maintenant** pour effectuer l’amorçage initial sur le réseau.

    ![Sauvegarder Windows Server maintenant](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Sélectionnez **État du système** dans l’écran **Sélectionner l’élément de sauvegarde** qui s’affiche, puis cliquez sur **Suivant**.

4. Sur la page Confirmation, vérifiez les paramètres utilisés par l’Assistant Sauvegarder maintenant pour sauvegarder les données de l’ordinateur, puis cliquez sur **Sauvegarder**.

4. Cliquez sur **Fermer** pour fermer l’assistant. Si vous fermez l’Assistant avant la fin du processus de sauvegarde, celui-ci continuera de s’exécuter en arrière-plan.


Une fois la sauvegarde initiale terminée, le statut **Tâche terminée** apparaît dans la console Backup.

  ![RI terminé](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Étapes suivantes
* Approfondissez vos connaissances sur la [sauvegarde de machines Windows](backup-configure-vault.md).
* L’état du système Windows Server étant sauvegardé, vous pouvez [gérer vos archivages et vos serveurs](backup-azure-manage-windows-server.md).
* Si vous avez besoin de restaurer une sauvegarde, utilisez cet article pour [restaurer des fichiers sur un ordinateur Windows](backup-azure-restore-windows-server.md).
