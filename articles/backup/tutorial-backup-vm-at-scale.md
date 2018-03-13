---
title: "Sauvegarder des machines virtuelles Azure à grande échelle | Microsoft Docs"
description: "Sauvegarder simultanément plusieurs machines virtuelles dans Azure"
services: backup
keywords: "sauvegarde de machine virtuelle ; sauvegarder une machine virtuelle ; sauvegarde de MV ; sauvegarder une MV ; sauvegarde de MV Azure ; sauvegarde et récupération d’urgence"
author: markgalioto
ms.author: markgal
ms.date: 2/14/2018
ms.topic: tutorial
ms.service: backup
ms.custom: mvc
ms.openlocfilehash: f1cfa72d0fb3c83ef6265649b740dec317f0e4b2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Utiliser le portail Azure pour sauvegarder plusieurs machines virtuelles

Lorsque vous sauvegardez des données dans Azure, vous stockez celles-ci dans une ressource Azure que l’on appelle coffre Recovery Services. La ressource coffre Recovery Services est disponible dans le menu Paramètres de la plupart des services Azure. L’avantage de disposer du coffre Recovery Services intégré dans le menu Paramètres de la plupart des services Azure est que cela facilite considérablement la sauvegarde de données. Toutefois, il est fastidieux de manipuler individuellement chaque base de données ou machine virtuelle disponible dans votre entreprise. Que se passe-t-il si vous souhaitez sauvegarder les données de toutes les machines virtuelles d’un service ou d’un emplacement ? Il est facile de sauvegarder plusieurs machines virtuelles en créant une stratégie de sauvegarde et en appliquant celle-ci aux machines virtuelles de votre choix. Ce didacticiel explique comment :

> [!div class="checklist"]
> * Créer un coffre Recovery Services
> * Créer une stratégie de sauvegarde
> * Appliquer la stratégie de sauvegarde pour protéger plusieurs machines virtuelles
> * Déclencher un travail de sauvegarde à la demande pour les machines virtuelles protégées

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Le coffre Recovery Services contient les données de sauvegarde et la stratégie de sauvegarde appliquée aux machines virtuelles protégées. La sauvegarde des machines virtuelles est un processus local. Vous ne pouvez pas sauvegarder une machine virtuelle se trouvant dans un emplacement vers un coffre Recovery Services situé dans un autre emplacement. Donc, chaque emplacement Azure contenant des machines virtuelles à sauvegarder, doit comprendre au moins un coffre Recovery Services.

1. Dans le menu de gauche, sélectionnez **Tous les services**, puis dans la liste des services, tapez *Recovery Services*. Lorsque vous tapez cette chaîne, la liste des ressources est filtrée en conséquence. Lorsque vous voyez l’option Coffres Recovery Services dans la liste, sélectionnez-la pour ouvrir le Coffres Recovery Services.

    ![Ouvrir le menu Coffres Recovery Services](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. Dans le menu **Coffres Recovery Services**, cliquez sur **Ajouter** pour ouvrir le menu du coffre Recovery Services.

    ![Ouvrir le menu du coffre](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. Dans le menu du coffre Recovery Services, 

    - Dans le champ **Nom**, tapez *myRecoveryServicesVault*,
    - L’ID d’abonnement actuel apparaît dans **Abonnement**. Si vous avez des abonnements supplémentaires, vous pouvez en choisir un autre pour le nouveau coffre.
    - Pour **Groupe de ressources**, sélectionnez **Utiliser l’existant**, puis choisissez *myResourceGroup*. Si *myResourceGroup* n’existe pas, sélectionnez **Créer un nouveau**, puis tapez *myResourceGroup*.
    - Dans le menu déroulant **Emplacement**, choisissez *Europe de l’Ouest*.
    - Cliquez sur **Créer** pour créer votre coffre Recovery Services.

Un coffre Recovery Services doit être situé dans le même emplacement que les machines virtuelles protégées. Si vous possédez des machines virtuelles dans plusieurs régions, créez un coffre Recovery Services dans chacune d’elles. Ce didacticiel crée un coffre Recovery Services *Europe de l’Ouest* car c’est là que la machine virtuelle *myVM* a été créée avec le démarrage rapide.

La création du coffre Recovery Services peut prendre plusieurs minutes. Surveillez les notifications d'état dans l'angle supérieur droit du portail. Une fois votre archivage créé, il apparaît dans la liste des archivages de Recovery Services.

Lorsque vous créez un coffre Recovery Services, par défaut, celui-ci a un stockage géo-redondant. Pour assurer la résilience des données, un stockage géo-redondant réplique celles-ci plusieurs fois dans deux régions Azure.

## <a name="set-backup-policy-to-protect-vms"></a>Définir une stratégie de sauvegarde pour protéger les machines virtuelles

Une fois le coffre Recovery Services créé, l’étape suivante consiste à configurer celui-ci pour le type de données, et à définir la stratégie de sauvegarde. La stratégie de sauvegarde planifie la fréquence et l’heure de la création des points de récupération. Elle inclut également la durée de rétention de ces derniers. Pour ce didacticiel, supposons que votre entreprise est un complexe sportif avec un hôtel, un stade, des restaurants et des concessions, et que vous protégez les données figurant sur les machines virtuelles. Les étapes suivantes créent une stratégie de sauvegarde pour les données financières.

1. Dans la liste des coffres Recovery Services, sélectionnez le coffre **myRecoveryServicesVault** pour ouvrir son tableau de bord.

   ![Ouvrir le menu Scénario](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. Dans le menu du tableau de bord du coffre, cliquez sur **Sauvegarder** pour ouvrir le panneau Sauvegarder.

3. Dans le menu Objectif de sauvegarde, dans le menu déroulant **Où s’exécute votre charge de travail ?**, choisissez *Azure*. Dans le menu déroulant **Que voulez-vous sauvegarder ?**, choisissez *Machine virtuelle*, puis cliquez sur **Sauvegarder**.

    Ces actions préparent le coffre Recovery Services à interagir avec une machine virtuelle. Les coffres Recovery Services ont une stratégie par défaut qui crée un point de restauration quotidiennement, et conserve les points de restauration pendant 30 jours.

    ![Ouvrir le menu Scénario](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. Pour créer une stratégie, dans le menu Stratégie de sauvegarde, dans le menu déroulant **Choisir une stratégie de sauvegarde**, sélectionnez *Créer un nouveau*.

    ![Sélectionner la charge de travail](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. Dans le menu **Stratégie de sauvegarde**, pour **Nom de la stratégie**, tapez *Finance*. Entrez les modifications suivantes pour la stratégie de sauvegarde : 
    - Pour **Fréquence de sauvegarde**, définissez le fuseau horaire *Central*. Le complexe sportif étant situé au Texas, son propriétaire souhaite que le calcul du temps soit local. Laissez la fréquence de sauvegarde définie sur Chaque jour à 3:30.
    - Pour **Rétention du point de sauvegarde quotidien**, définissez une période de 90 jours.
    - Pour **Rétention du point de sauvegarde hebdomadaire**, choisissez le point de restauration *lundi* et la rétention de 52  semaines.
    - Pour **Rétention du point de sauvegarde mensuel**, choisissez le point de restauration Premier dimanche de chaque mois et la rétention de 36 mois.
    - Désactivez l’option **Rétention du point de sauvegarde annuel**. Le responsable financier ne souhaite pas conserver les données plus de 36 mois.
    - Cliquez sur **OK** pour créer la stratégie de sauvegarde.

    ![Sélectionner la charge de travail](./media/tutorial-backup-vm-at-scale/set-new-policy.png) 

    Après avoir créé la stratégie de sauvegarde, associez-la aux machines virtuelles.

6. Dans la boîte de dialogue **Sélectionner les machines virtuelles**, sélectionnez *myVM*, puis cliquez sur **OK** pour déployer la stratégie de sauvegarde pour les machines virtuelles. 

    Tous les machines virtuelles situées dans le même emplacement qui ne sont pas encore associées à une stratégie de sauvegarde s’affichent. Les machines virtuelles *myVMH1* et *myVMR1* sont sélectionnées pour être associées la stratégie *Finance*.

    ![Sélectionner la charge de travail](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png) 

    Une fois le déploiement terminé, vous recevez une notification pour vous en informer.

## <a name="initial-backup"></a>Sauvegarde initiale

Vous avez activé la sauvegarde pour les coffres Recovery Services, mais aucune sauvegarde initiale n’a été créée. Une pratique recommandée pour la récupération d’urgence consiste à déclencher la première sauvegarde de façon à ce que les données soient protégées. 

Pour exécuter un travail de sauvegarde à la demande :

1. Sur le tableau de bord du coffre, sous **Éléments de sauvegarde**, cliquez sur **3** pour ouvrir le menu Éléments de sauvegarde.

    ![Icône Paramètres](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    Le menu **Éléments de sauvegarde** s’ouvre.

2. Dans le menu **Éléments de sauvegarde**, cliquez sur **Machine virtuelle Azure** pour ouvrir la liste des machines virtuelles associées au coffre.

    ![Icône Paramètres](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    La liste **Éléments de sauvegarde** s’affiche.

    ![Travail de sauvegarde déclenché](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. Dans la liste **Éléments de sauvegarde**, cliquez sur le bouton de sélection **...** pour ouvrir le menu contextuel.

4. Dans le menu contextuel, sélectionnez **Sauvegarder maintenant**.

    ![Menu contextuel](./media/tutorial-backup-vm-at-scale/context-menu.png)

    Le menu Sauvegarder maintenant s’ouvre.

5. Dans le menu Sauvegarder maintenant, entrez le dernier jour de rétention du point de récupération, puis cliquez sur **Sauvegarder**.

    ![Définition du dernier jour de conservation du point de récupération dans le panneau Sauvegarder maintenant](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    Les notifications de déploiement vous informent que la sauvegarde a été déclenchée et que vous pouvez surveiller la progression du travail sur la page Travaux de sauvegarde. Selon la taille de votre machine virtuelle, la création de la sauvegarde initiale peut prendre un certain temps.

    Une fois le travail de sauvegarde initiale terminé, vous pouvez voir son état dans le menu Travail de sauvegarde. Le travail de sauvegarde à la demande a créé le point de restauration initial pour *myVM*. Si vous souhaitez sauvegarder d’autres machines virtuelles, répétez ces étapes pour chacune d’elles. 

    ![Vignette Travaux de sauvegarde](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)
  
## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez suivre les didacticiels suivants, ne nettoyez pas les ressources créées dans celui-ci. Sinon, effectuez les opérations suivantes pour supprimer toutes les ressources créées au cours de ce didacticiel dans le portail Azure.

1. Sur le tableau de bord **myRecoveryServicesVault**, sous **Éléments de sauvegarde**, cliquez sur **3** pour ouvrir le menu Éléments de sauvegarde.

    ![Icône Paramètres](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. Dans le menu **Éléments de sauvegarde**, cliquez sur **Machine virtuelle Azure** pour ouvrir la liste des machines virtuelles associées au coffre.

    ![Icône Paramètres](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    La liste **Éléments de sauvegarde** s’affiche.

3. Dans le menu **Éléments de sauvegarde**, cliquez sur le bouton de sélection (...) pour ouvrir le menu contextuel.

    ![Icône Paramètres](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. Dans le menu contextuel, sélectionnez **Arrêter la sauvegarde** pour ouvrir le menu Arrêter la sauvegarde. 

    ![Icône Paramètres](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. Dans le menu **Arrêter la sauvegarde**, sélectionnez le menu déroulant supérieur, puis choisissez **Supprimer les données de sauvegarde**.

6. Dans la boîte de dialogue **Tapez le nom de l’élément de sauvegarde** , tapez *myVM*.
 
7. Une fois l’élément de sauvegarde vérifié (une coche s’affiche), le bouton **Arrêter la sauvegarde** est activé. Cliquez sur **Arrêter la sauvegarde** pour arrêter la stratégie et supprimer les points de restauration. 

    ![Cliquer sur Arrêter la sauvegarde pour supprimer le coffre](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png).

8. Dans le menu **myRecoveryServicesVault**, cliquez sur **Supprimer**.

    ![Cliquer sur Arrêter la sauvegarde pour supprimer le coffre](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Une fois le coffre supprimé, vous revenez à la liste des coffres Recovery Services.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé le portail Azure pour effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un coffre Recovery Services
> * Définir le coffre pour protéger les machines virtuelles
> * Créer une stratégie de sauvegarde et de rétention personnalisée
> * Attribuer la stratégie pour protéger plusieurs machines virtuelles
> * Déclencher une sauvegarde à la demande pour des machines virtuelles

Passez au didacticiel suivant pour restaurer une machine virtuelle Azure à partir d’un disque. 

> [!div class="nextstepaction"]
> [Restaurer des machines virtuelles à l’aide d’Azure](./tutorial-restore-disk.md)
