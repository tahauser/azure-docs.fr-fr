---
title: "Utiliser Log Analytics avec une application mutualisée SQL Database | Microsoft Docs"
description: Configurer et utiliser Log Analytics (OMS) avec une application SaaS Azure SQL Database multilocataire
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: billgib; sstein
ms.openlocfilehash: 7747092d5613a40fa0aff09cfbdfb9b786b37954
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Configurer et utiliser Log Analytics (OMS) avec une application SaaS Azure SQL Database multilocataire

Dans ce didacticiel, vous configurez et utilisez *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* pour surveiller les pools élastiques et les bases de données. Ce didacticiel s’appuie sur le [didacticiel relatif à la surveillance et à la gestion des performances](saas-dbpertenant-performance-monitoring.md). Il montre comment utiliser *Log Analytics* pour améliorer la surveillance et la création d’alertes disponibles dans le portail Azure. Log Analytics prend en charge la surveillance de milliers de pools élastiques et de centaines de milliers de bases de données. Log Analytics offre également une solution de surveillance unique qui permet d’intégrer la surveillance de différents services Azure et applications dans plusieurs abonnements Azure.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Installer et configurer Log Analytics (OMS)
> * Utiliser Log Analytics pour surveiller des pools et des bases de données

Pour suivre ce didacticiel, vérifiez que les prérequis suivants sont remplis :

* L’application de base de données Wingtip Tickets SaaS par client est déployée. Pour procéder à un déploiement en moins de cinq minutes, consultez la page [Déployer et explorer l’application de base de données Wingtip Tickets SaaS par client](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell est installé. Pour plus d’informations, voir [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Pour plus d’informations sur les scénarios et les modèles SaaS, et leur incidence sur les exigences applicables à une solution de surveillance, consultez le [didacticiel sur la surveillance et la gestion des performances](saas-dbpertenant-performance-monitoring.md).

## <a name="monitoring-and-managing-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite-oms"></a>Analyse et gestion des performances de base de données et de pool élastique avec Log Analytics ou Operations Management Suite (OMS)

Pour SQL Database, la surveillance et les alertes sont disponibles pour les bases de données et les pools dans le portail Azure. Cette surveillance et ces alertes intégrées sont pratiques, mais étant donné qu’elles sont propres aux ressources, elles sont moins adaptées pour surveiller des installations importantes ou offrir une vue unifiée des abonnements et ressources.

Pour les scénarios à volumes élevés, il est possible d’utiliser Log Analytics pour la surveillance et les alertes. Log Analytics est un service Azure distinct qui permet d’effectuer l’analytique des journaux de diagnostic et des données de télémétrie recueillis dans un espace de travail à partir de nombreux services. Log Analytics offre un langage de requête intégré et des outils de visualisation pour l’analytique des données opérationnelles. La solution SQL Analytics fournit plusieurs vues et requêtes prédéfinies de surveillance et d’alerte pour les pools élastiques et les bases de données. OMS offre également un concepteur de vues personnalisées.

Les espaces de travail et les solutions d’analyse de Log Analytics peuvent être ouverts à la fois dans le portail Azure et dans OMS. Le portail Azure constitue le point d’accès le plus récent, mais il peut être moins évolué que le portail OMS dans certains domaines.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Créer des données de diagnostic des performances en simulant une charge de travail sur vos locataires 

1. Dans **PowerShell ISE**, ouvrez *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\**Demo-PerformanceMonitoringAndManagement.ps1***. Gardez ce script ouvert, car vous souhaiterez peut-être exécuter plusieurs des scénarios de génération de charge au cours de ce didacticiel.
1. Si ce n’est déjà fait, provisionnez un lot de locataires pour bénéficier d’un contexte de surveillance plus intéressant. Ceci prend quelques minutes :
   1. Définissez **$DemoScenario = 1**, _Provision a batch of tenants_ (Provisionner un lot de locataires)
   1. Pour exécuter le script et déployer 17 locataires supplémentaires, appuyez sur **F5**.  

1. Démarrez le Générateur de charge pour exécuter une charge simulée sur tous les locataires.  
    1. Définissez **$DemoScenario = 2**, _Generate normal intensity load (Générer une charge d’intensité normale) (environ 30 DTU)_.
    1. Pour exécuter le script, appuyez sur la touche **F5**.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenir les scripts de l'application Wingtip Tickets SaaS Database Per Tenant

Les scripts et le code de l’application de base de données multi-locataire SaaS Wingtip Tickets sont disponibles dans le dépôt GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consultez les [conseils généraux](saas-tenancy-wingtip-app-guidance-tips.md) avant de télécharger et de débloquer les scripts Wingtip Tickets PowerShell.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Installation et configuration de Log Analytics et de la solution Azure SQL Analytics

Log Analytics est un service distinct qui doit être configuré. Log Analytics collecte des données de journaux, de télémétrie et les métriques dans un espace de travail dédié à l’analytique de journal. Un espace de travail d’analytique de journal constitue une ressource, et tout comme les autres ressources d’Azure, il doit être créé. Bien qu’il ne soit pas nécessaire de créer l’espace de travail dans le même groupe de ressources que les applications qu’il surveille, c’est souvent l’option la plus logique. Pour l’application Wingtip Tickets, l’utilisation d’un groupe de ressources unique garantit que l’espace de travail est supprimé avec l’application.

1. Dans **PowerShell ISE**, ouvrez *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\**Demo-LogAnalytics.ps1***.
1. Pour exécuter le script, appuyez sur la touche **F5**.

À ce stade, vous devez être en mesure d’ouvrir Log Analytics dans le portail Azure (ou le portail OMS). Il faut compter quelques minutes pour que les données de télémétrie soient collectées dans l’espace de travail Log Analytics et deviennent visibles. Plus vous laisserez de temps au système pour rassembler des données de diagnostic, plus l’expérience sera intéressante. C’est le bon moment pour aller vous chercher quelque chose à boire – vérifiez simplement que le générateur de charge est toujours en cours d’exécution !

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Utiliser Log Analytics et la solution SQL Analytics pour surveiller des pools et des bases de données


Dans cet exercice, ouvrez Log Analytics et le portail OMS pour examiner les données de télémétrie en cours de collecte pour les bases de données et les pools.

1. Accédez au [portail Azure](https://portal.azure.com) et ouvrez Log Analytics en cliquant sur **Tous les services**, puis recherchez Log Analytics :

   ![Ouvrir Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Sélectionnez l’espace de travail nommé _wtploganalytics-&lt;user&gt;_.

1. Sélectionnez **Vue d’ensemble** pour ouvrir la solution Log Analytics dans le portail Azure.

   ![lien Vue d’ensemble](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > L’activation de la solution peut prendre quelques minutes. Soyez patient !

1. Cliquez sur la vignette Azure SQL Analytics pour l’ouvrir.

    ![Vue d’ensemble](media/saas-dbpertenant-log-analytics/overview.png)

    ![Analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Vous pouvez faire défiler horizontalement les vues dans la solution à l’aide de leur propre barre de défilement située en bas (actualisez la page si nécessaire).

1. Explorez la page de résumé en cliquant sur les vignettes ou sur une base de données individuelle pour ouvrir un explorateur détaillé.

1. Modifiez le paramètre de filtre pour changer l’intervalle de temps, pour ce didacticiel choisissez _Dernière heure_

    ![filtre de temps](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Sélectionnez une seule base de données pour explorer l’utilisation des requêtes et les métriques pour cette base de données.

    ![analytique de base de données](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Pour afficher l’utilisation des métriques, faites défiler la page de l’analytique vers la droite.
 
     ![métriques de base de données](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Faites défiler la page de l’analytique vers la gauche, puis cliquez sur la vignette du serveur dans la liste Informations sur les ressources. Une page présentant les pools et les bases de données sur le serveur s’ouvre. 

     ![informations sur les ressources](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

 
     ![serveur avec pools et bases de données](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Sur la page du serveur qui s’ouvre et affiche les pools et les bases de données sur le serveur, cliquez sur le pool.  Sur la page du pool qui s’ouvre, faites défiler vers la droite pour afficher les métriques du pool.  

     ![métriques du pool](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)



1. Revenez dans l’espace de travail Log Analytics, sélectionnez **Portail OMS** pour ouvrir l’espace de travail à cet endroit.

    ![OMS](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Dans le portail OMS, vous pouvez explorer plus en détail les données de journal et les métriques dans l’espace de travail.  

La surveillance et les alertes dans Log Analytics et OMS sont basées sur des requêtes portant sur les données dans l’espace de travail, à la différence des alertes définies sur chaque ressource dans le portail Azure. En basant les alertes sur les requêtes, vous pouvez définir une seule alerte qui examine toutes les bases de données plutôt que d’en définir une par base de données. Les requêtes sont seulement limitées par les données disponibles dans l’espace de travail.

Pour plus d’informations sur l’utilisation d’OMS pour interroger et définir des alertes, consultez [Utilisation des règles d’alerte dans Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Log Analytics pour SQL Database est facturé en fonction du volume de données dans l’espace de travail. Dans ce didacticiel, vous avez créé un espace de travail gratuit, qui est limité à 500 Mo par jour. Une fois cette limite atteinte, l’ajout de données à l’espace de travail est interrompu.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Installer et configurer Log Analytics (OMS)
> * Utiliser Log Analytics pour surveiller des pools et des bases de données

[Didacticiel sur l’analyse des locataires](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Autres didacticiels reposant sur le déploiement initial de l’application de base de données Wingtip Tickets SaaS par client](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
