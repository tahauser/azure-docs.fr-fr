---
title: "Importation de données dans Recherche Azure | Microsoft Docs"
description: "Découvrez comment charger des données dans un index dans Azure Search."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 11/01/2017
ms.author: ashmaka
ms.openlocfilehash: ebf7319f0017b4adef25fe5840864e002c88fea7
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="data-import-in-azure-search"></a>Importation de données dans Recherche Azure
> [!div class="op_single_selector"]
> * [Vue d'ensemble](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Dans Recherche Azure, les requêtes s’exécutent sur votre contenu chargé dans un [index de recherche](search-what-is-an-index.md). Cet article examine les deux méthodes de base pour le chargement de contenu dans un index : *envoyer* les données dans l’index par programme ou pointer un [indexeur Recherche Azure](search-indexer-overview.md) à une source de données prise en charge pour *extraire* les données.

## <a name="pushing-data-to-an-index"></a>Envoyer des données à un index
Le modèle d’émission, utilisé pour envoyer vos données Recherche Azure par programme, est l’approche la plus flexible. Tout d’abord, il n’y a pas de restrictions sur le type de source de données. Tout jeu de données composé de documents JSON peut être appliqué à un index Recherche Azure, en supposant que chaque document dans le jeu de données possède des champs mappant des champs définis dans votre schéma d’index. En second lieu, il n’y a aucune restriction sur la fréquence d’exécution. Vous pouvez transmettre des modifications à un index aussi souvent que vous le souhaitez. Pour les applications ayant des exigences à très faible latence (par exemple, si vous devez synchroniser les opérations de recherche avec les bases de données d’inventaire dynamiques), le modèle d’émission est la seule option.

Cette approche est plus flexible que le modèle d’extraction, car vous pouvez charger des documents individuellement ou par lots (jusqu’à 1 000 par lot ou 16 Mo, quelle que soit la limite atteinte en premier). Le modèle d’émission vous permet également de charger des documents dans Azure Search indépendamment de l’emplacement des données.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Comment envoyer des données à un index Recherche Azure

Vous pouvez utiliser les API suivantes pour charger un ou plusieurs documents dans un index :

+ [Ajout, mise à jour ou suppression de documents (API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [classe indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) ou [classe indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Il n’existe actuellement aucune prise en charge de l’outil de diffusion de données via le portail.

Pour une présentation de chaque méthode, consultez [Importer des données à l’aide de l’API REST](search-import-data-rest-api.md) ou [Importer des données à l’aide du Kit de développement logiciel (SDK) .NET](search-import-data-dotnet.md).


## <a name="pulling-data-into-an-index"></a>Extraction de données dans un index
Le modèle d’extraction analyse une source de données prise en charge et charge automatiquement les données dans votre index. Dans Recherche Azure, cette fonctionnalité est implémentée via des *indexeurs*, actuellement disponibles pour ces plateformes :

+ [Stockage d’objets blob](search-howto-indexing-azure-blob-storage.md)
+ [Stockage Table](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer)
+ [Base de données SQL Azure et SQL Server sur les machines virtuelles Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Les indexeurs connectent un index à une source de données (généralement une table, une vue ou une structure équivalente) et mappent les champs source aux champs équivalents de l’index. Pendant l’exécution, l’ensemble de lignes est automatiquement transformé en JSON et chargé dans l’index spécifié. Tous les indexeurs prennent en charge la planification de sorte que vous puissiez spécifier la fréquence à laquelle les données sont à actualiser. La plupart des indexeurs fournissent le suivi des modifications si la source de données le prend en charge. En suivant les modifications et les suppressions effectuées dans les documents existants, et en reconnaissant les nouveaux documents, les indexeurs suppriment la nécessité de gérer activement les données de votre index. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Comment extraire des données dans un index Recherche Azure

La fonctionnalité de l’indexeur est exposée dans le [Portail Azure](search-import-data-portal.md), ainsi que dans [l’API REST](/rest/api/searchservice/Indexer-operations) et le [Kit de développement logiciel (SDK) .NET](/dotnet/api/microsoft.azure.search.indexersoperations). 

L’avantage du portail est qu’Azure Search peut générer un schéma d’index par défaut pour vous en lisant les métadonnées du jeu de données source. Vous pouvez modifier l’index généré jusqu’à ce que l’index soit traité, après quoi les seules modifications de schéma autorisées sont celles qui ne nécessitent pas la réindexation. Si les modifications que vous souhaitez apporter impactent directement le schéma, vous devez reconstruire l’index. 

## <a name="verify-data-import-with-search-explorer"></a>Vérifier l’importation de données avec l’Explorateur de recherche

Un moyen rapide d’effectuer une vérification préliminaire sur le téléchargement de document consiste à utiliser **l’Explorateur de recherche** dans le portail. L’Explorateur vous permet d’interroger un index sans avoir à écrire du code. L’expérience de recherche est basée sur les paramètres par défaut, tels que la [syntaxe simple](/rest/api/searchservice/simple-query-syntax-in-azure-search) et le [paramètre de requête searchMode](/rest/api/searchservice/search-documents). Les résultats sont retournés au format JSON afin que vous puissiez inspecter le document dans son intégralité.

> [!TIP]
> De nombreux [exemples de code Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluent des jeux de données incorporés ou disponibles rapidement, offrant ainsi une prise en main simplifiée. Le portail fournit également un exemple d’indexeur et de source de données composée d’un petit jeu de données immobilières (nommé « realestate-us-sample »). Lorsque vous exécutez l’indexeur préconfiguré sur l’exemple de source de données, un index est créé et chargé avec des documents qui peuvent ensuite être interrogés dans l’Explorateur de recherche ou par le code que vous écrivez.

## <a name="see-also"></a>Voir aussi

+ [Présentation de l’indexeur](search-indexer-overview.md)
+ [Procédure pas à pas dans le portail : créer, charger, interroger un index](search-get-started-portal.md)