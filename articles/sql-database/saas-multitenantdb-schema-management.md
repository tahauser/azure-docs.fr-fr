---
title: "Gérer le schéma Azure SQL Database dans une application mutualisée | Microsoft Docs"
description: "Gérer un schéma pour plusieurs locataires dans une application mutualisée qui utilise Azure SQL Database"
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
ms.date: 11/14/2017
ms.author: billgib
ms.openlocfilehash: e4b8e38d20ec408869f2228597afdf2f9620515b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Gérer un schéma pour plusieurs locataires dans une application mutualisée qui utilise Azure SQL Database

Le [premier didacticiel de base de données Wingtip Tickets SaaS mutualisée](saas-multitenantdb-get-started-deploy.md) montre comment l’application peut approvisionner une base de données mutualisée partitionnée et l’enregistrer dans le catalogue. Comme n’importe quelle application, l’application SaaS Wingtip Tickets évolue au fil du temps et nécessite parfois l’apport de modifications à la base de données. Ces modifications peuvent inclure un schéma nouveau ou modifié, des données de référence nouvelles ou modifiées et des tâches de maintenance de routine de la base de données pour garantir des performances optimales de l’application. Avec une application SaaS, ces modifications doivent être déployées de façon coordonnée dans un parc potentiellement immense de bases de données de locataire. Ces modifications doivent être incorporées au processus d’approvisionnement pour apparaître dans les futures bases de données client.

Ce didacticiel explore deux scénarios : le déploiement de mises à jour des données de référence pour tous les locataires et la reconstruction d’un index sur la table contenant les données de référence. La fonctionnalité de [travaux élastiques](sql-database-elastic-jobs-overview.md) est utilisée pour exécuter ces opérations sur l’ensemble des clients et sur la base de données de client *principale* qui sert de modèle pour les nouvelles bases de données.

Ce didacticiel vous explique comment effectuer les opérations suivantes :

> [!div class="checklist"]

> * Créer un compte de travail
> * Interroger plusieurs clients
> * Mettre à jour les données dans toutes les bases de données de locataire
> * Créer un index sur une table dans toutes les bases de données de locataire


Pour suivre ce didacticiel, vérifiez que les conditions préalables ci-dessous sont bien satisfaites :

* L’application de base de données Wingtip Tickets SaaS mutualisée est déployée. Pour procéder à un déploiement en moins de cinq minutes, consultez [Déployer et explorer l’application de base de données multi-locataire SaaS Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* La dernière version de SQL Server Management Studio (SSMS) est installée. [Télécharger et installer SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Ce didacticiel utilise des fonctionnalités du service SQL Database en préversion limitée (travaux de base de données élastiques). Si vous souhaitez réaliser ce didacticiel, envoyez votre ID d’abonnement à SaaSFeedback@microsoft.com avec l’objet Préversion des travaux élastiques. Une fois que vous avez reçu la confirmation que votre abonnement a été activé, [téléchargez et installez les dernières applets de commande pour les travaux en version préliminaire](https://github.com/jaredmoo/azure-powershell/releases). Cette version préliminaire est limitée. Contactez SaaSFeedback@microsoft.com pour toute question ou demande de prise en charge associées.


## <a name="introduction-to-saas-schema-management-patterns"></a>Présentation des modèles de gestion de schéma SaaS

Le modèle de base de données partitionnée mutualisée utilisé dans cet exemple permet de à une base de données de clients de contenir un nombre illimité de clients. Cet exemple présente la possibilité d’utiliser une combinaison de bases de données mutualisées et à propriétaire unique, créant ainsi un modèle de gestion de client « hybride ». La maintenance et la gestion de ces bases de données sont complexes. Les [travaux élastiques](sql-database-elastic-jobs-overview.md) facilitent l’administration et la gestion de la couche de données SQL. Les travaux vous permettent d’exécuter de manière sécurisée et fiable des tâches (scripts T-SQL) indépendantes des interactions ou des entrées des utilisateurs sur un groupe de bases de données. Cette méthode peut être utilisée pour déployer les modifications de schéma et de données de référence commune sur tous les locataires d’une application. Les travaux élastiques permettent également de maintenir à jour une copie *principale* de la base de données utilisée pour créer de nouveaux locataires afin de s’assurer qu’elle contient en permanence le schéma et les données de référence les plus récents.

![Écran](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Préversion limitée des travaux élastiques

Il existe une nouvelle version des travaux élastiques qui est désormais une fonctionnalité intégrée d’Azure SQL Database (qui ne nécessite pas de services ou de composants supplémentaires). Cette nouvelle version des travaux élastiques est pour le moment en préversion limitée. Cette préversion limitée prend actuellement en charge PowerShell pour créer des comptes de travail et T-SQL pour créer et gérer des travaux.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obtenir les scripts et le code source de l’application de base de données multi-locataire SaaS Wingtip Tickets

Les scripts et le code de l’application de base de données multi-locataire SaaS Wingtip Tickets sont disponibles dans le dépôt GitHub [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB). Consultez les [conseils généraux](saas-tenancy-wingtip-app-guidance-tips.md) avant de télécharger et de débloquer les scripts Wingtip Tickets SaaS. 

## <a name="create-a-job-account-database-and-new-job-account"></a>Créer une base de données de compte de travail et un compte de travail

Ce didacticiel nécessite l’utilisation de PowerShell pour créer la base de données de compte de travail et le compte de travail. Comme MSDB et SQL Agent, les travaux élastiques s’appuient sur une base de données SQL Azure pour stocker les définitions, l’état et l’historique des travaux. Une fois le compte de travail créé, vous pouvez immédiatement créer et surveiller des travaux.

1. Ouvrez *…\\Learning Modules\\Schema Management\\Demo-SchemaManagement.ps1* dans **PowerShell ISE**.
1. Appuyez sur **F5** pour exécuter le script.

Le script *Demo-SchemaManagement.ps1* appelle le script *Deploy-SchemaManagement.ps1* pour créer une base de données *S2* nommée **jobaccount** sur le serveur de catalogue. Il crée ensuite le compte de travail en transmettant la base de données jobaccount en tant que paramètre à l’appel de création du compte de travail.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Créer un travail pour déployer les nouvelles données de référence sur tous les locataires

Chaque base de données de locataire inclut un ensemble de types de lieux dans la table **VenueTypes** qui définissent le type d’événements qui se déroulent dans chacun de ces lieux. Dans cet exercice, vous allez déployer une mise à jour dans toutes les bases de données afin d’ajouter deux types de lieux supplémentaires : *Motorcycle Racing* (Courses de moto) *Swimming Club* (Club de natation). Ces types de lieux correspondent à l’image d’arrière-plan visible dans l’application que s’affiche dans l’application Events.

Cliquez sur le menu déroulant Venue Type (Type de lieu) et vérifiez que 10 options seulement sont disponibles, et en particulier que la liste ne contient pas les options « Motorcycle Racing » et « Swimming Club ».

Maintenant, nous allons créer un travail pour mettre à jour la table **VenueTypes** dans toutes les bases de données de locataire et ajouter les nouveaux types de lieux.

Pour créer un travail, nous utilisons un ensemble de procédures stockées système dédiées aux travaux créées dans la base de données jobaccount lors de la création du compte de travail.

1. Dans SSMS, connectez-vous également au serveur de locataire tenants1-mt-\<user\>.database.windows.net
2. Accédez à la base de données *tenants1* sur le serveur *tenants1-mt-\<user\>.database.windows.net* et interrogez la table *VenueTypes* pour vérifier que *Motorcycle Racing* et *Swimming Club* qui ne figurent **pas** dans la liste des résultats.
3. Connectez-vous au serveur de catalogue catalog-mt-\<user\>.database.windows.net
4. Connectez-vous à la base de données jobaccount dans le serveur de catalogue.
5. Dans SSMS, ouvrez le fichier ...\\Learning Modules\\Schema Management\\DeployReferenceData.sql
6. Modifiez l’instruction : set @User = &lt;utilisateur&gt; et remplacer la valeur de l’utilisateur utilisée lors du déploiement de l’application de base de données Wingtip Tickets SaaS mutualisée.
7. Appuyez sur **F5** pour exécuter le script.

Observez les événements suivants dans le script *DeployReferenceData.sql* :
* **sp\_add\_target\_group** crée le nom de groupe cible DemoServerGroup. Nous ajoutons ajouter des membres cibles au groupe.
* **sp\_add\_target\_group\_member** ajoute un type de membre cible *server*, qui juge toutes les bases de données dans ce serveur (notez qu’il s’agit du serveur tenants1-mt-&lt;user&gt; contenant les bases de données client) au moment de l’exécution du travail devant être incluses dans le travail ; un type de membre cible *database*, pour la base de données « or » (basetenantdb) qui réside sur le serveur catalog-mt-&lt;user&gt; et enfin un type de membre *database* permettant d’inclure la base de données adhocreporting utilisée dans un prochain didacticiel.
* **sp\_add\_job** crée une tâche appelée « Reference Data Deployment » (Déploiement des données de référence).
* **sp\_add\_jobstep** crée l’étape du travail contenant le texte de la commande T-SQL pour mettre à jour la table de référence, VenueTypes.
* Les autres vues dans le script indiquent l’existence des objets et contrôlent l’exécution du travail. Utilisez ces requêtes pour passer en revue la valeur d’état dans la colonne **cycle de vie** afin de déterminer le moment où la tâche a été terminée avec succès sur les bases de données client et deux autres bases de données contenant la table de référence.

Dans SSMS, accédez à la base de données sur le serveur *tenants1-mt-&lt;user&gt;* et interrogez la table *VenueTypes* pour vérifier que *Motorcycle Racing* et *Swimming Club* sont maintenant **ajoutés* à la table.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Créer une tâche pour gérer l’index de la table de référence

De façon similaire à l’exercice précédent, cet exercice crée un travail pour reconstruire l’index sur la clé primaire de la table de référence, une opération de gestion de base de données classique qu’un administrateur peut être amené à exécuter après le chargement d’un grand volume de données dans une table.


1. Dans SSMS, connectez-vous à la base de données jobaccount sur le serveur catalog-mt-&lt;Utilisateur&gt;.database.windows.net.
2. Dans SSMS, ouvrez le fichier ...\\Learning Modules\\Schema Management\\OnlineReindex.sql.
3. Appuyez sur **F5** pour exécuter le script.

Observez les événements suivants dans le script *OnlineReindex.sql* :
* sp**sp\_add\_job** crée un travail appelé « Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885 ».
* **sp\_add\_jobstep** crée l’étape du travail contenant le texte de la commande T-SQL pour mettre à jour l’index.
* Les vues restantes dans le script surveillent l’exécution du travail. Utilisez ces requêtes pour passer en revue la valeur d’état dans la colonne **cycle de vie** afin de déterminer le moment où la tâche a été terminée avec succès sur tous les membres du groupe cible.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Créer un compte de travail élastique pour interroger plusieurs locataires
> * Mettre à jour les données dans toutes les bases de données de locataire
> * Créer un index sur une table dans toutes les bases de données de locataire

Suivez maintenant le [didacticiel de génération de rapports ad-hoc](saas-multitenantdb-adhoc-reporting.md).


## <a name="additional-resources"></a>Ressources supplémentaires

<!--* Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Gestion des bases de données cloud avec montée en charge](sql-database-elastic-jobs-overview.md)
* [Créer et gérer des bases de données cloud avec montée en charge](sql-database-elastic-jobs-create-and-manage.md)
