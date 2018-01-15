---
title: "Leçon 5 du didacticiel Azure Analysis Services : Créer des colonnes calculées | Microsoft Docs"
description: "Explique comment créer des colonnes calculées dans le projet du didacticiel Azure Analysis Services."
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
ms.openlocfilehash: 9b491af1e5c9da03f48335f5793ed71ddaf644fb
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="create-calculated-columns"></a>Créer des colonnes calculées

Dans le cadre de cette leçon, vous créez des données dans votre modèle en ajoutant des colonnes calculées. Vous pouvez créer des colonnes calculées (sous la forme de colonnes personnalisées) lorsque vous utilisez Obtenir des données, à l’aide de l’Éditeur de requêtes, ou ultérieurement dans le Concepteur de modèles comme décrit dans cette leçon. Pour en savoir plus, consultez [Colonnes calculées](https://docs.microsoft.com/sql/analysis-services/tabular-models/ssas-calculated-columns).
  
Vous créez cinq colonnes calculées dans trois tables différentes. La procédure diffère légèrement pour chacune des tâches, de façon à montrer qu’il existe plusieurs façons de créer des colonnes, de les renommer et de les placer à différents emplacements d’une table.  

Cette leçon vous permet également d’utiliser pour la première fois le langage DAX (Data Analysis Expressions). DAX est un langage spécial permettant de créer des expressions de formule hautement personnalisables pour les modèles tabulaires. Dans le cadre de ce didacticiel, vous utilisez DAX pour créer des colonnes calculées, des mesures et des filtres de rôle. Pour en savoir plus, consultez [DAX dans les modèles tabulaires](https://docs.microsoft.com/sql/analysis-services/tabular-models/understanding-dax-in-tabular-models-ssas-tabular). 
  
Durée estimée pour suivre cette leçon : **15 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon, vous devez avoir suivi la leçon précédente : [Leçon 4 : Créer des relations](../tutorials/aas-lesson-4-create-relationships.md). 
  
## <a name="create-calculated-columns"></a>Créer des colonnes calculées  
  
#### <a name="create-a-monthcalendar-calculated-column-in-the-dimdate-table"></a>Créer une colonne calculée MonthCalendar dans la table DimDate  
  
1.  Cliquez sur le menu **Modèle**> **Vue du modèle** > **Vue de données**.  
  
    Les colonnes calculées peuvent uniquement être créées à l’aide du Concepteur de modèles dans la vue de données.  
  
2.  Dans le Concepteur de modèles, cliquez sur la table **DimDate** (onglet).  
  
3.  Cliquez avec le bouton droit sur l’en-tête de colonne **CalendarQuarter**, puis cliquez sur **Insérer une colonne**.  
  
    Une nouvelle colonne nommée **Calculated Column 1** est insérée à gauche de la colonne **Calendar Quarter**.  
  
4.  Dans la barre de formule située au-dessus de la table, tapez la formule DAX ci-après. La saisie semi-automatique vous aide à taper les noms complets des colonnes et des tables, et répertorie les fonctions disponibles.  
  
    ```  
    =RIGHT(" " & FORMAT([MonthNumberOfYear],"#0"), 2) & " - " & [EnglishMonthName]  
    ``` 
  
    Les valeurs remplissent alors toutes les lignes de la colonne calculée. Si vous faites défiler la table vers le bas, vous remarquez que les lignes peuvent avoir des valeurs différentes pour cette colonne, en fonction des données figurant dans chaque ligne.    
  
5.  Renommez cette colonne **MonthCalendar**. 

    ![aas-lesson5-newcolumn](../tutorials/media/aas-lesson5-newcolumn.png) 
  
La colonne calculée MonthCalendar fournit un nom triable pour le mois.  
  
#### <a name="create-a-dayofweek-calculated-column-in-the-dimdate-table"></a>Créer une colonne calculée DayOfWeek dans la table DimDate  
  
1.  La table **DimDate** étant toujours active, cliquez sur le menu **Colonne**, puis sur **Ajouter une colonne**.  
  
2.  Dans la barre de formule, tapez la formule suivante :  
    
    ```
    =RIGHT(" " & FORMAT([DayNumberOfWeek],"#0"), 2) & " - " & [EnglishDayNameOfWeek]  
    ```
    
    Quand vous avez terminé de générer la formule, appuyez sur Entrée. La nouvelle colonne est ajoutée à l’extrême droite de la table.  
  
3.  Renommez la colonne en **DayOfWeek**.  
  
4.  Cliquez sur l’en-tête de colonne, puis faites glisser la colonne entre la colonne **EnglishDayNameOfWeek** et la colonne **DayNumberOfMonth**.  
  
    > [!TIP]  
    > Déplacer les colonnes dans la table facilite la navigation.  
  
La colonne calculée DayOfWeek fournit un nom triable pour le jour de la semaine.  
  
#### <a name="create-a-productsubcategoryname-calculated-column-in-the-dimproduct-table"></a>Créer une colonne calculée ProductSubcategoryName dans la table DimProduct  
  
  
1.  Dans la table **DimProduct**, faites défiler l’affichage jusqu’à l’extrême droite de la table. Notez que la colonne la plus à droite est nommée **Add Column** (en italique). Cliquez sur l’en-tête de colonne.  
  
2.  Dans la barre de formule, tapez la formule suivante :  
    
    ```
    =RELATED('DimProductSubcategory'[EnglishProductSubcategoryName])  
    ```
  
3.  Renommez la colonne en **ProductSubcategoryName**.  
  
La colonne calculée ProductSubcategoryName est utilisée pour créer une hiérarchie dans la table DimProduct, qui inclut les données de la colonne EnglishProductSubcategoryName de la table DimProductSubcategory. Les hiérarchies ne peuvent pas couvrir plusieurs tables. Vous créerez des hiérarchies ultérieurement, dans le cadre de la Leçon 9.  
  
#### <a name="create-a-productcategoryname-calculated-column-in-the-dimproduct-table"></a>Créer une colonne calculée ProductCategoryName dans la table DimProduct  
  
1.  La table **DimProduct** étant toujours active, cliquez sur le menu **Colonne**, puis sur **Ajouter une colonne**.  
  
2.  Dans la barre de formule, tapez la formule suivante :  
  
    ```
    =RELATED('DimProductCategory'[EnglishProductCategoryName]) 
    ```
    
3.  Renommez la colonne en **ProductCategoryName**.  
  
La colonne calculée ProductCategoryName est utilisée pour créer une hiérarchie dans la table DimProduct, qui inclut les données de la colonne EnglishProductCategoryName de la table DimProductCategory. Les hiérarchies ne peuvent pas couvrir plusieurs tables.  
  
#### <a name="create-a-margin-calculated-column-in-the-factinternetsales-table"></a>Créer une colonne calculée Margin dans la table FactInternetSales  
  
1.  Dans le Concepteur de modèles, sélectionnez la table **FactInternetSales**.  
  
2.  Créez une colonne calculée entre la colonne **SalesAmount** et la colonne **TaxAmt**.  
  
3.  Dans la barre de formule, tapez la formule suivante :  
  
    ```
    =[SalesAmount]-[TotalProductCost]
    ``` 

4.  Renommez la colonne en **Margin**.  
 
      ![aas-lesson5-newmargin](../tutorials/media/aas-lesson5-newmargin.png)
      
    La colonne calculée Margin est utilisée pour analyser les marges pour chaque vente.  
  
## <a name="whats-next"></a>Et ensuite ?
[Leçon 6 : Créer des mesures](../tutorials/aas-lesson-6-create-measures.md).
  
  
  
