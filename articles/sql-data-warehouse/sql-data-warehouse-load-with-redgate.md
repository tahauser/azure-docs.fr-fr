---
title: "Utiliser Redgate pour charger des données sur votre entrepôt de données Azure | Microsoft Docs"
description: "Apprenez à utiliser Redgate Data Platform Studio pour les scénarios d’entreposage de données."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 670aef98-31f7-4436-86c0-cc989a39fe7f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: a38b237d5bfc0450c1ca79b53a5784dbb9bf8602
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="load-data-with-redgate-data-platform-studio"></a>Chargement de données avec Redgate Data Platform Studio
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

Ce didacticiel vous montre comment utiliser [Redgate Data Platform Studio](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) pour déplacer des données d’un serveur SQL Server local vers Azure SQL Data Warehouse. Data Platform Studio applique les correctifs de compatibilité et optimisations les plus appropriés. Il s’agit donc du moyen le plus rapide pour vous familiariser avec SQL Data Warehouse.

> [!NOTE]
> [Redgate](http://www.red-gate.com) est un partenaire Microsoft de longue date, et fournit divers outils SQL Server. Cette fonctionnalité de Data Platform Studio est disponible gratuitement pour un usage commercial et non commercial.
> 
> 

## <a name="before-you-begin"></a>Avant de commencer
### <a name="create-or-identify-resources"></a>Créer ou identifier des ressources
Avant de commencer ce didacticiel, vous devez disposer des ressources suivantes :

* **Base de données SQL Server locale** : les données que vous souhaitez importer dans SQL Data Warehouse doivent provenir d’un serveur SQL Server local (version 2008R2 ou ultérieure). Data Platform Studio ne peut pas importer les données directement à partir d’une base de données Azure SQL Database, ni à partir de fichiers texte.
* **Compte de stockage Azure** : Data Platform Studio organise les données dans le stockage Blob Azure avant de les charger dans SQL Data Warehouse. Le compte de stockage doit utiliser le modèle de déploiement « Resource Manager » (la valeur par défaut) plutôt que le modèle de déploiement « Classic ». Si vous ne possédez pas de compte de stockage, découvrez comment créer un compte de stockage. 
* **SQL Data Warehouse** : ce didacticiel déplace les données d’un serveur SQL Server local vers SQL Data Warehouse ; vous devez donc disposer d’un entrepôt de données en ligne. Si vous n’en possédez pas encore, découvrez comment créer un entrepôt Azure SQL Data Warehouse.

> [!NOTE]
> Les performances se trouvent améliorées si le compte de stockage et l’entrepôt de données sont créés dans la même région.
> 
> 

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Étape 1 : se connecter à Data Platform Studio avec votre compte Azure
Ouvrez votre navigateur Web et accédez au site [Data Platform Studio](https://www.dataplatformstudio.com/). Connectez-vous avec le même compte Azure que celui que vous avez utilisé pour créer le compte de stockage et l’entrepôt de données. Si votre adresse de messagerie est associée à un compte professionnel ou scolaire et à un compte Microsoft, veillez à choisir le compte qui a accès à vos ressources.

> [!NOTE]
> S’il s’agit de votre première utilisation de Data Platform Studio, vous êtes invité à autoriser l’application à gérer vos ressources Azure.
> 
> 

## <a name="step-2-start-the-import-wizard"></a>Étape 2 : démarrer l’Assistant Importation
Dans l’écran principal de DPS, sélectionnez le lien Import to Azure SQL Data Warehouse (Importer dans Azure SQL Data Warehouse) pour démarrer l’Assistant Importation.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Étape 3 : installer la passerelle Data Platform Studio
Pour vous connecter à votre base de données SQL Server locale, vous devez installer la passerelle DPS. La passerelle est un agent client qui donne accès à votre environnement local, extrait les données et les télécharge vers votre compte de stockage. Vos données ne transitent jamais via les serveurs de Redgate. Pour installer la passerelle :

1. Cliquez sur le lien **Create Gateway** (Créer une passerelle).
2. Téléchargez et installez la passerelle via le programme d’installation fourni.

![][2]

> [!NOTE]
> La passerelle peut être installée sur n’importe quel ordinateur ayant un accès réseau à la base de données SQL Server source. Elle accède à la base de données SQL Server en utilisant les identifiants d’authentification Windows de l’utilisateur actuel.
> 
> 

Une fois la passerelle installée, elle passe à l’état Connected (Connecté) et vous pouvez sélectionner Next (Suivant).

## <a name="step-4-identify-the-source-database"></a>Étape 4 : identifier la base de données source
Dans la zone de texte *Enter Server Name* (Entrer le nom du serveur), saisissez le nom du serveur qui héberge votre base de données et sélectionnez **Next** (Suivant). Ensuite, dans le menu déroulant, sélectionnez la base de données à partir de laquelle vous souhaitez importer les données.

![][3]

DPS recherche les tables à importer dans la base de données sélectionnée. Par défaut, DPS importe toutes les tables de la base de données. Vous pouvez sélectionner ou désélectionner des tables en développant le lien All Tables (Toutes les tables). Sélectionnez le bouton Next (Suivant) pour continuer.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Étape 5 : choisir un compte de stockage à utiliser pour stocker les données
DPS vous invite à entrer un emplacement à utiliser pour stocker les données. Choisissez un compte de stockage existant dans votre abonnement, puis sélectionnez **Next** (Suivant).

> [!NOTE]
> DPS crée un nouveau conteneur d’objets blob dans le compte de stockage choisi et utilise un dossier distinct pour chaque importation.
> 
> 

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Étape 6 : créer un entrepôt de données
Sélectionnez ensuite une base de données [Azure SQL Data Warehouse](http://aka.ms/sqldw) en ligne dans laquelle importer les données. Une fois que vous avez sélectionné votre base de données, vous devez entrer les informations d’identification pour vous connecter à la base de données et sélectionner **Next** (Suivant).

![][5]

> [!NOTE]
> DPS fusionne les tables de données source dans l’entrepôt de données. DPS vous avertit si le nom de la table nécessite d’écraser des tables existantes dans l’entrepôt de données. Vous pouvez supprimer des objets existants dans l’entrepôt de données en cochant Delete all existing objects before import (Supprimer tous les objets existants avant l’importation).
> 
> 

## <a name="step-7-import-the-data"></a>Étape 7 : importer les données
DPS vous demande de confirmer que vous souhaitez importer les données. Cliquez simplement sur le bouton de démarrage de l’importation pour commencer l’importation de données.

![][6]

DPS indique la progression de l’extraction et du chargement des données à partir du serveur SQL Server local, ainsi que la progression de l’importation dans SQL Data Warehouse.

![][7]

Une fois l’importation terminée, DPS affiche un résumé de l’importation des données et un rapport de modification des correctifs de compatibilité qui ont été effectués.

![][8]

## <a name="next-steps"></a>Étapes suivantes
Pour explorer les données dans SQL Data Warehouse, reportez-vous à :

* [Interroger Azure SQL Data Warehouse (sqlcmd) (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)]
* [Visualiser des données avec Power BI][Visualize data with Power BI]

Pour en savoir plus sur Redgate Data Platform Studio :

* [Visitez la page d’accueil de DPS](http://www.dataplatformstudio.com/)
* [Regardez une démonstration de DPS sur Channel9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Pour obtenir une vue d’ensemble d’autres méthodes de migration et de chargement de vos données dans SQL Data Warehouse, voir :

* [Migration de votre solution vers SQL Data Warehouse][Migrate your solution to SQL Data Warehouse]
* [Chargement de données dans Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md)

Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Vue d’ensemble sur le développement SQL Data Warehouse](sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualize data with Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrate your solution to SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
