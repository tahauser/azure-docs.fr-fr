---
title: Sauvegarde de machines virtuelles Azure | Microsoft Docs
description: "Détectez, inscrivez et sauvegardez des machines virtuelles Azure dans un coffre Recovery Services."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "sauvegarde de machine virtuelle ; sauvegarder la machine virtuelle ; sauvegarde et récupération d’urgence ; sauvegarde de machine virtuelle arm"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/15/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 892a88a2bc9d492f8a3afe59c05b4729f4830e6d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="back-up-azure-virtual-machines-to-a-recovery-services-vault"></a>Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services

Cet article décrit la procédure de sauvegarde des machines virtuelles Azure (déployées à l’aide du modèle Resource Manager ou du modèle Classic) dans un coffre Recovery Services. La plupart du travail requis pour la sauvegarde des machines virtuelles repose sur la préparation. Avant de sauvegarder ou de protéger une machine virtuelle, vous devez remplir les [conditions préalables](backup-azure-arm-vms-prepare.md) pour préparer votre environnement à la protection de vos machines virtuelles. Une fois que vous avez rempli les conditions préalables, vous pouvez lancer l’opération de sauvegarde pour prendre des instantanés de votre machine virtuelle.


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

Pour plus d’informations, consultez les articles sur la [planification de votre infrastructure de sauvegarde des machines virtuelles dans Azure](backup-azure-vms-introduction.md) et les [machines virtuelles Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="triggering-the-backup-job"></a>Déclenchement du travail de sauvegarde
La stratégie de sauvegarde associée au coffre Recovery Services définit la fréquence et à quel moment l’opération de sauvegarde s’exécute. Par défaut, la première sauvegarde planifiée est la sauvegarde initiale. Jusqu’à celle-ci, l’état de la dernière sauvegarde dans le panneau **Travaux de sauvegarde** est défini sur **Avertissement (sauvegarde initiale en attente)**.

![Sauvegarde en attente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

À moins que votre sauvegarde initiale ne soit prévue prochainement, il est recommandé de sélectionner l’option **Sauvegarder maintenant**. La procédure suivante commence à partir du tableau de bord du coffre. Cette procédure est utilisée pour l’exécution du travail de sauvegarde initial une fois les conditions préalables remplies. Si le travail de sauvegarde initial a déjà été exécuté, cette procédure n’est pas disponible. La stratégie de sauvegarde associée détermine le prochain travail de sauvegarde.  

Pour exécuter le travail de sauvegarde initial :

1. Dans le tableau de bord du coffre, cliquez sur le numéro sous **Éléments de sauvegarde** ou cliquez sur la vignette **Éléments de sauvegarde**. <br/>
  ![Icône Paramètres](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Le panneau **Éléments de sauvegarde** s’ouvre.

  ![Éléments de sauvegarde](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. Dans le panneau **Éléments de sauvegarde**, sélectionnez l’élément.

  ![Icône Paramètres](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  La liste **Éléments de sauvegarde** s’affiche. <br/>

  ![Travail de sauvegarde déclenché](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. Dans la liste **Éléments de sauvegarde**, cliquez sur le bouton de sélection **...** pour ouvrir le menu contextuel.

  ![Menu contextuel](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Le menu contextuel s’affiche.

  ![Menu contextuel](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. Dans le menu contextuel, cliquez sur **Sauvegarder maintenant**.

  ![Menu contextuel](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Le panneau Sauvegarder maintenant s’affiche.

  ![Affichage du panneau Sauvegarder maintenant](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. Dans le panneau Sauvegarder maintenant, cliquez sur l’icône de calendrier. Utilisez le contrôle de calendrier pour sélectionner le dernier jour de conservation de ce point de récupération, puis cliquez sur **Sauvegarder**.

  ![Définition du dernier jour de conservation du point de récupération dans le panneau Sauvegarder maintenant](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Les notifications de déploiement vous informent que la sauvegarde a été déclenchée et que vous pouvez surveiller la progression du travail sur la page Travaux de sauvegarde. Selon la taille de votre machine virtuelle, la création de la sauvegarde initiale peut prendre un certain temps.

6. Pour afficher ou suivre l’état de la sauvegarde initiale, dans le tableau de bord du coffre, au niveau de la vignette **Travaux de sauvegarde**, cliquez sur **En cours**.

  ![Vignette Travaux de sauvegarde](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Le panneau Travaux de sauvegarde s’ouvre.

  ![Vignette Travaux de sauvegarde](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  Le panneau **Travaux de sauvegarde** indique l’état de tous les travaux. Vérifiez si le travail de sauvegarde de votre machine virtuelle est en cours d’exécution ou s’il est terminé. Lorsqu’un travail de sauvegarde est achevé, il présente l’état *Terminé*.

  > [!NOTE]
  > Dans le cadre de l’opération de sauvegarde, le service Azure Backup émet une commande vers l’extension de sauvegarde de chaque machine virtuelle pour vider toutes les écritures et prendre un instantané cohérent.
  >
  >

## <a name="troubleshooting-errors"></a>Résolution des erreurs
Si vous rencontrez des problèmes pendant la sauvegarde de votre machine virtuelle, consultez [l’article sur le dépannage des machines virtuelles](backup-azure-vms-troubleshoot.md) pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes
Vous avez protégé votre machine virtuelle. Vous pouvez maintenant consulter les articles suivants pour en savoir plus sur les tâches de gestion de machine virtuelle et la restauration des machines virtuelles.

* [Gestion et surveillance de vos machines virtuelles](backup-azure-manage-vms.md)
* [Restauration des machines virtuelles](backup-azure-arm-restore-vms.md)
