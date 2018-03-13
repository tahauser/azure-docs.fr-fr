---
title: Surveiller Azure SQL Data Sync avec OMS Log Analytics | Microsoft Docs
description: "Découvrez comment surveiller Azure SQL Data Sync à l’aide d’OMS Log Analytics"
services: sql-database
ms.date: 11/07/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 8683b3aec569f210529c1188cbbf514f7956b340
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-sql-data-sync-preview-with-oms-log-analytics"></a>Surveiller SQL Data Sync (préversion) avec OMS Log Analytics 

Pour contrôler le journal d’activité de SQL Data Sync et détecter les erreurs et avertissements, vous deviez vérifier manuellement SQL Data Sync dans le portail Azure ou utiliser PowerShell ou l’API REST. Suivez les étapes décrites dans cet article pour configurer une solution personnalisée qui améliore l’expérience de surveillance de Data Sync. Vous pouvez personnaliser cette solution pour l’adapter à votre scénario.

Pour obtenir une vue d’ensemble de SQL Data Sync, consultez [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync (Préversion)](sql-database-sync-data.md).

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Tableau de bord de surveillance pour tous vos groupes de synchronisation 

Vous n’avez plus besoin d’examiner les journaux de chaque groupe de synchronisation individuellement pour rechercher des problèmes. Vous pouvez surveiller tous vos groupes de synchronisation à partir de n’importe lequel de vos abonnements et d’un seul emplacement à l’aide d’une vue personnalisée OMS (Operations Management Suite). Cette vue met en évidence les informations qui intéressent les clients SQL Data Sync.

![Tableau de surveillance Data Sync](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notifications automatiques par e-mail

Vous n’avez plus besoin de consulter le journal manuellement dans le portail Azure ou par le biais de PowerShell ou de l’API REST. Grâce à [OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), vous pouvez créer des alertes qui sont envoyées directement aux adresses e-mail des personnes devant être informées en cas d’erreur.

![Notifications Data Sync par e-mail](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Comment faire pour configurer ces fonctionnalités de surveillance ? 

Effectuez les étapes suivantes pour implémenter une solution de surveillance OMS personnalisée pour SQL Data Sync en moins d’une heure :

Vous devez configurer trois composants :

-   Un runbook PowerShell pour envoyer les données du journal SQL Data Sync à OMS.

-   Une alerte OMS Log Analytics pour les notifications par e-mail.

-   Une vue OMS pour la surveillance.

### <a name="samples-to-download"></a>Exemples à télécharger

Téléchargez les deux exemples suivants :

-   [Runbook PowerShell de journal Data Sync](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Vue OMS de journal Data Sync](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>configuration requise

Vérifiez que vous avez configuré les éléments suivants :

-   Un compte Azure Automation

-   Log Analytics lié à l’espace de travail OMS

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Un runbook PowerShell pour obtenir le journal SQL Data Sync 

Utilisez un runbook PowerShell hébergé dans Azure Automation pour extraire les données de journal SQL Data Sync et les envoyer à OMS. Un exemple de script est fourni. Comme prérequis, vous devez disposer d’un compte Azure Automation. Vous devez ensuite créer un runbook et planifier son exécution. 

### <a name="create-a-runbook"></a>Créer un runbook

Pour plus d’informations sur la création d’un runbook, consultez [Mon premier Runbook PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Sous votre compte Azure Automation, sélectionnez l’onglet **Runbooks** sous Automatisation des processus.

2.  Sélectionnez **Ajouter un runbook** en haut à gauche de la page Runbooks.

3.  Sélectionnez **Importer un runbook existant**.

4.  Sous **Fichier de runbook**, utilisez le fichier `DataSyncLogPowerShellRunbook` donné. Définissez `PowerShell` comme **Type de runbook**. Donnez un nom au runbook.

5.  Sélectionnez **Créer**. Vous disposez maintenant d’un runbook.

6.  Sous votre compte Azure Automation, sélectionnez l’onglet **Variables** sous Ressources partagées.

7.  Dans la page Variables, sélectionnez **Ajouter une variable**. Créez une variable pour stocker la dernière heure d’exécution du runbook. Si vous avez plusieurs runbooks, vous avez besoin d’une variable pour chaque runbook.

8.  Définissez `DataSyncLogLastUpdatedTime` comme nom de variable et DateTime comme Type.

9.  Sélectionnez le runbook, puis cliquez sur le bouton Modifier en haut de la page.

10. Apportez les modifications requises pour votre compte et votre configuration SQL Data Sync. (Pour plus d’informations, consultez l’exemple de script.)

    1.  Informations Azure.

    2.  Informations sur les groupes de synchronisation.

    3.  Informations OMS. Recherchez ces informations sur le portail OMS | Paramètres | Sources connectées. Pour plus d’informations sur l’envoi de données à Log Analytics, consultez [Transmettre des données à Log Analytics avec l’API Collecteur de données HTTP (préversion publique)](../log-analytics/log-analytics-data-collector-api.md).

11. Exécutez le runbook dans le volet Test. Vérifiez que le test a réussi.

    S’il y a des erreurs, vérifiez que le module PowerShell le plus récent est installé. Vous pouvez installer le module PowerShell le plus récent dans la **Galerie de modules** dans votre compte Automation.

12. Cliquez sur **Publier**.

### <a name="schedule-the-runbook"></a>Planifier le runbook

Pour planifier le runbook

1.  Sous le runbook, cliquez sur l’onglet **Planifications** sous Ressources.

2.  Sélectionnez **Ajouter une planification** dans la page Planifications.

3.  Sélectionnez **Associer une planification à votre runbook**.

4.  Sélectionnez **Créer une planification**.

5.  Définissez **Récurrence** sur Récurrent et définissez l’intervalle souhaité. Utilisez le même intervalle ici, dans le script et dans OMS.

6.  Sélectionnez **Créer**.

### <a name="check-the-automation"></a>Vérifier l’automation

Pour surveiller si votre automation s’exécute comme prévu, sous **Vue d’ensemble** pour votre compte automation, recherchez la vue **Statistiques des tâches** sous **Surveillance**. Épinglez cette vue à votre tableau de bord pour en faciliter la consultation. Les exécutions réussies du runbook sont affichées avec la mention « Terminé » et les exécutions ayant échoué avec la mention « Échec ».

## <a name="create-an-oms-log-reader-alert-for-email-notifications"></a>Créer une alerte de lecture du journal OMS pour les notifications par e-mail

Pour créer une alerte qui utilise OMS Log Analytics, effectuez les étapes suivantes. Comme prérequis, vous devez lier Log Analytics à un espace de travail OMS.

1.  Dans le portail OMS, sélectionnez **Recherche dans les journaux**.

2.  Créez une requête pour sélectionner les erreurs et avertissements par groupe de synchronisation dans l’intervalle que vous avez sélectionné. Par exemple : 

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Après avoir exécuté la requête, sélectionnez la cloche indiquant **Alerte**.

4.  Sous **Générer l’alerte selon**, sélectionnez **Mesure de métriques**.

    1.  Affectez **Supérieur à** comme Valeur de l’agrégat.

    2.  Après **Supérieur à**, entrez le seuil qui doit s’écouler avant que vous receviez des notifications. Une certaine quantité d’erreurs temporaires sont attendues dans Data Sync. Pour réduire le bruit, définissez le seuil avec la valeur 5.

5.  Sous **Actions**, choisissez Oui pour **Notification par e-mail**. Entrez les destinataires de messagerie de votre choix.

6.  Cliquez sur **Enregistrer**. Les destinataires spécifiés reçoivent maintenant des notifications par e-mail quand des erreurs se produisent.

## <a name="create-an-oms-view-for-monitoring"></a>Créer une vue OMS pour la surveillance

Cette étape crée une vue OMS pour surveiller visuellement tous les groupes de synchronisation spécifiés. La vue comprend plusieurs composants :

-   Une vignette de vue d’ensemble, qui montre combien d’erreurs, de succès et d’avertissements ont tous les groupes de synchronisation.

-   Une vignette pour tous les groupes de synchronisation, qui montre le nombre d’erreurs et d’avertissements par groupe de synchronisation. Les groupes sans problème n’apparaissent pas dans cette vignette.

-   Une vignette pour chaque groupe de synchronisation, qui montre le nombre d’erreurs, de succès et d’avertissements et les messages d’erreur récents.

Pour configurer la vue OMS, effectuez les étapes suivantes :

1.  Dans la page d’accueil OMS, sélectionnez le signe plus à gauche pour ouvrir le **Concepteur de vue**.

2.  Sélectionnez **Importer** dans la barre supérieure du Concepteur de vue. Sélectionnez ensuite l’exemple de fichier « DataSyncLogOMSView ».

3.  L’exemple de vue concerne la gestion de deux groupes de synchronisation. Modifiez cette vue pour l’adapter à votre scénario. Cliquez sur **Modifier** et apportez les modifications suivantes :

    1.  Créez des objets « Donut & List » à partir de la galerie en fonction des besoins.

    2.  Dans chaque vignette, mettez à jour les requêtes avec vos informations.

        1.  Dans chaque vignette, changez l’intervalle TimeStamp_t comme vous le souhaitez.

        2.  Dans les vignettes de chaque groupe de synchronisation, mettez à jour les noms des groupes de synchronisation.

    3.  Dans chaque vignette, mettez à jour le titre en fonction des besoins.

4.  Cliquez sur **Enregistrer** et la vue est prête.

## <a name="cost-of-this-solution"></a>Coût de cette solution

Dans la plupart des cas, cette solution est gratuite.

**Azure Automation :** il peut y avoir un coût associé au compte Azure Automation, en fonction de votre utilisation. Les 500 premières minutes d’exécution de travail par mois sont gratuites. Dans la plupart des cas, cette solution doit utiliser moins de 500 minutes par mois. Pour éviter des frais, planifiez le runbook pour qu’il s’exécute à un intervalle de deux heures ou plus. Pour plus d’informations, consultez [Tarification Automation](https://azure.microsoft.com/pricing/details/automation/).

**OMS Log Analytics :** il peut y avoir un coût associé à OMS en fonction de votre utilisation. Le niveau gratuit inclut 500 Mo de données ingérées par jour. Dans la plupart des cas, cette solution doit ingérer moins de 500 Mo par jour. Pour réduire l’utilisation, utilisez le filtrage « échec uniquement » inclus dans le runbook. Si vous utilisez plus de 500 Mo par jour, effectuez une mise à niveau vers le niveau payant pour éviter que l’analytique ne s’arrête quand la limite est atteinte. Pour plus d’informations, consultez [Tarification - Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Exemples de code

Téléchargez les exemples de code décrits dans cet article à partir des emplacements suivants :

-   [Runbook PowerShell de journal Data Sync](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Vue OMS de journal Data Sync](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur SQL Data Sync, consultez :

-   [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync](sql-database-sync-data.md)
-   [Configurer Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)
-   [Bonnes pratiques pour Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   [Résoudre les problèmes liés à Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

-   Exemples PowerShell complets qui montrent comment configurer SQL Data Sync :
    -   [Utilisez PowerShell pour la synchronisation entre plusieurs bases de données SQL Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Utiliser PowerShell pour la synchronisation entre une base de données SQL Azure et une base de données locale SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Télécharger la documentation de l’API REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Pour plus d’informations sur SQL Database, consultez :

-   [Vue d’ensemble des bases de données SQL](sql-database-technical-overview.md)
-   [Gestion du cycle de vie des bases de données](https://msdn.microsoft.com/library/jj907294.aspx)
