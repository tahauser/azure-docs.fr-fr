---
title: "Didacticiel SaaS du modèle de base de données par locataire - Azure SQL Database | Microsoft Docs"
description: "Déployez et explorez l’application multi-locataire Wingtip Tickets SaaS, qui illustre le modèle de base de données par locataire et d’autres modèles SaaS, en utilisant Azure SQL Database."
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: genemi
ms.openlocfilehash: 5342b5290fab9826a2b38cd7ada63a6736c77601
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Déployer et explorer une application multi-locataire SaaS qui illustre le modèle de base de données par locataire avec Azure SQL Database

Dans ce didacticiel, vous allez déployer et explorer l’application de *base de données par locataire* Wingtip Tickets SaaS. L’application utilise un modèle de base de données par locataire pour stocker les données de plusieurs locataires. L’application est conçue pour tirer parti des fonctionnalités Azure SQL Database qui simplifient l’activation des scénarios SaaS.

Cinq minutes après avoir cliqué sur le bouton bleu nommé **Déployer sur Azure**, vous avez une application SaaS multi-locataire. Cette application inclut l’exécution d’Azure SQL Database dans le cloud Microsoft Azure. Elle est déployée avec trois exemples de locataire, chacun avec sa propre base de données. Toutes les bases de données sont déployés sur un *pool élastique* SQL. L’application est déployée sur votre abonnement Azure. Vous avez un accès complet pour explorer et utiliser les composants individuels de l’application. Le code source en C# de l’application et les scripts de gestion sont disponibles dans le [dépôt GitHub WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt].

Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Déployer l’application Wingtip SaaS.
> - Obtenir le code source de l’application et les scripts de gestion.
> - Explorer les serveurs, les pools et les bases de données qui composent l’application.
> - Identifier comment les locataires sont mappés à leurs données grâce au *catalogue*.
> - Approvisionner un nouveau locataire.
> - Surveiller l’activité d’un locataire dans l’application.

Une [série de didacticiels associés](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) vous propose d’explorer les divers modèles de conception et de gestion SaaS. Ces didacticiels vont au-delà de ce déploiement initial.
Lorsque vous utilisez les didacticiels, vous pouvez voir comment les différents modèles SaaS sont implémentés en examinant les scripts fournis.
Ces scripts montrent comment les fonctionnalités de SQL Database simplifient le développement des applications SaaS.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vérifiez que les prérequis suivants sont remplis :

* Azure PowerShell est installé. Pour plus d’informations, consultez [Prise en main d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Déployer l’application Wingtip Tickets SaaS

Déployer l’application :

1. Choisissez et mémorisez les valeurs dont vous avez besoin pour les paramètres suivants :

    - **Utilisateur** : choisissez une valeur courte, comme vos initiales suivi d’un chiffre. Par exemple, *af1*. Le paramètre Utilisateur peut contenir uniquement des lettres, des chiffres et des traits d’union (sans espaces). Le premier et le dernier caractères doivent être une lettre ou un chiffre. Nous recommandons de mettre toutes les lettres en minuscules.
    - **Groupe de ressources** : chaque fois que vous déployez l’application Wingtip, vous devez choisir un autre nom unique pour le nouveau groupe de ressources. Nous vous recommandons d’inclure le nom de l’utilisateur à un nom de base pour le groupe de ressources. Par exemple, un nom de groupe de ressources peut être *wingtip-af1*. Là encore, nous recommandons de mettre toutes les lettres en minuscules.

2. Ouvrez le modèle de déploiement de base de données par locataire Wingtip Tickets SaaS dans le portail Azure, en cliquant sur le bouton bleu **Déployer sur Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

3. Dans le modèle, entrez les valeurs des paramètres obligatoires :

    > [!IMPORTANT]
    > Certaines authentifications et pare-feu de serveur sont volontairement non sécurisés à des fins de démonstration. Nous vous recommandons de *créer un groupe de ressources*. N’utilisez pas de groupes de ressources, serveurs ou pools existants. N’utilisez pas cette application, les scripts ou des ressources déployées pour la production. Supprimez ce groupe de ressources lorsque vous en avez terminé avec l’application pour interrompre la facturation associée.

    - **Groupe de ressources** : sélectionnez **Création** et indiquez le **nom** unique que vous avez auparavant choisi pour le groupe de ressources. 
    - **Emplacement** : sélectionnez un **emplacement** dans la liste déroulante.
    - **Utilisateur** : utilisez la valeur du nom d’utilisateur que vous avez choisi précédemment.

4. Déployez l’application.

    - Cliquez pour accepter les conditions générales.
    - Cliquez sur **Achat**.

5. Surveillez l’état du déploiement en cliquant sur **Notifications**, l’icône représentant une cloche à droite de la zone de recherche. Le déploiement de l’application Wingtip Tickets SaaS dure environ cinq minutes.

   ![déploiement réussi](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Télécharger et débloquer les scripts de gestion Wingtip Tickets

Lors du déploiement de l’application, téléchargez le code source et les scripts de gestion.

> [!IMPORTANT]
> Le contenu exécutable (scripts, DLL) peut être bloqué par Windows lorsque des fichiers .zip sont téléchargés à partir d’une source externe puis extraits. Lorsque vous extrayez les scripts d’un fichier zip, utilisez les étapes suivantes pour débloquer le fichier .zip avant l’extraction. Ce déblocage garantit que les scripts sont autorisés à s’exécuter.

1. Accédez au [dépôt GitHub WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt].
2. Cliquez sur **Cloner ou télécharger**.
3. Cliquez sur **Télécharger ZIP**, puis enregistrez le fichier.
4. Cliquez avec le bouton droit sur le fichier **WingtipTicketsSaaS-DbPerTenant-master.zip**, puis sélectionnez **Propriétés**.
5. Sous l’onglet **Général**, sélectionnez **Débloquer**, puis cliquez sur **Appliquer**.
6. Cliquez sur **OK**.
7. Procédez à l’extraction des fichiers.

Les scripts se trouvent dans le dossier *..\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules*.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Mettre à jour le fichier de configuration utilisateur pour ce déploiement

Avant d’exécuter des scripts, mettez à jour les valeurs *resource group* et *user* dans **UserConfig.psm1**. Pour ces variables, utilisez les valeurs que vous avez définies pendant le déploiement.

1. Dans *PowerShell ISE*, ouvrez ...\\Learning Modules\\*UserConfig.psm1*. 
2. Mettez à jour *ResourceGroupName* et *Name* avec les valeurs spécifiques à votre déploiement (lignes 10 et 11 uniquement).
3. Enregistrez les modifications !

Ces valeurs sont référencées dans la quasi-totalité des scripts.

## <a name="run-the-application"></a>Exécution de l'application

L’application présente les lieux qui hébergent des événements. Les types de lieu incluent des salles de concert, des clubs de jazz et des salles de sport. Dans Wingtip Tickets, les lieux sont enregistrés en tant que locataires. En étant locataire, un lieu obtient un moyen simple de répertorier des événements et de vendre des billets à ses clients. Chaque lieu obtient un site web personnalisé pour répertorier ses événements et vendre des billets.

En interne dans l’application, chaque locataire obtient une base de données SQL déployée sur un pool élastique SQL.

Une page **Events Hub** centrale fournit une liste de liens vers les locataires de votre déploiement.

1. Ouvrez la page *Events Hub* dans votre navigateur web en utilisant l’adresse http://events.wingtip-dpt.&lt;UTILISATEUR&gt;.trafficmanager.net et en remplaçant &lt;UTILISATEUR&gt; par la valeur Utilisateur de votre déploiement :

    ![events hub](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Cliquez sur **Fabrikam Jazz Club** dans le *concentrateur d’événements*.

    ![Événements](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure Traffic Manager

L’application Wingtip utilise [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md) pour contrôler la distribution des requêtes entrantes. L’URL d’accès au hub d’événements d’un seul locataire doit respecter le format suivant :

- http://events.wingtip-dpt.&lt;UTILISATEUR&gt;.trafficmanager.net/fabrikamjazzclub

Les parties du format précédent sont expliquées dans le tableau suivant.

| Partie de l’URL | Description |
| :------- | :---------- |
| http://events.wingtip-dpt | Parties des événements de l’application Wingtip.<br /><br />La portion ***-dpt*** permet de distinguer l’implémentation *Base de données par locataire* de Wingtip des autres implémentations légèrement différentes. Par exemple, d’autres articles de documentation proposent Wingtip pour *Standalong* (*-sa*) ou pour *Base de données multi-locataire*. |
| .*&lt;UTILISATEUR&gt;* | *af1* dans notre exemple. |
| .trafficmanager.net/ | URL de base Azure Traffic Manager. |
| fabrikamjazzclub | Pour le locataire nommé *Fabrikam Jazz Club*. |
| &nbsp; | &nbsp; |

1. Le nom du locataire est analysé à partir de l’URL, par l’application des événements.
2. Le nom du locataire est utilisé pour créer une clé.
3. La clé est utilisée pour accéder au catalogue, afin d’obtenir l’emplacement de la base de données du locataire.
    - Le catalogue est implémenté à l’aide de la *gestion des cartes de partitions*.
4. Le *hub d’événements* utilise les métadonnées étendues dans le catalogue pour obtenir la liste des URL d’événement.

Dans un environnement de production, vous créez généralement un enregistrement DNS CNAME pour [*pointer un domaine Internet d’entreprise*](../traffic-manager/traffic-manager-point-internet-domain.md) vers le profil Traffic Manager.

## <a name="start-generating-load-on-the-tenant-databases"></a>Démarrer la génération de charge sur les bases de données de locataires

Maintenant que l’application est déployée, nous allons l’exécuter !
Le script PowerShell *Demo-LoadGenerator* démarre une charge de travail qui s’exécute sur toutes les bases de données de locataires.
La charge réelle sur de nombreuses applications SaaS est sporadique et imprévisible.
Pour simuler ce type de charge, le générateur produit une charge avec des pics aléatoires ou des pics d’activité sur chaque locataire.
Les pics se produisent à intervalles aléatoires.
Plusieurs minutes sont nécessaires pour que le modèle de charge émerge. Il est donc préférable de laisser le générateur s’exécuter pendant au moins trois ou quatre minutes avant de surveiller la charge.

1. Dans *PowerShell ISE*, ouvrez le script... \\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
2. Appuyez sur **F5** pour exécuter le script et démarrer le générateur de charge. (Laissez les valeurs de paramètre par défaut pour le moment.)

Ne réutilisez pas la même instance de PowerShell ISE pour autre chose qu’une réexécution de *Demo-LoadGenerator.ps1*. Si vous avez besoin d’exécuter d’autres scripts PowerShell, démarrez PowerShell ISE séparément.

#### <a name="rerun-with-different-parameters"></a>Réexécuter avec des paramètres différents

Effectuez les étapes suivantes si vous voulez réexécuter le test de charge de travail avec des paramètres différents :

1. Arrêtez *LoadGenerator.ps1*.
    - Utilisez **Ctrl+C** ou cliquez sur le bouton **Arrêter**.
    - Cet arrêt n’interrompt pas et n’affecte pas les tâches en arrière-plan incomplètes toujours en cours d’exécution.

2. Réexécutez *Demo-LoadGenerator.ps1*.
    - Cette réexécution commence par interrompre toutes les tâches en arrière-plan susceptibles d’exécuter encore *sp_CpuLoadGenerator*.

Ou vous pouvez mettre fin à l’instance de PowerShell ISE, ce qui arrête toutes les tâches en arrière-plan. Ensuite, démarrez une nouvelle instance de PowerShell ISE et réexécutez *Demo-LoadGenerator.ps1*.

#### <a name="monitor-the-background-jobs"></a>Surveiller les tâches en arrière-plan

Si vous souhaitez contrôler et surveiller les tâches en arrière-plan, vous pouvez utiliser les applets de commande suivantes :

- Get-Job
- Receive-Job
- Stop-Job

#### <a name="demo-loadgeneratorps1-actions"></a>Actions de Demo-LoadGenerator.ps1

*Demo-LoadGenerator.ps1* reproduit une charge de travail active des transactions clientes. Les étapes suivantes décrivent la séquence d’actions lancée par *Demo-LoadGenerator.ps1* :

1. *Demo-LoadGenerator.ps1* démarre *LoadGenerator.ps1* au premier plan.
    - Ces deux fichiers .ps1 sont stockés dans les dossiers *Modules d’apprentissage\\Utilitaires\\*.

2. *LoadGenerator.ps1* effectue une boucle sur toutes les bases de données locataires enregistrées dans le catalogue.

3. Pour chaque base de données locataire, *LoadGenerator.ps1* démarre une exécution de la procédure stockée Transact-SQL nommée *sp_CpuLoadGenerator*.
    - Les exécutions sont démarrées en arrière-plan, en appelant l’applet de commande *Invoke-SqlAzureWithRetry*.
    - *sp_CpuLoadGenerator* effectue une boucle sur une instruction SQL SELECT pendant une durée par défaut de 60 secondes. L’intervalle de temps entre les problèmes de l’instruction SELECT varie en fonction des valeurs du paramètre.
    - Ce fichier .sql est stocké sous *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Pour chaque base de données locataire, *LoadGenerator.ps1* démarre également l’applet de commande *Start-Job*.
    - *Start-Job* reproduit une charge de travail de vente de tickets.

5. *LoadGenerator.ps1* continue à s’exécuter, en surveillant tous les nouveaux locataires provisionnés.

&nbsp;

Avant de passer à la section suivante, laissez le générateur de charge en cours d’exécution dans l’état d’appel de tâche.

## <a name="provision-a-new-tenant"></a>Provisionner un nouveau locataire

Le déploiement initial crée trois exemples de locataire. Maintenant, vous créez un autre locataire pour voir son impact sur l’application déployée. Dans l’application Wingtip, le flux de travail permettant de provisionner les nouveaux locataires est expliqué dans le [didacticiel sur le provisionnement et l’inscription dans le catalogue](saas-dbpertenant-provision-and-catalog.md). Au cours de cette phase, vous créez un locataire, ce qui prend moins d’une minute.

1. Dans *PowerShell ISE*, ouvrez ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*.
2. Appuyez sur **F5** pour exécuter le script. (Laissez les valeurs par défaut pour le moment.)

   > [!NOTE]
   > De nombreux scripts Wingtip SaaS utilisent *$PSScriptRoot* pour parcourir les dossiers afin d’appeler les fonctions d’autres scripts. Cette variable est évaluée uniquement lorsque le script entier est exécuté en appuyant sur **F5**.  La mise en surbrillance et l’exécution d’une sélection avec la touche **F8** peut engendrer des erreurs. Exécutez donc les scripts plutôt en appuyant sur **F5**.

La nouvelle base de données locataire est :

- Créée dans un pool élastique SQL.
- Initialisée.
- Inscrite dans le catalogue.

Une fois le provisionnement réussi, le site *Événements* du nouveau locataire s’affiche dans votre navigateur :

![Nouveau locataire](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Actualisez le *Hub d’événements* pour faire apparaître le nouveau locataire dans la liste.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorer les serveurs, les pools et les bases de données de locataires

Maintenant que vous avez démarré une charge dans le regroupement de locataires, examinons quelques-unes des ressources qui ont été déployées :

1. Dans le [portail Azure](http://portal.azure.com), accédez à la liste des serveurs SQL et ouvrez le serveur **catalog-dpt-&lt;UTILISATEUR&gt;**.
    - Le serveur de catalogue contient deux bases de données : **tenantcatalog** et **basetenantdb** (un modèle de base de données copié pour créer des locataires).

   ![bases de données](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Revenez à votre liste de serveurs SQL.

3. Ouvrez le serveur **tenants1-dpt-&lt;UTILISATEUR&gt;** qui contient les bases de données locataires.

4. Observez les points suivants :
    - Chaque base de données de locataires est une base de données *élastique standard* dans un pool standard de 50 eDTU.
    - Base de données *Red Maple Racing*, qui est la base de données locataire que vous avez précédemment provisionnée.

   ![serveur](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Surveiller le pool

Au bout de quelques minutes d’exécution de *LoadGenerator.ps1*, une quantité suffisante de données est disponible pour vous permettre de découvrir certaines fonctionnalités de surveillance. Ces fonctionnalités sont intégrées dans les pools et les bases de données.

Accédez au serveur **tenants1-dpt-&lt;UTILISATEUR&gt;**, puis cliquez sur **Pool1** pour afficher l’utilisation des ressources du pool. Dans les graphiques suivants, le générateur de charge s’est exécuté pendant une heure.

   ![surveiller un pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Le premier graphique, intitulé **Utilisation des ressources**, montre l’utilisation eDTU du pool.
- Le second graphique montre l’utilisation eDTU des cinq premières bases de données du pool.

Ces deux graphiques illustrent bien que les pools élastiques et SQL Database sont parfaitement adaptés aux charges de travail de l’application SaaS.
Les graphiques montrent que 4 bases de données ont chacune des pics d’activité allant jusqu’à 40 eDTU, et pourtant toutes les bases de données sont confortablement prises en charge par un pool de 50 eDTU. Le pool de 50 eDTU peut prendre en charge des charges de travail encore plus lourdes.
Si elles ont été provisionnées en tant que bases de données autonomes, elles devront être de type S2 (50 DTU) pour pouvoir gérer les pics d’activité.
Le coût de 4 bases de données S2 autonomes s’élèverait à presque 3 fois celui du pool.
Dans des situations réelles, les clients SQL Database exécutent jusqu'à 500 bases de données dans des pools de 200 eDTU.
Pour plus d’informations, consultez le [didacticiel sur la surveillance des performances](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ressources supplémentaires

- Autres [didacticiels reposant sur l’application de base de données par locataire Wingtip Tickets SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- Pour de plus amples informations sur les pools élastiques, consultez [*Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données SQL*](sql-database-elastic-pool.md).
- Pour de plus amples informations sur les travaux élastiques, consultez [*Gestion des bases de données cloud avec montée en charge*](sql-database-elastic-jobs-overview.md).
- Pour de plus amples informations sur les applications SaaS mutualisées, consultez [*Modèles de conception pour les applications SaaS mutualisées et Base de données SQL Azure*](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Déployer l’application Wingtip Tickets SaaS
> - Explorer les serveurs, les pools et les bases de données qui composent l’application
> - Explorer les locataires qui sont mappés à leurs données avec le *catalogue*
> - Approvisionner les nouveaux locataires
> - Afficher l’utilisation du pool pour surveiller l’activité du locataire
> - Supprimer les exemples de ressources pour arrêter la facturation associée

Ensuite, essayez le [didacticiel sur le provisionnement et l’inscription dans le catalogue](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

