---
title: "Provisionner et inscrire dans un catalogue de nouveaux locataires dans une application SaaS utilisant une base de données Azure SQL Database multilocataire | Microsoft Docs"
description: "Découvrez comment approvisionner et cataloguer de nouveaux locataires dans une application SaaS multilocataire Azure SQL Database."
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: billgib
ms.openlocfilehash: ec753027c8ce8040cbc574279a44eb24590fcb05
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-sql-database"></a>Provisionner et inscrire dans un catalogue de nouveaux locataires dans une application SaaS utilisant une base de données Azure SQL Database multilocataire

Dans ce didacticiel, vous découvrez des modèles qui permettent de provisionner des locataires et de les inscrire dans un catalogue quand vous utilisez un modèle de base de données multilocataire partitionnée. 

Un schéma multilocataire, qui comprend un ID de locataire dans la clé primaire des tables contenant les données de locataire, permet de stocker plusieurs locataires dans une seule base de données. Pour prendre en charge un grand nombre de locataires, les données de locataire sont réparties sur plusieurs partitions ou bases de données. Les données d’un seul locataire sont toujours contenues entièrement dans une base de données.  Un catalogue est utilisé pour contenir le mappage des locataires aux bases de données.   

Vous pouvez également choisir de remplir certaines bases de données avec un seul locataire. Les bases de données qui contiennent plusieurs locataires proposent un coût inférieur par locataire en contrepartie du partage de l’espace.  Les bases de données qui contiennent un seul locataire privilégient l’isolement par rapport au coût.  Vous pouvez avoir à la fois des bases de données multilocataires et des bases de données à un seul locataire dans la même application SaaS pour optimiser les coûts ou l’isolement de chaque locataire. Les locataires peuvent recevoir leur propre base de données au moment du provisionnement ou être déplacés sur leur propre base de données plus tard.

   ![Application de base de données multilocataire partitionnée avec un catalogue de locataires](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)


## <a name="tenant-catalog-pattern"></a>Modèle du catalogue de locataires

Si les données de locataire sont réparties sur plusieurs bases de données, vous devez savoir où sont stockées les données de chaque locataire. Une base de données de catalogue contient le mappage entre un locataire et la base de données qui stocke ses données.

Chaque locataire est identifié par une clé dans le catalogue. Dans les applications SaaS Wingtip Tickets, la clé de locataire est générée à partir d’un hachage du nom du locataire. Cela permet à l’application d’extraire le nom de locataire à partir d’une URL de page web et de l’utiliser pour se connecter à la base de données appropriée. Vous pouvez aussi utiliser d’autres schémas de clé de locataire.

Avec un catalogue, vous pouvez changer le nom ou l’emplacement d’une base de données de locataire après le provisionnement sans interrompre l’application.  Dans un modèle de base de données multilocataire, le catalogue vous permet de déplacer un locataire entre bases de données.  Le catalogue peut aussi être utilisé pour indiquer à une application si un locataire est hors connexion pour une maintenance ou d’autres opérations.

L’utilisation du catalogue peut être étendue au stockage de métadonnées supplémentaires (de locataire ou de base de données), comme le niveau ou la version d’une base de données, la version du schéma de base de données, le nom du locataire ou le plan de service ou SLA, ainsi que d’autres informations liées à la gestion des applications, au support client ou aux processus devops.  

Le catalogue peut aussi servir à activer la création de rapport pour plusieurs locataires, la gestion des schémas et l’extraction de données à des fins d’analytique. 

### <a name="elastic-database-client-library"></a>Bibliothèque cliente de base de données élastique 
Dans les applications SaaS Wingtip Tickets, le catalogue est implémenté dans la base de données *tenantcatalog* à l’aide des fonctionnalités de gestion de partitions de la [bibliothèque cliente de bases de données élastiques](sql-database-elastic-database-client-library.md). La bibliothèque permet à une application de créer, gérer et utiliser une carte de partitions reposant sur des bases de données. Une carte de partitions contient la liste des partitions (bases de données) et le mappage entre les clés (ID de locataire) et les partitions.  Les fonctions de la bibliothèque cliente de bases de données élastiques peuvent être utilisées dans des applications ou des scripts PowerShell pendant le provisionnement des locataires pour créer les entrées dans la carte de partitions et, ensuite, pour se connecter à la base de données appropriée. La bibliothèque met en cache les informations de connexion pour réduire le trafic sur la base de données de catalogue et accélérer la connexion. 

> [!IMPORTANT]
> Bien que les données de mappage soient accessibles dans la base de données de catalogue, *ne les modifiez pas !* Modifiez les données de mappage des API Elastic Database Client Library uniquement. Manipuler directement les données de mappage risque d’endommager le catalogue et n’est pas pris en charge.


## <a name="tenant-provisioning-pattern"></a>Modèle de provisionnement des locataires

Quand vous provisionnez un nouveau locataire dans le modèle de base de données multilocataire partitionnée, vous devez d’abord déterminer si le locataire doit être provisionné dans une base de données partagée ou dans sa propre base de données. En cas de base de données partagée, vous devez déterminer s’il reste de l’espace dans une base de données existante ou s’il faut créer une base de données. Si vous devez créer une base de données, elle doit être provisionnée dans le niveau de service et l’emplacement appropriés, initialisée avec les données de référence et le schéma appropriés, puis inscrite dans le catalogue. Enfin, le mappage de locataire peut être ajouté pour référencer la partition appropriée.

Provisionnez la base de données en exécutant des scripts SQL, en déployant un fichier bacpac ou en copiant une base de données de modèle. Les applications SaaS Tickets Wingtip copient une base de données de modèle pour créer des bases de données de locataire.

La méthode de provisionnement que vous utilisez doit respecter votre stratégie globale de gestion des schémas, laquelle doit vérifier que les nouvelles bases de données sont provisionnées avec le schéma le plus récent.  Plus de détails sont disponibles dans le [didacticiel sur la gestion des schémas](saas-tenancy-schema-management.md).  

Les scripts de provisionnement de locataire dans ce didacticiel incluent à la fois le provisionnement d’un locataire dans une base de données existante partagée avec d’autres locataires et le provisionnement d’un locataire dans sa propre base de données. Les données de locataire sont ensuite initialisées et inscrites dans la carte de partitions du catalogue. Dans l’exemple d’application, les bases de données contenant plusieurs locataires reçoivent un nom générique, comme *tenants1*, *tenants2*, etc., tandis que les bases de données contenant un seul client reçoivent le nom du locataire. Les conventions de nommage spécifiques utilisées dans l’exemple ne sont pas un élément essentiel du modèle, car l’utilisation d’un catalogue permet d’attribuer n’importe quel nom à la base de données.  

## <a name="provision-and-catalog-tutorial"></a>Didacticiel sur le provisionnement et l’inscription dans le catalogue

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]

> * Provisionner un locataire dans une base de données multilocataire
> * Provisionner un locataire dans une base de données à un seul locataire
> * Provisionner un groupe de locataires dans des bases de données à la fois multilocataires et à locataire unique
> * Inscrire un mappage base de données/locataire dans un catalogue

Pour suivre ce tutoriel, vérifiez que les conditions préalables suivantes sont bien satisfaites :

* L’application de base de données multilocataire SaaS Wingtip Tickets est déployée. Pour effectuer le déploiement en moins de cinq minutes, consultez [Déployer et explorer l’application de base de données multilocataire SaaS Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="get-the-wingtip-tickets-management-scripts"></a>Obtenir les scripts de gestion Wingtip Tickets

Le code source de l’application et les scripts de gestion sont disponibles dans le dépôt GitHub [WingtipTicketsSaaS-MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB). <!--See [Steps to download the Wingtip SaaS scripts](saas-tenancy-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts).-->


## <a name="provision-a-tenant-in-a-shared-database-with-other-tenants"></a>Provisionner un locataire dans une base de données partagée avec d’autres locataires

Pour comprendre comment l’application Wingtip Tickets implémente le provisionnement d’un nouveau locataire dans une base de données partagée, ajoutez un point d’arrêt et suivez les étapes du flux de travail :

1. Dans _PowerShell ISE_, ouvrez ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ et définissez les paramètres suivants :
   * **$TenantName** = **Bushwillow Blues**, nom du nouveau lieu.
   * **$VenueType** = **blues**, un des types prédéfinis de décor : *blues*, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (en minuscules, sans espace).
   * **$Scenario** = **1**, pour *Provisionner un locataire dans une base de données partagée avec d’autres locataires*.

1. Ajoutez un point d’arrêt en plaçant votre curseur n’importe où sur la ligne 38 qui indique *New-Tenant `*, puis appuyez sur **F9**.

   ![point d’arrêt](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

1. Pour exécuter le script, appuyez sur la touche **F5**.

1. Une fois que l’exécution du script s’arrête au point d’arrêt, appuyez sur **F11** pour effectuer un pas à pas détaillé du code.

   ![debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

Suivez l’exécution du script à l’aide des options du menu **Débogage**, **F10** et **F11**, pour parcourir les fonctions appelées. Pour plus d’informations sur le débogage des scripts PowerShell, consultez [Conseils sur l’utilisation et le débogage de scripts PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Voici des étapes clés du flux de travail de provisionnement :

* **Calculez la clé du nouveau locataire**. Une fonction de hachage est utilisée pour créer la clé de locataire à partir du nom du locataire.
* **Vérifiez si la clé de locataire existe déjà**. Le catalogue est examiné pour vérifier que la clé n’a pas déjà été inscrite.
* **Initialiser le locataire dans la base de données de locataire par défaut**. La base de données de locataire est mise à jour pour ajouter les informations du nouveau locataire.  
* **Inscrire le locataire dans le catalogue** Le mappage entre la clé du nouveau locataire et la base de données tenants1 existante est ajouté au catalogue. 
* **Ajouter le nom du locataire à une table d’extension de catalogue**. Le nom du lieu est ajouté à la table Tenants dans le catalogue.  Voici comment la base de données de catalogue peut être étendue pour prendre en charge des données supplémentaires spécifiques de l’application.
* **Ouvrir la page Événements du nouveau locataire**. La page des événements *Bushwillow Blues* est ouverte dans le navigateur :

   ![événements](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)


## <a name="provision-a-tenant-in-its-own-database"></a>Provisionner un locataire dans sa propre base de données

Suivons maintenant les étapes de création d’un locataire dans sa propre base de données :

1. Toujours dans ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_, définissez les paramètres suivants :
   * **$TenantName** = **Sequoia Soccer**, le nom du nouveau lieu.
   * **$VenueType** = **soccer**, un des types prédéfinis de décor : blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, *soccer* (en minuscules, sans espace).
   * **$Scenario** = **2**, pour *Provisionner un locataire dans une base de données partagée avec d’autres locataires*.

1. Ajoutez un nouveau point d’arrêt en plaçant votre curseur n’importe où sur la ligne 57 qui indique *&&nbsp;$PSScriptRoot\New-TenantAndDatabase `*, et appuyez sur **F9**.

   ![point d’arrêt](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

1. Appuyez sur **F5** pour exécuter le script.

1. Une fois que l’exécution du script s’arrête au point d’arrêt, appuyez sur **F11** pour effectuer un pas à pas détaillé du code.  Utilisez **F10** et **F11** pour parcourir les fonctions et suivre l’exécution.

Voici des étapes clés du flux de travail pendant le suivi de script :

* **Calculez la clé du nouveau locataire**. Une fonction de hachage est utilisée pour créer la clé de locataire à partir du nom du locataire.
* **Vérifiez si la clé de locataire existe déjà**. Le catalogue est examiné pour vérifier que la clé n’a pas déjà été inscrite.
* **Créer une base de données de locataire**. La base de données est créée en copiant la base de données *basetenantdb* à l’aide d’un modèle Resource Manager.  Le nom de la nouvelle base de données est basé sur le nom du locataire.
* **Ajouter la base de données au catalogue**. La nouvelle base de données de locataire est inscrite sous forme de partition dans le catalogue.
* **Initialiser le locataire dans la base de données de locataire par défaut**. La base de données de locataire est mise à jour pour ajouter les informations du nouveau locataire.  
* **Inscrire le locataire dans le catalogue** Le mappage entre la clé du nouveau locataire et la base de données *sequoiasoccer* est ajouté au catalogue.
* **Le nom du locataire est ajouté au catalogue**. Le nom du lieu est ajouté à la table d’extension Tenants dans le catalogue.
* **Ouvrir la page Événements du nouveau locataire**. La page des événements *Sequoia Soccer* est ouverte dans le navigateur :

   ![événements](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)


## <a name="provision-a-batch-of-tenants"></a>Approvisionner un lot de locataires

Cet exercice permet d’approvisionner un lot de 17 clients. Nous vous recommandons de provisionner ce groupe de locataires avant de suivre d’autres didacticiels Wingtip Tickets pour pouvoir utiliser plusieurs bases de données.

1. Dans *PowerShell ISE*, ouvrez...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* et définissez le paramètre *$Scenario* sur 3 :
   * **$Scenario** = **3**, sur *Provisionner un groupe de locataires dans une base de données partagée*.
1. Appuyez sur **F5** pour exécuter le script.


### <a name="verify-the-deployed-set-of-tenants"></a>Vérifier le groupe de locataires déployé 
À ce stade, vous avez des locataires déployés dans une base de données partagée et des locataires déployés dans leur propre base de données. Vous pouvez utiliser le portail Azure pour inspecter les bases de données créées :  

* Dans le [portail Azure](https://portal.azure.com), ouvrez le serveur **tenants1-mt-\<USER\>** en parcourant la liste des serveurs SQL Server.  La liste des **bases de données SQL** doit inclure la base de données **tenants1** partagée et les bases de données des locataires qui ont leur propre base de données :

   ![liste de base de données](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Bien que le portail Azure affiche les bases de données de locataire, vous ne pouvez pas voir les locataires *dans* la base de données partagée. La liste complète des locataires peut être consultée dans la page du hub d’événements Wingtip Tickets et en parcourant le catalogue :   

1. Ouvrez la page Hub d’événements dans le navigateur (http:events.wingtip-mt.\<USER\>.trafficmanager.net)  

   La liste complète des locataires et leur base de données correspondante est disponible dans le catalogue. Dans la base de données tenantcatalog, une vue SQL associe le nom du locataire stocké dans la table Tenants au nom de base de données dans les tables de gestion de partitions. Cette vue montre clairement l’intérêt d’étendre les métadonnées stockées dans le catalogue.

2. Dans *SQL Server Management Studio (SSMS)*, connectez-vous au serveur Tenants à l’adresse **tenants1-mt.\<USER\>.database.windows.net**, avec l’ID de connexion **developer** et le mot de passe **P@ssword1**

    ![Boîte de dialogue de connexion de SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. Dans *l’Explorateur d’objets*, accédez aux vues de la base de données *tenantcatalog*.
2. Cliquez avec le bouton droit sur la vue *TenantsExtended* et choisissez **Sélectionner les 1 000 premières lignes**. Notez le mappage entre le nom de locataire et la base de données pour les différents locataires.

    ![Vue ExtendedTenants dans SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Autres modèles d’approvisionnement

Voici d’autres modèles de provisionnement intéressants :

**Préprovisionnement de bases de données dans des pools élastiques** Le modèle de préprovisionnement exploite le fait que, quand vous utiliser des pools élastiques, la facturation s’applique au niveau du pool et non au niveau des bases de données. Par conséquent, des bases de données peuvent être ajoutées à un pool élastique sans qu’elles soient nécessaires, sans entraîner de frais supplémentaires. Quand vous préprovisionnez des bases de données dans un pool et que vous les allouez selon vos besoins, le délai de provisionnement d’un locataire dans une base de données peut être considérablement réduit. Le nombre de bases de données pré-approvisionnées peut être ajusté en fonction des besoins pour conserver une mémoire tampon adaptée au taux d’approvisionnement prévu.

**Approvisionnement automatique.** Dans le modèle de provisionnement automatique, un service de provisionnement dédié est utilisé pour provisionner automatiquement des serveurs, pools et bases de données en fonction des besoins, y compris le préprovisionnement de bases de données dans des pools élastiques. Si les bases de données sont mises hors service et supprimées, les écarts dans les pools élastiques peuvent être comblés par le service de provisionnement en fonction des besoins. Un tel service peut être simple ou complexe (par exemple, la gestion de l’approvisionnement sur plusieurs zones géographiques) et peut configurer la géoréplication pour la récupération d’urgence. Avec le modèle de provisionnement automatique, une application cliente ou un script envoie une demande de provisionnement à une file d’attente de traitement d’un service de provisionnement, puis interroge le service pour déterminer l’achèvement de l’opération. Si vous utilisez le préprovisionnement, les demandes sont gérées rapidement, car un autre service gère le provisionnement d’une base de données de remplacement en arrière-plan.



## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Provisionner un nouveau locataire unique dans une base de données multilocataire partagée et dans sa propre base de données
> * Approvisionner un lot de locataires supplémentaires
> * Suivre les étapes de provisionnement des locataires et de leur inscription dans le catalogue

Essayez le [didacticiel Surveillance des performances](saas-multitenantdb-performance-monitoring.md).

## <a name="additional-resources"></a>Ressources supplémentaires

<!--* Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Bibliothèque cliente de base de données élastique](sql-database-elastic-database-client-library.md)
* [Déboguer les scripts dans l’ISE Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
