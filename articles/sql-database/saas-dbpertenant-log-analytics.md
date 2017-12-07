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
ms.openlocfilehash: 48e8eb91a5febcc1109bee3404bb534bd0391f88
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Configurer et utiliser Log Analytics (OMS) avec une application SaaS Azure SQL Database multilocataire

Dans ce didacticiel, vous configurez et utilisez *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* pour surveiller les pools élastiques et les bases de données. Ce didacticiel s’appuie sur le [didacticiel relatif à la surveillance et à la gestion des performances](saas-dbpertenant-performance-monitoring.md). Il montre comment utiliser *Log Analytics* pour améliorer la surveillance et la création d’alertes disponibles dans le portail Azure. Log Analytics est adapté à la surveillance et à la création d’alertes à grande échelle, car il prend en charge des centaines de pools et des centaines de milliers de bases de données. Il offre également une solution de surveillance unique qui permet d’intégrer la surveillance de différents services Azure et applications dans plusieurs abonnements Azure.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Installer et configurer Log Analytics (OMS)
> * Utiliser Log Analytics pour surveiller des pools et des bases de données

Pour suivre ce tutoriel, vérifiez que les conditions préalables suivantes sont bien satisfaites :

* L’application Wingtip Tickets SaaS Database Per Tenant est déployée. Pour un déploiement en moins de cinq minutes, consultez [Déployer et explorer l’application Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Pour plus d’informations sur les scénarios et les modèles SaaS, et leur incidence sur les exigences applicables à une solution de surveillance, consultez le [didacticiel sur la surveillance et la gestion des performances](saas-dbpertenant-performance-monitoring.md).

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Surveillance et gestion des performances avec Log Analytics (OMS)

Pour SQL Database, la surveillance et les alertes sont disponibles pour les bases de données et les pools. Cette surveillance et ces alertes intégrées sont propres aux ressources et pratiques pour les ressources peu nombreuses, mais sont moins adaptées pour surveiller des installations importantes ou offrir une vue unifiée des différents abonnements et ressources.

Pour les scénarios à volumes élevés, il est possible d’utiliser Log Analytics. Il s’agit d’un service Azure distinct assurant l’analyse des journaux de diagnostic et des données de télémétrie émis, rassemblés dans un espace de travail qui permet de collecter de telles données pour de nombreux services, d’exécuter des requêtes et de définir des alertes. Log Analytics offre un langage de requête intégré et des outils d’analyse et de visualisation des données opérationnelles. La solution SQL Analytics fournit plusieurs vues et requêtes prédéfinies de surveillance et d’alerte pour les pools élastiques et les bases de données, et vous permet d’ajouter vos propres requêtes ad hoc et de les enregistrer si nécessaire. OMS offre également un concepteur de vues personnalisées.

Les espaces de travail et les solutions d’analyse de Log Analytics peuvent être ouverts à la fois dans le portail Azure et dans OMS. Le portail Azure constitue le point d’accès le plus récent, mais il peut être moins évolué que le portail OMS dans certains domaines.

### <a name="create-data-by-starting-the-load-generator"></a>Créer des données en démarrant le générateur de charges 

1. Dans **PowerShell ISE**, ouvrez le fichier **Demo-PerformanceMonitoringAndManagement.ps1**. Gardez ce script ouvert, car vous souhaiterez peut-être exécuter plusieurs des scénarios de génération de charge au cours de ce didacticiel.
1. Si vous avez moins de cinq locataires, approvisionnez un lot de locataires pour bénéficier d’un contexte de surveillance plus intéressant :
   1. Définissez **$DemoScenario = 1,** **Provision a batch of tenants** (Configurer un lot de locataires).
   1. Pour exécuter le script, appuyez sur la touche **F5**.

1. Définissez **$DemoScenario** = 2, **Generate normal intensity load (approx 40 DTU)** (Générer une charge d’intensité normale [environ 40 DTU]).
1. Pour exécuter le script, appuyez sur la touche **F5**.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenir les scripts de l'application Wingtip Tickets SaaS Database Per Tenant

Les scripts et le code de l’application de base de données multi-locataire SaaS Wingtip Tickets sont disponibles dans le dépôt GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consultez les [conseils généraux](saas-tenancy-wingtip-app-guidance-tips.md) avant de télécharger et de débloquer les scripts Wingtip Tickets SaaS.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Installation et configuration de Log Analytics et de la solution Azure SQL Analytics

Log Analytics est un service distinct qui doit être configuré. Log Analytics collecte des données de journaux et de télémétrie dans un espace de travail dédié à l’analyse. Un espace de travail constitue une ressource, et tout comme les autres ressources d’Azure, il doit être créé. Bien qu’il ne soit pas nécessaire de créer l’espace de travail dans le même groupe de ressources que les applications qu’il surveille, c’est souvent l’option la plus logique. Pour l’application Wingtip Tickets SaaS Database Per Tenant SaaS, cela permet de supprimer facilement l’espace de travail avec l’application en supprimant le groupe de ressources.

1. Dans **PowerShell ISE**, ouvrez ...\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\*Demo-LogAnalytics.ps1*.
1. Pour exécuter le script, appuyez sur la touche **F5**.

À ce stade, vous devez être en mesure d’ouvrir Log Analytics dans le portail Azure (ou le portail OMS). Il faut compter quelques minutes pour que les données de télémétrie soient collectées dans l’espace de travail Log Analytics et deviennent visibles. Plus vous laisserez de temps au système pour rassembler des données, plus l’expérience sera intéressante. C’est le bon moment pour aller vous chercher quelque chose à boire – vérifiez simplement que le générateur de charge est toujours en cours d’exécution !


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Utiliser Log Analytics et la solution SQL Analytics pour surveiller des pools et des bases de données


Dans cet exercice, ouvrez Log Analytics et le portail OMS pour examiner les données de télémétrie en cours de collecte pour les bases de données et les pools.

1. Accédez au [portail Azure](https://portal.azure.com) et ouvrez Log Analytics en cliquant sur Autres services, puis recherchez Log Analytics :

   ![Ouvrir Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Sélectionnez l’espace de travail nommé *wtploganalytics-&lt;USER&gt;*.

1. Sélectionnez **Vue d’ensemble** pour ouvrir la solution Log Analytics dans le portail Azure.
   ![Lien Vue d’ensemble](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > L’activation de la solution peut prendre quelques minutes. Soyez patient !

1. Cliquez sur la vignette Azure SQL Analytics pour l’ouvrir.

    ![Vue d’ensemble](media/saas-dbpertenant-log-analytics/overview.png)

    ![Analytics](media/saas-dbpertenant-log-analytics/analytics.png)

1. Vous pouvez faire défiler horizontalement la vue dans le panneau de la solution à l’aide de sa propre barre de défilement située en bas (actualisez le panneau si nécessaire).

1. Explorez les différentes vues en cliquant dessus ou sur des ressources individuelles pour ouvrir un explorateur de détails dans lequel vous pouvez utiliser le curseur de temps en haut à gauche ou cliquer sur une barre verticale pour zoomer sur une tranche horaire plus restreinte. Avec cette vue, vous pouvez sélectionner des bases de données ou des pools individuels pour vous focaliser sur des ressources spécifiques :

    ![Graphique](media/saas-dbpertenant-log-analytics/chart.png)

1. Dans le panneau de la solution, si vous faites défiler la vue au maximum vers la droite, vous pouvez voir des requêtes enregistrées que vous pouvez ouvrir et explorer en cliquant dessus. Vous pouvez essayer de les modifier de différentes manières et enregistrer les requêtes intéressantes que vous obtenez. Vous pourrez alors les rouvrir et les utiliser avec d’autres ressources.

1. Dans le panneau de l’espace de travail Log Analytics, sélectionnez Portail OMS pour ouvrir la solution à cet endroit.

    ![OMS](media/saas-dbpertenant-log-analytics/oms.png)

1. Le portail OMS permet de configurer des alertes. Cliquez sur la partie des alertes de la vue des DTU de la base de données.

1. Dans la vue Recherche des journaux qui s’affiche, vous pouvez voir un graphique à barres des mesures représentées.

    ![recherche dans les journaux](media/saas-dbpertenant-log-analytics/log-search.png)

1. Si vous cliquez sur Alerte dans la barre d’outils, vous pouvez voir la configuration des alertes et la modifier.

    ![Ajouter une règle d’alerte](media/saas-dbpertenant-log-analytics/add-alert.png)

La surveillance et les alertes dans Log Analytics et OMS sont basées sur des requêtes portant sur les données collectées dans l’espace de travail, à la différence des alertes dans chaque panneau de ressource, qui sont propres aux ressources. Ainsi, vous pouvez par exemple définir une alerte qui examine toutes les bases de données plutôt que d’en définir une par base de données. Vous pouvez également créer une alerte qui utilise une requête composite portant sur plusieurs types de ressources. Les requêtes sont seulement limitées par les données disponibles dans l’espace de travail.

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
