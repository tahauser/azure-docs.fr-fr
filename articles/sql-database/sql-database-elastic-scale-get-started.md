---
title: "Prise en main des outils de base de données élastiques | Microsoft Docs"
description: "Explication de base de la fonctionnalité des outils de bases de données élastiques d’Azure SQL Database, y compris un exemple d’application simple à exécuter."
services: sql-database
documentationcenter: 
manager: jstrauss
author: anumjs
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: anjangsh
ms.openlocfilehash: e7e072e310cabc2c4520df7e9f4f9e45b8218998
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="get-started-with-elastic-database-tools"></a>Prise en main des outils de base de données élastiques
Ce document présente l’expérience du développeur pour la [bibliothèque cliente de base de données élastique](sql-database-elastic-database-client-library.md) en vous aidant à exécuter un exemple d’application. L’exemple d’application crée une application partitionnée simple et explore les fonctionnalités clés des outils de base de données élastique. Il s’intéresse aux cas d’utilisation pour la [gestion des cartes de partition](sql-database-elastic-scale-shard-map-management.md), le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) et [l’interrogation de plusieurs partitions](sql-database-elastic-scale-multishard-querying.md). La bibliothèque cliente est disponible pour .Net ainsi que Java. 

## <a name="elastic-database-tools-for-java"></a>Outils de base de données élastique pour Java
### <a name="prerequisites"></a>Composants requis
* Kit de développement logiciel (SDK) Java version 1.8 ou ultérieure
* [Maven](http://maven.apache.org/download.cgi)
* Serveur logique dans Azure ou SQL Server local

### <a name="download-and-run-the-sample-app"></a>Télécharger et exécuter l’exemple d’application
Suivez les étapes ci-dessous pour générer les fichiers JAR et commencer avec l’exemple de projet : 
1. Clonez le [référentiel GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) contenant la bibliothèque cliente en même temps que l’exemple d’application. 
2. Modifiez le fichier _./sample/src/main/resources/resource.properties_ fichier pour définir les éléments suivants.
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME
3. Dans le répertoire _./sample_, exécutez la commande suivante pour générer l’exemple de projet.<br>

    ```
    mvn install
    ```
    
4. Dans le répertoire _./sample_, exécutez la commande suivante pour commencer l’exemple de projet. 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Essayez les différentes options pour découvrir les fonctionnalités de la bibliothèque cliente. Vous pouvez explorer le code pour en savoir plus sur l’implémentation de l’exemple d’application.

    ![Progress-java][5]
    
Félicitations ! Vous avez correctement conçu et exécuté votre première application partitionnée à l’aide des outils de bases de données élastique sur SQL Database. Utilisez Visual Studio ou SQL Server Management Studio pour vous connecter à votre base de données SQL, et regardez rapidement les partitions créées dans l’exemple. Vous remarquerez de nouveaux exemples de bases de données de partitions, ainsi que la base de données de gestionnaire de carte de partitions créée par l’exemple. Pour ajouter la bibliothèque cliente à votre propre projet Maven, ajoutez la dépendance suivante dans votre fichier POM.<br>

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Outils de base de données élastique pour .Net 
### <a name="prerequisites"></a>Composants requis
* Visual Studio 2012 ou ultérieur avec C#. Téléchargez une version gratuite à la page [Téléchargements Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 ou ultérieur. Pour obtenir la toute dernière version, consultez la page [Installation de NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Télécharger et exécuter l’exemple d’application
Pour installer la bibliothèque, accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). La bibliothèque est installée avec l’exemple d’application décrit dans la section ci-dessous.

Pour télécharger et exécuter les exemples, procédez comme suit : 

1. Téléchargez l[’exemple Outils de base de données pour base de données SQL Azure - Bien démarrer](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) à partir de MSDN. Décompressez l’exemple à l’emplacement de votre choix.

2. Pour créer un projet, ouvrez la solution **ElasticScaleStarterKit.sln** dans le répertoire **C#**.

3. Dans la solution de l’exemple de projet, ouvrez le fichier **app.config**. Suivez les instructions du fichier pour ajouter le nom du serveur de base de données Azure SQL Database et vos informations de connexion (nom d’utilisateur et mot de passe).

4. Générez et exécutez l'application. À l’invite, autorisez Visual Studio à restaurer les packages NuGet de la solution. Cela vous permet de télécharger la dernière version de la bibliothèque cliente de bases de données élastiques à partir de NuGet.

5. Essayez les différentes options pour découvrir les fonctionnalités de la bibliothèque cliente. Notez les étapes suivies par l’application dans la sortie de la console. N’hésitez pas à explorer le code en arrière-plan.
   
    ![Progression][4]

Félicitations. Vous avez correctement conçu et exécuté votre première application partitionnée à l’aide des outils de base de données élastique sur Azure SQL Database. Utilisez Visual Studio ou SQL Server Management Studio pour vous connecter à votre base de données SQL, et regardez rapidement les partitions créées dans l’exemple. Vous remarquerez de nouveaux exemples de bases de données de partitions, ainsi que la base de données de gestionnaire de carte de partitions créée par l’exemple.

> [!IMPORTANT]
> Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour d’Azure et de SQL Database. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="key-pieces-of-the-code-sample"></a>Éléments clés de l’exemple de code
* **Gestion des partitions et des cartes de partitions** : le code illustre l’utilisation des partitions, des plages et des mappages dans le fichier **ShardManagementUtils.cs**. Pour plus d’informations, consultez la page [Monter en charge les bases de données avec le Gestionnaire de cartes de partitions](http://go.microsoft.com/?linkid=9862595).  

* **Routage dépendant des données** : le routage des transactions vers la partition appropriée est indiqué dans **DataDependentRoutingSample.cs**. Pour plus d’informations, consultez la page [Routage dépendant des données](http://go.microsoft.com/?linkid=9862596). 

* **Interrogation sur plusieurs partitions** : l’interrogation sur plusieurs partitions est illustrée dans le fichier **MultiShardQuerySample.cs**. Pour plus d’informations, consultez la page [Interrogation de plusieurs partitions](http://go.microsoft.com/?linkid=9862597).

* **Ajout de partitions vides** : l’ajout itératif de nouvelles partitions vides est effectué par le code dans le fichier **CreateShardSample.cs**. Pour plus d’informations, consultez la page [Monter en charge les bases de données avec le Gestionnaire de cartes de partitions](http://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Autres opérations de mise à l’échelle élastique
* **Fractionnement d’une partition existante** : la fonctionnalité de fractionnement des partitions est fournie via **l’outil de fractionnement et de fusion**. Pour plus d’informations, consultez la page [Déplacement de données entre des bases de données cloud montées en charge](sql-database-elastic-scale-overview-split-and-merge.md).

* **Fusion des partitions existantes** : les fusions de partitions sont également effectuées à l’aide de **l’outil de fractionnement et de fusion**. Pour plus d’informations, consultez la page [Déplacement de données entre des bases de données cloud montées en charge](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Coût
Les outils de base de données élastique sont gratuits. Si vous utilisez des outils de base de données élastique, aucun frais supplémentaire n’est ajouté au coût d’utilisation d’Azure. 

Par exemple, l’exemple d’application crée des bases de données. Le coût correspondant dépend de l’édition de SQL Database choisie et de l’utilisation Azure de votre application.

Pour plus d’informations sur la tarification, consultez la page [Tarification de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les outils de base de données élastique, consultez les pages suivantes :

* Exemples de code : 
  * Outils de base de données élastique ([.NET](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Outils de base de données élastique pour SQL Azure - Intégration Entity Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE).
  * [Partitionner l’élasticité sur le centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog : [Annonce de la mise à l’échelle élastique](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy : [Vidéo d’implémentation de la montée en charge à l’aide du partitionnement avec la bibliothèque cliente de bases de données élastiques](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) 
* Channel 9 : [Vidéo de présentation de la mise à l’échelle élastique](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Forum de discussion : [Forum sur Azure SQL Database](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Pour mesurer les performances : [Compteurs de performances pour le Gestionnaire de cartes de partitions](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

