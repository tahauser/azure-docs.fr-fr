---
title: "Gérer Azure Analysis Services avec PowerShell | Microsoft Docs"
description: "Gestion d’Azure Analysis Services avec PowerShell."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: 0e154c7789b38843cd3784daa8cf60d078bdc546
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gérer Azure Analysis Services avec PowerShell

Cet article décrit les applets de commande PowerShell permettant d’effectuer les tâches de gestion de base de données et de serveur Azure Analysis Services. 

Les tâches de gestion de serveur telles que la création ou la suppression d’un serveur, l’interruption ou la reprise des opérations du serveur ou la modification du niveau de service utilisent les applets de commande Azure Resource Manager (AzureRM). D’autres tâches de gestion des bases de données comme l’ajout ou la suppression de membres de rôle, le traitement ou le partitionnement utilisent les applets de commande incluses dans le même module SqlServer que SQL Server Analysis Services.

## <a name="permissions"></a>Autorisations
La plupart des tâches PowerShell nécessitent que vous disposiez de privilèges d’administrateur sur le serveur Analysis Services que vous gérez. Les tâches PowerShell planifiées s’exécutent sans assistance. Le compte exécutant le planificateur doit disposer de privilèges d’administrateur sur le serveur Analysis Services. 

Pour les opérations de serveur utilisant des applets de commande AzureRm, votre compte ou le compte exécutant Scheduler doivent également appartenir au rôle Propriétaire associé à la ressource dans le [contrôle d’accès en fonction du rôle (RBAC) d’Azure](../active-directory/role-based-access-control-what-is.md). 

## <a name="server-operations"></a>Opérations de serveur 
Les applets de commande Azure Analysis Services sont incluses dans le module de composant [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Pour installer les modules d’applet de commande AzureRM, consultez [Azure Resource Manager cmdlets](/powershell/azure/overview) (Applets de commande Azure Resource Manager) dans PowerShell Gallery.

|Applet de commande|Description| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azurerm.analysisservices/add-azureanalysisservicesaccount)|Ajoute un compte authentifié à utiliser pour les demandes d’applet de commande du serveur Azure Analysis Services.| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Obtient les détails d’une instance de serveur.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Crée une instance de serveur.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Supprime une instance de serveur.|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Redémarre une instance du serveur Analysis Services dans l’environnement actuellement connecté ; spécifié dans la commande Add-AzureAnalysisServicesAccount.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Reprend une instance de serveur.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Interrompt une instance de serveur.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Modifie une instance de serveur.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Teste l’existence d’une instance de serveur.| 

## <a name="database-operations"></a>Opérations de base de données

Les opérations de bases de données Azure Analysis Services utilisent le même module [SqlServer](https://www.powershellgallery.com/packages/SqlServer) que SQL Server Analysis Services. Toutefois, certaines applets de commande ne sont pas prises en charge par Azure Analysis Services. 

Le module SqlServer fournit des applets de commande de gestion de base de données spécifiques à chaque tâche, ainsi que l’applet de commande Invoke-ASCmd à usage général, qui accepte un script ou une requête utilisant le langage de script de modèle tabulaire (TMSL). Les applets de commande suivantes du module SqlServer sont prises en charge par Azure Analysis Services.

  
|Applet de commande|Description|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Ajoute un membre à un rôle de base de données.| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Sauvegarde une base de données Analysis Services.|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Supprime un membre d’un rôle de base de données.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Exécute un script TMSL.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Traite une base de données.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Traite une partition.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Traiter une table.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Fusionne une partition.|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Restaurer une base de données Analysis Services.| 
  

## <a name="related-information"></a>Informations connexes

* [Télécharger le module SQL Server PowerShell](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Télécharger SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Module SqlServer dans PowerShell Gallery](https://www.powershellgallery.com/packages/SqlServer)    
* [Programmation de modèle tabulaire pour le niveau de compatibilité 1200 et ultérieur](https://msdn.microsoft.com/library/mt712541.aspx)
