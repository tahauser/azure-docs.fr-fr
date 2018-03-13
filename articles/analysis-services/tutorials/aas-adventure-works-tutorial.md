---
title: "Didacticiel Adventure Works d’Azure Analysis Services | Microsoft Docs"
description: "Présente le didacticiel Adventure Works d’Azure Analysis Services"
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
ms.date: 02/10/2018
ms.author: owend
ms.openlocfilehash: 3bc0ed965aea043fd5c105752ec39d07c4af818f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-analysis-services---adventure-works-tutorial"></a>Azure Analysis Services - Didacticiel Adventure Works

Ce didacticiel fournit des leçons sur la façon de créer et de déployer un modèle tabulaire au niveau de compatibilité 1400 à l’aide de Visual Studio avec [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

Si vous découvrez Analysis Services et la modélisation tabulaire, suivre ce didacticiel est la façon la plus rapide d’apprendre à créer et à déployer un modèle tabulaire de base en utilisant Visual Studio. Une fois les conditions préalables satisfaites, cela vous prendra environ deux ou trois heures.  
  
## <a name="what-you-learn"></a>Contenu   
  
-   Comment créer un projet de modèle tabulaire au **niveau de compatibilité 1400** dans Visual Studio avec SSDT.
  
-   Comment importer des données à partir d’une base de données relationnelle dans une base de données d’espace de travail d’un projet de modèle tabulaire.  
  
-   Comment créer et gérer des relations entre les tables dans le modèle.  
  
-   Comment créer des colonnes calculées, des mesures et des indicateurs de performance clés qui permettent aux utilisateurs d’analyser des métriques métier critiques.  
  
-   Comment créer et gérer des perspectives et des hiérarchies qui permettent aux utilisateurs de parcourir plus facilement des données de modèle en fournissant des points de vue spécifiques à l’entreprise et à l’application.  
  
-   Comment créer des partitions qui divisent des données de table en sous-parties logiques qui peuvent être traitées indépendamment des autres partitions.  
  
-   Comment sécuriser des objets et données de modèle en créant des rôles avec des membres utilisateurs.  
  
-   Comment déployer un modèle tabulaire sur un serveur **Azure Analysis Services** ou un **serveur SQL Server Analysis Services 2017** à l’aide de SSDT.  
  
## <a name="prerequisites"></a>configuration requise  
Pour suivre ce didacticiel, vous avez besoin des éléments suivants :  
  
-   Un serveur Azure Analysis Services. Inscrivez-vous pour bénéficier d’un [essai gratuit d’Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) et [créez un serveur](../analysis-services-create-server.md). 

-   [Azure SQL Data Warehouse](../../sql-data-warehouse/create-data-warehouse-portal.md) avec **l’exemple de base de données AdventureWorksDW**, ou SQL Server Data Warehouse avec [l’exemple de base de données Adventure Works](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks).

    **Important** : Si vous installez l’exemple de base de données sur un SQL Server Data Warehouse local et que vous déployez votre modèle sur un serveur Azure Analysis Services, une [passerelle de données locale](../analysis-services-gateway.md) est nécessaire.

-   La dernière version de [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) pour Visual Studio.

-   La dernière version de [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).    

-   Une application cliente telle que [Power BI Desktop](https://powerbi.microsoft.com/desktop/) ou Excel. 

## <a name="scenario"></a>Scénario  
Ce didacticiel repose sur la société fictive Adventure Works Cycles. Adventure Works est une grande société de fabrication multinationale qui produit et distribue des bicyclettes, des parties et des accessoires pour les marchés d’Amérique du Nord, Europe et Asie. L’entreprise compte 500 employés. Par ailleurs, Adventure Works emploie plusieurs équipes commerciales régionales pour couvrir son marché. Votre projet consiste à créer un modèle tabulaire afin de permettre aux utilisateurs commerciaux et marketing d’analyser les données des ventes sur Internet dans la base de données AdventureWorksDW.  
  
Pour effectuer ce didacticiel, vous devez suivre plusieurs leçons. Chaque leçon comprend des tâches. Il est indispensable d’exécuter chaque tâche dans l’ordre pour terminer la leçon. Une leçon peut comporter plusieurs tâches qui aboutissent au même résultat, mais la façon d’exécuter chaque tâche diffère légèrement. Cette méthode montre qu’il existe souvent plusieurs façons d’effectuer une tâche et de vous tester sur l’utilisation des compétences acquises au cours des leçons et tâches précédentes.  
  
L’objectif des leçons est de vous guider dans la création d’un modèle tabulaire de base s’exécutant à l’aide des nombreuses fonctionnalités incluses dans SSDT. Étant donné que chaque leçon repose sur la leçon précédente, vous devez suivre les leçons dans l’ordre.
  
Ce didacticiel ne propose pas de leçons sur la gestion d’un serveur dans le portail Azure, sur la gestion d’un serveur ou d’une base de données à l’aide de SSMS, ni sur l’utilisation d’une application cliente pour parcourir les données de modèle. 


## <a name="lessons"></a>Leçons  
Ce didacticiel comprend les leçons suivantes :  
  
|Leçon|Durée estimée|  
|----------|------------------------------|  
|[1 - Créer un nouveau projet de modèle tabulaire](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10 minutes|  
|[2 - Obtenir des données](../tutorials/aas-lesson-2-get-data.md)|10 minutes|  
|[3 - Marquer en tant que Table de dates](../tutorials/aas-lesson-3-mark-as-date-table.md)|3 minutes|  
|[4 - créer des relations](../tutorials/aas-lesson-4-create-relationships.md)|10 minutes|  
|[5 - Créer des colonnes calculées](../tutorials/aas-lesson-5-create-calculated-columns.md)|15 minutes|
|[6 - Créer des mesures](../tutorials/aas-lesson-6-create-measures.md)|30 minutes|  
|[7 - Créer des indicateurs de performance clés (KPI)](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|15 minutes|  
|[8 - Créer des perspectives](../tutorials/aas-lesson-8-create-perspectives.md)|5 minutes|  
|[9 - Créer des hiérarchies](../tutorials/aas-lesson-9-create-hierarchies.md)|20 minutes|  
|[10 - Créer des partitions](../tutorials/aas-lesson-10-create-partitions.md)|15 minutes|  
|[11 - Créer des rôles](../tutorials/aas-lesson-11-create-roles.md)|15 minutes|  
|[12 - Analyser dans Excel](../tutorials/aas-lesson-12-analyze-in-excel.md)|5 minutes| 
|[13 - Déployer](../tutorials/aas-lesson-13-deploy.md)|5 minutes|  
  
## <a name="supplemental-lessons"></a>Leçons supplémentaires  
Il n’est pas obligatoire de suivre ces leçons pour terminer le didacticiel, mais elles peuvent être utiles pour mieux comprendre les fonctionnalités avancées de création de modèles tabulaires.  
  
|Leçon|Durée estimée|  
|----------|------------------------------|  
|[Lignes de détails](../tutorials/aas-supplemental-lesson-detail-rows.md)|10 minutes|
|[Sécurité dynamique](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30 minutes|
|[Hiérarchies déséquilibrées](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20 minutes| 

  
## <a name="next-steps"></a>Étapes suivantes  
Pour commencer, consultez [Leçon 1 : Créer un projet de modèle tabulaire](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
  
  

