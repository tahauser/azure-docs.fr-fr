---
title: "Leçon 10 du didacticiel Azure Analysis Services : Créer des partitions | Microsoft Docs"
description: "Explique comment créer des partitions dans le projet du didacticiel Azure Analysis Services."
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
ms.openlocfilehash: a73836b784a5e86d01df51fb83d619890d56502a
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="create-partitions"></a>Créer des partitions

Dans cette leçon, vous allez créer des partitions pour diviser la table FactInternetSales en sous-parties logiques qui peuvent être traitées (actualisées) indépendamment des autres partitions. Par défaut, chaque table que vous incluez dans votre modèle comporte une partition, qui comprend toutes les colonnes et les lignes de la table. Pour la table FactInternetSales, nous allons diviser les données en fonction des années, en attribuant une partition pour chaque cinq années. Chaque partition peut ensuite être traitée indépendamment. Pour plus d’informations, consultez [Partitions](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular). 
  
Durée estimée pour suivre cette leçon : **15 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon, vous devez avoir terminé la leçon précédente : [Leçon 9 : Créer des hiérarchies](../tutorials/aas-lesson-9-create-hierarchies.md).  
  
## <a name="create-partitions"></a>Créer des partitions  
  
#### <a name="to-create-partitions-in-the-factinternetsales-table"></a>Pour créer des partitions dans la table FactInternetSales  
  
1.  Dans l’Explorateur de modèles tabulaires, développez **Tables**, puis cliquez avec le bouton droit sur **FactInternetSales** > **Partitions**.  
  
2.  Dans le Gestionnaire de partition, cliquez sur **Copier**, puis remplacez le nom par **FactInternetSales2010**.
  
    Étant donné que la partition doit inclure uniquement les lignes d’une période donnée (pour l’année 2010), vous devez modifier l’expression de requête.
  
4.  Cliquez sur **Conception** pour ouvrir l’éditeur de requête, puis cliquez sur la requête **FactInternetSales2010**.

5.  Dans l’aperçu, cliquez sur la flèche vers le bas située dans l’en-tête de colonne **OrderDate**, puis cliquez sur **Filtres de date/heure** > **Entre**.

    ![aas-lesson10-query-editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  Dans la boîte de dialogue Filtrer les lignes, dans **Afficher les lignes où : OrderDate**, conservez **postérieur ou égal à**, puis, dans le champ de date, entrez **1/1/2010**. Laissez l’opérateur **And** sélectionné, sélectionnez **avant**, puis, dans le champ de date, entrez **1/1/2011** et cliquez sur **OK**.

    ![aas-lesson10-filter-rows](../tutorials/media/aas-lesson10-filter-rows.png)
    
    Dans l’éditeur de requête, sous ÉTAPES APPLIQUÉES, vous voyez une autre étape nommée Lignes filtrées. Ce filtre permet de sélectionner uniquement les dates de commande à partir de 2010.

8.  Cliquez sur **Importer**.

    Dans le Gestionnaire de partition, notez que l’expression de requête comprend maintenant une clause Lignes filtrées supplémentaire.

    ![aas-lesson10-query](../tutorials/media/aas-lesson10-query.png)
  
    Cette instruction spécifie que cette partition doit inclure uniquement les données des lignes pour lesquelles OrderDate correspond à l’année 2010, comme spécifié dans la clause de Lignes filtrées.  
  
  
#### <a name="to-create-a-partition-for-the-2011-year"></a>Pour créer une partition pour l’année 2011  
  
1.  Dans la liste des partitions, cliquez sur la partition **FactInternetSales2010** que vous avez créée, puis cliquez sur **Copier**.  Remplacez le nom de la partition par **FactInternetSales2011**. 

    Il est inutile d’utiliser l’éditeur de requête pour créer une autre clause de lignes filtrées. Étant donné que vous avez créé une copie de la requête pour 2010, seule une légère modification de la requête est nécessaire pour 2011.
  
2.  Dans **Expression de requête**, pour que cette partition inclue uniquement les lignes de l’année 2011, remplacez les années de la clause de lignes filtrées par **2011** et **2012**, respectivement, comme ceci :  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
#### <a name="to-create-partitions-for-2012-2013-and-2014"></a>Pour créer des partitions pour 2012, 2013 et 2014.  
  
- Suivez les étapes précédentes, en créant des partitions pour 2012, 2013 et 2014, puis en modifiant les années de la clause Lignes filtrées de manière à inclure uniquement les lignes de cette année. 
  

## <a name="delete-the-factinternetsales-partition"></a>Supprimer la partition FactInternetSales
Maintenant que vous avez des partitions pour chaque année, vous pouvez supprimer la partition FactInternetSales, ce qui va éviter un chevauchement lorsque vous choisirez l’option Traiter tout.

#### <a name="to-delete-the-factinternetsales-partition"></a>Pour supprimer la partition FactInternetSales
-  Cliquez sur la partition FactInternetSales, puis cliquez sur **Supprimer**.



## <a name="process-partitions"></a>Traiter les partitions  
Dans le Gestionnaire de partition, notez que la colonne **Dernier traitement** de chacune des partitions créées montre que ces partitions n’ont jamais été traitées. Lorsque vous créez des partitions, vous devez exécuter l’opération Traiter les partitions ou Traiter la table pour actualiser les données de ces partitions.  
  
#### <a name="to-process-the-factinternetsales-partitions"></a>Pour traiter les partitions FactInternetSales  
  
1.  Cliquez sur **OK** pour fermer le Gestionnaire de partition.  
  
2.  Cliquez sur la table **FactInternetSales**, puis cliquez sur le menu **Modèle** > **Processus** > **Traiter les partitions**.  
  
3.  Dans la boîte de dialogue Traiter les partitions, vérifiez que **Mode** est défini sur **Traiter par défaut**.  
  
4.  Cochez la case située dans la colonne **Processus** pour chacune des cinq partitions que vous avez créées, puis cliquez sur **OK**.  

    ![aas-lesson10-process-partitions](../tutorials/media/aas-lesson10-process-partitions.png)
  
    Si vous êtes invité à entrer des informations d’identification d’emprunt d’identité, entrez le nom d’utilisateur Windows et le mot de passe spécifiés dans la leçon 2.  
  
    La boîte de dialogue **Traitement des données** s’affiche et montre les détails du processus pour chaque partition. Notez que le nombre de lignes transférées est différent pour chaque partition. Chaque partition contient uniquement les lignes de l’année spécifiée dans la clause WHERE de l’instruction SQL. Lorsque le traitement est terminé, fermez la boîte de dialogue Traitement des données.  
  
    ![aas-lesson10-process-complete](../tutorials/media/aas-lesson10-process-complete.png)
  
 ## <a name="whats-next"></a>Et ensuite ?
Accédez à la leçon suivante : [Leçon 11 : Créer des rôles](../tutorials/aas-lesson-11-create-roles.md). 
