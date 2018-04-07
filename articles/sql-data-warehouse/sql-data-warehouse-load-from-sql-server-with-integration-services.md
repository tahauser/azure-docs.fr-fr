---
title: "Charger des données à partir de SQL Server dans Azure SQL Data Warehouse (SSIS) | Microsoft Docs"
description: "Vous montre comment créer un package SQL Server Integration Services (SSIS) pour déplacer des données d’un large éventail de sources de données vers SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e2c252e9-0828-47c2-a808-e3bea46c134a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 03/30/2017
ms.author: cakarst;douglasl;barbkess
ms.openlocfilehash: 6c9cebdd715b6997d0633bc725a3945ba9e0c357
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Charger des données à partir de SQL Server dans Azure SQL Data Warehouse (SSIS)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Créez un package SQL Server Integration Services (SSIS) pour charger des données à partir de SQL Server dans Azure SQL Data Warehouse. Si vous le souhaitez, vous pouvez restructurer, transformer et nettoyer les données lorsqu’elles transitent par le flux de données SSIS.

Ce didacticiel présente les procédures suivantes :

* créer un nouveau projet Integration Services dans Visual Studio ;
* établir une connexion aux sources de données, y compris SQL Server (en tant que source) et SQL Data Warehouse (en tant que destination) ;
* concevoir un package SSIS pour charger des données de la source vers la destination ;
* exécuter le package SSIS pour charger les données.

Ce didacticiel utilise SQL Server en tant que source de données. SQL Server peut être exécuté en local ou dans une machine virtuelle Azure.

## <a name="basic-concepts"></a>Concepts de base
Le package est l’unité de travail de SSIS. Les packages associés sont regroupés dans des projets. Les projets et packages de conception sont créés dans Visual Studio à l’aide de SQL Server Data Tools. Le processus de conception est un processus visuel qui consiste à déplacer des composants de la boîte à outils vers l’aire de conception, à les connecter et à en définir les propriétés. Une fois le package terminé, vous pouvez éventuellement le déployer vers SQL Server pour bénéficier de fonctions de gestion, de surveillance et de sécurité complètes.

## <a name="options-for-loading-data-with-ssis"></a>Options de chargement des données avec SSIS
SQL Server Integration Services (SSIS) est un ensemble d’outils flexible qui fournit diverses options permettant de connecter et charger les données dans SQL Data Warehouse.

1. Utilisez une destination ADO.NET pour vous connecter à SQL Data Warehouse. Ce didacticiel utilise une destination ADO NET car il dispose des options de configuration minimum.
2. Utilisez une destination OLE DB pour vous connecter à SQL Data Warehouse. Cette option permet potentiellement d’obtenir des performances légèrement supérieures à celles d’une destination ADO NET.
3. Utilisez la tâche de chargement d’objets Blob Azure pour stocker les données dans Azure Blob Storage. Utilisez ensuite la tâche SSIS Execute SQL pour exécuter un script Polybase permettant de charger les données dans SQL Data Warehouse. Cette option est celle qui permet d’obtenir les meilleures performances parmi les trois options présentées ici. Pour obtenir la tâche de chargement d’objet Blob Azure, téléchargez [Microsoft SQL Server 2016 Integration Services Feature Pack pour Azure][Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]. Pour en savoir plus sur Polybase, consultez le [guide PolyBase][PolyBase Guide].

## <a name="before-you-start"></a>Avant de commencer
Pour parcourir ce didacticiel, vous avez besoin des éléments suivants :

1. **SQL Server Integration Services (SSIS)**. SSIS est un composant de SQL Server qui nécessite une version d’évaluation ou une version sous licence de SQL Server. Pour obtenir une version préliminaire de SQL Server 2016, consultez la page [SQL Server Évaluations][SQL Server Evaluations].
2. **Visual Studio**. Pour obtenir gratuitement Visual Studio Community Edition, consultez [Visual Studio Community][Visual Studio Community].
3. **SQL Server Data Tools pour Visual Studio (SSDT)**. Pour obtenir SQL Server Data Tools pour Visual Studio, consultez [Télécharger SQL Server Data Tools (SSDT)][Download SQL Server Data Tools (SSDT)].
4. **Exemples de données**. Des exemples de données stockées dans SQL Server, dans l’exemple de base de données AdventureWorks, sont ici utilisés comme sources de données à charger dans SQL Data Warehouse. Pour obtenir l’exemple de base de données AdventureWorks, consultez [Exemples de bases de données AdventureWorks 2014][AdventureWorks 2014 Sample Databases].
5. **Une base de données SQL Data Warehouse avec les autorisations requises**. Ce didacticiel se connecte à une instance SQL Data Warehouse dans laquelle sont chargées des données. Vous devez disposer des autorisations requises pour créer une table et charger des données.
6. **Une règle de pare-feu**. Vous devez créer une règle de pare-feu sur SQL Data Warehouse avec l’adresse IP de votre ordinateur local avant de pouvoir charger des données dans SQL Data Warehouse.

## <a name="step-1-create-a-new-integration-services-project"></a>Étape 1 : créer un nouveau projet Integration Services
1. Lancez Visual Studio.
2. Dans le menu **Fichier**, sélectionnez **Nouveau | Projet**.
3. Accédez aux types de projet **Installé | Modèles | Business Intelligence | Integration Services** .
4. Sélectionnez **Projet Integration Services**. Renseignez les champs **Nom** et **Emplacement**, puis cliquez sur **OK**.

Visual Studio s’ouvre et crée un nouveau projet Integration Services (SSIS). Visual Studio ouvre ensuite le concepteur pour le seul nouveau package SSIS (Package.dtsx) du projet. L’écran se décompose de la manière suivante :

* À gauche, la **Boîte à outils** des composants SSIS.
* Au centre, l’aire de conception, contenant plusieurs onglets. En règle générale, vous serez amené à utiliser au moins les onglets **Flux de contrôle** et **Flux de données**.
* À droite, les volets **Explorateur de solutions** et **Propriétés**.
  
    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Étape 2 : créer le flux de base de données de base
1. Faites glisser une tâche de flux de données de la boîte à outils vers le centre de l’aire de conception (sur l’onglet **Flux de contrôle** ).
   
    ![][02]
2. Double-cliquez sur la tâche de flux de données pour basculer vers l’onglet Flux de données.
3. Dans la liste Autres sources de la boîte à outils, faites glisser une source ADO.NET dans l’aire de conception. Tout en laissant l’adaptateur de source sélectionné, remplacez son nom par **Source SQL Server** dans le volet **Propriétés**.
4. Dans la liste Autres destinations de la boîte à outils, faites glisser une destination ADO.NET dans l’aire de conception sous la source ADO.NET. Tout en laissant l’adaptateur de source sélectionné, remplacez son nom par **Destination SQL DW** dans le volet **Propriétés**.
   
    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Étape 3 : configurer l’adaptateur de source
1. Double-cliquez sur l’adaptateur de source pour ouvrir l’ **Éditeur de source ADO.NET**.
   
    ![][03]
2. Sous l’onglet **Gestionnaire de connexions** de **l’Éditeur de source ADO.NET**, cliquez sur le bouton **Nouveau** situé en regard de la liste **Gestionnaire de connexions ADO.NET** pour ouvrir la boîte de dialogue **Configurer le gestionnaire de connexions ADO.NET** et créer les paramètres de connexion pour la base de données SQL Server à partir de laquelle les données seront chargées.
   
    ![][04]
3. Dans la boîte de dialogue **Configurer le gestionnaire de connexions ADO.NET**, cliquez sur le bouton **Nouveau** pour ouvrir la boîte de dialogue **Gestionnaire de connexions** et créer une connexion de données.
   
    ![][05]
4. Dans la boîte de dialogue **Gestionnaire de connexions** , procédez comme suit.
   
   1. Dans la zone **Fournisseur**, sélectionnez le fournisseur de données SqlClient.
   2. Dans la zone **Nom du serveur**, entrez le nom de l’instance SQL Server.
   3. Dans la zone **Connexion au serveur** , sélectionnez ou saisissez les informations d’authentification.
   4. Dans la zone **Connexion à une base de données** , sélectionnez l’exemple de base de données AdventureWorks.
   5. Cliquez sur **Tester la connexion**.
      
       ![][06]
   6. Dans la boîte de dialogue qui affiche les résultats du test de connexion, cliquez sur **OK** pour revenir à la boîte de dialogue **Gestionnaire de connexions**.
   7. Dans la boîte de dialogue **Gestionnaire de connexions**, cliquez sur **OK** pour revenir à la boîte de dialogue **Configurer le gestionnaire de connexions ADO.NET**.
5. Dans la boîte de dialogue **Configurer le gestionnaire de connexions ADO.NET**, cliquez sur **OK** pour revenir à **l’Éditeur de source ADO.NET**.
6. Dans la liste **Nom de la table ou de la vue** de **l’Éditeur de source ADO.NET**, sélectionnez la table **Sales.SalesOrderDetail**.
   
    ![][07]
7. Cliquez sur **Aperçu** pour afficher les 200 premières lignes de données de la table source dans la boîte de dialogue **Visualiser les résultats de la requête**.
   
    ![][08]
8. Dans la boîte de dialogue **Visualiser les résultats de la requête**, cliquez sur **Fermer** pour revenir à **l’Éditeur de source ADO.NET**.
9. Dans **l’Éditeur de source ADO.NET**, cliquez sur **OK** pour terminer la configuration de la source de données.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Étape 4 : connecter l’adaptateur de source à l’adaptateur de destination
1. Sélectionnez l’adaptateur de source dans l’aire de conception.
2. Cliquez sur la flèche bleue sortant de l’adaptateur de source et faites-la glisser vers l’éditeur de destination jusqu’à ce qu’elle soit bien en place.
   
    ![][10]
   
    Dans un package SSIS classique, vous utilisez certains autres composants de la boîte à outils SSIS entre la source et la destination afin de restructurer, transformer et nettoyer vos données lorsqu’elles transitent par le flux de données SSIS. Dans un souci de simplicité, nous allons ici connecter la source directement à la destination.

## <a name="step-5-configure-the-destination-adapter"></a>Étape 5 : configurer l’adaptateur de destination
1. Double-cliquez sur l’adaptateur de destination pour ouvrir l’ **Éditeur de destination ADO.NET**.
   
    ![][11]
2. Sous l’onglet **Gestionnaire de connexions** de **l’Éditeur de destination ADO.NET**, cliquez sur le bouton **Nouveau** situé en regard de la liste **Gestionnaire de connexions** pour ouvrir la boîte de dialogue **Configurer le gestionnaire de connexions ADO.NET** et créer les paramètres de connexion pour la base de données Azure SQL Data Warehouse dans laquelle les données seront chargées.
3. Dans la boîte de dialogue **Configurer le gestionnaire de connexions ADO.NET**, cliquez sur le bouton **Nouveau** pour ouvrir la boîte de dialogue **Gestionnaire de connexions** et créer une connexion de données.
4. Dans la boîte de dialogue **Gestionnaire de connexions** , procédez comme suit.
   1. Dans la zone **Fournisseur**, sélectionnez le fournisseur de données SqlClient.
   2. Dans la zone **Nom du serveur**, entrez le nom de la base de données SQL Data Warehouse.
   3. Dans la zone **Connexion au serveur**, sélectionnez **Utiliser l’authentification SQL Server** et saisissez les informations d’authentification.
   4. Dans la zone **Connexion à une base de données** , sélectionnez une base de données SQL Data Warehouse existante.
   5. Cliquez sur **Tester la connexion**.
   6. Dans la boîte de dialogue qui affiche les résultats du test de connexion, cliquez sur **OK** pour revenir à la boîte de dialogue **Gestionnaire de connexions**.
   7. Dans la boîte de dialogue **Gestionnaire de connexions**, cliquez sur **OK** pour revenir à la boîte de dialogue **Configurer le gestionnaire de connexions ADO.NET**.
5. Dans la boîte de dialogue **Configurer le gestionnaire de connexions ADO.NET**, cliquez sur **OK** pour revenir à **l’Éditeur de destination ADO.NET**.
6. Dans **l’Éditeur de destination ADO.NET**, cliquez sur **Nouveau** en regard de la liste **Utiliser une table ou une vue** pour ouvrir la boîte de dialogue **Créer une table** afin de créer une table de destination contenant une liste de colonnes correspondant à la table source.
   
    ![][12a]
7. Dans la boîte de dialogue **Créer une table** , procédez comme suit :
   
   1. Renommez la table de destination **SalesOrderDetail**.
   2. Supprimez la colonne **rowguid** . Le type de données **uniqueidentifier** n’est pas pris en charge dans SQL Data Warehouse.
   3. Remplacez le type de données de la colonne **LineTotal** par **money**. Le type de données **decimal** n’est pas pris en charge dans SQL Data Warehouse. Pour plus d’informations sur les types de données pris en charge, consultez la page [CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)][CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)].
      
       ![][12b]
   4. Cliquez sur **OK** pour créer la table et revenir à **l’Éditeur de destination ADO.NET**.
8. Dans **l’Éditeur de destination ADO.NET**, sélectionnez l’onglet **Mappages** pour voir comment les colonnes de la source sont mappées aux colonnes de la destination.
   
    ![][13]
9. Cliquez sur **OK** pour terminer la configuration de la source de données.

## <a name="step-6-run-the-package-to-load-the-data"></a>Étape 6 : exécuter le package pour charger les données
Pour exécuter le package, cliquez sur le bouton **Démarrer** dans la barre d’outils ou sélectionnez l’une des options **Exécuter** dans le menu **Déboguer**.

Le début de l’exécution du package est indiqué par des engrenages jaunes, qui reflètent l’activité ainsi que le nombre de lignes traitées jusqu’à présent.

![][14]

Une fois le package exécuté, des coches vertes s’affichent pour vous informer que le processus a abouti et pour indiquer le nombre total de lignes de données chargées de la source vers la destination.

![][15]

Félicitations ! Vous savez à présent comment utiliser SQL Server Integration Services pour charger des données dans Azure SQL Data Warehouse.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le flux de données SSIS. Article de référence : [Flux de données][Data Flow].
* Apprenez à déboguer et résoudre les problèmes de vos packages dans l’environnement de conception. Article de référence : [Outils de dépannage pour le développement des packages][Troubleshooting Tools for Package Development].
* Apprenez à déployer vos projets et packages SSIS dans Integration Services Server ou dans un autre emplacement de stockage. Article de référence : [Déploiement de projets et de packages][Deployment of Projects and Packages].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase Guide]: https://msdn.microsoft.com/library/mt143171.aspx
[Download SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Data Flow]: https://msdn.microsoft.com/library/ms140080.aspx
[Troubleshooting Tools for Package Development]: https://msdn.microsoft.com/library/ms137625.aspx
[Deployment of Projects and Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server Evaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 Sample Databases]: https://msftdbprodsamples.codeplex.com/releases/view/125550
