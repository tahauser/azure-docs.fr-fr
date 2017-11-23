---
title: "Exécuter des requêtes de rapport ad hoc sur plusieurs bases de données SQL Azure | Microsoft Docs"
description: "Exécutez des requêtes de rapport ad hoc sur plusieurs bases de données SQL dans un exemple d’application multiclient."
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
ms.topic: articles
ms.date: 11/13/2017
ms.author: billgib; sstein; AyoOlubeko
ms.openlocfilehash: db8a079c76f38bbf7b90f8d914ce1bbf192343d7
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Exécuter des requêtes d’analyse ad hoc sur plusieurs bases de données SQL Azure

Dans ce didacticiel, vous allez exécuter des requêtes distribuées sur l’ensemble des bases de données client pour permettre un rapport ad hoc. Ces requêtes peuvent extraire des analyses enfouies dans les données opérationnelles quotidiennes de l’application SaaS Wingtip Tickets. Pour cela, vous allez déployer une base de données analytique supplémentaire sur le serveur de catalogue et utiliser une requête élastique pour activer les requêtes distribuées.


Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Explorer les vues globales de chaque base de données pour permettre des requêtes efficaces sur l’ensemble des locataires
> * Guide de déploiement d’une base de données de création de rapports ad hoc
> * Exécuter des requêtes distribuées sur toutes les bases de données client



Pour suivre ce tutoriel, vérifiez que les conditions préalables suivantes sont bien satisfaites :


* L’application Wingtip Tickets SaaS Database Per Tenant est déployée. Pour un déploiement en moins de cinq minutes, consultez [Déployer et explorer l’application Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* SQL Server Management Studio (SSMS) est installé. Pour télécharger et installer SSMS, consultez la rubrique [Téléchargement de SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Modèle de création de rapports ad hoc

![modèle de création de rapports ad hoc](media/saas-tenancy-adhoc-analytics/adhocreportingpattern_dbpertenant.png)

Une des grandes opportunités avec les applications SaaS est l’exploitation de la vaste quantité de données partagées stockées de manière centralisée dans le cloud pour obtenir des insights sur le fonctionnement et l’utilisation de votre application. Ces analyses peuvent guider le développement des fonctionnalités, les améliorations de convivialité et les autres investissements dans vos applications et services.

L’accès à ces données dans une base de données mutualisée est facile, mais pas si simple lors d’une distribution à grande échelle sur des milliers de bases de données. Une approche consiste à utiliser une [requête élastique](sql-database-elastic-query-overview.md), permettant d’interroger un ensemble distribué de bases de données avec un schéma commun. Ces bases de données peuvent être distribuées sur différents abonnements et groupes de ressources, mais doivent partager la même connexion. Les requêtes élastiques utilisent une seule base de données *principale* dans laquelle sont définies des tables externes qui reflètent les tables ou les vues dans les bases de données distribuées (client). Les requêtes envoyées à cette base de données principale sont compilées pour produire un plan de requête distribué, avec des parties de la requête transmises aux bases de données client en fonction des besoins. Les requêtes élastiques utilisent la carte de partitions dans la base de données de catalogue pour déterminer l’emplacement de toutes les bases de données client. La configuration et les requêtes sont simples grâce à l’utilisation de [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) standard, et les requêtes ad hoc sont compatibles avec des outils tels que Power BI et Excel.

En distribuant les requêtes sur toutes les bases de données client, les requêtes élastiques permettent d’obtenir immédiatement des informations pour les transformer en données de production actives. Toutefois, étant donné qu’une requête élastique peut extraire des données provenant potentiellement de nombreuses bases de données, la latence de requête peut parfois être supérieure à celle observée pour des requêtes équivalentes soumises à une seule base de données mutualisée. Concevez des requêtes qui permettent de réduire le nombre de données retournées. Une requête élastique est souvent plus adaptée lorsqu’il s’agit d’interroger de petites quantités de données en temps réel. En revanche, ce n’est pas le cas pour la construction de requêtes ou de rapports d’analyse fréquemment utilisés ou complexes. Si les requêtes ne sont pas assez efficaces, consultez le [plan d’exécution](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) pour voir quelle partie de la requête a été repoussée vers la base de données distante et le nombre de résultats renvoyés. Les requêtes nécessitant un traitement d’analyse complexe renvoient parfois de meilleurs résultats si les données client sont extraites dans une base de données dédiée ou un entrepôt de données optimisé pour les requêtes d’analyse. Ce modèle est expliqué dans la [didacticiel sur l’analyse des clients](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenir les scripts de l'application Wingtip Tickets SaaS Database Per Tenant

Les scripts et le code source de l’application de base de données Wingtip Tickets SaaS par client sont disponibles dans le [référentiel github WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Assurez-vous de suivre les étapes de déblocage décrites dans le fichier Lisezmoi.

## <a name="create-ticket-sales-data"></a>Créer des données sur des ventes de tickets

Pour exécuter des requêtes sur un jeu de données plus concret, créez des données de ventes de tickets en exécutant le générateur de tickets.

1. Dans *PowerShell ISE*, ouvrez le script... \\Modules d’apprentissage\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1* et définissez les valeurs suivantes :
   * **$DemoScenario** = 1, **Acheter des tickets pour des événements dans tous les lieux**.
2. Appuyez sur **F5** pour exécuter le script et générer des ventes de tickets. Pendant l’exécution du script, poursuivez les étapes de ce didacticiel. Les données de ticket font l’objet d’une requête dans la section *Exécuter des requêtes distribuées ad hoc*. Vous devez donc attendre que le générateur de tickets ait terminé.

## <a name="explore-the-global-views"></a>Explorer les vues globales

Dans l’application Wingtip Tickets SaaS Database Per Tenant, chaque client est associé à une base de données. Ainsi, les données figurant dans les tables de base de données se limitent à la perspective d’un seul client. Toutefois, lorsque l’ensemble des bases de données sont interrogées, il est important que la requête élastique puisse traiter les données comme si elles faisaient partie d’une seule base de données logique partitionnée par le client. 

Pour simuler ce modèle, un ensemble de vues globales est ajouté à la base de données locataire. Ces vues projettent alors un ID de locataire dans chacune des tables interrogées globalement. Par exemple, la vue *VenueEvents* ajoute un élément *VenueId* calculé dans les colonnes projetées à partir de la table *Events*. De même, les vues *VenueTicketPurchases* et *VenueTickets* ajoutent une colonne *VenueId* projetée à partir de leurs tables respectives. Ces affichages sont utilisés par une requête élastique pour paralléliser les requêtes et les transmettre à la bonne base de données client distante lorsqu'une colonne *VenueId* est présente. Cela réduit considérablement la quantité de données renvoyées et augmente nettement les performances pour de nombreuses requêtes. Ces vues globales ont été créées au préalable dans toutes les bases de données client.

1. Ouvrez SSMS et [connectez-vous au serveur tenants1-&lt;USER&gt;](saas-dbpertenant-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
2. Développez **Bases de données**, cliquez avec le bouton droit sur **contosoconcerthall**, puis sélectionnez **Nouvelle requête**.
3. Exécutez les requêtes suivantes pour découvrir la différence entre les tables de client unique et les vues globales :

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Dans ces vues, l’élément *VenueId* est calculé en tant que hachage du nom de lieu, mais toute autre approche peut être utilisée pour introduire une valeur unique. Cette approche est semblable à la façon dont la clé de client est calculée pour être utilisée dans le catalogue.

Pour examiner la définition de la vue *Venues* :

1. Dans **Explorateur d’objets**, développez **contosoconcethall** > **Vues** :

   ![vues](media/saas-tenancy-adhoc-analytics/views.png)

2. Cliquez avec le bouton droit sur **dbo.Venues**.
3. Sélectionnez **Générer un script de la vue en tant que** > **CRÉER vers** > **Nouvelle fenêtre d’éditeur de requête**

Script d’une autre vue *Venue* illustrant la méthode d’ajout de l’élément *VenueId*.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Déployer la base de données utilisée pour les requêtes distribuées ad hoc

Cet exercice déploie la base de données *adhocreporting*. Cette base de données principale contient le schéma utilisé pour interroger toutes les bases de données locataires. La base de données est déployée sur le serveur de catalogue existant, qui est le serveur utilisé pour toutes les bases de données liées à la gestion dans l’exemple d’application.

1. Ouvrez \\Modules d’apprentissage\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReporting.ps1* dans *PowerShell ISE* et définissez les valeurs suivantes :
   * **$DemoScenario** = 2, **Déployer la base de données d’analyse ad hoc**.

2. Appuyez sur **F5** pour exécuter le script et créer la base de données *adhocreporting*.

Dans la section suivante, vous allez ajouter un schéma à la base de données, permettant ainsi l’exécution de requêtes distribuées.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Configurer la base de données 'head' pour l’exécution de requêtes distribuées

Cet exercice ajoute le schéma (la source de données externe et les définitions de la table externe) à la base de données d’analyse ad hoc qui permet l’interrogation de toutes les bases de données client.

1. Ouvrez SQL Server Management Studio et connectez-vous à la base de données de rapport ad hoc créée à l’étape précédente. Le nom de la base de données est *adhocreporting*.
2. Ouvrez ...\Modules d’apprentissage\Operational Analytics\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* dans SSMS.
3. Passez en revue le script SQL et notez les points suivants :

   La requête élastique utilise des informations d’identification de niveau base de données pour accéder à chacune des bases de données client. Ces informations d’identification doivent être disponibles dans toutes les bases de données et doivent normalement bénéficier des droits minimaux nécessaires pour activer ces requêtes ad-hoc.

    ![créer des informations d’identification](media/saas-tenancy-adhoc-analytics/create-credential.png)

   La source de données externe, définie pour utiliser la carte de partitions client dans la base de données de catalogue. En l’utilisant comme source de données externe, les requêtes sont distribuées sur toutes les bases de données inscrites dans le catalogue au moment de l’exécution de la requête. Étant donné que les noms de serveur sont différents pour chaque déploiement, ce script d’initialisation obtient l’emplacement de la base de données du catalogue en récupérant le serveur actuel (@@servername) dans lequel le script est exécuté.

    ![créer une source de données externe](media/saas-tenancy-adhoc-analytics/create-external-data-source.png)

   Les tables externes qui font référence aux vues globales décrites dans la section précédente et définies avec **DISTRIBUTION = SHARDED(VenueId)**. Étant donné que chaque élément *VenueId* correspond à une base de données unique, cela améliore les performances dans de nombreux scénarios, comme illustré dans la section suivante.

    ![créer des tables externes](media/saas-tenancy-adhoc-analytics/external-tables.png)

   La table locale *VenueTypes* qui est créée et alimentée. Cette table de données de référence étant courante dans toutes les bases de données client, elle peut être représentée ici comme une table locale et renseignée avec les données courantes. Pour certaines requêtes, cela peut réduire la quantité de données déplacées entre les bases de données client et la base de données *adhocreporting*.

    ![créer une table](media/saas-tenancy-adhoc-analytics/create-table.png)

   Si vous incluez des tables de référence de cette manière, veillez à mettre à jour le schéma et les données de la table lorsque vous mettez à jour les bases de données client.

4. Appuyez sur **F5** pour exécuter le script et initialiser la base de données *adhocreporting*. 

Vous pouvez à présent exécuter des requêtes distribuées et collecter des informations sur l’ensemble des clients !

## <a name="run-ad-hoc-distributed-queries"></a>Exécuter des requêtes distribuées ad hoc

Maintenant que la base de données *adhocreporting* est configurée, lancez-vous et exécutez des requêtes ad hoc. Intégrez le plan d’exécution pour mieux appréhender le processus de requête. 

Lors de l’inspection du plan d’exécution, passez la souris sur les icônes de plan pour plus d’informations. 

Remarque importante : lorsque nous avons défini la source de données externe, le paramètre **DISTRIBUTION = SHARDED(VenueId)** améliore les performances dans de nombreux scénarios. Étant donné que chaque élément *VenueId* correspond à une base de données unique, le filtrage peut être effectué à distance facilement, renvoyant uniquement les données nécessaires.

1. Ouvrir... \\Modules d’apprentissage\\Operational Analytics\\Adhoc Reporting\\*Demo-AdhocReportingQueries.sql* dans SSMS.
2. Assurez-vous que vous êtes connecté à la base de données **adhocreporting**.
3. Sélectionnez le menu **Requête** et cliquez sur **Inclure le plan d’exécution réel**
4. Mettez en surbrillance la requête *Quels lieux sont actuellement inscrits ?*, puis appuyez sur **F5**.

   La requête renvoie la liste complète des lieux, montrant à quel point il est facile d’interroger l’ensemble des clients et de renvoyer des données provenant de chacun d’eux.

   Inspectez le plan ; vous constaterez que la totalité des coûts correspond à la requête distante, car nous interrogeons simplement chaque base de données client et sélectionnons les informations relatives aux lieux.

   ![SELECT * FROM dbo.Venues](media/saas-tenancy-adhoc-analytics/query1-plan.png)

5. Sélectionnez la prochaine requête, puis appuyez sur **F5**.

   Cette requête joint les données de bases de données client et la table *VenueTypes* table (elle est locale dans la mesure où elle figure dans la base de données *adhocreporting*).

   Inspectez le plan ; vous constaterez que la majorité des coûts correspond à la requête distante, car nous interrogeons les informations relatives aux lieux de chaque client (dob.Venues), puis effectuons une jointure locale rapide avec la table *VenueTypes* pour afficher le nom convivial.

   ![Joindre des données locales et distantes](media/saas-tenancy-adhoc-analytics/query2-plan.png)

6. Sélectionnez à présent la requête *Quel jour y a-t-il eu le plus de tickets vendus ?*, puis appuyez sur **F5**.

   Cette requête effectue une opération de jointure et d’agrégation un peu plus complexe. Il est important de noter que la plupart du traitement est effectué à distance, et une fois encore, nous récupérons uniquement les lignes dont nous avons besoin, en renvoyant une seule ligne pour le cumul agrégé de ventes de tickets par jour pour chaque lieu.

   ![query](media/saas-tenancy-adhoc-analytics/query3-plan.png)


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Exécuter des requêtes distribuées sur toutes les bases de données client
> * Déployer une base de données de rapport ad hoc et y ajouter un schéma pour exécuter des requêtes distribuées.


Essayez maintenant le [Didacticiel sur l’analyse des clients](saas-tenancy-tenant-analytics.md) pour explorer l’extraction de données dans une base de données d’analyse distincte pour un traitement d’analyse plus complexe...

## <a name="additional-resources"></a>Ressources supplémentaires

* Autres [didacticiels reposant sur l’application Wingtip Tickets SaaS Database per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Requête élastique](sql-database-elastic-query-overview.md)
