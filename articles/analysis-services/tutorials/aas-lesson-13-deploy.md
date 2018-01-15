---
title: "Leçon 13 du didacticiel Azure Analysis Services : Déployer | Microsoft Docs"
description: "Explique comment déployer le projet du didacticiel Azure Analysis Services."
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
ms.openlocfilehash: 3442de96f346ef211672064584700a7ce183d929
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="deploy"></a>Déployer

Dans cette leçon, vous allez configurer les propriétés de déploiement en spécifiant un serveur Azure Analysis Services sur lequel procéder au déploiement et en indiquant le nom du modèle. Ensuite, vous allez déployer le modèle sur cette instance. Une fois votre modèle déployé, les utilisateurs peuvent s’y connecter à l’aide d’une application cliente de création de rapports. Pour en savoir plus, voir [Déployer sur Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy).  
  
Durée estimée pour suivre cette leçon : **5 minutes**  
  
## <a name="prerequisites"></a>Conditions préalables  
Cette rubrique fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon, vous devez avoir terminé la leçon précédente : [Leçon 12 : Analyser dans Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

> [!IMPORTANT]  
> Vous devez disposer des [autorisations d’administrateur](../analysis-services-server-admins.md) sur le serveur Analysis Services distant afin pouvoir procéder au déploiement.  

> [!IMPORTANT]  
> Si vous avez installé l’exemple de base de données AdventureWorksDW2014 sur un serveur local SQL Server, et que vous déployez votre modèle sur un serveur Azure Analysis Services, une [passerelle de données locale](../analysis-services-gateway.md) est nécessaire.
  
## <a name="deploy-the-model"></a>Déployer le modèle  
  
#### <a name="to-configure-deployment-properties"></a>Pour configurer les propriétés de déploiement  

  
1.  Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **AW Internet Sales**, puis cliquez sur **Propriétés**.  
  
2.  Dans la boîte de dialogue **Pages de propriétés de AW Internet Sales**, sous **Serveur de déploiement**, dans la propriété **Serveur**, entrez le nom du serveur complet.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  Dans la propriété **Base de données**, tapez **Adventure Works Internet Sales**.  
  
4.  Dans la propriété **Nom du modèle**, tapez **Adventure Works Internet Sales Model**.  
  
5.  Passez en revue vos sélections, puis cliquez sur **OK**.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Pour déployer Adventure Works Internet Sales
  
1.  Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **AW Internet Sales** > **Générer**.  

2.  Cliquez avec le bouton droit sur le projet **AW Internet Sales** > **Déployer**.

    Lors du déploiement sur Azure Analysis Services, vous pouvez être invité à entrer votre compte. Entrez votre compte professionnel et votre mot de passe, par exemple nancy@adventureworks.com. Ce compte doit faire partie du groupe Administrateurs sur le serveur.
  
    La boîte de dialogue Déployer s’affiche et montre l’état du déploiement des métadonnées, ainsi que chaque table incluse dans le modèle.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. Si le déploiement se termine sans erreurs, cliquez sur **Fermer**.  
  
## <a name="conclusion"></a>Conclusion  
Félicitations ! Vous venez de terminer la création et le déploiement de votre premier modèle tabulaire Analysis Services. Ce didacticiel vous a guidé dans les tâches les plus courantes associées à la création d’un modèle tabulaire. Maintenant que votre modèle Internet Sales Adventure Works est déployé, vous pouvez utiliser SQL Server Management Studio pour le gérer, ainsi que pour créer des scripts de processus et un plan de sauvegarde. Les utilisateurs peuvent désormais se connecter au modèle à l’aide d’une application cliente de création de rapports telle que Microsoft Excel ou Power BI.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>Et ensuite ?
[Connect with Power BI Desktop (Se connecter avec Power BI Desktop)](../analysis-services-connect-pbi.md)   
[Leçon supplémentaire – Sécurité dynamique](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Leçon supplémentaire – Lignes de détails](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[Leçon supplémentaire – Hiérarchies déséquilibrées](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
