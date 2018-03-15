---
title: "Comment interroger des données de graphique dans Azure Cosmos DB ? | Microsoft Docs"
description: "Apprendre à interroger des données de graphique dans Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: 
tags: 
ms.assetid: 8bde5c80-581c-4f70-acb4-9578873c92fa
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 5a635abfa9fa10cd8c8498e3c95a17af997cea3e
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="azure-cosmos-db-how-to-query-with-the-graph-api"></a>Azure Cosmos DB : Comment interroger avec l’API Graph ?

L’[API Graph](graph-introduction.md) d’Azure Cosmos DB prend en charge les requêtes [Gremlin](https://github.com/tinkerpop/gremlin/wiki). Cet article fournit des exemples de documents et de requêtes pour vous aider à démarrer. L’article relatif à la [prise en charge de Gremlin](gremlin-support.md) comporte des informations de référence détaillées sur Gremlin.

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Interrogation des données avec Gremlin

## <a name="prerequisites"></a>Prérequis

Pour le bon fonctionnement de ces requêtes, vous devez disposer d’un compte Azure Cosmos DB et de données de graphique dans le conteneur. Cela n’est pas le cas ? Lancez le [démarrage rapide de 5 minutes](create-graph-dotnet.md) ou le [didacticiel destiné aux développeurs](tutorial-query-graph.md) pour créer un compte et alimenter votre base de données. Vous pouvez exécuter les requêtes suivantes à l’aide de la [bibliothèque de graphiques Azure Cosmos DB .NET](graph-sdk-dotnet.md), la [console Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console), ou votre pilote Gremlin favori.

## <a name="count-vertices-in-the-graph"></a>Compter le nombre de vertex du graphique

L’extrait de code suivant montre comment compter le nombre de vertex du graphique :

```
g.V().count()
```

## <a name="filters"></a>Filtres

Vous pouvez appliquer des filtres à l’aide des étapes `has` et `hasLabel` de Gremlin et les associer à l’aide de `and`, `or`, et `not` pour créer des filtres plus complexes. Azure Cosmos DB propose une indexation sans schéma de toutes les propriétés au sein de vos vertex et degrés pour les requêtes rapides :

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projection

Vous pouvez projeter certaines propriétés dans les résultats de requête à l’aide de l’étape `values` :

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Rechercher des vertex et des bords associés

Jusqu’ici, nous avons seulement abordé les opérateurs de requête fonctionnant dans n’importe quelle base de données. Les graphiques sont rapides et efficaces pour les opérations de traversée lorsque vous avez besoin d’accéder aux vertex et bords associés. Recherchons à présent tous les amis de Thomas. Nous suivons pour cela l’étape `outE` de Gremlin pour rechercher toutes les bords externes de Thomas afin de se diriger vers les vertex internes de ces bords en suivant l’étape `inV` de Gremlin :

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

La requête suivante effectue deux sauts pour rechercher tous les « amis des amis de Thomas », en appelant `outE` et `inV` deux fois. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Vous pouvez créer des requêtes plus complexes et implémenter une logique de traversée de graphique puissante à l’aide de Gremlin, y compris en mixant des expressions de filtre, en exécutant des boucles à l’aide de l’étape `loop` et en mettant en œuvre la navigation conditionnelle à l’aide de l’étape `choose`. En savoir plus sur ce que la [prise en charge de Gremlin](gremlin-support.md) vous permet de faire !

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez :

> [!div class="checklist"]
> * Effectuer des interrogations à l’aide de Graph 

Vous pouvez maintenant poursuivre avec le didacticiel suivant montrant comment distribuer vos données globalement.

> [!div class="nextstepaction"]
> [Distribuer vos données globalement](tutorial-global-distribution-sql-api.md)
