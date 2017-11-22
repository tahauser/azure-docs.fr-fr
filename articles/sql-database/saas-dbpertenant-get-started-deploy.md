---
title: Didacticiel SaaS multi-locataire - Azure SQL Database | Microsoft Docs
description: "Déployez et explorez l’application multi-locataire Wingtip Tickets SaaS, qui illustre le modèle de base de données par locataire et d’autres modèles SaaS, en utilisant Azure SQL Database."
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
ms.date: 11/10/2017
ms.author: sstein
ms.openlocfilehash: 9b1ae219eb1278b818e3e1d4237d04fe54c980ec
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Déployer et explorer une application multi-locataire SaaS qui illustre le modèle de base de données par locataire avec Azure SQL Database

Dans ce didacticiel, vous allez déployer et explorer l’application de base de données par locataire Wingtip Tickets SaaS. L’application utilise un modèle de base de données par locataire pour stocker les données de plusieurs locataires. L’application est conçue pour tirer parti des fonctionnalités SQL Azure Database qui simplifient l’activation des scénarios SaaS.

Cinq minutes après avoir cliqué sur le bouton *Déployer sur Azure* ci-dessous, vous disposez d’une application SaaS mutualisée et fonctionnelle dans le cloud, utilisant SQL Database. L’application est déployée avec trois exemples de clients, chacun avec sa propre base de données, et tous déployés dans un pool élastique SQL. L’application est déployée sur votre abonnement Azure, ce qui vous donne un accès complet pour explorer et utiliser les composants d’application individuels. Le code source de l’application et les scripts de gestion sont disponibles dans le référentiel GitHub WingtipTicketsSaaS-DbPerTenant.


Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Déployer l’application Wingtip SaaS
> * Obtenir le code source de l’application et les scripts de gestion
> * Explorer les serveurs, les pools et les bases de données qui composent l’application
> * Identifier comment les clients sont mappés à leurs données grâce au *catalogue*
> * Configurer un nouveau client
> * Surveiller l’activité d’un client dans l’application

Pour explorer les différents modèles de gestion et de conception SaaS, une [série de didacticiels associés](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) reposant sur ce déploiement initial est disponible. Tout en parcourant les didacticiels, découvrez plus en détail les scripts fournis et examinez la façon dont les différents modèles de SaaS sont implémentés. Parcourez les scripts de chaque didacticiel pour mieux comprendre comment implémenter les nombreuses fonctionnalités SQL Database qui simplifient le développement des applications SaaS.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vérifiez que les prérequis suivants sont remplis :

* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Déployer l’application Wingtip Tickets SaaS

Déployer l’application :

1. Cliquez sur le bouton **Déployer sur Azure** pour ouvrir le modèle de déploiement de base de données par locataire Wingtip Tickets SaaS dans le Portail Azure. Le modèle nécessite deux valeurs de paramètre : le nom d’un nouveau groupe de ressources et un nom d’utilisateur qui distingue ce déploiement des autres déploiements de l’application de base de données par locataire Wingtip Tickets SaaS. L’étape suivante fournit des détails sur la définition de ces valeurs.

   Veillez à noter les valeurs exactes que vous utilisez car vous devrez les entrer plus tard dans un fichier de configuration.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Entrez les valeurs de paramètre requises pour le déploiement :

    > [!IMPORTANT]
    > Certaines authentifications et pare-feu de serveur sont volontairement non sécurisés à des fins de démonstration. **Créez un groupe de ressources**. N’utilisez pas de groupes de ressources, serveurs ou pools existants. N’utilisez pas cette application, les scripts ou des ressources déployées pour la production. Supprimez ce groupe de ressources lorsque vous en avez terminé avec l’application pour interrompre la facturation associée.

    * **Groupe de ressources** : sélectionnez **Création** et indiquez un **Nom** pour le groupe de ressources. 
    * **Emplacement** : sélectionnez un **emplacement** dans la liste déroulante.
    * **Utilisateur** : certaines ressources nécessitent des noms globalement uniques. Pour garantir l’unicité, chaque fois que vous déployez l’application, fournissez une valeur pour différencier les ressources que vous créez de celles créées par d’autres déploiements de l’application Wingtip. Nous vous recommandons d’utiliser un nom **Utilisateur** court, tel que vos initiales plus un chiffre (par exemple, *af1*) et de l’utiliser ensuite dans le nom du groupe de ressources (par exemple, *wingtip-af1*). Le paramètre **Utilisateur** peut contenir uniquement des lettres, des chiffres et des traits d’union (sans espaces). Le premier et le dernier caractère doivent être une lettre ou un chiffre (l’utilisation de minuscules est recommandée).


1. **Déployez l’application**.

    * Cliquez pour accepter les conditions générales.
    * Cliquez sur **Achat**.

1. Surveiller l’état du déploiement en cliquant sur **Notifications** (l’icône représentant une cloche à droite de la zone de recherche). Le déploiement de l’application Wingtip Tickets SaaS dure environ cinq minutes.

   ![déploiement réussi](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Télécharger et débloquer les scripts de gestion Wingtip Tickets

Lors du déploiement de l’application, téléchargez le code source et les scripts de gestion.

> [!IMPORTANT]
> Le contenu exécutable (scripts, DLL) peut être bloqué par Windows lorsque des fichiers zip sont téléchargés à partir d’une source externe puis extraits. Lorsque vous extrayez les scripts d’un fichier zip, suivez les étapes ci-dessous pour débloquer le fichier .zip avant l’extraction. Cela garantit que les scripts sont autorisés à s’exécuter.

1. Accédez au [référentiel GitHub WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
1. Cliquez sur **Cloner ou télécharger**.
1. Cliquez sur **Télécharger ZIP** et enregistrez le fichier.
1. Cliquez avec le bouton droit sur le fichier **WingtipTicketsSaaS-DbPerTenant-master.zip**, puis sélectionnez **Propriétés**.
1. Sous l’onglet **Général**, sélectionnez **Débloquer**, puis cliquez sur **Appliquer**.
1. Cliquez sur **OK**.
1. Procédez à l’extraction des fichiers.

Les scripts se trouvent dans le dossier *..\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules*.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Mettre à jour le fichier de configuration utilisateur pour ce déploiement

Avant d’exécuter des scripts, mettez à jour les valeurs *resource group* et *user* dans **UserConfig.psm1**. Pour ces variables, utilisez les valeurs que vous avez définies pendant le déploiement.

1. Dans *PowerShell ISE*, ouvrez ...\\Learning Modules\\*UserConfig.psm1*. 
1. Mettez à jour *ResourceGroupName* et *Name* avec les valeurs spécifiques à votre déploiement (lignes 10 et 11 uniquement).
1. Enregistrez les modifications !

Ces valeurs sont référencées dans la quasi-totalité des scripts.

## <a name="run-the-application"></a>Exécution de l'application

L’application cible des lieux, comme des salles de concert, des clubs de jazz ou des salles de sport qui accueillent des événements. Les lieux sont inscrits en tant que clients (ou locataires) de Wingtip Tickets, offrant ainsi un moyen simple de répertorier les événements et de vendre des tickets. Chaque lieu obtient un site web personnalisé pour gérer et répertorier ses événements, ainsi que pour vendre des billets indépendamment des autres locataires. En coulisse, chaque client obtient une base de données SQL déployée dans un pool élastique SQL.

Une page **Events Hub** centrale fournit une liste de liens vers les locataires de votre déploiement.

1. Ouvrez la page _Events Hub_ dans votre navigateur web en utilisant l’adresse http://events.wingtip-dpt.&lt;UTILISATEUR&gt;.trafficmanager.net et en remplaçant &lt;UTILISATEUR&gt; par la valeur Utilisateur de votre déploiement :

    ![events hub](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. Cliquez sur **Fabrikam Jazz Club** dans le *concentrateur d’événements*.

   ![Événements](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)


Pour contrôler la distribution des demandes entrantes, l’application utilise [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Les pages d’événements, qui sont spécifiques au locataire, requièrent que les noms des locataires soient inclus dans les URL. Toutes les URL de locataires incluent votre propre valeur *Utilisateur* et respectent le format suivant : http://events.wingtipp-dpt.&lt;UTILISATEUR&gt;.trafficmanager.net/*fabrikamjazzclub*. L’application d’événements analyse le nom du locataire à partir de l’URL et l’utilise pour créer une clé permettant d’accéder à un catalogue implémenté à l’aide de la [*gestion des cartes de partitions*](sql-database-elastic-scale-shard-map-management.md). Le catalogue mappe la clé à l’emplacement de la base de données du locataire. Le **concentrateur d’événements** utilise les métadonnées du catalogue afin de récupérer le nom du locataire associé à chaque base de données pour fournir la liste des URL.

Dans un environnement de production, vous créez généralement un enregistrement DNS CNAME pour [*pointer un domaine Internet d’entreprise*](../traffic-manager/traffic-manager-point-internet-domain.md) vers le profil Traffic Manager.

## <a name="start-generating-load-on-the-tenant-databases"></a>Démarrer la génération de charge sur les bases de données de locataires

Maintenant que l’application est déployée, nous allons l’exécuter ! Le script PowerShell *Demo-LoadGenerator* démarre une charge de travail qui s’exécute sur toutes les bases de données de locataires. La charge réelle sur de nombreuses applications SaaS est généralement sporadique et imprévisible. Pour simuler ce type de charge, le générateur produit une charge distribuée entre tous les locataires, et des rafales se produisent sur chaque client à intervalles aléatoires. Pour cette raison, plusieurs minutes sont nécessaires avant que le modèle de charge n’émerge, et il est donc préférable de laisser le générateur s’exécuter pendant au moins trois ou quatre minutes avant d’analyser la charge.

1. Dans *PowerShell ISE*, ouvrez le script... \\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
1. Appuyez sur **F5** pour exécuter le script et démarrer le générateur de charge (laissez les valeurs de paramètre par défaut pour l’instant).

> [!IMPORTANT]
> Pour exécuter d’autres scripts, ouvrez une nouvelle fenêtre PowerShell ISE. Le générateur de charge exécute une série de travaux dans votre session PowerShell locale. Le script *Demo-LoadGenerator.ps1* lance le script de générateur de charge réel, qui s’exécute comme une tâche de premier plan, ainsi qu’une série de tâches de génération de charge en arrière-plan. Une tâche de générateur de charge est appelée pour chaque base de données enregistrée dans le catalogue. Les tâches sont en cours d’exécution dans votre session PowerShell locale, de sorte que la fermeture de la session PowerShell interrompt tous les travaux. Si vous mettez en veille l’ordinateur, la génération de la charge est interrompue et reprendra dès la sortie de veille de votre ordinateur.

Lorsque le générateur de charge appelle des tâches de génération de charge pour chaque locataire, la tâche de premier plan reste à un état d’appel de tâche, dans lequel elle démarre d’autres tâches en arrière-plan pour les nouveaux locataires configurés par la suite. Vous pouvez utiliser *Ctrl-C* ou appuyer sur le bouton *Stop* pour arrêter la tâche de premier plan, mais les tâches en arrière-plan existantes continueront de générer de la charge sur chaque base de données. Si vous avez besoin de surveiller et de contrôler les tâches en arrière-plan, utilisez *Get-Job*, *Receive-Job* et *Stop-Job*. Pendant l’exécution de la tâche de premier plan, vous ne pouvez pas utiliser la même session PowerShell pour exécuter d’autres scripts. Pour exécuter d’autres scripts, ouvrez une nouvelle fenêtre PowerShell ISE.

Si vous souhaitez redémarrer la session du générateur de charge, par exemple avec des paramètres différents, vous pouvez arrêter la tâche de premier plan puis réexécuter le script *Demo-LoadGenerator.ps1*. La réexécution de *Demo-LoadGenerator.ps1* arrête d’abord tout travail en cours puis redémarre le générateur, ce qui lance un nouvel ensemble de tâches en utilisant les paramètres actuels.

Pour l’instant, laissez le générateur de charge en cours d’exécution dans l’état d’appel de tâche.


## <a name="provision-a-new-tenant"></a>Approvisionner un nouveau locataire

Le déploiement initial crée trois exemples de locataires, mais nous allons créer une autre locataire pour voir comment cela affecte l’application déployée. Le flux de travail d’approvisionnement des locataires Wingtip Tickets SaaS est détaillé dans le [didacticiel sur l’approvisionnement et le catalogue](saas-dbpertenant-provision-and-catalog.md). Dans cette étape, vous créez rapidement un nouveau locataire.

1. Dans *PowerShell ISE*, ouvrez ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*.
1. Appuyez sur **F5** pour exécuter le script (laissez les valeurs par défaut pour l’instant).

   > [!NOTE]
   > De nombreux scripts Wingtip SaaS utilisent *$PSScriptRoot* pour parcourir les dossiers afin d’appeler les fonctions d’autres scripts. Cette variable est évaluée uniquement lorsque le script entier est exécuté en appuyant sur **F5**.  La mise en surbrillance et l’exécution d’une sélection (**F8**) pouvant entraîner des erreurs, appuyez sur **F5** lors de l’exécution des scripts.

La nouvelle base de données du locataire est créée dans un pool élastique SQL, puis initialisée et enregistrée dans le catalogue. Une fois l’approvisionnement réussi, le nouveau site *Événements* du locataire s’affiche dans votre navigateur :

![Nouveau locataire](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Actualisez le *concentrateur d’événements* : le nouveau locataire apparaît dans la liste.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorer les serveurs, les pools et les bases de données de locataires

Maintenant que vous avez démarré une charge dans le regroupement de locataires, examinons quelques-unes des ressources qui ont été déployées :

1. Dans le [Portail Azure](http://portal.azure.com), accédez à la liste des serveurs SQL et ouvrez le serveur **catalog-dpt-&lt;UTILISATEUR&gt;**. Le serveur de catalogue contient deux bases de données : **tenantcatalog** et **basetenantdb** (un modèle de base de données copié pour créer des locataires).

   ![bases de données](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. Revenez à la liste des serveurs SQL et ouvrez le serveur **tenants1-dpt-&lt;UTILISATEUR&gt;** qui contient les bases de données de locataires. Chaque base de données de locataires est une base de données _élastique standard_ dans un pool standard de 50 eDTU. Notez également la présence d’une base de données _Red Maple Racing_, la base de données de locataires que vous avez approvisionnée précédemment.

   ![server](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Surveiller le pool

Si le générateur de charge s’exécute depuis plusieurs minutes, suffisamment de données doivent être disponibles pour commencer à rechercher certaines des fonctionnalités de surveillance intégrées aux pools et bases de données.

1. Naviguez jusqu’au serveur **tenants1-dpt-&lt;UTILISATEUR&gt;**, puis cliquez sur **Pool1** pour afficher l’utilisation des ressources du pool (le générateur de charge a été exécuté pendant une heure dans les graphiques suivants) :

   ![surveiller un pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

Le graphique du haut montre l’utilisation des eDTU de pool, tandis que le graphique du bas montre l’utilisation des eDTU des 5 premières bases de données du pool.  Ces deux graphiques montrent bien que les pools élastiques et SQL Database sont parfaitement adaptés aux charges de travail de l’application SaaS. Quatre bases de données qui migrent chacune jusqu’à 40 eDTU sont facilement prises en charge dans un pool de 50 eDTU. Si elles ont été approvisionnées en tant que bases de données autonomes, elles devront être de type S2 (50 DTU) pour pouvoir gérer les pics d’activité. Le coût de 4 bases de données autonomes S2 représente près de 3 fois le prix du pool, et le pool dispose toujours de suffisamment d’espace disponible pour accueillir de nombreuses autres bases de données. Dans des situations réelles, les clients SQL Database exécutent jusqu'à 500 bases de données dans des pools de 200 eDTU. Pour plus d’informations, consultez le [didacticiel Monitor performance of the WTP sample SaaS application](saas-dbpertenant-performance-monitoring.md) (Surveiller les performances de l’exemple d’application SaaS WTP).


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Déployer l’application Wingtip Tickets SaaS
> * Explorer les serveurs, les pools et les bases de données qui composent l’application
> * Explorer les locataires qui sont mappés à leurs données avec le *catalogue*
> * Approvisionner les nouveaux locataires
> * Afficher l’utilisation du pool pour surveiller l’activité du locataire
> * Supprimer les exemples de ressources pour arrêter la facturation associée

Essayez maintenant le [didacticiel sur l’approvisionnement et le catalogue](saas-dbpertenant-provision-and-catalog.md).



## <a name="additional-resources"></a>Ressources supplémentaires

* Autres [didacticiels reposant sur l’application de base de données par locataire Wingtip Tickets SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* Pour de plus amples informations sur les pools élastiques, consultez [*Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données SQL*](sql-database-elastic-pool.md).
* Pour de plus amples informations sur les travaux élastiques, consultez [*Gestion des bases de données cloud avec montée en charge*](sql-database-elastic-jobs-overview.md).
* Pour de plus amples informations sur les applications SaaS mutualisées, consultez [*Modèles de conception pour les applications SaaS mutualisées et Base de données SQL Azure*](saas-tenancy-app-design-patterns.md).
