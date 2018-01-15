---
title: "Leçon 6 du didacticiel Azure Analysis Services : Créer des mesures | Microsoft Docs"
description: "Explique comment créer des mesures dans le projet du didacticiel Azure Analysis Services."
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
ms.openlocfilehash: fa47d4ea9aa019464e465c051b016dac7c224dc9
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="create-measures"></a>Créer des mesures

Dans cette leçon, vous allez créer des mesures à inclure dans votre modèle. Une mesure est un calcul créé à l’aide d’une formule DAX, comme les colonnes calculées que vous avez créées. Toutefois, contrairement aux colonnes calculées, les mesures sont évaluées sur la base d’un *filtre* sélectionné par l’utilisateur. Exemple : une colonne ou un slicer spécifiques ajoutés au champ Étiquettes de lignes d’un tableau croisé dynamique. Pour chaque cellule du filtre, une valeur est alors calculée par la mesure appliquée. Les mesures sont des calculs puissants et flexibles que vous pouvez inclure dans quasiment tous les modèles tabulaires, afin d’effectuer des calculs dynamiques à partir de données numériques. Pour plus d’informations, consultez [Mesures](https://docs.microsoft.com/sql/analysis-services/tabular-models/measures-ssas-tabular).
  
Pour créer des mesures, utilisez la *Grille de mesures*. Par défaut, chaque table comprend une grille de mesures vide. Toutefois, la plupart du temps, vous n’êtes pas obligé de créer des mesures pour chaque table. Dans le Concepteur de modèles, lorsque vous êtes en mode Vue de données, la grille de mesures s’affiche sous une table. Pour afficher ou masquer la grille de mesures d’une table, cliquez sur le menu **Table**, puis sur **Afficher la grille de mesures**.  
  
Vous pouvez créer une mesure en cliquant sur une cellule vide de la grille de mesures, puis en saisissant une formule DAX dans la barre de formule. Lorsque vous cliquez sur ENTRÉE pour finaliser la formule, la mesure s’affiche dans la cellule. Vous pouvez également créer des mesures à l’aide d’une fonction d’agrégation standard en cliquant sur une colonne, puis en cliquant sur le bouton Somme automatique (**∑**) de la barre d’outils. Les mesures créées à l’aide de la fonction Somme automatique s’affichent dans la cellule de la grille de mesures, juste en dessous de la colonne. Cependant, elles peuvent être déplacées.  
  
Dans cette leçon, vous allez créer des mesures en entrant une formule DAX dans la barre de formule et en utilisant la fonction Somme automatique.  
  
Durée estimée pour suivre cette leçon : **30 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon, vous devez avoir terminé la leçon précédente : [Leçon 5 : Créer des colonnes calculées](../tutorials/aas-lesson-5-create-calculated-columns.md).  
  
## <a name="create-measures"></a>Créer des mesures  
  
#### <a name="to-create-a-dayscurrentquartertodate-measure-in-the-dimdate-table"></a>Pour créer une mesure DaysCurrentQuarterToDate dans la table DimDate  
  
1.  Dans le Concepteur de modèles, cliquez sur la table **DimDate**.  
  
2.  Dans la grille de mesures, cliquez sur la cellule vide située en haut à gauche.  
  
3.  Dans la barre de formule, tapez la formule suivante :  
  
    ```
    DaysCurrentQuarterToDate:=COUNTROWS( DATESQTD( 'DimDate'[Date])) 
    ```
  
    Notez que la cellule supérieure gauche contient maintenant une mesure nommée **DaysCurrentQuarterToDate**, suivie du résultat **92**. Le résultat n’est à ce stade pas pertinent car aucun filtre utilisateur n’a été appliqué.
    
      ![aas-lesson6-newmeasure](../tutorials/media/aas-lesson6-newmeasure.png) 
    
    Contrairement aux colonnes calculées, avec les formules de mesure, il est possible de saisir le nom de la mesure, suivi d’un signe deux-points, puis de l’expression de la formule.

  
#### <a name="to-create-a-daysincurrentquarter-measure-in-the-dimdate-table"></a>Pour créer une mesure DaysInCurrentQuarter dans la table DimDate  
  
1.  Vérifiez que la table **DimDate** est toujours active dans le Concepteur de modèles, puis, dans la grille de mesures, cliquez sur la cellule vide située sous la mesure que vous avez créée.  
  
2.  Dans la barre de formule, tapez la formule suivante :  
  
    ```
    DaysInCurrentQuarter:=COUNTROWS( DATESBETWEEN( 'DimDate'[Date], STARTOFQUARTER( LASTDATE('DimDate'[Date])), ENDOFQUARTER('DimDate'[Date])))
    ```
  
    lorsque vous créez un rapport de comparaison entre une période incomplète et la période précédente. La formule doit calculer la partie de la période qui s’est écoulée et la comparer à la même partie de la période précédente. Dans ce cas, [DaysCurrentQuarterToDate]/[DaysInCurrentQuarter] donne la durée écoulée de la période actuelle.  
  
#### <a name="to-create-an-internetdistinctcountsalesorder-measure-in-the-factinternetsales-table"></a>Pour créer une mesure InternetDistinctCountSalesOrder dans la table FactInternetSales  
  
1.  Cliquez sur la table **FactInternetSales**.   
  
2.  Cliquez sur l’en-tête de colonne **SalesOrderNumber**.  
  
3.  Dans la barre d’outils, cliquez sur la flèche vers le bas en regard du bouton Somme automatique (**∑**), puis sélectionnez **DistinctCount**.  
  
    La fonction Somme automatique crée automatiquement une mesure pour la colonne sélectionnée à l’aide de la formule d’agrégation standard DistinctCount.  
    
       ![aas-lesson6-newmeasure2](../tutorials/media/aas-lesson6-newmeasure2.png)
  
4.  Dans la grille de mesures, cliquez sur la nouvelle mesure, puis, dans la fenêtre **Propriétés**, dans **Nom de la mesure**, renommez la mesure ainsi : **InternetDistinctCountSalesOrder**. 
 
  
#### <a name="to-create-additional-measures-in-the-factinternetsales-table"></a>Pour créer des mesures supplémentaires dans la table FactInternetSales  
  
1.  À l’aide de la fonction Somme automatique, créez et nommez les mesures suivantes :  

    |des colonnes|Nom de la mesure|Somme automatique (∑)|Formule|  
    |----------------|----------|-----------------|-----------|  
    |SalesOrderLineNumber|InternetOrderLinesCount|Count|=COUNTA([SalesOrderLineNumber])|  
    |OrderQuantity|InternetTotalUnits|Somme|=SUM([OrderQuantity])|  
    |DiscountAmount|InternetTotalDiscountAmount|Somme|=SUM([DiscountAmount])|  
    |TotalProductCost|InternetTotalProductCost|Somme|=SUM([TotalProductCost])|  
    |SalesAmount|InternetTotalSales|Somme|=SUM([SalesAmount])|  
    |Margin|InternetTotalMargin|Somme|=SUM([Margin])|  
    |TaxAmt|InternetTotalTaxAmt|Somme|=SUM([TaxAmt])|  
    |Freight|InternetTotalFreight|Somme|=SUM([Freight])|  
  
2.  En cliquant sur une cellule vide de la grille de mesures, et à l’aide de la barre de formule, créez et nommez les mesures ci-dessous, dans l’ordre :  
  
      ```
      InternetPreviousQuarterMargin:=CALCULATE([InternetTotalMargin],PREVIOUSQUARTER('DimDate'[Date]))
      ```
      
      ```
      InternetCurrentQuarterMargin:=TOTALQTD([InternetTotalMargin],'DimDate'[Date])
      ```
  
      ```
      InternetPreviousQuarterMarginProportionToQTD:=[InternetPreviousQuarterMargin]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
      ```
      InternetPreviousQuarterSales:=CALCULATE([InternetTotalSales],PREVIOUSQUARTER('DimDate'[Date]))
      ```
  
      ```
      InternetCurrentQuarterSales:=TOTALQTD([InternetTotalSales],'DimDate'[Date])
      ```
      
      ```
      InternetPreviousQuarterSalesProportionToQTD:=[InternetPreviousQuarterSales]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
Les mesures créées pour la table FactInternetSales peuvent être utilisées pour analyser les données financières critiques telles que les ventes, les coûts et les profits, pour les éléments définis par le filtre sélectionné par l’utilisateur.  
  
## <a name="whats-next"></a>Et ensuite ?
[Leçon 7 : Afficher les indicateurs de performance clés](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  

  
