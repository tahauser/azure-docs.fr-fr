---
title: "Kit de développement logiciel (SDK) et ressources de l’API Graph .NET Azure CosmosDB | Microsoft Docs"
description: "Découvrez l’API Graph Azure CosmosDB, notamment les dates de lancement, les dates de suppression et les modifications apportées entre chaque version."
services: cosmos-db
documentationcenter: .net
author: luisbosquez
manager: jhubbard
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/17/2017
ms.author: lbosq
ms.openlocfilehash: 6bae97783b63b76d2de2dbcd5e618f75bc4d701d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>API Graph .NET Azure Cosmos DB : Télécharger et notes de publication

|   |   |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**Documentation de l’API**|[Documentation de référence sur l’API .NET](https://aka.ms/acdbgraphapiref)|
|**Démarrage rapide**|[Azure Cosmos DB : Créer une application de graphe à l’aide de .NET et de l’API Graph](create-graph-dotnet.md)|
|**Didacticiel**|[Azure Cosmos DB : Créer un conteneur avec l’API Graph](tutorial-develop-graph-dotnet.md)|
|**Infrastructure actuellement prise en charge**| [Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>Notes de publication

Nous vous recommandons d’utiliser Gremlin.Net, le pilote open source Gremlin pour .NET. Pour plus d’informations, consultez [Azure Cosmos DB : Créer une application de graphe à l’aide de .NET et de l’API Graph](create-graph-dotnet.md).

### <a name="a-name031-preview031-preview"></a><a name="0.3.1-preview"/>0.3.1-préversion

#### <a name="bug-fixes"></a>Résolution des bogues
* Correctif pour charger éventuellement `appsettings.json` (`netstandard1.6`)

#### <a name="whats-new"></a>Nouveautés
* Modification de Microsoft.Azure.Graphs pour cibler la plateforme AnyCPU.
* Suppression de l’assembly Mono du manifeste de package `net461`.

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-préversion

#### <a name="whats-new"></a>Nouveautés
* Ajout de la prise en charge de `.netstandard 1.6`
  * Nécessite `Microsoft.Azure.DocumentDB.Core >= 1.5.1`
* Ajout d’un nouvel analyseur `gremlin-groovy` pour remplacer l’analyseur existant. Cet analyseur prend en charge un sous-ensemble de la syntaxe `gremlin-groovy` de Tinkerpop et comprend :
  * Multiplication par deux des performances d’analyse.
  * Résolution de plusieurs problèmes liés à l’échappement des caractères dans les chaînes, au traitement incorrect des valeurs littérales et à d’autres irrégularités dans l’ancien analyseur.
* Ajout d’optimisations pour les traversées avec des prédicats de périphérie.
  *  Les sauts de traversée avec filtres devraient bénéficier de cette amélioration, par exemple : `g.V('1').outE().has('name', 'marko').inV()`.
* Ajout d’optimisations pour les traversées avec l’étape `limit()`.

#### <a name="breaking-changes"></a>Dernières modifications
* Suppression de la prise en charge de .NET Framework 4.5.1

* Le nouvel analyseur s’aligne avec la grammaire `gremlin-groovy`. Ainsi, certaines expressions qui fonctionnaient précédemment sont ambiguës pour le nouvel analyseur. À noter :
  * `in` et `as` étant des mots clés réservés dans `gremlin-groovy`, ces étapes doivent être qualifiées avec `.in()` ou `.as()` pour éviter les erreurs de syntaxe. Par exemple : `g.V().repeat(in()).times(2)` -> _lève une erreur de syntaxe_  
 `g.V().repeat(__.in()).times(2)` -> _réussit_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-préversion

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-préversion

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-préversion

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-préversion

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview
* Préversion initiale.

## <a name="release--retirement-dates"></a>Dates de lancement et de suppression
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible. 

Toute requête envoyée à Azure Cosmos DB qui utilise un SDK supprimé est rejetée par le service.

<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [0.3.1-préversion](#0.3.1-preview) |17 octobre 2017 |--- |
| [0.3.0-préversion](#0.3.0-preview) |2 octobre 2017 |--- |
| [0.2.4-préversion](#0.2.4-preview) |4 août 2017 |--- |
| [0.2.2-préversion](#0.2.2-preview) |23 juin 2017 |--- |
| [0.2.1-préversion](#0.2.2-preview) |8 juin 2017 |--- |
| [0.2.0-préversion](#0.2.2-preview) |10 mai 2017 |--- |
| [0.1.0-preview](#0.1.0-preview) |8 mai 2017 |--- |

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur l’API Graph Azure Cosmos DB, consultez [Présentation d’Azure Cosmos DB : API Graph](graph-introduction.md). 
