---
title: "Leçon 12 du didacticiel Azure Analysis Services : Analyser dans Excel | Microsoft Docs"
description: "Explique comment utiliser la fonctionnalité Analyser dans Excel dans le projet du didacticiel Azure Analysis Services."
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
ms.openlocfilehash: 8d7f79db612ee02eaf585f9b45d888b3533a91b2
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="analyze-in-excel"></a>Analyser dans Excel

Dans cette leçon, vous utilisez la fonctionnalité Analyser dans Excel pour ouvrir Microsoft Excel, créer automatiquement une connexion à l’espace de travail du modèle et ajouter automatiquement un tableau croisé dynamique à la feuille de calcul. La fonctionnalité Analyser dans Excel est destinée à fournir un moyen simple et rapide de tester l’efficacité de la conception de votre modèle avant de déployer ce dernier. Vous n’exécutez aucune analyse de données dans cette leçon. L’objectif de cette leçon est de vous familiariser, en tant qu’auteur de modèle, avec les outils que vous pouvez utiliser pour tester la conception de votre modèle.   
  
Pour que vous puissiez suivre cette leçon, Excel doit être installé sur le même ordinateur que Visual Studio.
  
Durée estimée pour suivre cette leçon : **5 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon, vous devez avoir suivi la leçon précédente : [Leçon 11 : Créer des rôles](../tutorials/aas-lesson-11-create-roles.md).  
  
## <a name="browse-using-the-default-and-internet-sales-perspectives"></a>Parcourir des données à l’aide des perspectives par défaut et Internet Sales  
Dans ces premières tâches, vous aller parcourir votre modèle en utilisant à la fois la perspective par défaut, qui inclut tous les objets du modèle, et la perspective Internet Sales créée précédemment. La perspective Internet Sales exclut l’objet table Customer.  
  
#### <a name="to-browse-by-using-the-default-perspective"></a>Pour parcourir des données à l’aide de la perspective par défaut  
  
1.  Cliquez sur le menu **Modèle** > **Analyser dans Excel**.  
  
2.  Dans la boîte de dialogue **Analyser dans Excel**, cliquez sur **OK**.  
  
    Excel s’ouvre avec un nouveau classeur. Une connexion de source de données est créée à l’aide du compte d’utilisateur actuel et la perspective par défaut est utilisée pour définir les champs visibles. Un tableau croisé dynamique est automatiquement ajouté à la feuille de calcul.  
  
3.  Dans Excel, dans la **liste des champs de tableau croisé dynamique**, les groupes de mesures **DimDate** et **FactInternetSales** sont affichés. Vous voyez également les tables **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** et **FactInternetSales**, avec leurs colonnes respectives.  
  
4.  Fermez Excel sans enregistrer le classeur.  
  
#### <a name="to-browse-by-using-the-internet-sales-perspective"></a>Pour parcourir les données à l’aide de perspective Internet Sales  
  
1.  Cliquez sur le menu **Modèle**, puis sur **Analyser dans Excel**.  
  
2.  Dans la boîte de dialogue **Analyser dans Excel**, laissez **Utilisateur Windows actuel** sélectionné, puis dans la zone de liste déroulante **Perspective**, sélectionnez **Internet Sales**. Cliquez ensuite sur **OK**. 
    
    ![aas-lesson12-perspective](../tutorials/media/aas-lesson12-perspective.png)
    
3.  Dans Excel, dans **Champs de tableau croisé dynamique**, notez que la table DimCustomer est exclue de la liste des champs.  
    
    ![aas-lesson12-fields](../tutorials/media/aas-lesson12-fields.png)
    
4.  Fermez Excel sans enregistrer le classeur.  
  
## <a name="browse-by-using-roles"></a>Parcourir les données à l’aide de rôles  
Les rôles sont importants dans n’importe quel modèle tabulaire. Si les utilisateurs ne disposent pas d’au moins un rôle auquel ils sont ajoutés en tant que membres, ils ne peuvent pas accéder aux données ni les analyser à l’aide de votre modèle. La fonctionnalité Analyser dans Excel fournit un moyen de tester les rôles que vous avez définis.  
  
#### <a name="to-browse-by-using-the-sales-manager-user-role"></a>Pour parcourir les données à l’aide du rôle utilisateur Sales Manager  
  
1.  Dans SSDT, cliquez sur le menu **Modèle**, puis sur **Analyser dans Excel**.  
  
2.  Dans **Spécifier le nom d’utilisateur ou le rôle à utiliser pour se connecter au modèle**, sélectionnez **Rôle**, puis dans la zone de liste déroulante, sélectionnez **Sales Manager**. Cliquez ensuite sur **OK**.  
  
    Excel s’ouvre avec un nouveau classeur. Un tableau croisé dynamique est automatiquement créé. La liste des champs du tableau croisé dynamique inclut tous les champs de données disponibles dans votre nouveau modèle.  
      
3.  Fermez Excel sans enregistrer le classeur.  
  
## <a name="whats-next"></a>Et ensuite ?
Accédez à la leçon suivante : [Leçon 13 : Déployer](../tutorials/aas-lesson-13-deploy.md).

  
  
  
