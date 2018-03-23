---
title: Approvisionner de nouveaux locataires dans une application mutualisée utilisant Azure SQL Database | Microsoft Docs
description: Découvrez comment approvisionner et cataloguer de nouveaux locataires dans une application SaaS multilocataire Azure SQL Database.
keywords: didacticiel sur les bases de données SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: 21f0bca3a16164ead4e0990842a968fd9b95c33f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Découvrez comment approvisionner de nouveaux locataires et les inscrire dans le catalogue

Ce didacticiel décrit les modèles SaaS d’approvisionnement et d’inscription au catalogue et comment ils sont implémentés dans l’application Wingtip Tickets SaaS Database per Tenant. Vous allez créer et initialiser de nouvelles bases de données de clients et les enregistrer dans le catalogue de clients de l’application. Le catalogue est une base de données qui gère le mappage entre les nombreux clients des applications SaaS et les données associées. Le catalogue joue un rôle important, car il dirige les demandes de l’application et de gestion vers la base de données appropriée.  

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]

> * Approvisionner un nouveau locataire unique
> * Approvisionner un lot de locataires supplémentaires


Pour suivre ce didacticiel, vérifiez que les prérequis suivants sont remplis :

* L’application de base de données Wingtip Tickets SaaS par client est déployée. Pour procéder à un déploiement en moins de cinq minutes, consultez [Déployer et explorer l’application Wingtip Tickets SaaS Database per Tenant](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell est installé. Pour plus d’informations, voir [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Présentation du modèle de catalogue SaaS

Dans une application SaaS mutualisée appuyée par une base de données, il est important de savoir où sont stockées les informations de chaque locataire. Dans le modèle de catalogue SaaS, une base de données de catalogue permet de conserver le mappage entre les locataires et la base de données dans laquelle sont stockées leurs données. Ce modèle s’applique chaque fois que les données de client sont réparties sur plusieurs bases de données.

Chaque client est identifié par une clé dans le catalogue, qui est mappée à l’emplacement de sa base de données. Dans le cas de l’application Wingtip Tickets, la clé est générée à partir d’un code de hachage du nom du client. Cela permet à l’application de construire la clé à partir du nom de client inclus dans l’URL de l’application. D’autres schémas de clé de client peuvent être utilisés.  

Le catalogue permet de modifier le nom ou l’emplacement de la base de données avec un impact minimal sur l’application.  Dans un modèle de base de données multiclient, il facilite également le « déplacement » d’un client entre les différentes bases de données.  Le catalogue peut également être utilisé pour indiquer si un client ou une base de données est hors connexion pour maintenance ou dans le cadre d’autres opérations. Ce processus est décrit dans le [didacticiel sur la restauration d’un client unique](saas-dbpertenant-restore-single-tenant.md).

Le catalogue peut également peut stocker des métadonnées supplémentaires relatives au client ou à la base de données, telles que la version du schéma, le plan de service ou les SLA proposés aux clients, ainsi que d’autres informations associées à la gestion des applications, au support technique ou à devops.  

Au-delà de l’application SaaS, le catalogue permet d’accéder à des outils de base de données.  Dans l’exemple de l’application Wingtip Tickets SaaS Database per Tenant, le catalogue est utilisé pour activer des requêtes entre locataires, tel que décrit dans le [didacticiel sur les rapports ad hoc](saas-tenancy-cross-tenant-reporting.md). La gestion des travaux entre différentes bases de données est expliquée dans les didacticiels sur la [gestion des schémas](saas-tenancy-schema-management.md) et les [analyses de clients](saas-tenancy-tenant-analytics.md). 

Dans les exemples de Wingtip Tickets SaaS, le catalogue est implémenté à l’aide des fonctionnalités de gestion des partitions de la [bibliothèque EDCL (Elastic Database Client Library, bibliothèque cliente de bases de données élastiques)](sql-database-elastic-database-client-library.md). L’EDCL est disponible dans Java et .Net Framework. La bibliothèque EDCL permet à une application de créer, gérer et utiliser une carte de partitions reposant sur des bases de données. Une carte de partitions contient une liste de partitions (bases de données) et le mappage entre les clés (locataires) et les partitions.  Les fonctions de la bibliothèque cliente de bases de données élastiques sont utilisées pendant le provisionnement des locataires pour créer les entrées dans la carte de partitions et, ensuite, au moment de l’exécution par les applications pour se connecter à la base de données appropriée. La bibliothèque EDCL met en cache les informations de connexion pour réduire le trafic vers la base de données de catalogue et booster les performances de l’application.  

> [!IMPORTANT]
> Bien que les données de mappage soient accessibles dans la base de données de catalogue, *ne les modifiez pas*. Modifiez les données de mappage des API Elastic Database Client Library uniquement. Manipuler directement les données de mappage risque d’endommager le catalogue et n’est pas pris en charge.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Présentation du modèle d’approvisionnement SaaS

Lors de l’intégration d’un nouveau client dans une application SaaS qui utilise un modèle de base de données à client unique, une nouvelle base de données client doit être approvisionnée.  La base de données doit être créée dans l’emplacement et le niveau de service appropriés, initialisée avec les données de référence et le schéma appropriés, puis enregistrée dans le catalogue sous la clé de client correspondante.  

Il existe différentes approches d’approvisionnement des bases de données, notamment l’exécution de scripts SQL, le déploiement d’un fichier bacpac ou la copie d’un modèle de base de données.  

Le provisionnement de bases de données doit faire partie de votre stratégie globale de gestion des schémas, laquelle doit vérifier que les nouvelles bases de données sont provisionnées avec le schéma le plus récent. Cette exigence est expliquée dans le [didacticiel sur la gestion des schémas](saas-tenancy-schema-management.md).  

L’application Wingtip Tickets database per tenant approvisionne les nouveaux locataires en copiant une base de données nommée _basetenantdb_, déployée sur le serveur de catalogue.  L’approvisionnement peut être intégré au processus d’inscription de l’application et/ou pris en charge en mode hors connexion à l’aide de scripts. Ce didacticiel décrit le processus d’approvisionnement à l’aide des scripts PowerShell. Les scripts d’approvisionnement copient la base de données _basetenantdb_ pour créer une nouvelle base de données locataire dans un pool élastique, avant d’initialiser la base de données avec les informations spécifiques au client et de l’inscrire dans la carte de partitions du catalogue.  Les bases de données clientes sont nommées en fonction du nom de client, mais ce schéma d’affectation de noms n’est pas un élément essentiel du modèle : le catalogue mappe la clé de locataire sur le nom de la base de données, aussi toute convention d’affectation de noms peut être utilisée. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenir les scripts de l'application Wingtip Tickets SaaS Database Per Tenant

Les scripts et le code source de l’application Wingtip Tickets SaaS sont disponibles dans le référentiel GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consultez les [conseils généraux](saas-tenancy-wingtip-app-guidance-tips.md) avant de télécharger et de débloquer les scripts Wingtip Tickets SaaS.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Procédure pas à pas détaillée sur l’approvisionnement et l’inscription dans le catalogue

Pour comprendre comment l’application Wingtip Tickets gère l’approvisionnement du nouveau client, ajoutez un point d’arrêt et suivez les étapes du flux de travail dans le cadre de l’approvisionnement d’un client :

1. Dans _PowerShell ISE_, ouvrez ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ et définissez les paramètres suivants :
   * **$TenantName** = nom du nouveau lieu (par exemple *Bushwillow Blues*).
   * **$VenueType** = un des types prédéfinis de décor : _blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer_.
   * **$DemoScenario** = **1**, sur *Approvisionner un seul locataire*.

1. Ajoutez un point d’arrêt en plaçant votre curseur n’importe où sur la ligne qui indique *New-Tenant `*, puis appuyez sur **F9**.

   ![point d’arrêt](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

1. Pour exécuter le script, appuyez sur la touche **F5**.

1. Une fois que l’exécution du script s’arrête au point d’arrêt, appuyez sur **F11** pour effectuer un pas à pas détaillé du code.

   ![débogage](media/saas-dbpertenant-provision-and-catalog/debug.png)



Suivez l’exécution du script à l’aide des options du menu **Débogage** **F10** et **F11** pour parcourir les fonctions invoquées. Pour plus d’informations sur le débogage des scripts PowerShell, consultez [Conseils sur l’utilisation et le débogage de scripts PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Les points suivants ne sont pas des étapes à suivre explicitement, mais constituent une explication du flux de travail parcouru pendant le débogage du script :

1. **Importe le module CatalogAndDatabaseManagement.psm1** qui fournit un catalogue et l’abstraction au niveau du locataire sur les fonctions de [gestion des partitions](sql-database-elastic-scale-shard-map-management.md). Ce module encapsule une bonne partie du modèle de catalogue et que nous vous conseillons d’explorer.
1. **Importe le module SubscriptionManagement.psm1** qui contient des fonctions pour la connexion à Azure et la sélection de l’abonnement Azure que vous utilisez.
1. **Accédez aux détails de configuration**. Accédez à Get-Configuration (avec F11) et découvrez la façon dont la configuration de l’application est spécifiée. Les noms de ressources et d’autres valeurs propres à l’application sont définis ici, mais ne les modifiez pas tant que vous n’êtes pas familiarisé avec les scripts.
1. **Obtenez l’objet catalogue**. Accédez à la fonction Get-Catalogue qui compose et retourne un objet de catalogue utilisé dans le script de niveau supérieur.  Cette fonction utilise les fonctions de gestion de partitions qui sont importées à partir de **AzureShardManagement.psm1**. L’objet de catalogue est composé des éléments suivants :
   * $catalogServerFullyQualifiedName est construit à l’aide de la ressource standard et de votre nom d’utilisateur : _catalog-\<utilisateur\>.database.windows.net_.
   * $catalogDatabaseName est extrait de la configuration : *tenantcatalog*.
   * L’objet $shardMapManager est initialisé à partir de la base de données de catalogue.
   * L’objet $shardMap est initialisé à partir de la carte de partitions _tenantcatalog_ dans la base de données de catalogue.
   Un objet catalogue est composé, retourné et utilisé dans le script de niveau supérieur.
1. **Calculez la clé du nouveau locataire**. Une fonction de hachage est utilisée pour créer la clé de locataire à partir du nom du locataire.
1. **Vérifiez si la clé de locataire existe déjà**. Le catalogue est passé en revue pour vérifier que la clé est disponible.
1. **La base de données de locataire est approvisionnée avec New-TenantDatabase.** Utilisez **F11** pour parcourir les étapes du script et voir la façon dont la base de données est approvisionnée à l’aide d’un modèle [Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Le nom de la base de données est construit à partir du nom de locataire, ce qui permet d’indiquer clairement quelle partition appartient à tel locataire, même si vous pouvez utiliser d’autres conventions d’affectation de noms.
    Un modèle Resource Manager crée une base de données locataire en copiant une base de données modèle (_baseTenantDB_) sur le serveur de catalogue. En guise d’alternative, vous pourriez créer une base de données et l’initialiser en important un fichier bacpac ou en exécutant un script d’initialisation à partir d’un emplacement connu.

    Le modèle Resource Manager est situé dans le dossier ...\Learning Modules\Common\ : *tenantdatabasecopytemplate.json*

1. Une fois que la base de données client a été créée, elle continue à être **initialisée avec le nom du lieu (client) et le type de lieu**. Une autre initialisation peut également être accomplie ici.

1. La **base de données client est inscrite dans le catalogue** avec *Add-TenantDatabaseToCatalog* à l’aide de la clé de client. Utilisez **F11** pour parcourir le script en détail :

    * La base de données de catalogue est ajoutée à la carte de partitions (liste des bases de données connues).
    * Le mappage qui lie la valeur de clé à la partition est créé.
    * Des métadonnées supplémentaires sur le locataire (nom du lieu) sont ajoutées dans le tableau *Clients* du catalogue.  Le tableau Clients ne fait pas partie du schéma ShardManagement et n’est pas installé par la bibliothèque EDCL.  Le tableau suivant illustre comment la base de données de catalogue peut être étendue pour prendre en charge des données supplémentaires spécifiques à l’application.   


Une fois l’approvisionnement terminé, l’exécution retourne au script d’origine *Demo-ProvisionAndCatalog* qui ouvre la page **Events** (Événements) du nouveau client dans le navigateur :

   ![événements](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Approvisionner un lot de locataires

Cet exercice permet d’approvisionner un lot de 17 clients. Il est recommandé d’approvisionner ce lot de locataires avant de suivre d’autres didacticiels Wingtip Tickets SaaS Database per Tenant afin de pouvoir utiliser plusieurs bases de données.

1. Dans *PowerShell ISE*, ouvrez...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* et définissez le paramètre *$DemoScenario* sur 3 :
   * **$DemoScenario** = **3**, sur *Approvisionner un lot de locataires*.
1. Appuyez sur **F5** pour exécuter le script.

Le script déploie un lot de locataires supplémentaires. Il utilise un [modèle Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) qui contrôle le lot et délègue ensuite la configuration de chaque base de données à un modèle lié. Utiliser des modèles de cette façon permet à Azure Resource Manager de répartir le processus d’approvisionnement pour votre script. Les modèles approvisionnent des bases de données et, au besoin, gèrent les nouvelles tentatives. Comme le script est idempotent, en cas d’échec ou d’arrêt pour une raison quelconque, exécutez-le à nouveau.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Vérifier que le lot de locataires a été correctement déployé

* Dans le [portail Azure](https://portal.azure.com), accédez à la liste des serveurs et ouvrez le serveur *tenants1*. Cliquez sur **Bases de données SQL** et vérifiez que le lot de 17 bases de données supplémentaires figure désormais dans la liste :

   ![liste de base de données](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Autres modèles d’approvisionnement

Voici les autres modèles d’approvisionnement non inclus dans ce tutoriel :

**Pré-approvisionnement des bases de données.** Le modèle de pré-approvisionnement exploite le fait que les bases de données d’un pool élastique n’ajoutent pas de frais supplémentaires. La facturation concerne le pool élastique, pas les bases de données, et les bases de données inactives ne consomment aucune ressource. En pré-approvisionnant les bases de données d’un pool et en les allouant en cas de besoin, le délai d’intégration du client peut être considérablement réduit. Le nombre de bases de données pré-approvisionnées peut être ajusté en fonction des besoins pour conserver une mémoire tampon adaptée au taux d’approvisionnement prévu.

**Approvisionnement automatique.** Dans le modèle d’approvisionnement automatique, un service d’approvisionnement approvisionne automatiquement les serveurs, pools et bases de données en fonction des besoins, notamment en pré-approvisionnant les bases de données dans des pools élastiques si besoin. Si les bases de données sont mises hors service et supprimées, les écarts dans les pools élastiques peuvent être remplis par le service d’approvisionnement. Un tel service peut être simple ou complexe (par exemple, la gestion de l’approvisionnement sur plusieurs zones géographiques) et peut configurer la géoréplication pour la récupération d’urgence. Avec le modèle d’approvisionnement automatique, une application cliente ou un script soumet une requête d’approvisionnement à une file d’attente pour traitement par le service d’approvisionnement et interroge ensuite le service pour déterminer l’achèvement de l’opération. Si le pré-approvisionnement est utilisé, les requêtes sont gérées rapidement grâce au service approvisionnant d’une base de données de remplacement en arrière-plan.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Approvisionner un nouveau locataire unique
> * Approvisionner un lot de locataires supplémentaires
> * Parcourir les étapes de l’approvisionnement des locataires et de leur inscription dans le catalogue

Essayez le [didacticiel Surveillance des performances](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* Autres [didacticiels reposant sur l’application Wingtip Tickets SaaS Database per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Bibliothèque cliente de base de données élastique](sql-database-elastic-database-client-library.md)
* [Déboguer les scripts dans l’ISE Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
