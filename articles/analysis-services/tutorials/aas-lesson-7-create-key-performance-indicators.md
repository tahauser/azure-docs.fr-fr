---
title: "Leçon 7 du didacticiel Azure Analysis Services : Créer des indicateurs de performance clés | Microsoft Docs"
description: "Indique comment créer des indicateurs de performance clés dans le projet du didacticiel Azure Analysis Services."
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
ms.openlocfilehash: 8f25773541deba2a94d3adc0c9b61c1b90a90aa6
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="create-key-performance-indicators"></a>Créer des indicateurs de performance clés (KPI)

Dans cette leçon, vous allez créer des indicateurs de performance clés (KPI). Les indicateurs de performance clés (KPI) évaluent la performance d’une valeur, définie par une mesure de *base*, par rapport à une valeur *cible*, également définie par une mesure ou par une valeur absolue. Dans les applications clientes de création de rapports, les indicateurs de performance clés offrent aux professionnels un moyen d’obtenir rapidement et aisément un résumé d’un succès commercial ou d’identifier les tendances. Pour plus d’informations, consultez [Indicateurs de performance clés](https://docs.microsoft.com/sql/analysis-services/tabular-models/kpis-ssas-tabular).
  
Durée estimée pour suivre cette leçon : **15 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon, vous devez avoir suivi la leçon précédente : [Leçon 6 : Créer des mesures](../tutorials/aas-lesson-6-create-measures.md).   
  
## <a name="create-key-performance-indicators"></a>Créer des indicateurs de performance clés (KPI)  
  
#### <a name="to-create-an-internetcurrentquartersalesperformance-kpi"></a>Pour créer un indicateur de performance clé InternetCurrentQuarterSalesPerformance  
  
1.  Dans le Concepteur de modèles, cliquez sur la table **FactInternetSales**.  
  
2.  Dans la grille de mesure, cliquez sur une cellule vide.  
  
3.  Dans la barre de formule située au-dessus de la table, tapez la formule suivante : 
 
    ```  
    InternetCurrentQuarterSalesPerformance :=DIVIDE([InternetCurrentQuarterSales]/[InternetPreviousQuarterSalesProportionToQTD],BLANK())  
    ```

    Cette mesure sert de mesure de base pour l’indicateur de performance clé.  
  
4.  Dans la grille de mesures, cliquez avec le bouton droit sur **InternetCurrentQuarterSalesPerformance** > **Créer un KPI**.   
  
5.  Dans la boîte de dialogue Indicateur de performance clé (KPI), dans **Cible**, sélectionnez **Valeur absolue**, puis tapez **1.1**.  
  
7.  Dans le champ à curseur de gauche (valeurs basses), tapez **1**, puis dans le champ à curseur de droite (valeurs élevées), tapez **1.07**.  
  
8.  Dans **Sélectionnez le style d’icône**, sélectionnez le losange (rouge), le triangle (jaune) ou le cercle (vert) comme type d’icône.
  
    ![aas-lesson7-kpi](../tutorials/media/aas-lesson7-kpi.png)
    
    > [!TIP]  
    > Notez l’étiquette extensible **Descriptions** sous les styles d’icônes disponibles. Utilisez les descriptions des différents éléments KPI pour faciliter l’identification de ces derniers dans les applications clientes.  
  
9. Cliquez sur **OK** pour terminer l’indicateur KPI.  
  
    Dans la grille de mesure, notez l’icône en regard de la mesure **InternetCurrentQuarterSalesPerformance**. Cette icône indique que cette mesure sert de valeur de base pour un indicateur KPI.  
  
#### <a name="to-create-an-internetcurrentquartermarginperformance-kpi"></a>Pour créer un indicateur de performance clé InternetCurrentQuarterMarginPerformance  
  
1.  Dans la grille de mesure de la table **FactInternetSales**, cliquez sur une cellule vide.  
  
2.  Dans la barre de formule située au-dessus de la table, tapez la formule suivante :  

    ```
    InternetCurrentQuarterMarginPerformance :=IF([InternetPreviousQuarterMarginProportionToQTD]<>0,([InternetCurrentQuarterMargin]-[InternetPreviousQuarterMarginProportionToQTD])/[InternetPreviousQuarterMarginProportionToQTD],BLANK())  
    ```
 
3.  Cliquez avec le bouton droit sur **InternetCurrentQuarterMarginPerformance** > **Créer un KPI**.  
  
4.  Dans la boîte de dialogue Indicateur de performance clé (KPI), dans **Cible**, sélectionnez **Valeur absolue**, puis tapez **1.25**.   
  
5.  Dans le champ à curseur de gauche (valeurs basses), faites glisser le curseur jusqu’à ce que le champ affiche **0.8**, puis faites glisser le curseur de droite (valeurs élevées) jusqu’à ce que le champ affiche **1.03**.  
  
6.  Dans **Sélectionnez le style d’icône**, sélectionnez le losange (rouge), le triangle (jaune) ou le cercle (vert) comme type d’icône, puis cliquez sur **OK**.  
  
## <a name="whats-next"></a>Et ensuite ?
[Leçon 8 : Créer des perspectives](../tutorials/aas-lesson-8-create-perspectives.md).
  
  
