---
title: Surveiller des alertes de sauvegarde pour les machines virtuelles Azure | Microsoft Docs
description: Surveillez les événements et les alertes des travaux de sauvegarde de machine virtuelle Azure. Envoyer un e-mail en fonction des alertes.
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: ''
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: markgal;trinadhk;giridham;
ms.openlocfilehash: fbdce5c244d733a2978d473f01c8d875cbeaa65e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Suivez les alertes des sauvegardes de machines virtuelles Azure
Les alertes sont des indications du service indiquant qu’un seuil d’événement a été atteint ou dépassé. Le fait de prendre connaissance d’un problème dès qu’il se produit peut être indispensable pour limiter les dépenses. Les alertes ne sont généralement pas planifiées. Il est donc utile de savoir aussi tôt possible lorsque des alertes sont générées. Par exemple, lorsqu’un travail de sauvegarde ou de restauration échoue, une alerte se produit dans les cinq minutes suivant l’échec. Dans le tableau de bord du coffre, la vignette Alertes de sauvegarde affiche des événements de niveaux Critique et Avertissement. Dans les paramètres des alertes de sauvegarde, vous pouvez afficher tous les événements. Mais que faire si une alerte se produit lorsque vous travaillez sur un autre problème ? Si vous ne savez pas quand l’alerte se produit, il peut s’agir d’un désagrément mineur, ou d’un problème entraînant des pertes de données. Pour vous assurer que les bonnes personnes sont prévenues en cas d’alerte, configurez le service de manière à envoyer des notifications d’alerte par e-mail. Pour plus d’informations sur la configuration des notifications par e-mail, consultez [Configurer les notifications](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Comment puis-je trouver des informations sur les alertes ?
Pour afficher des informations sur l’événement qui a généré l’alerte, vous devez ouvrir la section Alertes de sauvegarde. Il existe deux manières d’ouvrir la section Alertes de sauvegarde : soit à partir de la vignette Alertes de sauvegarde dans le tableau de bord du coffre, soit à partir de la section Alertes et événements.

Pour ouvrir le panneau Alertes de sauvegarde à partir de la vignette Alertes de sauvegarde :

* Sur la vignette **Alertes de sauvegarde** dans le tableau de bord du coffre, cliquez sur **Critique** ou **Avertissement** pour afficher les événements opérationnels pour ce niveau de gravité.

    ![Vignette Alertes de sauvegarde](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Pour ouvrir le panneau Alertes de sauvegarde à partir de la section Alertes et événements :

1. À partir du tableau de bord du coffre, cliquez sur **Tous les paramètres**. ![Bouton Tous les paramètres](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Dans le panneau **Paramètres**, cliquez sur **Alertes et événements**. ![Bouton Alertes et événements](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Dans le panneau **Alertes et événements**, cliquez sur **Alertes de sauvegarde**. ![Bouton Alertes de sauvegarde](./media/backup-azure-monitor-vms/backup-alerts.png)

    La section **Alertes de sauvegarde** s’ouvre et affiche les alertes filtrées.

    ![Vignette Alertes de sauvegarde](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Pour afficher des informations détaillées sur une alerte spécifique, cliquez sur l’alerte de votre choix dans la liste des événements pour ouvrir la section **Détails** correspondante.

    ![Détail sur l’événement](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Pour personnaliser les attributs affichés dans la liste, consultez [Afficher les attributs d’événement supplémentaires](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Configurer les notifications
 Vous pouvez configurer le service de manière à envoyer des notifications par e-mail pour les alertes qui se sont produites au cours de la dernière heure, ou lorsque des événements particuliers se produisent.

Pour configurer des notifications par e-mail pour les alertes

1. Dans le menu Alertes de sauvegarde, cliquez sur **Configurer les notifications**

    ![Menu Alertes de sauvegarde](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    La section Configurer les notifications s’ouvre.

    ![Panneau Configurer les notifications](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Dans la section Configurer les notifications, cliquez sur **Activé** pour les notifications par e-mail.

    Les boîtes de dialogue Destinataires et Gravité comportent une étoile, car ces informations sont requises. Fournissez au moins une adresse e-mail et sélectionnez au moins un niveau de gravité.
3. Dans la boîte de dialogue **Destinataires (e-mail)** , saisissez les adresses e-mail des personnes devant recevoir les notifications. Utilisez le format : username@domainname.com. Séparez les adresses e-mail par des point-virgule (;).
4. Dans la zone **Notification**, choisissez **Par alerte** pour envoyer une notification lorsque l’alerte indiquée se produit ou **Synthèse horaire** pour envoyer un résumé de la dernière heure.
5. Dans la boîte de dialogue **Gravité** , sélectionnez un ou plusieurs niveaux pour lesquels vous voulez envoyer des notifications par e-mail.
6. Cliquez sur **Enregistrer**.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Quels sont les types d’alertes disponibles pour la sauvegarde des machines virtuelles Azure IaaS ?
   | Niveau d’alerte | Alertes envoyées |
   | --- | --- |
   | Critique | Pour les échecs de sauvegarde et les échecs de récupération |
   | Avertissement | Pour les travaux de sauvegarde réussis avec avertissements (par exemple : échec de certains enregistreurs lors de la création d’un instantané) |
   | Informations | Actuellement, aucune alerte d’information n’est disponible pour la sauvegarde des machines virtuelles Azure |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Existe-t-il des situations lors desquelles un e-mail n’est pas envoyé même si les notifications sont configurées ?
Il existe des situations lors desquelles une alerte n’est pas envoyée, même si les notifications ont été correctement configurées, afin de réduire le nombre d’alertes. Ces situations sont les suivantes :

* Si les notifications sont configurées sur une base horaire et qu’une alerte est déclenchée et résolue dans l’heure.
* Si le travail est annulé.
* Si un travail de sauvegarde est déclenché et échoue, et si un autre travail de sauvegarde est en cours.
* Si un travail de sauvegarde planifiée pour une machine virtuelle Resource Manager démarre, mais que celle-ci n’existe plus.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Utilisation des journaux d’activité pour obtenir des notifications en cas de sauvegardes réussies

Si vous souhaitez être averti lorsque les sauvegardes se sont terminées correctement, vous pouvez utiliser les alertes basées sur les [journaux d’activité](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) du coffre.

### <a name="login-into-azure-portal"></a>Connexion au portail Azure
Connectez-vous au portail Azure, accédez au coffre Azure Recovery Services approprié, puis cliquez sur la section « Journal d’activité » dans les propriétés.

### <a name="identify-appropriate-log"></a>Identifier le journal approprié

Appliquez les filtres de l’image suivante pour vérifier si vous recevez bien les journaux d’activité en cas de sauvegardes réussies. Changez l’intervalle de temps pour afficher les enregistrements.

![Journaux d’activité](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Vous pouvez cliquer sur le segment « JSON » pour obtenir plus de détails et les afficher en les copiant-collant dans un éditeur de texte. Il doit afficher les détails du coffre, ainsi que l’élément qui a déclenché le journal d’activité (par exemple, l’élément de sauvegarde).

Ensuite, cliquez sur « Ajouter une alerte de journal d’activité » afin de générer des alertes pour tous les journaux de ce type.

### <a name="add-activity-log-alert"></a>Ajouter une alerte de journal d’activité

Lorsque vous cliquez sur « Ajouter une alerte de journal d’activité », l’écran suivant s’affiche :

![Alerte du journal d’activité](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png)
    
L’abonnement et le groupe de ressources permettent de stocker l’alerte. Les critères sont préremplis. Vérifiez que toutes les valeurs correspondent à vos besoins.

En cas de sauvegarde réussie, le niveau indiqué est « Informationnel » et l’état est « Réussi ».

Si vous sélectionnez une ressource ci-dessus, l’alerte est générée lorsque des journaux d’activité sont enregistrés pour cette ressource ou ce coffre. Si vous souhaitez que la règle soit appliquée à tous les coffres, laissez le champ « Ressource » vide.

### <a name="define-action-on-alert-firing"></a>Définir une action en cas de déclenchement d’alerte

Utilisez le « groupe d’actions » pour définir l’action qui doit être effectuée lors de la génération d’une alerte. Pour plus d’informations sur les actions disponibles (e-mail/SMS/intégration à ITSM, etc.), cliquez sur « Type d’Action ».

![Groupe d’actions Journal d’activité](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)


Une fois que vous cliquez sur OK, une alerte de journal d’activité est générée, puis les journaux d’activité suivants enregistrés pour les sauvegardes réussies déclenchent l’action, telle que défini dans le groupe d’actions.

### <a name="limitations-on-alerts"></a>Limitations sur les alertes
Les alertes basées sur des événements sont soumises aux limitations suivantes :

1. Des alertes sont déclenchées sur toutes les machines virtuelles du coffre Recovery Services. Vous ne pouvez pas personnaliser l’alerte pour un sous-ensemble de machines virtuelles à l’intérieur d’un coffre Recovery Services.
2. Les alertes sont envoyées par « alerts-noreply@mail.windowsazure.com ». Actuellement, vous ne pouvez pas modifier l’expéditeur de courrier électronique.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la manière de recréer une machine virtuelle à partir d’un point de récupération, consultez [Restauration de machines virtuelles Azure](backup-azure-arm-restore-vms.md).

Pour plus d’informations sur la protection de vos machines virtuelles, consultez [Premier aperçu : sauvegarder les machines virtuelles ARM dans un archivage de Recovery Services](backup-azure-vms-first-look-arm.md). 

Pour plus d’informations sur les tâches de gestion relatives aux sauvegardes de machines virtuelles, consultez [Gérer les sauvegardes des machines virtuelles Azure](backup-azure-manage-vms.md).
