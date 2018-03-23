---
title: Effectuer une mise à niveau vers la dernière version de la bibliothèque cliente de bases de données élastiques | Microsoft Docs
description: Utilisez NuGet pour mettre à niveau la bibliothèque cliente de bases de données élastiques.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 03/06/2017
ms.author: sstein
ms.openlocfilehash: 54ddde10935fa5add07aaf0c514cf48c6115678e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Mettre à niveau une application pour utiliser la dernière version de la bibliothèque cliente de bases de données élastiques
Les nouvelles versions de la [bibliothèque cliente de bases de données élastiques](sql-database-elastic-database-client-library.md) sont disponibles via NuGet et l’interface du Gestionnaire de package NuGet dans Visual Studio. Les mises à niveau contiennent des résolutions de bogues et offrent une prise en charge des nouvelles fonctionnalités de la bibliothèque cliente.

**Pour la version la plus récente :** accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Régénérez votre application avec la nouvelle bibliothèque et modifiez les métadonnées existantes du gestionnaire des cartes de partitions stockées dans vos bases de données SQL Azure pour prendre en charge les nouvelles fonctionnalités.

Effectuez ces étapes afin de garantir que plus aucune version antérieure de la bibliothèque cliente ne reste présente dans votre environnement après la mise à jour des objets de métadonnées. De cette façon, il n’y aura pas d’objets de métadonnées d’une version antérieure qui seront créés après la mise à niveau.   

## <a name="upgrade-steps"></a>Étapes de la mise à niveau
**1. Mettez à niveau vos applications.** Dans Visual Studio, téléchargez la dernière version de la bibliothèque cliente et ajoutez une référence à cette bibliothèque dans tous vos projets de développement qui utilisent la bibliothèque. Ensuite, régénérez et déployez les applications. 

* Dans votre solution Visual Studio, sélectionnez **Outils** --> **Gestionnaire de package NuGet** -->  **Gérer les packages NuGet pour la solution**. 
* (Visual Studio 2013) Dans le volet gauche, sélectionnez **Mises à jour**, puis cliquez sur le bouton **Mettre à jour** dans le package **Bibliothèque cliente de la mise à l’échelle élastique de base de données SQL Azure** qui s’affiche dans la fenêtre.
* (Visual Studio 2015) Définissez la zone de filtre sur **Mise à niveau disponible**. Sélectionnez le package à mettre à jour puis cliquez sur le bouton **Mettre à jour** .
* (Visual Studio 2017) En haut de la boîte de dialogue, sélectionnez **Mises à jour**. Sélectionnez le package à mettre à jour puis cliquez sur le bouton **Mettre à jour** .
* Générez et déployez les applications. 

**2. Mettez à niveau vos scripts.** Si vous utilisez des scripts **PowerShell** pour gérer des partitions, [téléchargez la nouvelle version de la bibliothèque](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) et copiez-la dans le répertoire à partir duquel vous exécutez ces scripts. 

**3. Mettez à niveau votre service de fractionnement/fusion.** Si vous utilisez l’outil de fractionnement/fusion de la mise à l’échelle élastique pour réorganiser les données partitionnées, [téléchargez et déployez la dernière version de l’outil](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Vous trouverez plus d’informations sur les étapes de mise à niveau du service [ici](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. Mettez à niveau vos bases de données du Gestionnaire de cartes de partitions**. Mettez à niveau les métadonnées de prise en charge de vos cartes de partitions dans Azure SQL Database.  Vous pouvez effectuer cette opération avec PowerShell ou C#. Ces deux options sont expliquées ci-dessous.

***Option 1 : mise à niveau des métadonnées à l’aide de PowerShell***

1. Téléchargez [ici](http://nuget.org/nuget.exe) la dernière version de l’utilitaire de ligne de commande de NuGet et enregistrez-la dans un dossier. 
2. Ouvrez une invite de commandes, accédez au dossier choisi et exécutez cette commande : `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Accédez au sous-dossier contenant la nouvelle version de la DLL du client que vous venez de télécharger. Par exemple : `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Téléchargez le scriptlet de la mise à niveau du client de la base de données élastique à partir du [centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)et enregistrez-le dans le dossier contenant la DLL.
5. Dans ce dossier, exécutez « PowerShell .\upgrade.ps1 » à partir de l’invite de commandes et suivez les invites.

***Option 2 : mise à niveau des métadonnées à l’aide de C#***

Une autre solution consiste à créer une application Visual Studio qui ouvre votre gestionnaire de mappages de partitions, effectue une itération sur toutes les partitions, et met à niveau les métadonnées en appelant les méthodes [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) et [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx). Voici un exemple : 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Ces méthodes de mise à niveau des métadonnées peuvent être appliquées plusieurs fois sans risque. Par exemple, si une version antérieure du client crée une partition à tort après la mise à jour, vous pouvez effectuer une nouvelle mise à niveau des métadonnées de toutes les partitions pour vous assurer que votre infrastructure contient bien la dernière version des métadonnées. 

**Remarque :** les nouvelles versions de la bibliothèque cliente publiées à ce jour continuent de fonctionner avec les versions antérieures des métadonnées du Gestionnaire de mappages de partitions sur Azure SQL DB et inversement.   Toutefois, pour tirer parti de certaines nouvelles fonctionnalités de la dernière version client, les métadonnées doivent être mises à niveau.   Notez que les mises à niveau des métadonnées n’affectent pas les données utilisateur ou les données spécifiques à l’application, mais uniquement les objets créés et utilisés par le Gestionnaire de mappages de partitions.  Et les applications continuent de fonctionner à travers la séquence de mise à niveau décrite ci-dessus. 

## <a name="elastic-database-client-version-history"></a>Historique des versions du client des bases de données élastiques
Pour l’historique des versions, accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

