---
title: "Guide pratique pour détecter les sources de données dans Azure Data Catalog | Microsoft Docs"
description: "Cet article traite de la détection des ressources de données inscrites dans Azure Data Catalog, et aborde notamment les fonctionnalités de recherche, de filtrage et de mise en surbrillance des correspondances dans le portail Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: f72ae3a3-6573-4710-89a7-f13555e1968c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: e2db429860e6e3a60211c993bebfecbca98d67fd
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Comment détecter les sources de données dans Azure Data Catalog
## <a name="introduction"></a>Introduction
Azure Data Catalog est un service cloud entièrement géré qui permet d’inscrire et de détecter les sources de données d’entreprise. En d’autres termes, Data Catalog aide les utilisateurs à détecter, comprendre et utiliser les sources de données, et permet aux organisations de mieux exploiter leurs données existantes. Une fois qu’une source de données a été inscrite auprès d’Azure Data Catalog, ses métadonnées sont indexées par le service. Vous pouvez donc effectuer des recherches pour détecter les données dont vous avez besoin.

## <a name="searching-and-filtering"></a>Recherche et filtrage
Dans Data Catalog, la détection utilise deux mécanismes principaux : la recherche et le filtrage.

La recherche est conçue pour être intuitive et puissante. Par défaut, les termes de recherche sont comparés à toutes les propriétés du catalogue, notamment aux annotations fournies par l’utilisateur.

Le filtrage est conçu pour compléter la recherche. Vous pouvez sélectionner des caractéristiques telles que les experts, le type de source de données, le type d’objet ou les balises. Vous pouvez afficher uniquement les ressources de données correspondant à votre recherche et limiter les résultats de recherche aux ressources correspondantes.

En combinant recherche et filtrage, vous pouvez parcourir rapidement les sources de données qui ont été inscrites dans Data Catalog pour découvrir celles qui vous sont utiles.

## <a name="search-syntax"></a>Syntaxe de recherche
La recherche en texte libre par défaut est simple et intuitive. Cependant, vous pouvez également utiliser la syntaxe de recherche de Data Catalog pour mieux contrôler les résultats de recherche. La recherche avec Data Catalog prend en charge les techniques suivantes :

| Technique | Utilisation | exemples |
| --- | --- | --- |
| Recherche de base |Recherche de base à l’aide d’un ou plusieurs termes de recherche. Les résultats sont constitués de toutes les ressources correspondant à une propriété quelconque avec un ou plusieurs termes spécifiés. |`sales data` |
| Étendue de la propriété |Retourne des sources de données uniquement si le terme de recherche correspond à la propriété spécifiée. |`name:finance` |
| Opérateurs booléens |Les opérations booléennes permettent d’étendre et de limiter une recherche. |`finance NOT corporate` |
| Parenthèses de regroupement |Utilisez des parenthèses pour grouper les parties de la requête à isoler logiquement, notamment lorsque vous appliquez des opérateurs booléens. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Opérateurs de comparaison |Utilisez des comparaisons autres que l’égalité pour les propriétés comportant des types de données numériques et de date. |`modifiedTime > "11/05/2014"` |

Pour plus d’informations sur la recherche Data Catalog, consultez l’article [Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## <a name="hit-highlighting"></a>Mise en surbrillance des correspondances
Lorsque vous affichez les résultats de la recherche, les propriétés affichées correspondant aux termes de recherche spécifiés (par exemple, le nom de la ressource de données, sa description ou ses balises) sont mises en surbrillance. Il est ainsi plus facile d’identifier la raison pour laquelle une ressource de données spécifique a été retournée par une recherche.

> [!NOTE]
> Pour désactiver la mise en surbrillance des correspondances, utilisez le bouton **Mettre en surbrillance** dans le portail Data Catalog.
>
>

Lorsque vous affichez les résultats de la recherche, il n’est pas toujours évident de comprendre pourquoi une ressource de données a été retournée, même lorsque la mise en surbrillance des correspondances est activée. Étant donné que, par défaut, toutes les propriétés font l’objet de la recherche, une ressource de données peut être retournée si une correspondance avec une propriété de colonne est détectée. De plus, dans la mesure où plusieurs utilisateurs peuvent annoter des ressources de données inscrites avec leurs propres balises et descriptions, il est possible que toutes les métadonnées ne s’affichent pas dans la liste des résultats de recherche.

Dans l’affichage en mosaïque par défaut, chaque vignette affichée dans les résultats de recherche inclut l’icône **Afficher les correspondances des termes recherchés**. Vous pouvez ainsi visualiser rapidement le nombre de correspondances, ainsi que leur emplacement, et y accéder si vous le souhaitez.

 ![Mise en surbrillance des correspondances et résultats de recherche dans le portail Azure Data Catalog](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Résumé
L’inscription des sources de données dans Data Catalog facilite leur détection et leur compréhension, car elle consiste à copier les métadonnées descriptives et structurelles des sources de données dans le service Catalog. Une fois qu’une source de données a été inscrite, vous pouvez la détecter grâce aux fonctionnalités de filtrage et de recherche du portail Data Catalog.

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des informations détaillées sur la détection des sources de données, consultez [Bien démarrer avec Azure Data Catalog](data-catalog-get-started.md).
