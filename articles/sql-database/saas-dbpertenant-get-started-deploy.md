---
title: Didacticiel SaaS du modèle de base de données par locataire - Azure SQL Database | Microsoft Docs
description: Déployez et explorez l’application multilocataire SaaS Wingtip Tickets, qui illustre le modèle de base de données par locataire et d’autres modèles SaaS, en utilisant Azure SQL Database.
keywords: didacticiel sur les bases de données SQL
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 12/07/2017
ms.author: genemi
ms.openlocfilehash: c62817b6bb60d99a4762e433510cc54d15add35a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Déployer et explorer une application SaaS multilocataire qui utilise le modèle de base de données par locataire avec SQL Database

Dans ce didacticiel, vous allez déployer et explorer l’application de base de données par locataire SaaS Wingtip Tickets. L’application utilise un modèle de base de données par locataire pour stocker les données de plusieurs locataires. L’application est conçue pour tirer parti des fonctionnalités Azure SQL Database qui simplifient l’activation des scénarios SaaS.

Cinq minutes après avoir sélectionné **Déployer sur Azure**, vous avez une application SaaS multilocataire. L’application inclut une base de données SQL qui s’exécute dans le cloud. Elle est déployée avec trois exemples de locataire, chacun avec sa propre base de données. Toutes les bases de données sont déployées sur un pool élastique SQL. L’application est déployée sur votre abonnement Azure. Vous avez un accès complet pour explorer et utiliser les composants individuels de l’application. Le code source en C# de l’application et les scripts de gestion sont disponibles dans le [référentiel GitHub WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt].

Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Déployer l’application Wingtip SaaS.
> - Obtenir le code source de l’application et les scripts de gestion.
> - Explorer les serveurs, les pools et les bases de données qui composent l’application.
> - Identifier comment les locataires sont mappés à leurs données grâce au *catalogue*.
> - Approvisionner un nouveau locataire.
> - Surveiller l’activité d’un locataire dans l’application.

Une [série de didacticiels associés](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) vous propose d’explorer les divers modèles de conception et de gestion SaaS. Ces didacticiels vont au-delà de ce déploiement initial. Lorsque vous utilisez les didacticiels, vous pouvez examiner les scripts fournis pour voir comment les différents modèles SaaS sont implémentés. Ces scripts montrent comment les fonctionnalités de SQL Database simplifient le développement des applications SaaS.

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, assurez-vous qu’Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Déployer l’application Wingtip Tickets SaaS

#### <a name="plan-the-names"></a>Planifier les noms

Dans les étapes de cette section, vous fournissez une valeur utilisateur qui est utilisée pour vous assurer que les noms de ressources sont globalement uniques. Vous fournissez également un nom pour le groupe de ressources qui contient toutes les ressources créées par un déploiement de l’application. Pour une personne fictive nommée Ann Finley, nous vous suggérons :

- **Utilisateur** : *af1* est constitué des initiales d’Ann Finley plus un chiffre. Si vous déployez l’application une deuxième fois, utilisez une valeur différente. Par exemple, af2.
- **Groupe de ressources** : *wingtip-dpt-af1* indique qu’il s’agit de l’application de base de données par locataire. Ajoutez af1 au nom d’utilisateur pour que le nom du groupe de ressources corresponde aux noms des ressources qu’il contient.

Choisissez vos noms maintenant et notez-les. 

#### <a name="steps"></a>Étapes

1. Pour ouvrir le modèle de déploiement de base de données par locataire SaaS Wingtip Tickets dans le portail Azure, sélectionnez **Déployer sur Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Entrez les valeurs des paramètres obligatoires dans le modèle.

    > [!IMPORTANT]
    > Certaines authentifications et pare-feu de serveur sont volontairement non sécurisés à des fins de démonstration. Nous vous recommandons de créer un groupe de ressources. N’utilisez pas de groupes de ressources, serveurs ou pools existants. N’utilisez pas cette application, les scripts ou des ressources déployées pour la production. Supprimez ce groupe de ressources lorsque vous en avez terminé avec l’application pour interrompre la facturation associée.

    - **Groupe de ressources** : sélectionnez **Création** et indiquez le nom unique que vous avez choisi précédemment pour le groupe de ressources. 
    - **Emplacement** : sélectionnez un emplacement dans la liste déroulante.
    - **Utilisateur** : utilisez la valeur du nom d’utilisateur que vous avez choisi précédemment.

3. Déployez l’application.

    a. Sélectionnez pour accepter les conditions générales.

    b. Sélectionnez **Achat**.

4. Pour surveiller l’état du déploiement, sélectionnez **Notifications** (l’icône représentant une cloche à droite de la zone de recherche). Le déploiement de l’application Wingtip Tickets SaaS dure environ cinq minutes.

   ![Déploiement réussi](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Télécharger et débloquer les scripts de gestion Wingtip Tickets

Lors du déploiement de l’application, téléchargez le code source et les scripts de gestion.

> [!IMPORTANT]
> Le contenu exécutable (scripts et DLL) peut être bloqué par Windows lors du téléchargement et de l’extraction de fichiers .zip à partir d’une source externe. Suivez les étapes pour débloquer le fichier .zip avant d’extraire les scripts. Le déblocage garantit que les scripts sont autorisés à s’exécuter.

1. Accédez au [dépôt GitHub WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt].
2. Sélectionnez **Cloner ou télécharger**.
3. Sélectionnez **Télécharger ZIP**, puis enregistrez le fichier.
4. Cliquez avec le bouton droit sur le fichier **WingtipTicketsSaaS-DbPerTenant-master.zip**, puis sélectionnez **Propriétés**.
5. Sous l’onglet **Général**, sélectionnez **Débloquer** > **Appliquer**.
6. Sélectionnez **OK** et extrayez les fichiers

Les scripts se trouvent dans le dossier ...\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Mettre à jour le fichier de configuration utilisateur pour ce déploiement

Avant d’exécuter des scripts, mettez à jour les valeurs de groupe de ressources et d’utilisateur dans le fichier User Config. Pour ces variables, utilisez les valeurs que vous avez définies pendant le déploiement.

1. Dans PowerShell ISE, ouvrez ...\\Learning Modules\\**UserConfig.psm1** 
2. Mettez à jour **ResourceGroupName** et **Name** avec les valeurs spécifiques à votre déploiement (lignes 10 et 11 uniquement).
3. Enregistrez les modifications.

Ces valeurs sont référencées dans la quasi-totalité des scripts.

## <a name="run-the-application"></a>Exécution de l'application

L’application présente les lieux qui hébergent des événements. Les types de lieu incluent des salles de concert, des clubs de jazz et des salles de sport. Dans Wingtip Tickets, les lieux sont enregistrés en tant que locataires. En étant locataire, un lieu obtient un moyen simple de répertorier des événements et de vendre des billets à ses clients. Chaque lieu obtient un site web personnalisé pour répertorier ses événements et vendre des billets.

En interne dans l’application, chaque locataire obtient une base de données SQL déployée sur un pool élastique SQL.

Une page **Events Hub** centrale fournit une liste de liens vers les locataires de votre déploiement.

1. Utilisez l’URL pour ouvrir la page Events Hub dans votre navigateur web en utilisant l’adresse : http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net. Remplacez &lt;user&gt; par la valeur utilisateur de votre déploiement.

    ![Concentrateur d’événements](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Sélectionnez **Fabrikam Jazz Club** dans l’Events Hub.

    ![Événements](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

L’application Wingtip utilise [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) pour contrôler la distribution des requêtes entrantes. L’URL pour accéder à la page d’événements pour un client spécifique utilise le format suivant :

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

    Les parties du format précédent sont expliquées dans le tableau suivant.

    | Partie de l’URL        | Description       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | Parties des événements de l’application Wingtip.<br /><br /> *-dpt* distingue l’implémentation de *base de données par locataire* de Wingtip Tickets des autres implémentations. Par exemple, les implémentations d’application par locataire *autonomes* (*-sa*), ou les *bases de données multilocataires* (*- mt*). |
    | .*&lt;user&gt;* | *af1* dans l’exemple. |
    | .trafficmanager.net/ | Traffic Manager, URL de base. |
    | fabrikamjazzclub | Identifie le locataire nommé Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

* Le nom du locataire est analysé à partir de l’URL, par l’application des événements.
* Le nom du locataire est utilisé pour créer une clé.
* La clé est utilisée pour accéder au catalogue, afin d’obtenir l’emplacement de la base de données du locataire.
    - Le catalogue est implémenté à l’aide de la *gestion des cartes de partitions*.
* L’Events Hub utilise les métadonnées étendues dans le catalogue pour construire la liste des URL d’événement pour chaque locataire.

Dans un environnement de production, vous créez généralement un enregistrement DNS CNAME pour [*pointer un domaine Internet d’entreprise*](../traffic-manager/traffic-manager-point-internet-domain.md) vers le nom DNS Traffic Manager.

## <a name="start-generating-load-on-the-tenant-databases"></a>Démarrer la génération de charge sur les bases de données de locataires

Maintenant que l’application est déployée, nous allons l’exécuter.

Le script PowerShell *Demo-LoadGenerator* démarre une charge de travail qui s’exécute sur toutes les bases de données de locataires. La charge réelle sur de nombreuses applications SaaS est sporadique et imprévisible. Pour simuler ce type de charge, le générateur produit une charge avec des pics aléatoires ou des pics d’activité sur chaque locataire. Les pics se produisent à intervalles aléatoires. Plusieurs minutes sont nécessaires pour que le modèle de charge émerge. Laissez le générateur s’exécuter pendant au moins trois ou quatre minutes avant de surveiller la charge.

1. Dans PowerShell ISE, ouvrez le script ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
2. Appuyez sur F5 pour exécuter le script et démarrer le générateur de charge. Laissez les valeurs de paramètre par défaut pour le moment.
3. Connectez-vous à votre compte Azure et sélectionnez l’abonnement vous souhaitez utiliser si nécessaire.

Le script de génération de charge démarre une tâche en arrière-plan pour chaque base de données dans le catalogue, puis s’arrête. Si vous exécutez à nouveau le script de génération de charge, il arrête toutes les tâches en arrière-plan qui sont en cours d’exécution avant de démarrer les nouvelles.

#### <a name="monitor-the-background-jobs"></a>Surveiller les tâches en arrière-plan

Si vous souhaitez contrôler et surveiller les tâches en arrière-plan, utilisez les cmdlets suivantes :

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>Actions de Demo-LoadGenerator.ps1

*Demo-LoadGenerator.ps1* reproduit une charge de travail active des transactions clientes. Les étapes suivantes décrivent la séquence d’actions lancée par *Demo-LoadGenerator.ps1* :

1. *Demo-LoadGenerator.ps1* démarre *LoadGenerator.ps1* au premier plan.

    - Les deux fichiers .ps1 sont stockés dans les dossiers Learning Modules\\Utilities\\.

2. *LoadGenerator.ps1* effectue une boucle sur toutes les bases de données locataires enregistrées dans le catalogue.

3. *LoadGenerator.ps1* démarre une tâche PowerShell en arrière-plan pour chaque base de données client :

    - Par défaut, les tâches en arrière-plan s’exécutent pendant 120 minutes.
    - Chaque travail cause une charge UC sur une base de données client en exécutant *sp_CpuLoadGenerator*. L’intensité et la durée de la charge varient en fonction de `$DemoScenario`. 
    - *sp_CpuLoadGenerator* effectue une boucle sur une instruction SQL SELECT qui cause une charge UC élevée. L’intervalle de temps entre les problèmes de l’instruction SELECT varie en fonction des valeurs du paramètre pour créer une charge processeur contrôlable. Les niveaux de charge et les intervalles sont aléatoires pour simuler des charges plus réalistes.
    - Ce fichier .sql est stocké sous *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Si `$OneTime = $false`, le générateur de charge démarre les tâches en arrière-plan et continue de s’exécuter. Toutes les 10 secondes, il contrôle les nouveaux locataires qui sont approvisionnés. Si vous définissez `$OneTime = $true`, le générateur de charge démarre les tâches en arrière-plan, puis arrête son exécution au premier plan. Pour ce didacticiel, laissez `$OneTime = $false`.

  Utilisez Ctrl-C ou Ctrl-Pause pour arrêter l’opération si vous souhaitez arrêter ou redémarrer le générateur de charge. 

  Si vous laissez le générateur de charge s’exécuter au premier plan, utilisez une autre instance de PowerShell ISE pour exécuter d’autres scripts PowerShell.

&nbsp;

Avant de passer à la section suivante, laissez le générateur de charge en cours d’exécution dans l’état d’appel de tâche.

## <a name="provision-a-new-tenant"></a>Approvisionner un nouveau locataire

Le déploiement initial crée trois exemples de locataire. Maintenant, vous créez un autre locataire pour voir son impact sur l’application déployée. Dans l’application Wingtip, le flux de travail permettant de provisionner les nouveaux locataires est expliqué dans le [didacticiel sur le provisionnement et l’inscription dans le catalogue](saas-dbpertenant-provision-and-catalog.md). Au cours de cette phase, vous créez un locataire, ce qui prend moins d’une minute.

1. Ouvrez une nouvelle instance de PowerShell ISE.
2. Ouvrez ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*.
3. Pour exécuter le script, appuyez sur la touche F5. Laissez les valeurs par défaut pour le moment.

   > [!NOTE]
   > De nombreux scripts Wingtip SaaS utilisent *$PSScriptRoot* pour parcourir les dossiers afin d’appeler les fonctions d’autres scripts. Cette variable est évaluée uniquement lorsque le script entier est exécuté en appuyant sur F5. La mise en surbrillance et l’exécution d’une sélection avec la touche F8 peut engendrer des erreurs. Pour exécuter les scripts, appuyez sur la touche F5.

La nouvelle base de données locataire est :

- Créée dans un pool élastique SQL.
- Initialisée.
- Inscrite dans le catalogue.

Une fois l’approvisionnement réussi, le site *Événements* du nouveau locataire s’affiche dans votre navigateur.

![Nouveau locataire](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Actualisez l’Events Hub pour faire apparaître le nouveau locataire dans la liste.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorer les serveurs, les pools et les bases de données de locataires

Maintenant que vous avez démarré une charge dans le regroupement de locataires, examinons quelques-unes des ressources qui ont été déployées.

1. Dans le [portail Azure](http://portal.azure.com), accédez à votre liste de serveurs SQL. Ouvrez ensuite le serveur **catalog-dpt-&lt;UTILISATEUR&gt;**.
    - Le serveur de catalogue contient deux bases de données : **tenantcatalog** et **basetenantdb** (un modèle de base de données copié pour créer des locataires).

   ![Bases de données](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Revenez à votre liste de serveurs SQL.

3. Ouvrez le serveur **tenants1-dpt-&lt;UTILISATEUR&gt;** qui contient les bases de données locataires.

4. Observez les points suivants :

    - Chaque base de données de locataires est une base de données **élastique standard** dans un pool standard de 50 eDTU.
    - La base de données Red Maple Racing est la base de données de locataires que vous avez approvisionnée précédemment.

   ![Serveur avec des bases de données](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Surveiller le pool

Au bout de quelques minutes d’exécution de *LoadGenerator.ps1*, une quantité suffisante de données est disponible pour vous permettre de découvrir certaines fonctionnalités de surveillance. Ces fonctionnalités sont intégrées dans les pools et les bases de données.

Accédez au serveur **tenants1-dpt-&lt;utilisateur&gt;**, puis sélectionnez **Pool1** pour afficher l’utilisation des ressources du pool. Dans les graphiques suivants, le générateur de charge s’est exécuté pendant une heure.

   ![Surveiller un pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Le premier graphique, intitulé **Utilisation des ressources**, montre l’utilisation eDTU du pool.
- Le second graphique montre l’utilisation eDTU des bases de données les plus actives du pool.

Ces deux graphiques illustrent bien que les pools élastiques et SQL Database sont parfaitement adaptés aux charges de travail imprévisibles de l’application SaaS. Les graphiques montrent que quatre bases de données ont chacune des pics d’activité allant jusqu’à 40 eDTU, et pourtant toutes les bases de données sont confortablement prises en charge par un pool de 50 eDTU. Le pool de 50 eDTU peut prendre en charge des charges de travail encore plus lourdes. Si les bases de données ont été approvisionnées en tant que bases de données autonomes, chacune doit être de type S2 (50 DTU) pour pouvoir gérer les pics d’activité. Le coût de quatre bases de données S2 autonomes s’élève à presque trois fois celui du pool. Dans des situations réelles, les clients SQL Database exécutent jusqu'à 500 bases de données dans des pools de 200 eDTU. Pour plus d’informations, consultez le [didacticiel sur la surveillance des performances](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ressources supplémentaires

- Pour plus d’informations, consultez d’autres [didacticiels reposant sur l’application de base de données par locataire SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Pour en savoir plus sur les pools élastiques, consultez [Qu’est-ce qu’un pool élastique SQL Azure ?](sql-database-elastic-pool.md).
- Pour en savoir plus sur les travaux élastiques, consultez [Gérer des bases de données cloud montées en charge avec les travaux élastiques](sql-database-elastic-jobs-overview.md).
- Pour en savoir plus sur les applications SaaS multilocataires, consultez [Modèles de conception pour les applications SaaS multilocataires](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Comment déployer l’application SaaS Wingtip Tickets.
> - Explorer les serveurs, les pools et les bases de données qui composent l’application.
> - Identifier comment les locataires sont mappés à leurs données grâce au *catalogue*.
> - Approvisionner les nouveaux locataires.
> - Afficher l’utilisation du pool pour surveiller l’activité des locataires.
> - Comment supprimer les exemples de ressources pour arrêter la facturation associée.

Ensuite, essayez le [didacticiel sur le provisionnement et l’inscription dans le catalogue](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

