---
title: Vue d'ensemble de Microsoft Azure Data Lake Analytics | Microsoft Docs
description: "Data Lake Analytics est un service de Big Data Azure qui vous permet de gérer votre entreprise en exploitant les informations issues de vos données dans le cloud, quels que soient leur taille ou leur emplacement."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: 316c35fa4b04bdb251c2b9ae14f6b32f4e4bf939
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Vue d'ensemble de Microsoft Azure Data Lake Analytics

## <a name="what-is-azure-data-lake-analytics"></a>Définition d'Azure Data Lake Analytics
Azure Data Lake Analytics est un service de travaux d’analyse à la demande, qui simplifie les Big Data. Au lieu de déployer, de configurer et d’optimiser le matériel, vous écrivez des requêtes pour transformer vos données et en extraire des informations pertinentes. Le service d’analyse peut traiter les travaux instantanément, quelle qu’en soit l’échelle, en définissant le compteur sur la puissance requise. Vous payez les travaux uniquement lorsque ceux-ci sont exécutés, ce qui rend le service économique. Ce service d’analyse prend également en charge U-SQL, un langage qui associe les avantages de SQL à la puissance de code impératif. Le langage U-SQL vous permet d’analyser des données dans Data Lake Store, SQL Server dans Azure, Azure SQL Database et Azure SQL Data Warehouse.

## <a name="dynamic-scaling"></a>Mise à l’échelle dynamique
  
Data Lake Analytics a été conçu pour fournir des performances et une mise à l’échelle dans le cloud.  Il approvisionne dynamiquement les ressources et vous permet d'analyser plusieurs téraoctets voire exaoctets de données. Lorsque le travail est terminé, le service ralentit automatiquement les ressources et vous payez uniquement la puissance de traitement utilisée. Il n’est pas nécessaire de réécrire le code à mesure que vous augmentez ou diminuez la taille des données stockées ou le volume des ressources de calcul utilisé. Vous pouvez vous concentrer uniquement sur votre logique métier, et non sur la manière dont vous traitez et stockez les jeux de données volumineux.

## <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>Développez plus rapidement, déboguez et optimisez de manière plus intelligente à l’aide d’outils familiers
  
L’étroite intégration de Data Lake Analytics avec Visual Studio vous permet d’utiliser des outils familiers pour exécuter, déboguer et optimiser votre code. Les visualisations de vos travaux U-SQL vous permettent de voir la façon dont votre code est exécuté à l'échelle. Vous pouvez ainsi facilement identifier les goulots d'étranglement en matière de performances et optimiser les coûts.


## <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>U-SQL : simple et familier, puissant, et extensible
  
Data Lake Analytics inclut U-SQL, un langage de requête qui étend la nature connue, simple, déclarative de SQL avec la puissance d'expression de C#. Le langage U-SQL est basé sur le runtime distribué qui alimente les systèmes Big Data au sein de Microsoft. Des millions de développeurs SQL et .NET peuvent désormais traiter et analyser leurs données avec les compétences dont ils disposent.

## <a name="integrates-seamlessly-with-your-it-investments"></a>S’intègre de façon transparente avec vos investissements informatiques
  
Data Lake Analytics peut utiliser vos investissements informatiques existants pour l'identité, la gestion, la sécurité et l'entreposage des données. Cette approche simplifie la gouvernance des données et facilite l’extension de vos applications de données actuelles. Data Lake Analytics est intégré à Active Directory pour les autorisations et la gestion des utilisateurs. Il est fourni avec des capacités de surveillance et d’audit intégrées.

## <a name="affordable-and-cost-effective"></a>Abordable et économique

Data Lake Analytics est une solution économique permettant d'exécuter vos charges de travail Big Data. Vous payez pour chaque travail au cours duquel des données sont traitées. Aucun matériel, aucune licence ni aucun contrat de support technique propre au service ne sont requis. Le système est mis à l’échelle automatiquement au démarrage du travail puis à la fin de son exécution de sorte que vous ne payez que ce dont vous avez réellement besoin. [En savoir plus sur le contrôle des coûts et les économies](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c).
    
## <a name="works-with-all-your-azure-data"></a>Fonctionne avec toutes vos données Azure
  
Data Lake Analytics fonctionne avec Azure Data Lake Store pour fournir les performances, le débit et la parallélisation les plus élevés, et fonctionne avec les objets blob Stockage Azure, Azure SQL Database et Azure Warehouse.

## <a name="next-steps"></a>Étapes suivantes
 
  * Prise en main de Data Lake Analytics à l’aide du [portail Azure](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli2.md)
  * Gérer Data Lake Analytics à l’aide du [portail Azure](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) | [Kit de développement logiciel (SDK) Azure .NET](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md)
  * [How to control costs and save money with Data Lake Analytics](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c) (Comment contrôler les coûts et réaliser des économies avec Data Lake Analytics)
