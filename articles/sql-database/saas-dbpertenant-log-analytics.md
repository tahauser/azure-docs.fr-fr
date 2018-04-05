---
title: Utiliser Log Analytics avec une application mutualisée SQL Database | Microsoft Docs
description: Configurer et utiliser Log Analytics (Operations Management Suite) avec une application SaaS Azure SQL Database mutualisée
keywords: didacticiel sur les bases de données SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 38a849ca5f4a767a4b9d9b9b86549e89a8217a2a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="set-up-and-use-log-analytics-operations-management-suite-with-a-multitenant-sql-database-saas-app"></a>Configurer et utiliser Log Analytics (Operations Management Suite) avec une application SaaS SQL Database mutualisée

Dans ce didacticiel, vous configurez et utilisez Azure Log Analytics ([Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)) pour surveiller les bases de données et les pools élastiques. Ce didacticiel s’appuie sur le [didacticiel Surveiller et gérer les performances des bases de données SQL Azure et des pools dans une application SaaS multilocataire](saas-dbpertenant-performance-monitoring.md). Il montre comment utiliser Log Analytics pour améliorer la surveillance et la création d’alertes disponibles dans le portail Azure. Log Analytics prend en charge la surveillance de milliers de pools élastiques et de centaines de milliers de bases de données. Log Analytics offre une solution de surveillance unique qui permet d’intégrer la surveillance de différents services Azure et applications dans plusieurs abonnements Azure.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Installer et configurer Log Analytics (Operations Management Suite).
> * Utiliser Log Analytics pour surveiller des pools et des bases de données.

Pour suivre ce didacticiel, vérifiez que les prérequis suivants sont remplis :

* L’application de base de données par locataire SaaS Wingtip Tickets est déployée. Pour procéder à un déploiement en moins de cinq minutes, consultez [Déployer et explorer une application multi-locataire SaaS qui illustre le modèle de base de données par locataire avec Azure SQL Database](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Pour plus d’informations sur les scénarios et les modèles SaaS, et leur incidence sur les exigences applicables à une solution de surveillance, consultez le [didacticiel Surveiller et gérer les performances des bases de données SQL Azure et des pools dans une application SaaS multilocataire](saas-dbpertenant-performance-monitoring.md).

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite"></a>Surveiller et gérer des performances de base de données et de pool élastique avec Log Analytics ou Operations Management Suite

Pour Azure SQL Database, la surveillance et les alertes sont disponibles pour les bases de données et les pools dans le portail Azure. Ces fonctions de surveillance et de génération d’alertes intégrées sont pratiques, mais également propres à la ressource. Cela signifie qu’elles sont moins bien adaptées pour surveiller les installations de grande taille ou fournir une vue unifiée sur l’ensemble des ressources et abonnements.

Pour les scénarios à volumes élevés, vous pouvez utiliser Log Analytics pour la surveillance et les alertes. Log Analytics est un service Azure distinct qui permet d’analyser des journaux de diagnostic et des données de télémétrie recueillis dans un espace de travail à partir de nombreux services. Log Analytics offre un langage de requête intégré et des outils de visualisation pour l’analyse des données opérationnelles. La solution SQL Analytics fournit plusieurs vues et requêtes prédéfinies de surveillance et d’alertes pour les pools élastiques et les bases de données. Operations Management Suite fournit également un concepteur de vues personnalisées.

Les espaces de travail et les solutions d’analyse de Log Analytics s’ouvrent dans le portail Azure et dans Operations Management Suite. Le portail Azure constitue le point d’accès le plus récent, mais il peut être moins évolué que le portail Operations Management Suite dans certains domaines.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Créer des données de diagnostic des performances en simulant une charge de travail sur vos locataires 

1. Dans PowerShell ISE, ouvrez *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Demo-PerformanceMonitoringAndManagement.ps1*. Gardez ce script ouvert, car vous souhaiterez peut-être exécuter plusieurs des scénarios de génération de charge au cours de ce didacticiel.
2. Si ce n’est déjà fait, approvisionnez un lot de locataires pour bénéficier d’un contexte de surveillance plus intéressant. Ce processus prend quelques minutes.

   a. Définissez **$DemoScenario = 1**, _Approvisionner un lot de locataires_.

   b. Pour exécuter le script et déployer 17 locataires supplémentaires, appuyez sur F5.

3. Démarrez le Générateur de charge pour exécuter une charge simulée sur tous les locataires.

    a. Définissez **$DemoScenario = 2**, _Generate normal intensity load (Générer une charge d’intensité normale) (environ 30 DTU)_.

    b. Pour exécuter le script, appuyez sur F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenir les scripts de l’application de base de données par locataire SaaS Wingtip Tickets

Les scripts et le code de l’application de base de données mutualisée SaaS Wingtip Tickets sont disponibles dans le répertoire GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consultez les [conseils généraux](saas-tenancy-wingtip-app-guidance-tips.md) avant de télécharger et de débloquer les scripts Wingtip Tickets PowerShell.

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>Installer et configurer Log Analytics et la solution Azure SQL Analytics

Log Analytics est un service distinct qui doit être configuré. Log Analytics collecte des données de journaux, de télémétrie et les métriques dans un espace de travail Log Analytics. Tout comme les autres ressources dans Azure, un espace de travail Log Analytics doit être créé. L’espace de travail ne doit pas être créé dans le même groupe de ressources que les applications qu’il surveille, même si cela est souvent l’option la plus logique. Pour l’application Wingtip Tickets, utilisez un groupe de ressources unique pour vous assurer que l’espace de travail est supprimé avec l’application.

1. Dans PowerShell ISE, ouvrez *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\**Demo-LogAnalytics.ps1***.
2. Pour exécuter le script, appuyez sur F5.

Vous pouvez maintenant ouvrir Log Analytics dans le portail Azure ou le portail Operations Management Suite. Il faut compter quelques minutes pour que les données de télémétrie soient collectées dans l’espace de travail Log Analytics et deviennent visibles. Plus vous laisserez de temps au système pour rassembler des données de diagnostic, plus l’expérience sera intéressante. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Utiliser Log Analytics et la solution SQL Analytics pour surveiller des pools et des bases de données


Dans cet exercice, ouvrez Log Analytics et le portail Operations Management Suite pour examiner les données de télémétrie collectées pour les bases de données et les pools.

1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez **All services** (Tous les services) pour Log Analytics. Recherchez ensuite Log Analytics.

   ![Ouvrir Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

2. Sélectionnez l’espace de travail nommé _wtploganalytics-&lt;user&gt;_.

3. Sélectionnez **Vue d’ensemble** pour ouvrir la solution Log Analytics dans le portail Azure.

   ![Vue d'ensemble](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > L’activation de la solution peut prendre quelques minutes. 

4. Sélectionnez la vignette **Azure SQL Analytics** pour l’ouvrir.

    ![Vignette d’aperçu](media/saas-dbpertenant-log-analytics/overview.png)

5. Vous pouvez faire défiler horizontalement les vues dans la solution à l’aide de leur propre barre de défilement située en bas. Actualisez la page, si nécessaire.

6. Pour explorer la page de résumé, sélectionnez les vignettes ou les bases de données individuelles pour ouvrir un explorateur détaillé.

    ![Tableau de bord Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

7. Modifiez le paramètre de filtre pour changer l’intervalle de temps. Pour ce didacticiel, choisissez **Dernière heure**.

    ![Filtre de temps](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

8. Sélectionnez une seule base de données pour explorer l’utilisation des requêtes et les métriques pour cette base de données.

    ![Analyse de base de données](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

9. Pour afficher l’utilisation des métriques, faites défiler la page de l’analyse vers la droite.
 
     ![Métriques de base de données](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

10. Faites défiler la page de l’analyse vers la gauche, puis sélectionnez la vignette du serveur dans la liste **Informations sur les ressources**.  

    ![Liste Informations sur les ressources](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Une page présentant les pools et les bases de données sur le serveur s’ouvre.

    ![Serveur avec pools et bases de données](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

11. Sélectionnez un pool. Sur la page du pool qui s’ouvre, faites défiler vers la droite pour afficher les métriques du pool. 

    ![Métriques du pool](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


12. Revenez dans l’espace de travail Log Analytics, sélectionnez **Portail OMS** pour ouvrir l’espace de travail à cet endroit.

    ![Vignette du portail Operations Management Suite](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Dans le portail Operations Management Suite, vous pouvez explorer plus en détail les données de journal et les métriques dans l’espace de travail. 

La surveillance et les alertes dans Log Analytics et Operations Management Suite sont basées sur des requêtes portant sur les données dans l’espace de travail, à la différence des alertes définies sur chaque ressource dans le portail Azure. En basant les alertes sur les requêtes, vous pouvez définir une seule alerte qui examine toutes les bases de données plutôt que d’en définir une par base de données. Les requêtes sont seulement limitées par les données disponibles dans l’espace de travail.

Pour plus d’informations sur l’utilisation d’Operations Management Suite pour interroger et définir des alertes, consultez [Utilisation des règles d’alerte dans Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Log Analytics pour SQL Database est facturé en fonction du volume de données dans l’espace de travail. Dans ce didacticiel, vous avez créé un espace de travail gratuit, qui est limité à 500 Mo par jour. Une fois cette limite atteinte, l’ajout de données à l’espace de travail est interrompu.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Installer et configurer Log Analytics (Operations Management Suite).
> * Utiliser Log Analytics pour surveiller des pools et des bases de données.

Essayez maintenant le [didacticiel sur l’analyse des locataires](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Autres didacticiels reposant sur le déploiement initial de l’application de base de données Wingtip Tickets SaaS par locataire](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [Operations Management Suite](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
