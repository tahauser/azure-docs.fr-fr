---
title: "Créer un modèle tabulaire à l’aide du concepteur web Azure Analysis Services | Microsoft Docs"
description: "Découvrez comment créer un modèle tabulaire Azure Analysis Services à l’aide du concepteur web dans le portail Azure."
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
ms.openlocfilehash: 038cfb72cfc98fdddaca6751b321c11066527e56
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-model-in-azure-portal"></a>Créer un modèle dans le portail Azure

La fonctionnalité de concepteur Web Azure Analysis Services (aperçu) dans le portail Azure fournit un moyen simple et rapide de créer et de modifier des modèles tabulaires et d’interroger les données des modèles directement dans votre navigateur. 

N’oubliez pas que le concepteur Web est en **aperçu**. Malgré l’ajout permanent de nouvelles fonctionnalités, les fonctionnalités disponibles en préversion sont limitées. Pour un développement et un test de modèles plus avancés, utilisez plutôt Visual Studio (SSDT) et SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>configuration requise

- Un serveur Azure Analysis Services au niveau Standard ou Développeur. Les modèles créés à l’aide du concepteur web sont en mode DirectQuery, uniquement pris en charge par ces niveaux.
- Un fichier Azure SQL Database, Azure SQL Data Warehouse ou Power BI Desktop (.pbix) comme source de données. Les modèles créés à partir de fichiers Power BI Desktop prennent en charge les sources de données Azure SQL Database, Azure SQL Data Warehouse, Oracle et Teradata.
- Un compte et un mot de passe SQL Server pour se connecter aux sources de données Azure SQL Database ou Azure SQL Data Warehouse.

## <a name="to-create-a-new-tabular-model"></a>Pour créer un modèle tabulaire

1. Dans le panneau **Vue d’ensemble** > **Concepteur web** de votre serveur, cliquez sur **Ouvrir**.

    ![Créer un modèle dans le portail Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. Dans **Concepteur web** > **Modèles**, cliquez sur **Ajouter**.

    ![Créer un modèle dans le portail Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. Dans **Nouveau modèle**, tapez un nom de modèle, puis sélectionnez une source de données.

    ![Boîte de dialogue Nouveau modèle dans le portail Azure](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. Dans **Connexion**, entrez les propriétés de connexion. Le nom d’utilisateur et le mot de passe doivent correspondre à un compte SQL Server.

     ![Boîte de dialogue Connexion dans le portail Azure](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. Dans **Tables et vues**, sélectionnez les tables à inclure dans votre modèle, puis cliquez sur **Créer**. Les relations sont créées automatiquement entre les tables avec une paire de clés.

     ![Sélectionner les tables et les vues](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

Votre nouveau modèle s’affiche dans votre navigateur. À ce stade, vous pouvez :   

- Interroger les données du modèle en faisant glisser des champs vers le concepteur de requêtes et en ajoutant des filtres.
- Créer des mesures dans les tables.
- Modifier les métadonnées du modèle à l’aide de l’éditeur json.
- Ouvrir le modèle dans Visual Studio (SSDT), Power BI Desktop ou Excel.

![Sélectionner les tables et les vues](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> Quand vous modifiez les métadonnées du modèle ou créez des mesures dans votre navigateur, vous enregistrez les modifications apportées à votre modèle dans Azure. Si vous travaillez également sur votre modèle dans SSDT, Power BI Desktop ou Excel, il peut être désynchronisé.


## <a name="next-steps"></a>étapes suivantes 
[Gérer les utilisateurs et rôles de bases de données](analysis-services-database-users.md)  
[Connexion avec Excel](analysis-services-connect-excel.md)  


