---
title: "Leçon 4 du didacticiel Azure Analysis Services : Créer des relations | Microsoft Docs"
description: "Explique comment créer des relations dans le projet du didacticiel Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/08/2018
ms.author: owend
ms.openlocfilehash: 57b3a172445047291f0aea5b1616b9dcbf6bf745
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="create-relationships"></a>Créer des relations

Dans cette leçon, vous allez vérifier les relations qui ont été créées automatiquement lorsque vous avez importé les données, et vous allez ajouter des relations entre différentes tables. Une relation est une connexion entre deux tables qui définit la manière dont les données de ces tables doivent être mises en corrélation. Par exemple, les tables DimProduct et DimProductSubcategory ont une relation basée sur le fait que chaque produit appartient à une sous-catégorie. Pour plus d’informations, consultez [Relations](https://docs.microsoft.com/sql/analysis-services/tabular-models/relationships-ssas-tabular).
  
Durée estimée pour suivre cette leçon : **10 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon, vous devez avoir terminé la leçon précédente : [Leçon 3 : Marquer en tant que Table de dates](../tutorials/aas-lesson-3-mark-as-date-table.md). 
  
## <a name="review-existing-relationships-and-add-new-relationships"></a>Vérifier les relations existantes et ajouter de nouvelles relations  
Lorsque vous avez importé des données à l’aide de la fonctionnalité Obtenir des données, vous avez obtenu sept tables de la base de données AdventureWorksDW2014. En règle générale, lorsque vous importez des données à partir d’une source relationnelle, les relations existantes sont importées automatiquement avec les données. Pour que Get Data crée automatiquement des relations dans le modèle de données, il doit y avoir des relations entre les tables à la source de données.

Avant de poursuivre avec la création de votre modèle, vous devez vérifier que les relations entre les tables ont été créées correctement. Dans le cadre de ce didacticiel, vous ajoutez aussi trois nouvelles relations.  

  
#### <a name="to-review-existing-relationships"></a>Pour vérifier les relations existantes  
  
1.  Cliquez sur le menu **Modèle** > **Vue du modèle** > **Vue de diagramme**.  

    Le Concepteur de modèles apparaît à présent dans la vue de diagramme, sous la forme d’un graphique affichant toutes les tables que vous avez importées, reliées entre elles par des lignes. Ces lignes indiquent les relations qui ont été créées automatiquement lorsque vous avez importé les données.
    
    ![aas-lesson4-diagram](../tutorials/media/aas-lesson4-diagram.png)
  
    > [!NOTE]
    > Si vous ne voyez aucune relation entre les tables, cela veut probablement dire qu’il n’y a aucune relation entre elles à la source des données.

    Incluez autant de tables que possible en utilisant les contrôles de la minicarte situés dans le coin inférieur droit du Concepteur de modèles. Vous pouvez également cliquer et faire glisser les tables à différents emplacements, de manière à les rapprocher ou à les placer dans un ordre particulier. Le déplacement des tables n’affecte pas les relations entre elles. Pour visualiser toutes les colonnes d’une table spécifique, cliquez sur un bord de la table et faites-le glisser pour développer ou réduire la taille de la table.  
  
2.  Cliquez sur la ligne pleine située entre la table **DimCustomer** et la table **DimGeography**. La ligne pleine entre ces deux tables indique que cette relation est active, autrement dit, qu’elle est utilisée par défaut lors du calcul des formules DAX.  
  
    Notez que la colonne **GeographyKey** de la table **DimCustomer** et la colonne **GeographyKey** de la table **DimGeography** s’affichent désormais dans un cadre. Ces colonnes sont utilisées dans la relation. Les propriétés de la relation apparaissent maintenant aussi dans la fenêtre **Propriétés**.  
  
    > [!TIP]  
    > Outre l’utilisation du Générateur de modèles dans la vue de diagramme, vous pouvez également utiliser la boîte de dialogue Gérer les relations pour afficher les relations entre toutes les tables dans un format tabulaire. Dans l’Explorateur de modèles tabulaires, cliquez sur **Relations** > **Gérer les relations**.
  
3.  Vérifiez que les relations ci-après ont été créées lorsque chacune des tables a été importée à partir de la base de données AdventureWorksDW :  
  
    |Actif|Table|Table de recherche associée|  
    |----------|---------|------------------------|  
    |Oui|**DimCustomer [GeographyKey]**|**DimGeography [GeographyKey]**|  
    |Oui|**DimProduct [ProductSubcategoryKey]**|**DimProductSubcategory [ProductSubcategoryKey]**|  
    |Oui|**DimProductSubcategory [ProductCategoryKey]**|**DimProductCategory [ProductCategoryKey]**|  
    |Oui|**FactInternetSales [CustomerKey]**|**DimCustomer [CustomerKey]**|  
    |Oui|**FactInternetSales [ProductKey]**|**DimProduct [ProductKey]**|  
  
    Si l’une des relations est manquante, vérifiez que votre modèle comprend les tables suivantes : DimCustomer, DimDate, DimGeography, DimProduct, DimProductCategory, DimProductSubcategory et FactInternetSales. Si vous importez des tables provenant de la même connexion de source de données à des moments différents, les relations entre ces tables ne sont pas créées et doivent être créées manuellement. Si aucune relation n’apparaît, cela veut dire qu’il n’y en a aucune à la source de données. Vous pouvez les créer manuellement dans le modèle de données.

### <a name="take-a-closer-look"></a>Examen approfondi
Dans la vue de diagramme, notez la présence d’une flèche, d’un astérisque et d’un nombre sur les lignes, qui indiquent la relation entre les tables.

![aas-lesson4-line](../tutorials/media/aas-lesson4-line.png)

La flèche indique la direction du filtre. L’astérisque signifie que cette table est le côté « plusieurs » de la cardinalité de la relation, tandis que le 1 indique que cette table est le côté « un » de la relation. Si vous devez modifier une relation (par exemple, modifier la direction du filtre de la relation ou sa cardinalité), double-cliquez sur la ligne de relation pour ouvrir la boîte de dialogue Modifier la relation.

![aas-lesson4-edit](../tutorials/media/aas-lesson4-edit.png)

Ces fonctionnalités sont destinées à la modélisation de données avancée et sortent du cadre de ce didacticiel. Pour plus d’informations, consultez [Filtres croisés bidirectionnels pour les modèles tabulaires dans Analysis Services](https://docs.microsoft.com/sql/analysis-services/tabular-models/bi-directional-cross-filters-tabular-models-analysis-services).

Dans certains cas, vous devrez peut-être créer des relations supplémentaires entre les tables de votre modèle pour répondre aux besoins d’une certaine logique métier. Pour ce didacticiel, vous devez créer trois relations supplémentaires entre la table FactInternetSales et la table DimDate.  
  
#### <a name="to-add-new-relationships-between-tables"></a>Pour ajouter de nouvelles relations entre les tables  
  
1.  Dans le Concepteur de modèles, dans la table **FactInternetSales**, cliquez sur la colonne **OrderDate** et maintenez le bouton de la souris enfoncé, faites glisser le curseur vers la colonne **Date** de la table **DimDate**, puis relâchez le bouton de la souris.  

    Une ligne pleine s’affiche pour indiquer que vous avez créé une relation active entre la colonne **OrderDate** de la table **Internet Sales** et la colonne **Date** de la table **Date**. 
  
      ![aas-lesson4-new](../tutorials/media/aas-lesson4-new.png) 
  
    > [!NOTE]  
    > Lors de la création de relations, la cardinalité et la direction du filtre entre la table primaire et la table de recherche associée sont automatiquement sélectionnées.  
  
2.  Dans la table **FactInternetSales**, cliquez sur la colonne **DueDate** et maintenez le bouton enfoncé, faites glisser le curseur vers la colonne **Date** de la table **DimDate**, puis relâchez le bouton.  
  
    Une ligne en pointillé s’affiche pour indiquer que vous avez créé une relation inactive entre la colonne **DueDate** de la table **FactInternetSales** et la colonne **Date** de la table **DimDate**. Vous pouvez avoir plusieurs relations entre les tables, mais une seule relation peut être active à la fois. Les relations inactives peuvent devenir actives si vous devez effectuer des agrégations spéciales dans des expressions DAX personnalisées.  
  
3.  Pour finir, créez une dernière relation. Dans la table **FactInternetSales**, cliquez sur la colonne **ShipDate** et maintenez le bouton de la souris enfoncé, faites glisser le curseur vers la colonne **Date** de la table **DimDate**, puis relâchez le bouton de la souris.  
    
     ![aas-lesson4-newinactive](../tutorials/media/aas-lesson4-newinactive.png)
  
## <a name="whats-next"></a>Et ensuite ?
[Leçon 5 : Créer des colonnes calculées](../tutorials/aas-lesson-5-create-calculated-columns.md).
  
  
  
