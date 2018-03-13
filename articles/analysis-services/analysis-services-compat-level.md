---
title: "Niveau de compatibilité des modèles de données dans Azure Analysis Services | Microsoft Docs"
description: "Présentation du niveau de compatibilité des modèles de données tabulaires."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: a07514c7734f3cd4be1e11a984151abb8ee3f23d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Niveau de compatibilité pour les modèles tabulaires Analysis Services

Le *niveau de compatibilité* fait référence aux comportements spécifiques de chaque version dans le moteur Analysis Services. Les modifications apportées au niveau de compatibilité coïncident généralement avec les versions principales de SQL Server. Ces modifications sont également implémentées dans Azure Analysis Services pour maintenir la parité entre les deux plateformes. Les modifications du niveau de compatibilité affectent également les fonctionnalités disponibles dans vos modèles tabulaires. Par exemple, DirectQuery et les métadonnées d’objet tabulaire présentent des implémentations différentes selon le niveau de compatibilité. 

Azure Analysis Services prend en charge les modèles tabulaires aux niveaux de compatibilité 1200 et 1400.

Le dernier niveau de compatibilité est 1400. Ce niveau coïncide avec SQL Server 2017 Analysis Services. Voici les principales fonctionnalités du niveau de compatibilité 1400 :

*  Nouvelle infrastructure pour la connectivité de données et l’importation dans les modèles tabulaires avec prise en charge des API TOM et des scripts TMSL. Cette nouvelle fonctionnalité permet la prise en charge de sources de données supplémentaires, comme le stockage blob Azure.
*  Fonctionnalités de transformation de données et de mashup des données à l’aide d’expressions Obtenir les données et M.
*  Les mesures prennent en charge une propriété Lignes de détails avec une expression DAX. Cette propriété permet aux outils clients tels que Microsoft Excel d’explorer les données détaillées à partir d’un rapport agrégé. Par exemple, lorsque les utilisateurs consultent le total des ventes pour une région ou un mois spécifique, ils peuvent afficher les détails de la commande associée. 
*  Sécurité au niveau objet pour les noms de table et de colonne, en plus des données qui s’y trouvent.
*  Prise en charge améliorée des hiérarchies déséquilibrées.
*  Amélioration des performances et de la surveillance.
  
## <a name="set-compatibility-level"></a>Définir le niveau de compatibilité 
 Lorsque vous créez un projet de modèle tabulaire dans SSDT, vous pouvez spécifier le niveau de compatibilité dans la boîte de dialogue **Concepteur de modèle tabulaire**. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 Si vous sélectionnez l’option **Ne plus afficher ce message**, tous les projets suivants utiliseront le niveau de compatibilité que vous avez spécifié par défaut. Vous pouvez modifier le niveau de compatibilité par défaut dans SSDT sous **Outils** > **Options**.  
  
 Pour mettre à niveau un projet de modèle tabulaire existant dans SSDT, définissez la propriété **Niveau de compatibilité** dans la fenêtre **Propriétés** du modèle. N’oubliez pas que la mise à niveau du niveau de compatibilité est irréversible.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Vérifier le niveau de compatibilité d’une base de données de modèles tabulaires dans SQL Server Management Studio 
 Dans SSMS, cliquez avec le bouton droit sur le nom de la base de données > **Propriétés** > **Niveau de compatibilité**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Vérifier le niveau de compatibilité pris en charge pour un serveur dans SSMS  
 Dans SSMS, cliquez avec le bouton droit sur le nom du serveur > **Propriétés** > **Niveau de compatibilité pris en charge**.  
  
 Cette propriété spécifie le niveau de compatibilité le plus élevé d’une base de données, qui s’exécutera sur le serveur (à l’exception de la préversion). Le niveau de compatibilité pris en charge ne peut pas être modifié.  

## <a name="next-steps"></a>Étapes suivantes
  [Créer un modèle dans le portail Azure](analysis-services-create-model-portal.md)   
  [Gérer Analysis Services](analysis-services-manage.md)  
