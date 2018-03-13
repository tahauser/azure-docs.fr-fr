---
title: "Gérer les serveurs et les coffres Azure Recovery Services | Microsoft Docs"
description: "Découvrez dans cet article comment gérer les serveurs et les coffres Azure Recovery Services."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: 4eea984b-7ed6-4600-ac60-99d2e9cb6d8a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: markgal
ms.openlocfilehash: 2e5fd9e7e3cae1665519e4f08604fddf7834fd51
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="monitor-and-manage-azure-recovery-services-vaults-and-servers-for-windows-machines"></a>Surveiller et gérer les serveurs et les coffres Azure Recovery services pour les ordinateurs Windows

Cet article présente une vue d’ensemble des tâches de surveillance et de gestion des sauvegardes via le portail Azure et l’agent Sauvegarde Microsoft Azure. Cet article part du principe que vous disposez déjà d’un abonnement Azure et que vous avez créé au moins un coffre Recovery Services.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Ouvrez un coffre Recovery Services

Le tableau de bord du coffre Recovery Services vous montre les détails ou les attributs d’un coffre Recovery Services.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide de votre abonnement Azure.
2. Cliquez sur **Tous les services**. 

3. Vous voulez ouvrir un coffre Recovery Services. Dans la boîte de dialogue, commencez à taper **Recovery Services**. Au fur et à mesure de la saisie, la liste est filtrée. Cliquez sur **Coffres Recovery Services** pour afficher la liste des coffres Recovery Services de votre abonnement.

     ![Ouvrir une liste de Recovery Services - Étape 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png) <br/>

    La liste des coffres Recovery Services s’affiche.

    ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Dans la liste des coffres, sélectionnez le coffre Recovery Services que vous souhaitez ouvrir. Le menu du tableau de bord du coffre Recovery Services s’ouvre.

    ![coffre recovery services tableau de bord](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Une fois le coffre Recovery Services ouvert, essayez l’une des tâches d’analyse ou de gestion.

## <a name="monitor-backup-jobs-and-alerts"></a>Surveiller les travaux et les alertes de sauvegarde

Vous pouvez surveiller les travaux et les alertes à partir du tableau de bord du coffre Recovery Services dans lequel s’affichent :

* Les détails des alertes de sauvegarde
* Les fichiers et dossiers, ainsi que les machines virtuelles Azure protégées dans le cloud
* Le stockage total consommé dans Azure
* L’état du travail de sauvegarde

![Tâches du tableau de bord Backup](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

Cliquez sur les informations contenues dans chacune de ces vignettes pour ouvrir le menu correspondant où vous pouvez gérer les tâches associées.

De haut en bas dans le tableau de bord :

* Panneau Paramètres : vous donne accès aux tâches de sauvegarde disponibles.
* Panneau Sauvegarde : vous permet de sauvegarder de nouveaux fichiers et dossiers (ou machines virtuelles Azure) dans le coffre Recovery Services.
* Panneau Supprimer : si un coffre Recovery Services n’est plus utilisé, supprimez-le pour libérer de l’espace de stockage. L’option Supprimer est disponible uniquement une fois que tous les serveurs protégés ont été supprimés du coffre.

![Tâches du tableau de bord Backup](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## <a name="alerts-for-backups-using-azure-backup-agent"></a>Alertes relatives aux sauvegardes à l’aide de l’agent de sauvegarde Azure :
| Niveau d’alerte | Alertes envoyées |
| --- | --- |
| Critique | Pour les échecs de sauvegarde, les échecs de récupération et la suppression différée, par exemple, quand un utilisateur arrête la protection avec une suppression de données |
| Avertissement | Pour les sauvegardes terminées avec des avertissements (lorsque < 100 fichiers n’ont pas été sauvegardés du fait de problèmes d’altération, et que > 1 000 000 fichiers ont été correctement sauvegardés) |
| Informations | Actuellement, aucune alerte d’information n’est disponible pour l’agent de sauvegarde Azure |

## <a name="manage-backup-alerts"></a>Gérer les alertes de sauvegarde
Cliquez sur la vignette **Alertes de sauvegarde** pour ouvrir le menu **Alertes de sauvegarde** et gérer les alertes.

![Alertes de sauvegarde](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

La mosaïque Alertes de sauvegarde indique :

* le nombre d’alertes critiques non résolues dans les 24 dernières heures
* le nombre d’alertes d’avertissement non résolues dans les 24 dernières heures

Cliquez sur le lien pour afficher le menu **Alertes de sauvegarde**, avec une vue filtrée de ces alertes (Critique ou Avertissement).

Dans le menu Alertes de sauvegarde, vous pouvez :

* Choisir les informations appropriées à inclure dans vos alertes

    ![Choisir les colonnes](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)
* Filtrer les alertes selon leur gravité, leur état et les heures de début/fin

    ![Filtrer les alertes](./media/backup-azure-manage-windows-server/filter-alerts.png)
* Configurer les paramètres de gravité, de fréquence et de destination des notifications, mais aussi activer ou désactiver les alertes

    ![Filtrer les alertes](./media/backup-azure-manage-windows-server/configure-notifications.png)

Si la fréquence de **notification** est définie sur **Par alerte**, le système ne procède à aucun regroupement ou réduction des e-mails. Chaque alerte se traduit par une notification (paramètre par défaut), et un e-mail de résolution est immédiatement envoyé.

Si la fréquence de **notification** est définie sur **Synthèse horaire**, un e-mail est envoyé à l’utilisateur pour lui indiquer que de nouvelles alertes non résolues ont été générées au cours de la dernière heure. Un e-mail de résolution est envoyé à la fin de l’heure.

Des alertes peuvent être envoyées pour les niveaux de gravité suivants :

* Critique
* Avertissement
* information

Pour désactiver l’alerte, cliquez sur le bouton **Désactiver** dans le menu contenant les détails du travail. Lorsque vous cliquez sur Inactivate (Désactiver), vous pouvez ajouter des commentaires pour la résolution de l’alerte.

Pour choisir les colonnes que vous souhaitez voir apparaître dans l’alerte, cliquez sur le bouton **Choisir les colonnes** .

> [!NOTE]
> Dans le menu **Paramètres**, vous gérez les alertes de sauvegarde en sélectionnant **Surveillance et rapports > Alertes et événements > Alertes de sauvegarde**, puis en cliquant sur **Filtrer** ou **Configurer les notifications**.
>
>

## <a name="manage-backup-items"></a>Gérer les éléments de sauvegarde
Vous pouvez désormais gérer les sauvegardes locales depuis le portail de gestion. Dans la section Sauvegarde du tableau de bord, la mosaïque **Éléments de sauvegarde** affiche le nombre d’éléments de sauvegarde protégés dans le coffre.

Dans la mosaïque Éléments de sauvegarde, cliquez sur **File-Folders** (Dossiers de fichiers).

![Mosaïque Éléments de sauvegarde](./media/backup-azure-manage-windows-server/backup-items-tile.png)

Le menu Éléments de sauvegarde s’ouvre avec le filtre défini sur Fichiers-dossiers et affiche chaque élément de sauvegarde spécifique.

![Éléments de sauvegarde](./media/backup-azure-manage-windows-server/backup-item-list.png)

Sélectionnez un élément de sauvegarde spécifique dans la liste pour en afficher les principaux détails.

> [!NOTE]
> Dans le menu **Paramètres**, vous gérez les fichiers et dossiers en sélectionnant **Éléments protégés > Éléments de sauvegarde**, puis en sélectionnant **Fichiers-dossiers** dans le menu déroulant.
>
>

![Éléments de sauvegarde à partir des paramètres](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## <a name="manage-backup-jobs"></a>Gérer les travaux de sauvegarde
Les travaux de sauvegarde réalisés en local (lorsque le serveur local sauvegarde vers Azure) et dans Azure s’affichent dans le tableau de bord.

Dans la section Sauvegarde du tableau de bord, la mosaïque Travail de sauvegarde indique le nombre de travaux :

* en cours
* ayant échoué au cours des 24 dernières heures

Pour gérer vos travaux de sauvegarde, cliquez sur la vignette **Travaux de sauvegarde** pour ouvrir le menu Travaux de sauvegarde.

![Éléments de sauvegarde à partir des paramètres](./media/backup-azure-manage-windows-server/backup-jobs.png)

Cliquez sur le bouton **Choisir les colonnes** en haut de la page pour modifier les informations disponibles dans le menu Travaux de sauvegarde.

Utilisez le bouton **Filtrer** pour sélectionner les fichiers ou les dossiers, ainsi que la sauvegarde de la machine virtuelle Azure.

Si vous ne voyez pas vos fichiers et dossiers de sauvegarde, cliquez sur le bouton **Filtrer** en haut de la page et sélectionnez **Fichiers et dossiers** dans le menu Type d’élément.

> [!NOTE]
> Dans le menu **Paramètres**, vous gérez les travaux de sauvegarde en sélectionnant **Surveillance et rapports > Travaux > Travaux de sauvegarde**, puis en sélectionnant **Fichiers-dossiers** dans le menu déroulant.
>
>

## <a name="monitor-backup-usage"></a>Surveiller l’utilisation de la sauvegarde
Dans la section Sauvegarde du tableau de bord, la mosaïque Backup Usage (Utilisation de la sauvegarde) indique le stockage consommé dans Azure. Les données d’utilisation du stockage incluent :

* L’utilisation du stockage cloud LRS associée au coffre
* L’utilisation du stockage cloud GRS associée au coffre

## <a name="manage-your-production-servers"></a>Gérer vos serveurs de production
Pour gérer vos serveurs de production, cliquez sur **Paramètres**.

Sous Gérer, cliquez sur **Infrastructure de sauvegarde > Serveurs de Production**.

Le menu Serveurs de Production affiche une liste de tous vos serveurs de production disponibles. Cliquez sur un serveur dans la liste pour afficher les détails correspondants.

![Éléments protégés](./media/backup-azure-manage-windows-server/production-server-list.png)


## <a name="open-the-azure-backup-agent"></a>Ouvrir l’agent Azure Backup
Ouvrez **l’agent Microsoft Azure Backup** (vous pouvez le trouver en recherchant *Microsoft Azure Backup*sur votre ordinateur).

![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)

Dans les **Actions** disponibles à droite de la console de l’agent de sauvegarde, vous pouvez effectuer les tâches de gestion suivantes :

* Inscription du serveur
* Planification d’une sauvegarde
* Sauvegarder maintenant
* Modifier les propriétés

![Actions de la console de l’agent Microsoft Azure Backup.](./media/backup-azure-manage-windows-server/console-actions.png)

> [!NOTE]
> Pour **Récupérer des données**, consultez [Restauration de fichiers sur un serveur Windows ou un ordinateur client Windows](backup-azure-restore-windows-server.md).
>
>

## <a name="modify-the-backup-schedule"></a>Modifier la planification de sauvegarde
1. Dans l’agent Microsoft Azure Backup, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)
2. Dans **l’Assistant Planification de sauvegarde**, conservez l’option **Modifier les éléments ou les dates/heures de sauvegarde** sélectionnée, puis cliquez sur **Suivant**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
3. Si vous souhaitez ajouter ou modifier des éléments, cliquez sur **Ajouter des éléments** dans l’écran **Sélectionner les éléments à sauvegarder**.

    Vous pouvez également définir les **Paramètres d’exclusion** sur cette page de l’Assistant. Si vous souhaitez exclure des fichiers ou des types de fichiers, lisez la procédure permettant d’ajouter des [paramètres d’exclusion](#manage-exclusion-settings).
4. Sélectionnez les fichiers et les dossiers que vous souhaitez sauvegarder, puis cliquez sur **OK**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/add-items-modify.png)
5. Spécifiez la **planification de sauvegarde**, puis cliquez sur **Suivant**.

    Vous pouvez planifier des sauvegardes quotidiennes (au maximum 3 fois par jour) ou hebdomadaires.

    ![Éléments de sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > La définition de la planification de sauvegarde est décrite en détail dans cet [article](backup-azure-backup-cloud-as-tape.md).
   >

6. Sélectionnez la **Stratégie de rétention** pour la copie de sauvegarde et cliquez sur **Suivant**.

    ![Éléments de sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)
7. Dans l’écran **Confirmation**, passez en revue les informations, puis cliquez sur **Terminer**.
8. Dès que l’Assistant a terminé la création de la **planification de sauvegarde**, cliquez sur **Fermer**.

    Après la modification de la protection, vous pouvez confirmer le déclenchement correct des sauvegardes en accédant à l’onglet **Travaux** et en vérifiant que les modifications sont répercutées dans les tâches de sauvegarde.

## <a name="enable-network-throttling"></a>Activation de la limitation du réseau

L’agent Azure Backup contient un onglet Limitation qui vous permet de contrôler l’utilisation de la bande passante réseau pendant le transfert de données. Cela peut s’avérer utile si vous avez besoin de sauvegarder des données pendant les heures de travail, mais ne souhaitez pas que le processus de sauvegarde interfère avec le reste du trafic internet. La limitation du transfert de données s’applique aux activités de sauvegarde et de restauration.  

Pour activer la limitation :

1. Dans **l’agent de Sauvegarde**, cliquez sur **Modifier les propriétés**.
2. Sous l’onglet **Limitation, sélectionnez la case **Activer la limitation de la bande passante sur Internet pour les opérations de sauvegarde**.

    ![Limitation du réseau](./media/backup-azure-manage-windows-server/throttling-dialog.png)

    Une fois que vous avez activé la limitation, spécifiez la bande passante autorisée pour le transfert des données de sauvegarde durant les **Heures de travail** et les **Heures chômées**.

    Les valeurs de bande passante, qui démarrent à 512 kilo-octets par seconde, peuvent aller jusqu’à 1 023 mégaoctets par seconde. Vous pouvez également définir le début et la fin des **Heures de travail**et identifier les jours de la semaine considérés comme des jours de travail. Les intervalles de temps situés en dehors des heures de travail sont considérés comme des périodes (heures) chômées.
3. Cliquez sur **OK**.

## <a name="manage-exclusion-settings"></a>Gérer les paramètres d’exclusion
1. Ouvrez **l’agent Microsoft Azure Backup** (vous pouvez le trouver en recherchant *Microsoft Azure Backup*sur votre ordinateur).

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)
2. Dans l’agent Microsoft Azure Backup, cliquez sur **Planifier la sauvegarde**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)
3. Dans l’Assistant Planification de sauvegarde, conservez l’option **Modifier les éléments ou les dates/heures de sauvegarde** sélectionnée, puis cliquez sur **Suivant**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
4. Cliquez sur **Paramètres d’exclusion**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/exclusion-settings.png)
5. Cliquez sur **Ajouter une exclusion**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/add-exclusion.png)
6. Sélectionnez l’emplacement, puis cliquez sur **OK**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/exclusion-location.png)
7. Ajoutez l’extension de fichier dans le champ **Type de fichier** .

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    Ajout d’une extension .mp3

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    Pour ajouter une autre extension, cliquez sur **Ajouter une exclusion** et entrez une autre extension de type de fichier (ajout d’une extension .jpeg).

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/exclude-jpg.png)
8. Lorsque vous avez ajouté toutes les extensions, cliquez sur **OK**.
9. Suivez les instructions de l’Assistant Planification de sauvegarde en cliquant sur **Suivant** jusqu’à la **page de Confirmation**, puis cliquez sur **Terminer**.

    ![Planifier une sauvegarde de Windows Server](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)
**Q1. Le travail de sauvegarde est indiqué comme terminé dans l’agent de sauvegarde Azure, pourquoi cela ne s’affiche pas immédiatement dans le portail ?**

R1. Un délai maximal de 15 minutes est nécessaire pour que l’état du travail de sauvegarde dans l’agent de sauvegarde Azure soit répercuté dans le portail Azure.

**Q2. En cas d’échec d’un travail de sauvegarde, combien de temps faut-il pour déclencher une alerte ?**

R2. Une alerte est générée dans les 20 minutes suivant l’échec de la sauvegarde Azure.

**Q3. Est-il possible qu’aucun e-mail ne soit envoyé alors que les notifications sont activées ?**

R3 Voici les cas pour lesquels la notification ne sera pas envoyée afin de réduire le bruit des alertes :

* Si les notifications sont configurées sur une base horaire et qu’une alerte est déclenchée et résolue dans l’heure.
* Si le travail est annulé.
* Si le travail de sauvegarde secondaire a échoué, car un travail de sauvegarde d’origine est en cours.

## <a name="troubleshooting-monitoring-issues"></a>Résolution des problèmes de surveillance
**Problème :** les travaux et/ou les alertes de l’agent de sauvegarde Azure n’apparaissent pas sur le portail.

**Étapes de dépannage :** le processus ```OBRecoveryServicesManagementAgent``` envoie les données relatives à l’alerte et au travail au service de sauvegarde Azure. Il peut arriver que ce processus se bloque ou s’arrête.

1. Pour vérifier que le processus n’est pas en cours d’exécution, ouvrez **Gestionnaire des tâches** et vérifiez si le processus ```OBRecoveryServicesManagementAgent``` est en cours.
2. En supposant que le processus ne fonctionne pas, ouvrez le **Panneau de configuration** et parcourez la liste des services. Démarrez ou redémarrez l’**Agent de gestion Microsoft Azure Recovery Services**.

    Pour plus d’informations, consultez les journaux à l’emplacement suivant :<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Par exemple :<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Étapes suivantes
* [Restaurer un serveur Windows Server ou un client Windows à partir d’Azure](backup-azure-restore-windows-server.md)
* Pour en savoir plus sur Azure Backup, consultez la [vue d’ensemble d’Azure Backup](backup-introduction-to-azure-backup.md)
* Consultez le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933)
