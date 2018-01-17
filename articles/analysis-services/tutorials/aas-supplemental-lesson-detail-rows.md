---
title: "Leçon supplémentaire du didacticiel Azure Analysis Services : Lignes de détails | Microsoft Docs"
description: "Explique comment créer une expression de lignes de détails dans le didacticiel Azure Analysis Services."
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
ms.openlocfilehash: 5a4dc7004245923fa6bda779114166ecf08d075f
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="supplemental-lesson---detail-rows"></a>Leçon supplémentaire – Lignes de détails

Dans cette leçon supplémentaire, vous allez utiliser l’éditeur DAX pour définir une expression de lignes de détail personnalisée. Une expression de lignes de détail est une propriété sur une mesure qui fournit aux utilisateurs finaux des informations supplémentaires sur les résultats agrégées d’une mesure. 
  
Durée estimée pour suivre cette leçon : **10 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette leçon supplémentaire fait partie d’un didacticiel de modélisation tabulaire. Avant d’effectuer les tâches de cette leçon supplémentaire, vous devez avoir effectué toutes les leçons précédentes ou disposer d’un exemple de projet de modèle de ventes sur Internet Adventure Works.  
  
## <a name="whats-the-issue"></a>Quel est le problème ?
Examinons les détails de notre mesure InternetTotalSales avant l’ajout d’expression de lignes de détail.

1.  Dans SSDT, cliquez sur le menu **Modèle** > **Analyser dans Excel** pour ouvrir Excel et créer un tableau croisé dynamique vide.
  
2.  Dans **Champs de tableau croisé dynamique**, ajoutez la mesure **InternetTotalSales** de la table FactInternetSales à **Valeurs**, **CalendarYear** de la table DimDate à **Colonnes** et **EnglishCountryRegionName** à **Lignes**. Le tableau croisé dynamique nous indique maintenant les résultats agrégés de la mesure InternetTotalSales par région et par année. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. Dans le tableau croisé dynamique, double-cliquez sur une valeur agrégée pour une année et un nom de région. Ici, nous avons double-cliqué sur la valeur correspondant à l’Australie et à l’année 2014. Cette opération affiche une nouvelle feuille contenant des données dont nous n’avons pas l’utilité.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
Nous aimerions voir ici une table contenant des colonnes et des lignes de données qui contribuent au résultat agrégé de notre mesure InternetTotalSales. Pour ce faire, nous pouvons ajouter une expression de lignes de détail comme propriété de la mesure.

## <a name="add-a-detail-rows-expression"></a>Ajouter une expression de lignes de détail

#### <a name="to-create-a-detail-rows-expression"></a>Pour créer une expression de lignes de détail 
  
1. Dans la grille de mesure de la table FactInternetSales, cliquez sur la mesure **InternetTotalSales**. 

2. Dans **Propriétés** > **Expression de lignes de détail**, cliquez sur le bouton de l’éditeur pour ouvrir l’éditeur DAX.

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. Dans l’éditeur DAX, entrez l’expression suivante :

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    Cette expression indique que des noms, des colonnes et des résultats de mesure provenant de la table FactInternetSales et de tables associées sont retournés quand un utilisateur double-clique sur un résultat agrégé dans un tableau croisé dynamique ou un rapport.

4. De retour dans Excel, supprimez la feuille créée à l’étape 3, puis double-cliquez sur une valeur agrégée. Cette fois, avec une propriété d’expression de lignes de détail définie pour la mesure, une nouvelle feuille contenant des données beaucoup plus utiles s’ouvre.

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. Redéployez votre modèle.

  
## <a name="see-also"></a>Voir aussi  
[SELECTCOLUMNS, fonction (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)  
[Leçon supplémentaire – Sécurité dynamique](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Leçon supplémentaire – Hiérarchies irrégulières](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)  
