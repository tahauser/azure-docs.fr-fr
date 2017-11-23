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
ms.date: 07/28/2017
ms.author: billgib; sstein
ms.openlocfilehash: 701a7296368cd8150eedf8cc50b989fdf6112101
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Gérer un schéma pour plusieurs locataires dans une application mutualisée qui utilise Azure SQL Database

Le [premier didacticiel Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md) montre comment l’application peut provisionner une base de données mutualisée et l’enregistrer dans le catalogue. Comme n’importe quelle application, l’application Wingtip Tickets SaaS Database Per Tenant évolue au fil du temps et nécessite parfois l’apport de modifications à la base de données. Ces modifications peuvent inclure un schéma nouveau ou modifié, des données de référence nouvelles ou modifiées et des tâches de maintenance de routine de la base de données pour garantir des performances optimales de l’application. Avec une application SaaS, ces modifications doivent être déployées de façon coordonnée dans un parc potentiellement immense de bases de données de locataire. Ces modifications doivent être incorporées au processus d’approvisionnement pour apparaître dans les futures bases de données client.

Ce didacticiel explore deux scénarios : le déploiement de mises à jour des données de référence pour tous les locataires et la reconstruction d’un index sur la table contenant les données de référence. La fonctionnalité de [travaux élastiques](sql-database-elastic-jobs-overview.md) est utilisée pour exécuter ces opérations sur l’ensemble des clients et sur la base de données de client *principale* qui sert de modèle pour les nouvelles bases de données.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]

> * Créer un compte de travail
> * Interroger plusieurs clients
> * Mettre à jour les données dans toutes les bases de données de locataire
> * Créer un index sur une table dans toutes les bases de données de locataire


Pour suivre ce didacticiel, vérifiez que les conditions préalables ci-dessous sont bien satisfaites :

* L’application Wingtip Tickets SaaS Database Per Tenant est déployée. Pour un déploiement en moins de cinq minutes, consultez [Déployer et explorer l’application Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* La dernière version de SQL Server Management Studio (SSMS) est installée. [Télécharger et installer SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

*Ce didacticiel utilise des fonctionnalités du service SQL Database en préversion limitée (travaux de base de données élastiques). Si vous souhaitez réaliser ce didacticiel, envoyez votre ID d’abonnement à SaaSFeedback@microsoft.com avec l’objet Préversion des travaux élastiques. Une fois que vous avez reçu la confirmation que votre abonnement a été activé, [téléchargez et installez les dernières applets de commande pour les travaux en version préliminaire](https://github.com/jaredmoo/azure-powershell/releases). Cette version préliminaire est limitée. Contactez SaaSFeedback@microsoft.com pour toute question ou demande de prise en charge associées.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Présentation des modèles de gestion de schéma SaaS

Le modèle SaaS de base de données à un seul locataire bénéficie à de nombreux égards de l’isolation des données qui en résulte, mais il s’accompagne également de la complexité associée à la maintenance et à la gestion d’un grand nombre de bases de données. Les [travaux élastiques](sql-database-elastic-jobs-overview.md) facilitent l’administration et la gestion de la couche de données SQL. Les travaux vous permettent d’exécuter de manière sécurisée et fiable des tâches (scripts T-SQL) indépendantes des interactions ou des entrées des utilisateurs sur un groupe de bases de données. Cette méthode peut être utilisée pour déployer les modifications de schéma et de données de référence commune sur tous les locataires d’une application. Les travaux élastiques permettent également de maintenir à jour une copie *principale* de la base de données utilisée pour créer de nouveaux locataires afin de s’assurer qu’elle contient en permanence le schéma et les données de référence les plus récents.

![Écran](media/saas-tenancy-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>Préversion limitée des travaux élastiques

Il existe une nouvelle version des travaux élastiques qui est désormais une fonctionnalité intégrée d’Azure SQL Database (qui ne nécessite pas de services ou de composants supplémentaires). Cette nouvelle version des travaux élastiques est pour le moment en préversion limitée. Cette préversion limitée prend actuellement en charge PowerShell pour créer des comptes de travail et T-SQL pour créer et gérer des travaux.

> [!NOTE]
> *Ce didacticiel utilise des fonctionnalités du service SQL Database en préversion limitée (travaux de base de données élastiques). Si vous souhaitez réaliser ce didacticiel, envoyez votre ID d’abonnement à SaaSFeedback@microsoft.com avec l’objet Préversion des travaux élastiques. Une fois que vous avez reçu la confirmation que votre abonnement a été activé, [téléchargez et installez les dernières applets de commande pour les travaux en version préliminaire](https://github.com/jaredmoo/azure-powershell/releases). Cette version préliminaire est limitée. Contactez SaaSFeedback@microsoft.com pour toute question ou demande de prise en charge associées.*

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenir les scripts de l'application Wingtip Tickets SaaS Database Per Tenant

Les scripts et le code source de l’application de base de données Wingtip Tickets SaaS par client sont disponibles dans le référentiel github [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). [Étapes à suivre pour télécharger les scripts de l’application de base de données Wingtip Tickets SaaS par client](saas-dbpertenant-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-tickets-saas-database-per-tenant-scripts).

## <a name="create-a-job-account-database-and-new-job-account"></a>Créer une base de données de compte de travail et un compte de travail

Ce didacticiel nécessite l’utilisation de PowerShell pour créer la base de données de compte de travail et le compte de travail. Comme MSDB et SQL Agent, les travaux élastiques s’appuient sur une base de données SQL Azure pour stocker les définitions, l’état et l’historique des travaux. Une fois le compte de travail créé, vous pouvez immédiatement créer et surveiller des travaux.

1. **Dans PowerShell ISE**, ouvrez …\\Learning Modules\\Schema Management\\*Demo-SchemaManagement.ps1*.
1. Appuyez sur **F5** pour exécuter le script.

Le script *Demo-SchemaManagement.ps1* appelle le script *Deploy-SchemaManagement.ps1* pour créer une base de données *S2* nommée **jobaccount** sur le serveur de catalogue. Il crée ensuite le compte de travail en transmettant la base de données jobaccount en tant que paramètre à l’appel de création du compte de travail.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Créer un travail pour déployer les nouvelles données de référence sur tous les locataires

Chaque base de données de locataire inclut un ensemble de types de lieux qui définissent le type d’événements qui se déroulent dans chacun de ces lieux. Dans cet exercice, vous allez déployer une mise à jour dans toutes les bases de données de locataire afin d’ajouter deux types de lieux supplémentaires : *Motorcycle Racing* (Courses de moto) *Swimming Club* (Club de natation). Ces types de lieux correspondent à l’image d’arrière-plan visible dans l’application que s’affiche dans l’application Events.

Cliquez sur le menu déroulant Venue Type (Type de lieu) et vérifiez que 10 options seulement sont disponibles, et en particulier que la liste ne contient pas les options « Motorcycle Racing » et « Swimming Club ».

Maintenant, nous allons créer un travail pour mettre à jour la table *VenueTypes* dans toutes les bases de données de locataire et ajouter les nouveaux types de lieux.

Pour créer un travail, nous utilisons un ensemble de procédures stockées système dédiées aux travaux créées dans la base de données jobaccount lors de la création du compte de travail.

1. Ouvrez SSMS et connectez-vous au serveur de catalogue catalog-dpt-\<user\>.database.windows.net
1. Connectez-vous également au serveur de locataire tenants1-dpt-\<user\>.database.windows.net
1. Accédez à la base de données *contosoconcerthall* sur le serveur *tenants1-dpt-\<user\>* et interrogez la table *VenueTypes* pour vérifier que *Motorcycle Racing* et *Swimming Club* **ne figurent pas** dans la liste des résultats.
1. Dans SSMS, ouvrez le fichier ...\\Learning Modules\\Schema Management\\DeployReferenceData.sql
1. Modifiez l’instruction : SET @wtpUser = &lt;utilisateur&gt; et remplacer la valeur de l’utilisateur utilisée lors du déploiement de l’application Wingtip Tickets SaaS Database Per Tenant
1. Assurez-vous que vous êtes connecté à la base de données jobaccount, puis appuyez sur **F5** pour exécuter le script.

* **sp\_add\_target\_group** crée le nom de groupe cible DemoServerGroup. Nous devons à présent ajouter des membres cibles.
* **sp\_add\_target\_group\_member** ajoute un type de membre cible *server*, qui juge toutes les bases de données dans ce serveur (notez qu’il s’agit du serveur tenants1-dpt-&lt;User&gt; contenant les bases de données client) au moment de l’exécution du travail devant être incluses dans le travail ; la deuxième ajoute un type de membre cible *database*, plus précisément la base de données « or » (basetenantdb) qui réside sur le serveur catalog-dpt-&lt;Utilisateur&gt; et enfin un autre type de membre de groupe *database* permettant d’inclure la base de données adhocanalytics utilisée dans un prochain didacticiel.
* **sp\_add\_job** crée une tâche appelée « Reference Data Deployment » (Déploiement des données de référence).
* **sp\_add\_jobstep** crée l’étape du travail contenant le texte de la commande T-SQL pour mettre à jour la table de référence, VenueTypes.
* Les autres vues dans le script indiquent l’existence des objets et contrôlent l’exécution du travail. Utilisez ces requêtes pour passer en revue la valeur d’état dans la colonne **cycle de vie** afin de déterminer le moment où la tâche a été terminée avec succès sur toutes les bases de données client et deux autres bases de données contenant la table de référence.

1. Dans SSMS, accédez à la base de données *contosoconcerthall* sur le serveur *tenants1-dpt-\<user\>* et interrogez la table *VenueTypes* pour vérifier que *Motorcycle Racing* et *Swimming Club* **figurent** à présent dans la liste des résultats.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Créer une tâche pour gérer l’index de la table de référence

De façon similaire à l’exercice précédent, cet exercice crée un travail pour reconstruire l’index sur la clé primaire de la table de référence, une opération de gestion de base de données classique qu’un administrateur peut être amené à exécuter après le chargement d’un grand volume de données dans une table.

Créez un travail en utilisant les mêmes procédures stockées « système » dédiées aux travaux.

1. Ouvrez SSMS et connectez-vous au serveur catalog-dpt-&lt;User&gt;.database.windows.net
1. Ouvrez le fichier ...\\Learning Modules\\Schema Management\\OnlineReindex.sql.
1. Si vous n’êtes pas déjà connecté, cliquez avec le bouton droit, sélectionnez Connexion et connectez-vous au serveur catalog-dpt-&lt;User&gt;.database.windows.net
1. Assurez-vous que vous êtes connecté à la base de données jobaccount, puis appuyez sur F5 pour exécuter le script.

* sp\_add\_job crée un travail appelé « Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885 ».
* sp\_add\_jobstep crée l’étape du travail contenant le texte de la commande T-SQL pour mettre à jour l’index.




## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Créer un compte de travail élastique pour interroger plusieurs locataires
> * Mettre à jour les données dans toutes les bases de données de locataire
> * Créer un index sur une table dans toutes les bases de données de locataire

[Didacticiel sur les analyses ad hoc](saas-tenancy-adhoc-analytics.md)


## <a name="additional-resources"></a>Ressources supplémentaires

* [Autres didacticiels reposant sur le déploiement de l’application de base de données Wingtip Tickets SaaS par client](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Gestion des bases de données cloud avec montée en charge](sql-database-elastic-jobs-overview.md)
* [Créer et gérer des bases de données cloud avec montée en charge](sql-database-elastic-jobs-create-and-manage.md)