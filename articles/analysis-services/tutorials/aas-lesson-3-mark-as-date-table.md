---
title: "Leçon 3 du didacticiel Azure Analysis Services : Marquer en tant que Table de dates | Microsoft Docs"
description: Explique comment marquer une table de dates dans le projet du didacticiel Azure Analysis Services.
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
ms.openlocfilehash: 25e4475c77a25e4dcdcb90729f8633656bb186ff
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="mark-as-date-table"></a>Marquer comme table de dates

Dans Leçon 2 : Obtenir les données, vous avez importé une table de dimension nommée DimDate. Dans votre modèle, cette table se nomme DimDate. Toutefois, elle peut également être appelée *Table de dates*, car elle contient des données de date et d’heure.  
  
Chaque fois que vous utilisez les fonctions Time Intelligence DAX (comme lorsque vous allez créer des mesures), vous devez spécifier des propriétés qui correspondent à une *table de dates* et à sa *colonne de date* à identificateur unique.
  
Dans cette leçon, vous allez marquer la table DimDate comme étant la *Table de dates*, et la colonne de date (comprise dans la table de dates) comme étant la *Colonne de date* (à identificateur unique).  

Avant de marquer la table de dates et la colonne de date, il peut être intéressant d’apporter quelques modifications visant à faciliter la compréhension de votre modèle. Notez que, dans la table DimDate, une colonne est nommée **FullDateAlternateKey**. Cette colonne contient une ligne pour chaque jour de chaque année civile incluse dans la table. Cette colonne est très souvent utilisée dans les formules de mesure et dans les rapports. Cependant, FullDateAlternateKey n’est pas vraiment un bon identificateur pour cette colonne. Renommez cette colonne **Date** pour faciliter son identification et son utilisation dans les formules. Quand cela est possible, il est préférable de renommer les objets tels que les tables et les colonnes pour les rendre plus faciles à identifier dans SSDT et dans les applications clientes de création de rapports, telles que Power BI et Excel. 
  
Durée estimée pour suivre cette leçon : **3 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon, vous devez avoir terminé la leçon précédente : [Leçon 2 : Obtenir des données](../tutorials/aas-lesson-2-get-data.md). 

### <a name="to-rename-the-fulldatealternatekey-column"></a>Pour renommer la colonne FullDateAlternateKey

1.  Dans le Concepteur de modèles, cliquez sur la table **DimDate**.

2.  Double-cliquez sur l’en-tête de la colonne **FullDateAlternateKey**, puis renommez-le **Date**.

  
### <a name="to-set-mark-as-date-table"></a>Pour marquer en tant que table de dates  
  
1.  Sélectionnez la colonne **Date**, puis, dans la fenêtre **Propriétés**, sous **Type de données**, vérifiez que **Date** est sélectionné.  
  
2.  Cliquez sur le menu **Table**, cliquez sur **Date**, puis cliquez sur **Marquer en tant que table de dates**.  
  
3.  Dans la boîte de dialogue **Marquer en tant que table de dates**, dans la zone de liste **Date**, sélectionnez la colonne **Date** comme identificateur unique. Elle est généralement sélectionnée par défaut. Cliquez sur **OK**. 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

## <a name="whats-next"></a>Et ensuite ?
[Leçon 4 : Créer des relations](../tutorials/aas-lesson-4-create-relationships.md).
  
