---
title: 'Azure Cosmos DB : API SQL .NET Core, Kit SDK et ressources | Microsoft Docs'
description: Tout savoir sur l’API SQL .NET Core et le kit de développement logiciel (SDK), notamment les dates de sortie, les dates de déclassement et les modifications effectuées entre chaque version du kit de développement logiciel (SDK) .NET Core Azure Cosmos DB.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/22/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b367112c68574cc81d00e3d4b40b405d2d35131
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Kit SDK .NET Core Azure Cosmos DB pour API SQL : notes de publication et ressources
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Flux de modification .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java asynchrone](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Téléchargement du Kit de développement logiciel (SDK)**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Documentation de l’API**</td><td>[Documentation de référence sur l’API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Exemples**</td><td>[Exemples de code .NET](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Prise en main**</td><td>[Prise en main du kit de développement logiciel (SDK) .NET Core Azure Cosmos DB](sql-api-dotnetcore-get-started.md)</td></tr>

<tr><td>**Didacticiel d’application web**</td><td>[Développement d’applications web avec Azure Cosmos DB](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Infrastructure actuellement prise en charge**</td><td>[.NET Standard 1.6 et .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Notes de publication

Le kit de développement logiciel (SDK) .NET Core Azure Cosmos DB assure la parité des fonctions avec la dernière version du [kit de développement logiciel (SDK) .NET Azure Cosmos DB](sql-api-sdk-dotnet.md).

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* Correction de KeyNotFoundException pour une commande entre partition par requête dans des cas extrêmes.
* Correction du bogue lié au non-respect de l’attribut JsonPropery dans la clause Select pour les requêtes LINQ.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Correction du bogue qui apparaît dans certaines conditions de concurrence et qui génère des erreurs « Microsoft.Azure.Documents.NotFoundException : la session de lecture n’est pas disponible pour le jeton de session d’entrée » par intermittence lors de l’utilisation du niveau de cohérence Session.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Régression corrigée où FeedOptions.MaxItemCount = -1 levait une exception System.ArithmeticException : taille de la page est négative.
* Nouvelle fonction ToString() ajoutée à QueryMetrics.
* Statistiques de partition exposées sur la lecture des collections.
* Propriété PartitionKey ajoutée à ChangeFeedOptions.
* Correctifs de bogues mineurs.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Ajoute la possibilité de spécifier des index uniques pour les documents à l’aide de la propriété UniqueKeyPolicy sur la collection DocumentCollection.
 * Correction d’un bogue par lequel les paramètres personnalisés de JsonSerializer n’étaient pas respectés pour certaines requêtes et l’exécution de la procédure stockée.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Remplacement du nom Azure DocumentDB par le nom Azure Cosmos DB dans la documentation de référence des API, les informations de métadonnées des assemblys et le package NuGet. 
 * Exposition des informations de diagnostic et de la latence à partir de la réponse aux requêtes envoyées en mode de connexion directe. Les noms des propriétés sont RequestDiagnosticsString et RequestLatency dans la classe ResourceResponse.
 * Cette version du kit de développement logiciel (SDK) nécessite la dernière version de l’émulateur Azure Cosmos DB, que vous pouvez télécharger à l’adresse https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Ajout de plusieurs améliorations et correctifs de fiabilité.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Changements internes liées à la prise en charge de [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Ajout de la prise en charge du paramètre PartitionKeyRangeId en tant qu’option FeedOption pour la définition de l’étendue des résultats des requêtes sur une valeur de plage de clés de partition spécifique. 
* Ajout de la prise en charge du paramètre StartTime en tant qu’option ChangeFeedOption pour le démarrage de la recherche de modifications après cette heure. 

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Correction d’un problème dans la classe JsonSerializable qui peut provoquer une exception de dépassement de la capacité de la pile.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Ajout de la prise en charge de la spécification des JsonSerializerSettings personnalisés lors de l’instanciation d’une instance [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Prise en charge de .NET Standard 1.5 en tant que version cible de .NET Framework.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Correction d’un problème concernant les ordinateurs x64 qui ne prennent pas en charge l’instruction SSE4 et génèrent une SEHException lors de l’exécution de requêtes Azure Cosmos DB.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Prise en charge ajoutée pour un nouveau niveau de cohérence nommé ConsistentPrefix.
*   Prise en charge ajoutée pour les mesures de requête liées aux partitions individuelles.
*   Prise en charge ajoutée pour la limitation de la taille du jeton de continuation concernant les requêtes.
*   Prise en charge ajoutée pour un suivi plus détaillé des demandes ayant échoué.
*   Améliorations de certaines performances du Kit de développement logiciel (SDK).

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Résolution du problème qui ignorait la valeur PartitionKey fournie dans FeedOptions pour les requêtes d’agrégation.
* Résolution du problème de gestion transparente des partitions pendant l’exécution d’une requête Order By (Trier par) entre partitions intermédiaire.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Résolution du problème qui provoquait des blocages parmi certaines API asynchrones lorsqu’elles sont utilisées dans le contexte ASP.NET.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Correctifs pour augmenter la résistance du Kit de développement logiciel (SDK) au basculement automatique dans certaines conditions.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Résolution du problème qui provoque parfois une exception WebException : Le nom distant n’a pas pu être résolu.
* Ajout de la prise en charge permettant de lire directement un document tapé en ajoutant de nouvelles surcharges à l’API ReadDocumentAsync.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Ajout de la prise en charge LINQ des requêtes d’agrégation (COUNT, MIN, MAX, SUM et AVG).
* Correction d’un problème de fuite de mémoire pour l’objet ConnectionPolicy dû à l’utilisation du Gestionnaire d’événements.
* Correction d’un problème de fonctionnement d’UpsertAttachmentAsync avec l’utilisation d’ETag.
* Correction d’un problème de fonctionnement de la liaison des requêtes ORDER BY entre les partitions lors d’un tri sur un champ de chaîne.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Ajout de la prise en charge des requêtes d’agrégation (COUNT, MIN, MAX, SUM et AVG). Consultez l’article [Aggregation support (Prise en charge de l’agrégation)](sql-api-sql-query.md#Aggregates).
* Débit minimal réduit sur les collections partitionnées de 10 100 unités de demande/s à 2 500 unités de demande/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Le kit de développement logiciel (SDK) .NET Core Azure Cosmos DB permet de générer des applications [ASP.NET Core](https://www.asp.net/core) et [.NET Core](https://www.microsoft.com/net/core#windows) rapides et multiplateformes à exécuter sous Windows, Mac et Linux. La dernière version du kit de développement logiciel (SDK) .NET Core Azure Cosmos DB est entièrement compatible avec [Xamarin](https://www.xamarin.com) et est utilisée pour générer des applications qui ciblent iOS, Android et Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

La version préliminaire du kit de développement logiciel (SDK) .NET Core Azure Cosmos DB permet de générer des applications [ASP.NET Core](https://www.asp.net/core) et [.NET Core](https://www.microsoft.com/net/core#windows) rapides et multiplateformes à exécuter sous Windows, Mac et Linux.

La version préliminaire du kit de développement logiciel (SDK) .NET Core Azure Cosmos DB assure la parité des fonctions avec la dernière version du [kit de développement logiciel (SDK) .NET Azure Cosmos DB](sql-api-sdk-dotnet.md) et prend en charge les éléments suivants :
* Tous les [modes de connexion](performance-tips.md#networking) : mode passerelle, TCP Direct et HTTPs Direct. 
* Tous les [niveaux de cohérence](consistency-levels.md): Forte, Session, Obsolescence limitée et Éventuelle.
* [Collections partitionnées](partition-data.md). 
* [Comptes de base de données multirégions et géoréplication](distribute-data-globally.md).

Si vous avez des questions liées à ce SDK, publiez sur [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) ou envoyez une demande sur le [référentiel GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Dates de lancement et de suppression

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [1.9.1](#1.9.1) |09 mars 2018 |--- |
| [1.8.2](#1.8.2) |21 février 2018 |--- |
| [1.8.1](#1.8.1) |05 février 2018 |--- |
| [1.7.1](#1.7.1) |16 novembre 2017 |--- |
| [1.7.0](#1.7.0) |10 novembre 2017 |--- |
| [1.6.0](#1.6.0) |17 octobre 2017 |--- |
| [1.5.1](#1.5.1) |2 octobre 2017 |--- |
| [1.5.0](#1.5.0) |10 août 2017 |--- | 
| [1.4.1](#1.4.1) |7 août 2017 |--- |
| [1.4.0](#1.4.0) |2 août 2017 |--- |
| [1.3.2](#1.3.2) |12 juin 2017 |--- |
| [1.3.1](#1.3.1) |23 mai 2017 |--- |
| [1.3.0](#1.3.0) |10 mai 2017 |--- |
| [1.2.2](#1.2.2) |19 avril 2017 |--- |
| [1.2.1](#1.2.1) |29 mars 2017 |--- |
| [1.2.0](#1.2.0) |25 mars 2017 |--- |
| [1.1.2](#1.1.2) |20 mars 2017 |--- |
| [1.1.1](#1.1.1) |14 mars 2017 |--- |
| [1.1.0](#1.1.0) |16 février 2017 |--- |
| [1.0.0](#1.0.0) |21 décembre 2016 |--- |
| [0.1.0-preview](#0.1.0-preview) |15 novembre 2016 |31 décembre 2016 |

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

