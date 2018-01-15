---
title: "Leçon 11 du didacticiel Azure Analysis Services : Créer des rôles | Microsoft Docs"
description: "Explique comment créer des rôles dans le projet du didacticiel Azure Analysis Services."
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
ms.openlocfilehash: 5fb0e2dd56e373ecf723a3672d9538bcc6dc68e3
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="create-roles"></a>Créer des rôles

Dans cette leçon, vous allez créer des rôles. Les rôles fournissent la sécurité des données et des objets d’une base de données de modèles en limitant l’accès aux utilisateurs qui en sont membres. Chaque rôle est défini avec une autorisation unique : aucune autorisation, autorisation de lecture, autorisation de lecture et de traitement, autorisation de traitement ou autorisation d’administrateur. Les rôles peuvent être définis lors de la création des modèles à l’aide du Gestionnaire de rôles. Une fois un modèle déployé, vous pouvez gérer des rôles à l’aide de SQL Server Management Studio (SSMS). Pour plus d’informations, consultez la rubrique [Rôles](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular).
  
> [!NOTE]  
> Il n’est pas nécessaire de créer des rôles pour suivre ce didacticiel. Par défaut, le compte avec lequel vous êtes actuellement connecté dispose de privilèges Administrateur sur le modèle. Toutefois, pour permettre à d’autres utilisateurs de votre organisation de parcourir le modèle à l’aide d’un client de création de rapports, vous devez créer au moins un rôle avec des autorisations de lecture et ajouter ces utilisateurs comme membres.  
  
Vous devez créer trois rôles :  
  
-   **Sales Manager (Responsable des ventes)** – Ce rôle peut inclure les utilisateurs de votre organisation auxquels vous souhaitez donner un accès en lecture à l’ensemble des objets et données du modèle.  
  
-   **Sales Analyst US (Analyste en ventes aux États-Unis)** – Ce rôle peut inclure les utilisateurs de votre organisation que vous souhaitez autoriser à parcourir uniquement les données relatives aux ventes aux États-Unis. Pour ce rôle, vous utilisez une formule DAX de façon à définir un *filtre de lignes*, qui limite les membres à parcourir uniquement les données concernant les États-Unis.  
  
-   **Administrator (Administrateur)** – Ce rôle peut inclure des utilisateurs auxquels vous souhaitez fournir une autorisation d’administrateur, ce qui permet un accès et des autorisations illimités pour effectuer des tâches administratives sur la base de données de modèles.  
  
Étant donné que les comptes d’utilisateurs et de groupes Windows de votre organisation sont uniques, vous pouvez ajouter des comptes de votre organisation spécifique aux membres. Toutefois, pour ce didacticiel, vous pouvez également laisser les membres vides. Vous allez tester l’effet de chaque rôle ultérieurement dans la leçon 12 : Analyser dans Excel.  
  
Durée estimée pour suivre cette leçon : **15 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon, vous devez avoir suivi la leçon précédente : [Leçon 10 : Créer des partitions](../tutorials/aas-lesson-10-create-partitions.md).  
  
## <a name="create-roles"></a>Créer des rôles  
  
#### <a name="to-create-a-sales-manager-user-role"></a>Pour créer un rôle d’utilisateur Sales Manager (Responsable des ventes)  
  
1.  Dans l’Explorateur de modèle tabulaire, cliquez avec le bouton droit sur **Rôles** > **Rôles**.  
  
2.  Dans le Gestionnaire de rôles, cliquez sur **Nouveau**.  
  
3.  Cliquez sur le nouveau rôle, puis dans la colonne **Nom**, renommez le rôle en **Sales Manager**.  
  
4.  Dans la colonne **Autorisations**, cliquez sur la liste déroulante et sélectionnez l’autorisation **Lecture**. 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  Facultatif : Cliquez sur l’onglet **Membres**, puis sur **Ajouter**. Dans la boîte de dialogue **Sélectionner des utilisateurs ou des groupes**, entrez les utilisateurs ou les groupes Windows de votre organisation à inclure dans le rôle.  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>Pour créer un rôle d’utilisateur Sales Analyst US (Analyste en ventes aux États-Unis)  
  
1.  Dans le Gestionnaire de rôles, cliquez sur **Nouveau**.    
  
2.  Renommez le rôle en **Sales Analyst US**.  
  
3.  Attribuez à ce rôle l’autorisation **Lecture**.  
  
4.  Cliquez sur l’onglet Filtres de lignes, puis uniquement pour la table **DimGeography**, dans la colonne Filtre DAX, tapez la formule suivante :  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    Une formule de filtre de lignes doit être résolue en une valeur booléenne (TRUE/FALSE). Avec cette formule, vous spécifiez que l’utilisateur doit voir uniquement les lignes dont la valeur Code pays/région est « US ».  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  Facultatif : Cliquez sur l’onglet **Membres**, puis sur **Ajouter**. Dans la boîte de dialogue **Sélectionner des utilisateurs ou des groupes**, entrez les utilisateurs ou les groupes Windows de votre organisation à inclure dans le rôle.  
  
#### <a name="to-create-an-administrator-user-role"></a>Pour créer un rôle d’utilisateur Administrator  
  
1.  Cliquez sur **Nouveau**.  
  
2.  Renommez le rôle en **Administrator**.  
  
3.  Attribuez à ce rôle l’autorisation **Administrateur**.  
  
4.  Facultatif : Cliquez sur l’onglet **Membres**, puis sur **Ajouter**. Dans la boîte de dialogue **Sélectionner des utilisateurs ou des groupes**, entrez les utilisateurs ou les groupes Windows de votre organisation à inclure dans le rôle. 
  
  
## <a name="whats-next"></a>Et ensuite ?
[Leçon 12 : Analyser dans Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).

  
  
