---
title: Restaurer une base de données SQL Azure dans une application SaaS multilocataire | Microsoft Docs
description: Découvrez comment restaurer une base de données SQL à client unique après la suppression accidentelle des données
keywords: didacticiel sur les bases de données SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 05/10/2017
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 7ae8bcb6172d9f9d56c531e149635434057fc2af
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Restaurer un seul locataire avec une application SaaS de base de données par locataire

Le modèle de base de données par locataire facilite la restauration d’un seul client sur un point antérieur dans le temps sans affecter d’autres locataires.

Dans ce didacticiel, vous allez découvrir deux modèles de récupération des données :

> [!div class="checklist"]

> * Restaurer une base de données dans une base de données parallèle (côte à côte)
> * Restaurer une base de données sur place en remplaçant la base de données existante


|||
|:--|:--|
| **Restaurer dans une base de données parallèle** | Ce modèle peut être utilisé pour la révision, l’audit, la conformité, etc. afin d’autoriser un locataire à inspecter ses données à partir d’un point antérieur.  La base de données actuelle du locataire reste inchangée et en ligne. |
| **Restaurer sur place** | Ce modèle est généralement utilisé pour récupérer un locataire à un point antérieur dans le temps, après la suppression ou la corruption accidentelle des données. La base de données d’origine est mise hors connexion et remplacée par la base de données restaurée. |
|||

Pour suivre ce didacticiel, vérifiez que les prérequis suivants sont remplis :

* L’application SaaS Wingtip est déployée. Pour procéder à un déploiement en moins de cinq minutes, consultez la page [Déployer et explorer une application SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell est installé. Pour plus d’informations, voir [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Présentation des modèles SaaS de restauration d’un locataire

Il existe deux modèles simples pour restaurer les données d’un locataire individuel. Étant donné que les bases de données des clients sont isolées les unes des autres, la restauration d’un client n’a aucun impact sur les données des autres clients.  La fonctionnalité de limite de restauration dans le temps (PITR) de SQL Database est utilisée dans les deux modèles.  PITR crée toujours une nouvelle base de données.   

Dans le premier modèle, **restaurer en parallèle**, une nouvelle base de données parallèle est créée à côté de la base de données actuelle du locataire. Le locataire reçoit l’accès en lecture seule à la base de données restaurée. Les données restaurées peuvent être consultées et potentiellement utilisées pour remplacer les valeurs des données actuelles. Il revient au concepteur d’application de choisir comment le locataire accède à la base de données restaurée et les options de récupération fournies. Autoriser simplement le locataire à consulter ses données à un point antérieur dans le temps peut suffire dans certains scénarios. 

Le second modèle, **restaurer en place**, est utile si les données ont été perdues ou corrompues et si le locataire souhaite restaurer à un point antérieur dans le temps.  Le locataire est mis hors connexion pendant la restauration de la base de données. La base de données d’origine est supprimée et la base de données restaurée est renommée. La chaîne de sauvegarde de la base de données d’origine reste accessible après la suppression, ce qui vous permet de restaurer la base de données à un point antérieur dans le temps, si nécessaire.

Si la base de données utilise la [géoréplication](sql-database-geo-replication-overview.md) et la restauration en parallèle, nous vous recommandons de copier les données requises de la copie restaurée vers la base de données d’origine. Si vous remplacez la base de données d’origine par la base de données restaurée, vous devez reconfigurer et resynchroniser la géoréplication.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenir les scripts de l'application Wingtip Tickets SaaS Database Per Tenant

Les scripts et le code de l’application de base de données multi-locataire SaaS Wingtip Tickets sont disponibles dans le dépôt GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consultez les [conseils généraux](saas-tenancy-wingtip-app-guidance-tips.md) avant de télécharger et de débloquer les scripts Wingtip Tickets SaaS.

## <a name="before-you-start"></a>Avant de commencer

Lorsqu’une base de données est créée, entre 10 et 15 minutes peuvent être nécessaires avant que la première sauvegarde complète soit disponible pour la restauration.  Si vous venez d’installer l’application, vous devrez peut-être attendre quelques minutes avant d’essayer de ce scénario.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simuler la suppression accidentelle des données par le client

Pour illustrer ces scénarios de récupération, commencez par supprimer *accidentellement* un événement dans l’une des bases de données de locataire. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Ouvrir l’application Events pour passer en revue les événements en cours

1. Ouvrez *Events Hub* (http://events.wtp.&lt;user&gt;.trafficmanager.net) et cliquez sur **Contoso Concert Hall** :

   ![events hub](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. Faites défiler la liste des événements, puis notez le dernier événement de la liste :

   ![dernier événement](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>Supprimer « accidentellement » le dernier événement

1. Ouvrez ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\*Demo-RestoreTenant.ps1* dans *PowerShell ISE*, puis définissez la valeur suivante :
   * **$DemoScenario** = **1**, Supprimer le dernier événement (sans ventes de tickets).
1. Appuyez sur **F5** pour exécuter le script et supprimer le dernier événement. Le message de confirmation suivant doit s’afficher :

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. La page des événements de Contoso s’ouvre. Faites défiler vers le bas et vérifiez que l’événement a disparu. Si l’événement figure toujours dans la liste, actualisez la page et vérifiez qu’il a disparu.

   ![dernier événement](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Restaurer une base de données client en parallèle avec la base de données de production

Cet exercice restaure la base de données Contoso Concert Hall à un point dans le temps, avant la suppression de l’événement. Dans ce scénario, nous supposons que vous souhaitez uniquement consulter les données supprimées dans une base de données parallèle.

 Le script *Restore-TenantInParallel.ps1* crée une base de données de locataire parallèle nommée *ContosoConcertHall\_old*, avec une entrée de catalogue parallèle. Ce modèle de restauration convient davantage dans le cas d’une récupération après une perte de données mineure ou si vous avez besoin de consulter les données à des fins de conformité et d’audit. Cette approche est également recommandée lorsque vous utilisez la [géoréplication](sql-database-geo-replication-overview.md).

1. Terminez la section [Simuler une suppression accidentelle de données par le locataire](#simulate-a-tenant-accidentally-deleting-data).
1. Dans *PowerShell ISE*, ouvrez ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\_Demo-RestoreTenant.ps1_.
1. Définissez **$DemoScenario** = **2**, *Restore tenant in parallel* (Restaurer le locataire en parallèle).
1. Appuyez sur **F5** pour exécuter le script.

Le script restaure la base de données de locataire sur un point dans le temps situé avant la suppression de l’événement. La base de données est restaurée vers une nouvelle base de données nommée _ContosoConcertHall\_old_. Les métadonnées de catalogue qui existent dans cette base de données restaurée sont supprimées, puis la base de données est ajoutée au catalogue à l’aide d’une clé créée à partir du nom *ContosoConcertHall\_old*.

Le script de démonstration ouvre la page des événements pour cette nouvelle base de donnés de locataire dans votre navigateur. À noter dans l’URL : ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` les données sont affichées d’après la base de données restaurée et *_old* est ajouté au nom.

Faites défiler les événements répertoriés dans le navigateur pour confirmer que l’événement supprimé dans la section précédente a bien été restauré.

Notez que le fait d’exposer le locataire restauré en tant que locataire supplémentaire (avec sa propre application Events) n’est probablement pas ce que vous feriez pour fournir un accès au locataire aux données restaurées. Néanmoins, cela nous permet d’illustrer le modèle de restauration. En réalité, il vaudrait probablement mieux donner accès en lecture seule aux anciennes données et conserver cette base de données restaurée pendant une période définie. Dans l’exemple, vous pouvez supprimer l’entrée de locataire restauré une fois que vous avez terminé en exécutant le scénario _Remove restored tenant_ (Supprimer le locataire restauré).

1. Définissez **$DemoScenario** = **4**, *Remove restored tenant* (Supprimer le locataire restauré)
1. **Appuyez****sur****F5** pour exécuter le script.
1. L’entrée *ContosoConcertHall\_old* est maintenant supprimée du catalogue. Poursuivez et fermez la page des événements pour ce client dans votre navigateur.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Restaurer un client sur place en remplaçant la base de données client existante

Cet exercice restaure le locataire Contoso Concert Hall à un point dans le temps, avant la suppression de l’événement. Le script *Restore-TenantInPlace* restaure une base de données de locataire sur une nouvelle base de données et supprime l’original.  Ce modèle de restauration convient davantage dans le cas d’une récupération après une grave corruption des données, dans la mesure où le locataire peut faire face à une perte importante de données.

1. Ouvrez le fichier **Demo-RestoreTenant.ps1** dans PowerShell ISE.
1. Définissez **$DemoScenario** = **5**, *Restore tenant in place* (Restaurer le locataire en place).
1. Appuyez sur **F5** pour exécuter le script.

Le script restaure la base de données de locataire sur un point dans le temps avant la suppression de l’événement. Il déconnecte tout d’abord le locataire Contoso Concert Hall afin d’empêcher d’autres mises à jour. Ensuite, une base de données parallèle est créée en restaurant à partir du point de restauration.  La base de données restaurée est nommé avec un horodatage pour garantir que le nom de la base de données n’entre pas en conflit avec le nom de base de données de locataire existante. Ensuite, l’ancienne base de données client est supprimée, et la base de données restaurée est renommée d’après le nom de la base de données d’origine. Enfin, la connexion de Contoso Concert Hall est rétablie pour permettre l’accès de l’application à la base de données restaurée.

Vous avez correctement restauré la base de données à un point dans le temps, avant la suppression de l’événement. La page Events s’ouvre. Confirmez alors que le dernier événement a été restauré.

Notez qu’une fois que vous restaurez la base de données, 10 à 15 minutes supplémentaires sont nécessaires avant que la première sauvegarde complète soit à nouveau disponible pour la restauration. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]

> * Restaurer une base de données dans une base de données parallèle (côte à côte)
> * Restaurer une base de données sur place

[Gérer le schéma de base de données client](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Autres didacticiels reposant sur l’application SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Vue d’ensemble de la continuité de l’activité avec la base de données Azure SQL](sql-database-business-continuity.md)
* [En savoir plus sur les sauvegardes SQL Database](sql-database-automated-backups.md)
