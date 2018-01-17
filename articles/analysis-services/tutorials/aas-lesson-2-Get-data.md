---
title: "Leçon 2 du didacticiel Azure Analysis Services : Obtenir des données | Microsoft Docs"
description: "Explique comment obtenir et importer des données dans le projet du didacticiel Azure Analysis Services."
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
ms.openlocfilehash: 138f9f6e85d5e206c8b09d5c93822cfef5dd1246
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="get-data"></a>Obtention des données

Dans cette leçon, vous allez utiliser l’option Obtenir des données de SSDT pour vous connecter à l’exemple de base de données AdventureWorks, sélectionner des données, filtrer et afficher un aperçu des données, puis les importer dans votre espace de travail de modèle.  
  
L’option Obtenir des données vous permet d’importer des données à partir d’une grande variété de sources : Azure SQL Database, Oracle, Sybase, flux OData, bases de données Teradata, fichiers, entre autres. Les données peuvent également être interrogées à l’aide d’une expression de formule M Power Query.

> [!NOTE]
> Les tâches et images de ce didacticiel montrent la connexion à une base de données AdventureWorksDW2014 sur un serveur local. Une base de données AdventureWorks peut être différente sur Azure.
  
Durée estimée pour suivre cette leçon : **10 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon, vous devez avoir terminé la leçon précédente : [Leçon 1 : Créer un projet de modèle tabulaire](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
## <a name="create-a-connection"></a>Créer une connexion  
  
#### <a name="to-create-a-connection-to-the-adventureworksdw2014-database"></a>Pour créer une connexion à la base de données AdventureWorksDW2014  
  
1.  Dans l’Explorateur de modèles tabulaires, cliquez avec le bouton droit sur **Sources de données** > **Importer à partir de la source de données**.  
  
    Cela lance la fonctionnalité Obtenir des données, qui vous guide lors de la connexion à une source de données. Si l’Explorateur de modèles tabulaires ne s’affiche pas, dans **Explorateur de solutions**, double-cliquez sur **Model.bim** pour ouvrir le modèle dans le concepteur. 
    
    ![aas-lesson2-getdata](../tutorials/media/aas-lesson2-getdata.png)
  
2.  Dans l’option Obtenir des données, cliquez sur **Base de données** > **Base de données SQL Server** > **Connecter**.  
  
3.  Dans la boîte de dialogue **Base de données SQL Server**, dans **Serveur**, tapez le nom du serveur sur lequel vous avez installé la base de données AdventureWorksDW2014, puis cliquez sur **Connecter**.  

4.  Lorsque vous êtes invité à entrer les informations d’identification, vous devez spécifier celles qu’Analysis Services utilise pour se connecter à la source de données lors de l’importation et du traitement des données. Dans **Mode d’emprunt d’identité**, sélectionnez **Emprunter l’identité du compte**, entrez les informations d’identification, puis cliquez sur **Connecter**. Il est recommandé d’utiliser un compte dont le mot de passe n’expire pas.

    ![aas-lesson2-account](../tutorials/media/aas-lesson2-account.png)
  
    > [!NOTE]  
    > L’utilisation d’un compte d’utilisateur Windows et d’un mot de passe constitue la méthode la plus sûre de se connecter à une source de données.
  
5.  Dans le navigateur, sélectionnez la base de données **AdventureWorksDW2014**, puis cliquez sur **OK**. Cela crée la connexion à la base de données. 
  
6.  Dans le navigateur, cochez la case correspondant aux tables suivantes : **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** et **FactInternetSales**.  

    ![aas-lesson2-select-tables](../tutorials/media/aas-lesson2-select-tables.png)
  
Après que vous avez cliqué sur OK, l’éditeur de requête s’ouvre. Dans la section suivante, sélectionnez uniquement les données que vous souhaitez importer.

  
## <a name="filter-the-table-data"></a>Filtrer les données de la table  
Les tables de l’exemple de base de données AdventureWorksDW2014 contiennent des données qu’il n’est pas nécessaire d’inclure dans votre modèle. Dans la mesure du possible, vous devez filtrer les données inutiles de manière à économiser l’espace mémoire utilisé par le modèle. Vous filtrez certaines colonnes des tables pour qu’elles ne soient pas importées dans la base de données de l’espace de travail ou dans la base de données du modèle après son déploiement. 
  
#### <a name="to-filter-the-table-data-before-importing"></a>Pour filtrer les données de table avant l’importation  
  
1.  Dans l’éditeur de requêtes, sélectionnez la table **DimCustomer**. Une vue de la table DimCustomer dans la source de données (l’exemple de base de données AdventureWorksDW2014) s’affiche. 
  
2.  Effectuez une sélection multiple (Ctrl + clic) de **SpanishEducation**, **FrenchEducation**, **SpanishOccupation** et **FrenchOccupation**. Cliquez avec le bouton droit, puis cliquez sur **Supprimer les colonnes**. 

    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-columns.png)
  
    Étant donné que les valeurs de ces colonnes ne sont pas pertinentes pour l’analyse des ventes Internet, il n’est pas nécessaire d’importer ces colonnes. L’élimination des colonnes inutiles permet de réduire la taille de votre modèle et de le rendre plus efficace.  

    > [!TIP]
    > Si vous faites une erreur, vous pouvez revenir en arrière en supprimant une étape dans **APPLIED STEPS** (ÉTAPES EFFECTUÉES).   
    
    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-step.png)

  
4.  Filtrez les autres tables en supprimant les colonnes suivantes dans chacune des tables :  
    
    **DimDate**
    
      |Colonne|  
      |--------|  
      |**DateKey**|  
      |**SpanishDayNameOfWeek**|  
      |**FrenchDayNameOfWeek**|  
      |**SpanishMonthName**|  
      |**FrenchMonthName**|  
  
    **DimGeography**
  
      |Colonne|  
      |-------------|  
      |**SpanishCountryRegionName**|  
      |**FrenchCountryRegionName**|  
      |**IpAddressLocator**|  
  
    **DimProduct**
  
      |Colonne|  
      |-----------|  
      |**SpanishProductName**|  
      |**FrenchProductName**|  
      |**FrenchDescription**|  
      |**ChineseDescription**|  
      |**ArabicDescription**|  
      |**HebrewDescription**|  
      |**ThaiDescription**|  
      |**GermanDescription**|  
      |**JapaneseDescription**|  
      |**TurkishDescription**|  
  
    **DimProductCategory**
  
      |Colonne|  
      |--------------------|  
      |**SpanishProductCategoryName**|  
      |**FrenchProductCategoryName**|  
  
    **DimProductSubcategory**
  
      |Colonne|  
      |-----------------------|  
      |**SpanishProductSubcategoryName**|  
      |**FrenchProductSubcategoryName**|  
  
    **FactInternetSales**
  
      Aucune colonne supprimée.
  
## <a name="Import"></a>Importer les tables et les données de colonne sélectionnées  
Maintenant que vous avez prévisualisé et filtré les données inutiles, vous pouvez importer le reste des données souhaitées. L’Assistant importe les données de table et toutes les relations qui existent entre les tables. Les nouvelles tables et colonnes sont créées dans le modèle et les données filtrées ne sont pas importées.  
  
#### <a name="to-import-the-selected-tables-and-column-data"></a>Pour importer les tables et les données de colonne sélectionnées  
  
1.  Passez en revue vos sélections. Si tout semble correct, cliquez sur **Importer**. La boîte de dialogue Traitement des données indique l’état des données en cours d’importation depuis votre source de données vers la base de données de votre espace de travail.
  
    ![aas-lesson2-success](../tutorials/media/aas-lesson2-success.png) 
  
2.  Cliquez sur **Fermer**.  

  
## <a name="save-your-model-project"></a>Enregistrer votre projet de modèle  
Il est important de sauvegarder fréquemment votre projet de modèle.  
  
#### <a name="to-save-the-model-project"></a>Pour enregistrer votre projet de modèle  
  
-   Cliquez sur **Fichier** > **Enregistrer tout**.  
  
## <a name="whats-next"></a>Et ensuite ?
[Leçon 3 : Marquer en tant que Table de dates](../tutorials/aas-lesson-3-mark-as-date-table.md).

  
  
