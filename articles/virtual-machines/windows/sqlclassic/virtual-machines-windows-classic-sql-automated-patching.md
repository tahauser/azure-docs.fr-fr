---
title: "Mise à jour corrective automatisée pour les machines virtuelles SQL Server (classiques) | Microsoft Docs"
description: "Décrit la fonctionnalité de mise à jour corrective automatisée pour les machines virtuelles SQL Server exécutées dans Azure à l’aide du modèle de déploiement classique."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: 
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.openlocfilehash: 42be8c814a7b4c0bb1bd2f88d2e610d20442048e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure (classiques)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Classique](../classic/sql-automated-patching.md)
> 
> 

La mise à jour corrective automatisée établit une fenêtre de maintenance pour une machine virtuelle Azure exécutant SQL Server. Les mises à jour automatisées ne peuvent être installées qu’au cours de cette fenêtre de maintenance. Pour SQL Server, les mises à jour système et les redémarrages associés ont ainsi lieu au meilleur moment possible pour la base de données. La mise à jour corrective automatisée utilise l’ [extension de l’agent IaaS de SQL Server](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour afficher la version Resource Manager de cet article, consultez [Mise à jour corrective automatisée pour SQL Server dans les machines virtuelles Azure (classiques)](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>configuration requise
Pour utiliser la mise à jour corrective automatisée, prenez en compte les conditions préalables suivantes :

**Système d’exploitation**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Version de SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Installez les commandes de la version la plus récente d’Azure PowerShell](/powershell/azure/overview).

**Extension IaaS SQL Server**:

* [Installez l’extension IaaS SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Paramètres
Le tableau suivant décrit les options qui peuvent être configurées pour une mise à jour corrective automatisée. Pour les machines virtuelles classiques, vous devez utiliser PowerShell pour configurer ces paramètres.

| Paramètre | Valeurs possibles | Description |
| --- | --- | --- |
| **Mise à jour corrective automatisée** |Activer/Désactiver (désactivé) |Active ou désactive la mise à jour corrective automatisée pour une machine virtuelle Azure. |
| **Planification de la maintenance** |Tous les jours, lundi, mardi, mercredi, jeudi, vendredi, samedi et dimanche |Planification du téléchargement et de l’installation des mises à jour Windows, SQL Server et Microsoft pour votre machine virtuelle. |
| **Heure de début de la maintenance** |0-24 |Heure locale de début de la mise à jour de la machine virtuelle. |
| **Durée de la fenêtre de maintenance** |30 à 180 |Nombre de minutes autorisées pour terminer le téléchargement et l’installation des mises à jour. |
| **Catégorie de correctif** |Important |Catégorie des mises à jour à télécharger et installer. |

## <a name="configuration-with-powershell"></a>Configuration avec PowerShell
Dans l’exemple suivant, PowerShell permet de configurer une mise à jour corrective automatisée sur une machine virtuelle SQL Server existante. La commande **New-AzureVMSqlServerAutoPatchingConfig** configure une nouvelle fenêtre de maintenance pour les mises à jour automatiques.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

En s’appuyant sur cet exemple, le tableau suivant décrit les effets concrets sur la machine virtuelle Azure cible :

| Paramètre | Résultat |
| --- | --- |
| **DayOfWeek** |Les correctifs sont installés tous les jeudis. |
| **MaintenanceWindowStartingHour** |Les mises à jour démarrent à 11 h 00. |
| **MaintenanceWindowsDuration** |Les correctifs doivent être installés dans les 120 minutes. Selon l’heure de début, leur installation doit être terminée à 13 h 00 au plus tard. |
| **PatchCategory** |La seule définition possible pour ce paramètre est « Important ». |

L’installation et la configuration de l’agent IaaS de SQL Server peuvent prendre plusieurs minutes.

Pour désactiver la mise à jour corrective automatisée, exécutez le même script sans le paramètre -Enable pour la commande New-AzureVMSqlServerAutoPatchingConfig. À l’instar de l’installation, la désactivation de la mise à jour corrective automatisée peut prendre plusieurs minutes.

## <a name="next-steps"></a>étapes suivantes
Pour plus d’informations sur les autres tâches d’automatisation disponibles, voir [Extension de l’agent IaaS SQL Server](../classic/sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur des machines virtuelles Azure, voir [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

