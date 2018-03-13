---
title: Sauvegarder Windows Server dans Azure | Microsoft Docs
description: "Ce didacticiel décrit en détail la sauvegarde de serveurs Windows locaux dans un coffre Recovery Services."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: "sauvegarde serveur windows ; sauvegarder windows server ; sauvegarde et récupération d’urgence"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/14/2018
ms.author: saurabhsensharma;markgal;
ms.custom: mvc
ms.openlocfilehash: de0398b7f295894a3cac7c67b68cef237c3ac2c2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="back-up-windows-server-to-azure"></a>Sauvegarder Windows Server dans Azure


Vous pouvez utiliser Sauvegarde Azure pour protéger votre serveur Windows contre les altérations, les attaques et sinistres. Sauvegarde Azure fournit un outil léger appelé l’agent Microsoft Azure Recovery Services (MARS). L’agent MARS est installé sur le serveur Windows pour protéger les fichiers et les dossiers, et les informations de configuration du serveur via l’état du système Windows Server. Ce didacticiel explique comment vous pouvez utiliser l’agent MARS pour sauvegarder votre serveur Windows sur Azure. Ce didacticiel vous montre comment effectuer les opérations suivantes : 


> [!div class="checklist"]
> * Télécharger et configurer l’agent MARS
> * Configurer les temps de sauvegarde et la planification de rétention pour les sauvegardes de votre serveur
> * Effectuer une hoc sauvegarde ad-hoc


## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Avant que vous puissiez sauvegarder Windows Server, vous devez créer un emplacement, ou des points de restauration, pour les sauvegardes à stocker. Un [coffre Recovery Services](backup-azure-recovery-services-vault-overview.md) est un conteneur dans Azure qui stocke les sauvegardes de votre serveur Windows. Suivez les étapes ci-dessous pour créer un coffre Recovery Services dans le portail Azure. 

1. Dans le menu de gauche, sélectionnez **Tous les services**, puis dans la liste des services, tapez **Recovery Services**. Cliquez sur **Coffres Recovery Services**.

   ![ouvrir le coffre Recovery Services](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2.  Dans le menu **Coffres Recovery Services**, cliquez sur **Ajouter**.

   ![fournir des informations pour le coffre](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3.  Dans le menu **Coffre Recovery Services**,

    - tapez *myRecoveryServicesVault* dans **Nom**.
    - L’ID d’abonnement actuel apparaît dans **Abonnement**.
    - Pour **Groupe de ressources**, sélectionnez **Utiliser existant** et choisissez *myResourceGroup*. Si *myResourceGroup* n’existe pas, sélectionnez **Créer un nouveau** et tapez *myResourceGroup*. 
    - Dans le menu déroulant **Emplacement**, choisissez *Europe de l’Ouest*.
    - Cliquez sur **Créer** pour créer votre coffre Recovery Services.
 
Une fois votre archivage créé, il apparaît dans la liste des archivages de Recovery Services.

## <a name="download-recovery-services-agent"></a>Télécharger l’agent Azure Recovery Services

L’agent Microsoft Azure Recovery Services (MARS) crée une association entre Windows Server et votre coffre Recovery Services. La procédure suivante explique comment télécharger l’agent sur votre serveur.

1.  Dans la liste des coffres Recovery Services, sélectionnez **myRecoveryServicesVault** pour ouvrir son tableau de bord.

   ![fournir des informations pour le coffre](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2.  Dans le menu du tableau de bord du coffre, cliquez sur **Sauvegarder**.

3.  Dans le menu **Objectif de sauvegarde** :

    - pour **Où s'exécute votre charge de travail ?**, sélectionnez **Local**, 
    - pour **Que voulez-vous sauvegarder ?**, sélectionnez **Fichiers et dossiers** et **État système** 

    ![fournir des informations pour le coffre](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)
    
4.  Cliquez sur **Préparer l’infrastructure** pour ouvrir le menu **Préparer l’infrastructure**.
5.  Dans le menu **Préparer l’infrastructure**, cliquez sur **Télécharger l’agent pour Windows Server ou pour le client Windows** pour télécharger *MARSAgentInstaller.exe*. 

    ![Télécharger l’agent pour Windows Server ou Windows Client](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    Le programme d’installation ouvre un autre navigateur et télécharge **MARSAgentInstaller.exe**.
 
6.  Avant d’exécuter le fichier téléchargé, cliquez sur le bouton **Télécharger** dans le panneau Préparer l’infrastructure pour télécharger et enregistrer le fichier **Informations d’identification du coffre**. Ce fichier est requis pour la connexion de l’agent MARS avec le coffre Recovery Services.

    ![Télécharger l’agent pour Windows Server ou Windows Client](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)
 
## <a name="install-and-register-the-agent"></a>Installer et inscrire l’agent

1. Recherchez et double-cliquez sur le fichier **MARSagentinstaller.exe** téléchargé.
2. L’**Assistant Installation de l’Agent Microsoft Azure Recovery Services** s’affiche. Dans l’Assistant, fournissez les informations suivantes lorsque vous y êtes invité, puis cliquez sur **S’inscrire**.
    - Emplacement pour le dossier d’installation et de cache.
    - Informations relatives au serveur proxy, si vous en utilisez un pour vous connecter à Internet.
    - Votre nom d’utilisateur et votre mot de passe si vous utilisez un proxy authentifié.

    ![Télécharger l’agent pour Windows Server ou Windows Client](./media/tutorial-backup-windows-server-to-azure/mars-installer.png) 

3. À la fin de l’Assistant, cliquez sur **Procéder à l’enregistrement** et fournissez le fichier **Informations d’identification du coffre** que vous avez téléchargé dans la procédure précédente.
 
4. Lorsque vous y êtes invité, fournissez une phrase secrète de chiffrement pour chiffrer les sauvegardes à partir de Windows Server. Enregistrez la phrase secrète dans un emplacement sécurisé car Microsoft ne peut pas récupérer celle-ci si elle est perdue.

5. Cliquez sur **Terminer**. 

## <a name="configure-backup-and-retention"></a>Configurer la sauvegarde et la rétention

Vous utilisez l’agent Microsoft Azure Recovery Services pour planifier quand les sauvegardes dans Azure se produisent sur Windows Server. Exécutez les étapes suivantes sur le serveur où vous avez téléchargé l’agent.

1. Ouvrez l’agent Microsoft Azure Recovery Services. Vous pouvez le trouver en recherchant **Sauvegarde Microsoft Azure**sur votre ordinateur.

2.  Dans la console de l’agent Recovery Services, cliquez sur **Planifier la sauvegarde** sous le **volet Actions**.

    ![Télécharger l’agent pour Windows Server ou Windows Client](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Cliquez sur **Suivant** pour accéder à la page **Sélectionner les éléments à sauvegarder**.

4. Cliquez sur **Ajouter les éléments** puis, dans la boîte de dialogue qui s’ouvre, sélectionnez **État du système** et les fichiers ou dossiers que vous souhaitez sauvegarder. Cliquez ensuite sur **OK**.

5. Cliquez sur **Suivant**.

6. Dans la page **Spécifier la planification de sauvegarde (état du système)**, spécifiez les heures du jour ou de la semaine auxquelles les sauvegardes doivent être déclenchées pour l’état du système, puis cliquez sur **Suivant**. 

7.  Dans la page **Sélectionner la stratégie de rétention (état du système)**, définissez la stratégie de rétention pour la copie de l’état du système, puis cliquez sur **Suivant**.
8. De même, sélectionnez la planification de sauvegarde et la stratégie de rétention pour les fichiers et dossiers sélectionnés. 
8.  Dans la page **Choisir un type de sauvegarde initiale**, laissez l’option **Automatiquement sur le réseau** sélectionnée, puis cliquez sur **Suivant**.
9.  Dans la page **Confirmation**, passez en revue les informations, puis cliquez sur **Terminer**.
10. Lorsque l’Assistant a terminé la création de la planification de la sauvegarde, cliquez sur **Fermer**.

## <a name="perform-an-ad-hoc-back-up"></a>Effectuer une hoc sauvegarde ad-hoc

Vous avez établi la planification lors de l’exécution des tâches de sauvegarde. Toutefois, vous n’avez pas sauvegardé le serveur. Comme meilleure pratique en matière de récupération d’urgence, il est recommandé d’exécuter une sauvegarde à la demande pour garantir la résilience de données pour votre serveur.

1.  Dans la console de l’agent Microsoft Azure Recovery Services, cliquez sur **Sauvegarder maintenant**.

    ![Télécharger l’agent pour Windows Server ou Windows Client](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2.  Dans l’Assistant **Sauvegarder maintenant**, sélectionnez dans **Fichiers et dossiers** ou **État du système** l’élément que vous souhaitez sauvegarder, puis cliquez sur **Suivant**. 
3. Dans la page **Confirmation**, vérifiez les paramètres utilisés par l’Assistant **Sauvegarder maintenant** pour sauvegarder votre serveur, puis cliquez sur **Sauvegarder**.
4.  Cliquez sur **Fermer** pour fermer l’assistant. Si vous fermez l’Assistant avant la fin du processus de sauvegarde, celui-ci continuera de s’exécuter en arrière-plan.
4.  Une fois la sauvegarde initiale terminée, le statut **Tâche terminée** apparaît dans le volet **Travaux** de la console de l’agent MARS.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé le portail Azure pour effectuer les opérations suivantes : 
 
> [!div class="checklist"] 
> * Créer un coffre Recovery Services 
> * Télécharger l’agent Microsoft Azure Recovery Services 
> * Installer l’agent 
> * Configurer la sauvegarde de Windows Server 
> * Effectuer une sauvegarde à la demande 

Passez au didacticiel suivant pour récupérer des fichiers d’Azure vers Windows Server

> [!div class="nextstepaction"] 
> [Restaurer des fichiers à partir d’Azure vers Windows Server](./tutorial-backup-restore-files-windows-server.md) 

