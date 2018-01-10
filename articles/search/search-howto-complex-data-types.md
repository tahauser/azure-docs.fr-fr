---
title: "Modélisation de types de données complexes dans Recherche Azure | Microsoft Docs"
description: "Les structures de données imbriquées ou hiérarchiques peuvent être modélisées dans un index Recherche Azure à l’aide d’un ensemble de lignes aplati et du type de données Collection."
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: complex data types; compound data types; aggregate data types
ms.assetid: e4bf86b4-497a-4179-b09f-c1b56c3c0bb2
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: d7a7400fe7470439dfa957f1ddb463e0a7f1a271
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Modélisation de types de données complexes dans Recherche Azure
Les jeux de données externes utilisés pour remplir un index Recherche Azure inclut parfois des sous-structures hiérarchiques ou imbriquées qui ne sont pas réparties proprement en un ensemble de lignes tabulaire. Des exemples de telles structures incluent les emplacements et les numéros de téléphone multiples pour un même client, les couleurs et les tailles multiples pour une même référence, les auteurs multiples pour un même livre, etc. En termes de modélisation, ces structures peuvent être désignées sous le nom de *types de données complexes*, *types de données composées*, *types de données composites* ou *types de données agrégées*, entre autres.

Les types de données complexes ne sont pas pris en charge nativement dans Recherche Azure, mais une solution de contournement éprouvée inclut un processus en deux étapes consistant à aplatir la structure, puis à utiliser un type de données **Collection** pour reconstituer la structure interne. Avec la technique décrite dans cet article, le contenu peut faire l’objet de recherches, être à choix multiples, filtré et trié.

## <a name="example-of-a-complex-data-structure"></a>Exemple d’une structure de données complexe
En règle générale, les données en question sont stockées sous la forme d’un ensemble de documents JSON ou XML, ou sous la forme d’éléments dans une banque NoSQL telle que Azure Cosmos DB. Du point de vue structurel, le défi provient de l’existence de plusieurs éléments enfants qui doivent pouvoir faire l’objet de recherches et être filtrés.  Pour commencer à illustrer la solution de contournement, prenons l’exemple du document JSON suivant, qui répertorie un ensemble de contacts :

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Bien que les champs nommés « id », « name » et « company » puissent facilement être mappés un à un en tant que champs au sein d’un index Recherche Azure, le champ « locations » contient un tableau d’emplacements, qui présentent chacun un ID et une description. Comme Recherche Azure ne propose pas de type de données prenant cette structure en charge, nous devons trouver un autre moyen pour la modéliser dans Recherche Azure. 

> [!NOTE]
> Cette technique est également décrite par Kirk Evans dans le billet de blog [Indexing Azure Cosmos DB with Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/) (Indexation d’Azure Cosmos DB avec Recherche Azure), où il présente une technique appelée « aplatissement de données », qui consiste, dans notre exemple, à faire appel à des champs `locationsID` et `locationsDescription` qui sont tous deux des [collections](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou un tableau de chaînes).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Partie 1 : Aplatir le tableau en champs individuels
Pour créer un index Recherche Azure prenant en charge ce jeu de données, créez des champs individuels pour la sous-structure imbriquée : `locationsID` et `locationsDescription` avec un type de données [collections](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou un tableau de chaînes). Vous devez indexer les valeurs « 1 » et « 2 » dans le champ `locationsID` pour John Smith et les valeurs « 3 » et « 4 » dans le champ `locationsID` pour Jen Campbell.  

Vos données dans Recherche Azure présentent l’aspect suivant : 

![Exemple de données, 2 lignes](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Partie 2 : Ajouter un champ collection dans la définition d’index
Dans le schéma d’index, les définitions de champ peuvent ressembler à cet exemple.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Valider les comportements de recherche et éventuellement étendre l’index
En supposant que vous avez créé l’index et chargé les données, vous pouvez maintenant tester la solution pour vérifier l’exécution des requêtes de recherche sur le jeu de données. Chaque champ **collection** doit **pouvoir faire l’objet de recherches**, être **filtrable** et **à choix multiples**. Vous devez pouvoir exécuter des requêtes telles que :

* Trouver toutes les personnes qui travaillent au siège d’Adventureworks (Adventureworks Headquarters).
* Obtenir le nombre de personnes qui font du télétravail (Home Office).  
* Des personnes qui font du télétravail, afficher les bureaux avec lesquels elles travaillent, ainsi que le nombre d’employés de chaque emplacement.  

Cette technique s’avère cependant inutilisable lorsque vous avez besoin d’effectuer une recherche associant l’ID et la description d’emplacement. Par exemple :

* Trouver toutes les personnes qui font du télétravail et dont l’ID d’emplacement est 4.  

Pour mémoire, le contenu d’origine ressemblait à ceci :

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Cependant, maintenant que nous avons séparé les données en champs distincts, il est impossible de savoir si la valeur « Home Office » pour Jen Campbell se rapporte à `locationsID 3` ou `locationsID 4`.  

Pour gérer ce cas, définissez un autre champ d’index qui regroupe l’ensemble des données dans une collection unique.  Pour notre exemple, nous appellerons ce champ `locationsCombined` et nous séparerons le contenu à l’aide d’un `||`, bien que vous puissiez choisir comme séparateur n’importe quel ensemble de caractères unique pour votre contenu. Par exemple : 

![Exemple de données, 2 lignes avec séparateur](./media/search-howto-complex-data-types/sample-data-2.png)

À l’aide de ce champ `locationsCombined` , nous pouvons maintenant effectuer davantage de requêtes, telles que :

* Afficher le nombre de personnes qui font du télétravail et dont l’ID d’emplacement est 4.  
* Rechercher les personnes qui font du télétravail et dont l’ID d’emplacement est 4. 

## <a name="limitations"></a>Limitations
Cette technique est utile pour un certain nombre de scénarios, mais elle n’est pas applicable dans tous les cas.  Par exemple :

1. Si vous ne disposez pas d’un ensemble de champs dans votre type de données complexe et il était impossible de mapper tous les types possibles à un seul champ. 
2. La mise à jour des objets imbriqués demande du travail supplémentaire pour déterminer avec exactitude ce qui doit être mis à jour dans l’index Recherche Azure.

## <a name="sample-code"></a>Exemple de code
Pour obtenir un exemple de l’indexation d’un jeu de données JSON complexe dans Recherche Azure de l’exécution d’un certain nombre de requêtes sur ce jeu de données, consultez ce [référentiel GitHub](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Étape suivante
[Votez pour la prise en charge native des types de données complexes](https://feedback.azure.com/forums/263029-azure-search) sur la page UserVoice de Recherche Azure et ajoutez tout commentaire que vous aimeriez que nous prenions en compte concernant l’implémentation de fonctionnalités. Vous pouvez également me contacter directement sur Twitter sur @liamca.

