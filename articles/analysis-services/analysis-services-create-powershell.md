---
title: "Créer un serveur Azure Analysis Services à l’aide de PowerShell | Microsoft Docs"
description: "Découvrir comment se connecter à un serveur Azure Analysis Services à l’aide de PowerShell"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/14/2018
ms.author: owend
ms.custom: mvc
ms.openlocfilehash: 083c5a9bb5c57f3287c259900e6a23d6f0a886de
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="create-an-azure-analysis-services-server-by-using-powershell"></a>Créer un serveur Azure Analysis Services à l’aide de PowerShell

Ce démarrage rapide décrit comment utiliser PowerShell à partir d’une ligne de commande pour créer un serveur Azure Analysis Services dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) de votre abonnement Azure.

Cette tâche requiert le module Azure PowerShell version 4.0 ou ultérieure. Pour connaître la version de l’interface, exécutez ` Get-Module -ListAvailable AzureRM`. Pour installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). 

> [!NOTE]
> La création d’un serveur peut donner lieu à un nouveau service facturable. Pour en savoir plus, consultez [Tarification d’Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="before-you-begin"></a>Avant de commencer
Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

* **Abonnement Azure** : visitez [version d’évaluation gratuite d’Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) pour créer un compte.
* **Azure Active Directory** : votre abonnement doit être associé à un client Azure Active Directory et vous devez disposer d’un compte dans ce répertoire. Pour en savoir plus, consultez [Authentification et autorisations utilisateur](analysis-services-manage-users.md).

## <a name="import-azurermanalysisservices-module"></a>Importer le module de AzureRm.AnalysisServices
Pour créer un serveur dans votre abonnement, vous utilisez le module de composant [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Chargez le module AzureRm.AnalysisServices dans votre session PowerShell.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure à l’aide de la commande [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount). Suivez les instructions à l’écran.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
 
Un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe. Lorsque vous créez votre serveur, vous devez spécifier un groupe de ressources dans votre abonnement. Si vous ne disposez pas d’un groupe de ressources, créez-en un à l’aide de la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` dans la région États-Unis de l’Ouest.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
```

## <a name="create-a-server"></a>Créer un serveur

Créer un nouveau serveur à l’aide de la commande [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). L’exemple suivant crée un serveur nommé myServer dans myResourceGroup, dans la région États-Unis de l’Ouest, au niveau D1, et spécifie philipc@adventureworks.com en tant qu’administrateur de serveur.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myServer" -Location West US -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez supprimer le serveur à partir de votre abonnement à l’aide de la commande [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Si vous continuez avec d’autres démarrages rapides et didacticiels de cette collection, ne supprimez pas votre serveur. L’exemple suivant supprime le serveur créé à l’étape précédente.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myServer" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Étapes suivantes
[Gérer Azure Analysis Services avec PowerShell](analysis-services-powershell.md)   
[Déployer un modèle à partir de SSDT](analysis-services-deploy.md)   
[Créer un modèle dans le portail Azure](analysis-services-create-model-portal.md)