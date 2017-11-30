---
title: "Déployer une application SaaS multilocataire partitionnée qui utilise Azure SQL Database | Microsoft Docs"
description: "Déployez et explorez l’application de base de données Wingtip Tickets SaaS multilocataire partitionnée, qui présente les modèles SaaS à l’aide d’Azure SQL Database."
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: billgib;MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.openlocfilehash: cb55bf1f1c7eeb0fc7608aca8d70818b5e3e06c0
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Déployer et explorer une application multilocataire partitionnée qui utilise Azure SQL Database

Dans ce didacticiel, vous allez déployer et explorer un exemple d’application de base de données multilocataire SaaS nommée Wingtip Tickets. L’application Wingtip est conçue pour tirer parti des fonctionnalités Azure SQL Database qui simplifient l’implémentation de scénarios SaaS.

Cette implémentation de Wingtips utilise un modèle de base de données multilocataire partitionnée. Le partitionnement est effectué par identificateur du locataire. Les données du locataire sont transmises à une base de données spécifique en fonction des valeurs d’identificateur du locataire. Quel que soit le nombre de locataires contenus dans une base de données, toutes les bases de données sont multilocataires en ce sens que les schémas de table incluent un identificateur du locataire. 

Ce modèle de base de données vous permet de stocker un ou plusieurs locataires dans chaque partition ou base de données. Vous pouvez optimiser les coûts en partitionnant chaque base de données entre plusieurs locataires. Vous pouvez également l’isolation en faisant en sorte que chaque base de données ne stocke qu’un seul locataire. Votre choix d’optimisation peut s’appliquer de manière indépendante pour chaque locataire spécifique. Effectuez votre choix lors du premier stockage du locataire, vous pourrez toujours changer d’avis ultérieurement. L’application est conçue pour fonctionner correctement dans les deux cas.

#### <a name="app-deploys-quickly"></a>Déploiement rapide de l’application

La section relative au déploiement qui suit inclut le bouton **Déployer dans Azure**. Lorsque vous appuyez sur le bouton, l’application Wingtip est entièrement déployée en seulement cinq minutes. L’application Wingtip s’exécute dans le cloud Azure et utilise Azure SQL Database. Wingtip est déployée dans votre abonnement Azure. Vous avez un accès complet pour utiliser les composants d’application individuels.

L’application est déployée avec des données pour trois exemples de locataires. Les locataires sont stockés ensemble dans une base de données multilocataire.

N’importe quel utilisateur peut télécharger le code source C# et PowerShell pour Wingtip Tickets à partir de [notre référentiel GitHub][link-github-wingtip-multitenantdb-55g].

#### <a name="learn-in-this-tutorial"></a>Découvrir, dans ce didacticiel, comment

> [!div class="checklist"]

> - Déployer l’application Wingtip SaaS.
> - Obtenir le code source de l’application et les scripts de gestion.
> - Explorer les serveurs et les bases de données qui composent l’application.
> - Identifier comment les locataires sont mappés à leurs données grâce au *catalogue*.
> - Approvisionner un nouveau locataire.
> - Surveiller l’activité d’un locataire dans l’application.

Une série de didacticiels associés, basés sur ce déploiement initial, est disponible. Les didacticiels explorent une gamme de modèles de conception et de gestion de SaaS. Lorsque vous utilisez les didacticiels, vous êtes encouragé à parcourir les scripts fournis pour voir comment les différents modèles SaaS sont implémentés.

## <a name="prerequisites"></a>Composants requis

Pour suivre ce tutoriel, vérifiez que les conditions préalables suivantes sont bien satisfaites :

- La dernière version d’Azure PowerShell est installée. Pour plus d’informations, consultez [Prise en main d’Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Déployer l’application Wingtip Tickets

1. Cliquez sur le bouton **Déployer dans Azure** suivant.
    - Il ouvre le portail Azure avec le modèle de déploiement Wingtip Tickets SaaS. Le modèle nécessite deux valeurs de paramètre ; le nom d’un nouveau groupe de ressources et une valeur utilisateur qui distingue ce déploiement des autres déploiements de l’application. L’étape suivante fournit des détails sur ces valeurs de paramètre.
        - Veillez à noter les valeurs exactes que vous utilisez, car vous en aurez besoin plus tard pour un fichier de configuration.

    [![Bouton pour Déployer dans Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

2. Entrez les valeurs de paramètre requises pour le déploiement.

    > [!IMPORTANT]
    > L’authentification, et les paramètres de pare-feu serveur, sont intentionnellement non sécurisés afin de faciliter la démonstration. Choisissez **Créer un groupe de ressources** et n’utilisez pas de groupes de ressources, serveurs ou pools existants. N’utilisez pas cette application, ni les ressources qu’elle crée, pour la production. Supprimez ce groupe de ressources lorsque vous en avez terminé avec l’application pour interrompre la facturation associée.

    Il est préférable d’utiliser uniquement des lettres minuscules, des chiffres et des traits d’union dans les noms de ressource.

    - Pour **Groupe de ressources**, sélectionnez **Création** et indiquez un **Nom** pour le groupe de ressources (sensible à la casse).
        - Nous recommandons que toutes les lettres du nom de votre groupe de ressources soient en minuscules.
        - Nous vous recommandons d’inclure un tiret, suivi de vos initiales, puis d’un chiffre : par exemple, *wingtip-af1*.
        - Sélectionnez un **Emplacement** dans la liste déroulante.
    - Pour **Utilisateur**, nous vous recommandons de choisir une valeur **Utilisateur** courte, comme vos initiales plus un chiffre : par exemple, *af1*.

3. **Déployez l’application**.

    - Cliquez pour accepter les conditions générales.
    - Cliquez sur **Achat**.

4. Surveillez l’état du déploiement en cliquant sur **Notifications**, l’icône représentant une cloche à droite de la zone de recherche. Le déploiement de l’application Wingtip dure environ cinq minutes.

   ![déploiement réussi](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Télécharger et débloquer les scripts de gestion

Lors du déploiement de l’application, téléchargez le code source de l’application et les scripts de gestion.

> [!IMPORTANT]
> Le contenu exécutable (scripts, DLL) peut être bloqué par Windows lorsque des fichiers zip sont téléchargés à partir d’une source externe puis extraits. Lorsque vous extrayez les scripts d’un fichier zip, utilisez les étapes suivantes pour débloquer le fichier .zip avant l’extraction. En débloquant le fichier .zip, vous êtes assuré de l’exécution des scripts.

1. Accédez au [référentiel GitHub WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Cliquez sur **Cloner ou télécharger**.
3. Cliquez sur **Télécharger ZIP** et enregistrez le fichier.
4. Cliquez avec le bouton droit sur le fichier **WingtipTicketsSaaS-MultiTenantDb-master.zip**, puis sélectionnez **Propriétés**.
5. Sous l’onglet **Général**, sélectionnez **Débloquer**, puis cliquez sur **Appliquer**.
6. Cliquez sur **OK**.
7. Procédez à l’extraction des fichiers.

Les scripts se trouvent dans le dossier *...\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\*.

## <a name="update-the-configuration-file-for-this-deployment"></a>Mettre à jour le fichier de configuration pour ce déploiement

Avant d’exécuter des scripts, définissez les valeurs *resource group* et *user* dans **UserConfig.psm1**. Pour ces variables, utilisez les mêmes valeurs que vous avez définies pendant le déploiement.

1. Ouvrez ...\\Learning Modules\\*UserConfig.psm1* dans *PowerShell ISE*.
2. Mettez à jour *ResourceGroupName* et *Name* avec les valeurs spécifiques à votre déploiement (lignes 10 et 11 uniquement).
3. Enregistrez les modifications.

Les valeurs définies dans ce fichier sont utilisées par tous les scripts, il est donc important qu’elles sont exactes. Si vous redéployez l’application, veillez à choisir un groupe de ressources et une valeur utilisateur différents. Mettez ensuite à jour UserConfig avec les nouvelles valeurs.

## <a name="run-the-application"></a>Exécution de l'application

L’application présente des lieux, telles que des salles de concert, des clubs de jazz, des salles de sport, etc. qui accueillent des événements. Les lieux sont inscrits en tant que clients de la plateforme Wingtip, offrant ainsi un moyen simple de répertorier les événements et de vendre des tickets. Chaque lieu obtient une application web personnalisée pour gérer et répertorier ses événements, ainsi que pour vendre des billets indépendamment des autres locataires. En coulisse, les données de chaque locataire sont stockées par défaut dans une base de données multilocataire partitionnée.

Un **concentrateur d’événements** central fournit une liste de liens vers les locataires de votre déploiement spécifique.

1. Ouvrez le *concentrateur d’événements* dans votre navigateur web :
    - http://events.wingtip-mt.&lt;USER&gt;.trafficmanager.net &nbsp; *(Remplacez par la valeur utilisateur de votre déploiement.)*

    ![events hub](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Cliquez sur **Fabrikam Jazz Club** dans le *concentrateur d’événements*.

   ![Événements](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

Pour contrôler la distribution des requêtes entrantes, l’application utilise [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Les pages d’événements, qui sont spécifiques au locataire, incluent le nom du locataire dans l’URL. Les URL incluent également votre valeur Utilisateur spécifique et respectent ce format :

- http://events.wingtip-mt.&lt;USER&gt;.trafficmanager.net/*fabrikamjazzclub*
 
L’application d’événements analyse le nom du locataire à partir de l’URL et la hache pour créer une clé permettant d’accéder à un catalogue utilisant la [gestion des cartes de partitions](sql-database-elastic-scale-shard-map-management.md). Le catalogue mappe la clé à l’emplacement de la base de données du locataire. Le **concentrateur d’événements** répertorie tous les locataires qui sont enregistrés dans le catalogue. Le **concentrateur d’événements** utilise les métadonnées étendues dans le catalogue pour récupérer le nom du locataire associé à chaque mappage pour créer les URL.

Dans un environnement de production, vous créez généralement un enregistrement DNS CNAME pour [pointer un domaine Internet d’entreprise](../traffic-manager/traffic-manager-point-internet-domain.md) vers le profil Traffic Manager.

## <a name="start-generating-load-on-the-tenant-databases"></a>Démarrer la génération de charge sur les bases de données de locataires

Maintenant que l’application est déployée, nous allons l’exécuter ! Le script PowerShell *Demo-LoadGenerator* démarre une charge de travail qui s’exécute pour chaque locataire. La charge réelle sur de nombreuses applications SaaS est généralement sporadique et imprévisible. Pour simuler ce type de charge, le générateur produit une charge répartie sur tous les locataires. La charge inclut des pics aléatoires sur chaque locataire qui se produisent à des intervalles aléatoires. Plusieurs minutes sont nécessaires avant que le modèle de charge n’émerge, et il est donc préférable de laisser le générateur s’exécuter pendant au moins trois ou quatre minutes avant d’analyser la charge.

1. Dans *PowerShell ISE*, ouvrez le script... \\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
2. Appuyez sur **F5** pour exécuter le script et démarrer le générateur de charge (laissez les valeurs de paramètre par défaut pour l’instant).

> [!IMPORTANT]
> Le script *Demo-LoadGenerator.ps1* ouvre une autre session PowerShell dans laquelle le générateur de charge s’exécute. Le générateur de charge s’exécute dans cette session comme une tâche de premier plan qui appelle des travaux de génération de charge en arrière-plan, un pour chaque locataire.

Après le démarrage de la tâche de premier plan, elle reste dans un état d’appel de travail. La tâche démarre les travaux en arrière-plan supplémentaires pour les nouveaux locataires qui sont approvisionnés par la suite.

La fermeture de la session PowerShell arrête tous les travaux.

Vous pouvez souhaiter redémarrer la session de générateur de charge pour utiliser des valeurs de paramètre différentes. Dans ce cas, fermez la session de génération PowerShell, puis réexécutez *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Approvisionner un nouveau locataire dans la base de données partitionnée

Le déploiement initial inclut trois exemples de locataires dans la base de données *Tenants1*. Nous allons créer un autre locataire pour voir comment il affecte l’application déployée. Dans cette étape, vous créez rapidement un nouveau locataire.

1. Ouvrez ...\\Learning Modules\ProvisionTenants\\*Demo-ProvisionTenants.ps1* dans *PowerShell ISE*.
2. Appuyez sur **F5** pour exécuter le script (laissez les valeurs par défaut pour l’instant).

   > [!NOTE]
   > De nombreux scripts Wingtip Tickets SaaS utilisent *$PSScriptRoot* pour pouvoir naviguer dans les dossiers, appeler d’autres scripts ou importer des modules. Cette variable est évaluée uniquement lorsque le script est exécuté complètement en appuyant sur **F5**.  La mise en surbrillance et l’exécution d’une sélection (**F8**) pouvant entraîner des erreurs, appuyez sur **F5** lors de l’exécution des scripts.

Le nouveau locataire Red Maple Racing est ajouté à la base de données *Tenants1* et enregistré dans le catalogue. Le site *événements* de vente de tickets du nouveau locataire s’ouvre dans votre navigateur :

![Nouveau locataire](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Actualisez le *concentrateur d’événements* : le nouveau locataire apparaît dans la liste.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Approvisionner un nouveau locataire dans sa propre base de données

Le modèle multilocataire partitionné vous permet de choisir s’il faut approvisionner un nouveau locataire dans une base de données qui contient d’autres locataires, ou d’approvisionner le locataire dans sa propre base de données. Les données d’un locataire dans son propre base de données sont isolées des données d’autres locataires. L’isolation vous permet de gérer les performances de ce client indépendamment des autres locataires. Il est également plus facile de restaurer les données à une heure antérieure pour le locataire isolé. Vous pouvez choisir de placer les clients standard ou ceux profitant d’un essai gratuit dans une base de données multilocataire et les clients premium dans des bases de données individuelles. Si vous créez un grand nombre de bases de données qui ne contiennent chacune qu’un seul locataire, vous pouvez les gérer collectivement dans un pool élastique afin d’optimiser les coûts de ressource.  

Nous approvisionnons maintenant un autre locataire, dans sa propre base de données cette fois-ci.

1. Dans ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*, définissez *$TenantName* sur **Salix Salsa**, *$VenueType* sur **dance** et *$Scenario* sur **2**.

2. Appuyez sur **F5** pour réexécuter le script.
    - Cet appui sur F5 configure le nouveau locataire dans une base de données distincte. La base de données et le locataire sont enregistrés dans le catalogue. Le navigateur s’ouvre alors sur la page des événements du locataire.

   ![Page d’événements Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Faites défiler vers le bas de la page. Dans la bannière, vous voyez le nom de la base de données dans laquelle les données du locataire sont stockées.

3. Actualisez le concentrateur d’événements, les deux nouveaux locataires apparaissent maintenant dans la liste.



## <a name="explore-the-servers-and-tenant-databases"></a>Explorer les serveurs et les bases de données de locataires

Examinons maintenant quelques-unes des ressources qui ont été déployées :

1. Dans le [portail Azure](http://portal.azure.com), accédez à la liste des groupes de ressources. Ouvrez le groupe de ressources que vous avez créé lors du déploiement de l’application.

   ![resource group](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Cliquez sur le serveur **catalog-mt&lt;USER&gt;**. Le serveur de catalogue contient deux bases de données nommées *tenantcatalog* et *basetenantdb*. La base de données *basetenantdb* est une base de données de modèle vide. Elle est copiée pour créer une nouvelle base de données de locataires, quelle soit utilisée par plusieurs locataires ou un seul.

   ![catalog server](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Revenez au groupe de ressources et sélectionnez le serveur *tenants1-mt* contenant les bases de données de locataires.
    - La base de données tenants1 est une base de données multilocataire dans laquelle les trois locataires d’origine, plus le premier locataire que vous avez ajouté, sont stockés. Elle est configurée comme une base de données 50 DTU standard.
    - La base de données **salixsalsa** contient la salle de danse Salix Salsa comme son unique locataire. Elle est configurée comme une base de données d’édition standard avec 50 DTU par défaut.

   ![serveur de locataires](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)


## <a name="monitor-the-performance-of-the-database"></a>Surveiller les performances de la base de données

Si le générateur de charge s’exécute depuis plusieurs minutes, suffisamment de télémétrie est disponible pour commencer à rechercher certaines des fonctionnalités de surveillance de base de données intégrées au portail.

1. Accédez au serveur **tenants1-mt&lt;USER&gt;**, puis cliquez sur **tenants1** pour afficher l’utilisation des ressources pour la base de données contenant quatre locataires. Chaque client est soumis à une charge sporadique importante dans le générateur de charge :

   ![surveiller tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Le graphique d’utilisation de DTU montre clairement comment une base de données peut multilocataire peut supporter une charge de travail imprévisible entre plusieurs locataires. Dans ce cas, le générateur de charge applique une charge sporadique de 30 DTU environ sur chaque locataire. Cette charge équivaut à 60 % d’utilisation d’une base de données de 50 DTU. Des pics supérieurs à 60 % sont le résultat d’une charge appliquée sur plusieurs locataires simultanément.

2. Accédez au serveur **tenants1-mt&lt;USER&gt;**, puis cliquez sur la base de données **salixsalsa** pour afficher l’utilisation des ressources sur cette base de données ne contenant qu’un seul locataire.

   ![base de données salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Le générateur de charge applique une charge similaire sur chaque locataire, quelle que soit la base de données dans laquelle se trouve chaque locataire. Avec un seul locataire dans la base de données **salixsalsa**, vous pouvez constater que la base de données peut supporter une charge bien supérieure à celle de la base de données en contenant plusieurs. 

> [!NOTE]
> Les charges créées par le générateur de charge sont données à titre d’illustration uniquement.  Les ressources allouées aux bases de données multilocataires et à locataire unique, et le nombre de locataires que vous pouvez héberger dans une base de données multilocataire, varient selon les modèles de charge de travail réelle dans votre application.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> - Déployer l’application de base de données multilocataire Wingtip Tickets SaaS
> - Explorer les serveurs et les bases de données qui composent l’application
> - Explorer les locataires qui sont mappés à leurs données avec le *catalogue*
> - Approvisionner les nouveaux locataires dans une base de données multilocataire et une base de données à locataire unique.
> - Afficher l’utilisation du pool pour surveiller l’activité du locataire
> - Supprimer les exemples de ressources pour arrêter la facturation associée

Essayez maintenant le [didacticiel sur les clients d’approvisionnement](sql-database-saas-tutorial-provision-and-catalog.md).



## <a name="additional-resources"></a>Ressources supplémentaires

- Pour de plus amples informations sur les applications SaaS mutualisées, consultez [*Modèles de conception pour les applications SaaS mutualisées et Base de données SQL Azure*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications).








<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: http://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/





<!--  Image references.

[image-deploy-to-azure-blue-48d]: http://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."

-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png

