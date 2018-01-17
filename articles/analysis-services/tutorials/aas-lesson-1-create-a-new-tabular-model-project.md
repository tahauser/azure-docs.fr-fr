---
title: "Leçon 1 du didacticiel Azure Analysis Services : Créer un projet de modèle tabulaire | Microsoft Docs"
description: "Explique comment créer un projet du didacticiel Azure Analysis Services."
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
ms.openlocfilehash: fbe0784ae133a0b9a54c94b4ba3db317c14b3766
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="create-a-tabular-model-project"></a>Créer un projet de modèle tabulaire

Dans cette leçon, vous utilisez Visual Studio avec SQL Server Data Tools (SSDT) pour créer un projet de modèle tabulaire au niveau de compatibilité 1400. Une fois votre nouveau projet créé, vous pouvez commencer à ajouter des données et à créer votre modèle. Cette leçon vous présente brièvement l’environnement de création de modèle tabulaire dans Visual Studio.  
  
Durée estimée pour suivre cette leçon : **10 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique est la première leçon d’un didacticiel de création de modèle tabulaire. Pour suivre cette leçon, un certain nombre de conditions préalables doivent être satisfaites. Pour en savoir plus, consultez [Azure Analysis Services -Didacticiel Adventure Works](../tutorials/aas-adventure-works-tutorial.md).  
  
## <a name="create-a-new-tabular-model-project"></a>Créer un projet de modèle tabulaire  
  
#### <a name="to-create-a-new-tabular-model-project"></a>Pour créer un projet de modèle tabulaire  
  
1.  Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau** > **Projet**.  
  
2.  Dans la boîte de dialogue **Nouveau projet**, développez **Installé** > **Business Intelligence** > **Analysis Services**, puis cliquez sur **Projet tabulaire Analysis Services**.  
  
3.  Dans **Nom**, tapez **AW Internet Sales**, puis spécifiez un emplacement pour les fichiers projet.  
  
    Par défaut, le **nom de la solution** est identique au nom du projet. Toutefois, vous pouvez taper un nom de solution différent.  
  
4.  Cliquez sur **OK**.  
  
5.  Dans la boîte de dialogue **Concepteur de modèles tabulaires**, sélectionnez **Espace de travail intégré**.  
  
    L’espace de travail héberge une base de données de modèle tabulaire portant le même nom que le projet pendant la création du modèle. L’espace de travail intégré signifie que Visual Studio utilise une instance intégrée, ce qui supprime la nécessité d’installer une instance de serveur Analysis Services distincte uniquement pour la création de modèle.
      
6.  Dans **Niveau de compatibilité**, sélectionnez **SQL Server 2017/Azure Analysis Services (1400)**.   
 
    ![aas-lesson1-tmd](../tutorials/media/aas-lesson1-tmd.png)
      
    Si SQL Server 2017/Azure Analysis Services (1400) ne s’affiche pas dans la zone de liste Niveau de compatibilité, vous n’utilisez pas la dernière version de SQL Server Data Tools. Pour obtenir la version la plus récente, consultez [Installer SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
      
  
## <a name="understanding-the-ssdt-tabular-model-authoring-environment"></a>Présentation de l’environnement de création de modèles tabulaires dans SSDT  
Maintenant que vous avez créé un projet de modèle tabulaire, prenons un moment pour explorer l’environnement de création de modèles tabulaires dans Visual Studio.  
  
Une fois votre projet créé, il s’ouvre dans Visual Studio. Sur le côté droit, dans l’**Explorateur de modèles tabulaires**, une arborescence des objets de votre modèle s’affiche. Étant donné que vous n’avez pas encore importé de données, les dossiers sont vides. Vous pouvez cliquer avec le bouton droit sur un dossier objet pour effectuer des actions, comme avec la barre de menus. À mesure que vous parcourez ce didacticiel, vous utilisez l’Explorateur de modèles tabulaires pour naviguer parmi les différents objets de votre projet de modèle.

![aas-lesson1-tme](../tutorials/media/aas-lesson1-tme.png)

Cliquez sur l’onglet **Explorateur de solutions**. Le fichier **Model.bim** s’y trouve. Si la fenêtre du concepteur n’est pas affichée à gauche (la fenêtre vide avec l’onglet Model.bim), dans l’**Explorateur de solutions**, sous le projet **AW Internet Sales**, double-cliquez sur le fichier **Model.bim**. Le fichier Model.bim contient les métadonnées de votre projet de modèle. 

![aas-lesson1-se](../tutorials/media/aas-lesson1-se.png)
  
Cliquez sur **Model.bim**. La fenêtre **Propriétés** contient les propriétés du modèle, la plus importante étant la propriété **Mode DirectQuery**. Cette propriété spécifie si le modèle est déployé en mode In-Memory (Désactivé) ou en mode DirectQuery (Activé). Pour ce didacticiel, vous créez et déployez votre modèle en mode In-Memory.

![aas-lesson1-properties](../tutorials/media/aas-lesson1-properties.png)
  
Quand vous créez un projet de modèle, certaines propriétés du modèle sont automatiquement définies en fonction des paramètres de modélisation des données qui peuvent être spécifiés dans le menu **Outils** > boîte de dialogue **Options**. Les propriétés Sauvegarde de données, Rétention de l’espace de travail et Serveur d’espace de travail spécifient le mode et l’emplacement de sauvegarde, de conservation en mémoire et de génération de la base de données d’espace de travail (votre base de données de création de modèle), conservée en mémoire et généré. Vous pouvez modifier ces paramètres ultérieurement si nécessaire, mais pour l’instant, laissez ces propriétés en l’état.  

Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **AW Internet Sales**, puis cliquez sur **Propriétés**. La boîte de dialogue **Pages de propriétés de AW Internet Sales** s’affiche. Vous allez définir certaines de ces propriétés ultérieurement lors du déploiement de votre modèle.  
  
Quand vous avez installé SSDT, plusieurs nouveaux éléments de menu ont été ajoutés à l’environnement Visual Studio. Cliquez sur le menu **Modéliser**. Vous pouvez alors importer des données, actualiser les données de l’espace de travail, parcourir votre modèle dans Excel, créer des perspectives et des rôles, sélectionner la vue du modèle et définir les options de calcul. Cliquez sur le menu **Table**. À ce stade vous pouvez créer et gérer les relations, spécifier les paramètres de la table de dates, créer des partitions et modifier les propriétés de la table. Si vous cliquez sur le menu **Colonne**, vous pouvez ajouter et supprimer des colonnes dans une table, figer des colonnes et spécifier l’ordre de tri. SSDT ajoute également des boutons à la barre. La fonctionnalité Somme automatique est la plus utile pour créer une mesure d’agrégation standard pour une colonne sélectionnée. D’autres boutons de la barre d’outils permettent d’accéder rapidement aux commandes et fonctionnalités fréquemment utilisées.  
  
Recherchez dans certaines des boîtes de dialogue et certains des emplacements les fonctionnalités spécifiques à la création de modèles tabulaires. Bien que certains éléments ne soient pas encore actifs, vous pouvez vous faire une idée claire de l’environnement de création de modèles tabulaires.  
  

## <a name="whats-next"></a>Et ensuite ?
[Leçon 2 : Obtenir des données](../tutorials/aas-lesson-2-get-data.md).

  
  
  
