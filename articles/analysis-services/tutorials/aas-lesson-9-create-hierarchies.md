---
title: "Leçon 9 du didacticiel Azure Analysis Services : Créer des hiérarchies | Microsoft Docs"
description: 
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
ms.openlocfilehash: 041096b1a93fdc671939b6d6715a7836d1977e3c
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="create-hierarchies"></a>Créer des hiérarchies

Dans cette leçon, vous allez créer des hiérarchies. Les hiérarchies sont des groupes de colonnes organisées en niveaux. Par exemple, la hiérarchie Géographie peut comporter des sous-niveaux correspondant à des pays, des régions, des départements et des villes. Les hiérarchies peuvent être affichées séparément des autres colonnes, dans une liste de champs d’applications clientes de création de rapports, ce qui permet aux utilisateurs clients de naviguer facilement parmi les hiérarchies et de les inclure dans un rapport. Pour plus d’informations, consultez [Hiérarchies](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular).
  
Pour créer des hiérarchies, utilisez le Générateur de modèles disponible dans la *Vue de diagramme*. La création et la gestion des hiérarchies ne sont pas prises en charge dans la vue de données.  
  
Durée estimée pour suivre cette leçon : **20 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon, vous devez avoir terminé la leçon précédente : [Leçon 8 : Créer des perspectives](../tutorials/aas-lesson-8-create-perspectives.md).  
  
## <a name="create-hierarchies"></a>Créer des hiérarchies  
  
#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>Pour créer une hiérarchie de catégories dans la table DimProduct  
  
1.  Dans le Concepteur de modèles (vue de diagramme), cliquez sur la table **DimProduct** > **Créer une hiérarchie**. Une nouvelle hiérarchie s’affiche au bas de la table. Attribuez à la hiérarchie le nom de **Catégorie**.  
  
2.  Cliquez et faites glisser la colonne **ProductCategoryName** vers la nouvelle hiérarchie **Catégorie**.  
  
3.  Dans la hiérarchie **Catégorie**, cliquez avec le bouton droit sur **ProductCategoryName** > **Renommer**, puis tapez **Catégorie**.  
  
    > [!NOTE]  
    > Le fait de renommer une colonne dans une hiérarchie ne renomme pas cette colonne dans la table. Une colonne de hiérarchie n’est que la représentation de cette colonne dans la table.  
  
4.  Cliquez et faites glisser la colonne **ProductSubcategoryName** vers la hiérarchie **Catégorie**. Renommez cette colonne **Sous-catégorie**. 
  
5.  Cliquez avec le bouton droit sur la colonne **ModelName** > **Ajouter à la hiérarchie**, puis sélectionnez **Catégorie**. Renommez cette colonne **Modèle**.

6.  Enfin, ajoutez **EnglishProductName** à la hiérarchie Catégorie. Renommez cette colonne **Produit**.  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>Pour créer des hiérarchies dans la table DimDate  
  
1.  Dans la table **DimDate**, créez une hiérarchie nommée **Calendar**.  
  
3.  Ajoutez les colonnes suivantes, dans cet ordre :

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
    
4.  Dans la table **DimDate**, créez une hiérarchie **Fiscal**. Ajoutez les colonnes suivantes, dans cet ordre :  
  
    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
  
5.  Enfin, dans la table **DimDate**, créez la hiérarchie **ProductionCalendar**. Ajoutez les colonnes suivantes, dans cet ordre :  
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek
  
 ## <a name="whats-next"></a>Et ensuite ?
[Leçon 10 : Créer des partitions](../tutorials/aas-lesson-10-create-partitions.md). 
  
  
