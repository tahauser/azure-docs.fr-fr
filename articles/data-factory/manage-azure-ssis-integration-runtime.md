---
title: "Reconfigurer un runtime d’intégration Azure-SSIS | Microsoft Docs"
description: "Découvrez comment reconfigurer le runtime d’intégration Azure-SSIS dans Azure Data Factory après l’avoir déjà configuré."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: spelluru
ms.openlocfilehash: 19a81917ade977a0d04934b77e8213ef6d9e0f12
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="reconfigure-an-azure-ssis-integration-runtime"></a>Reconfigurer un runtime d’intégration Azure-SSIS
L’article [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md) vous explique comment créer un runtime d’intégration Azure-SSIS à l’aide d’Azure Data Factory. Cet article fournit des informations sur la reconfiguration d’un runtime d’intégration Azure-SSIS existant.  

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-introduction.md).

Une fois que vous avez configuré et démarré une instance du runtime d’intégration Azure-SSIS, vous pouvez la reconfigurer en exécutant plusieurs applets de commande PowerShell `Stop`  -  `Set`  -  `Start`, les unes à la suite des autres. Par exemple, le script PowerShell suivant modifie le nombre de nœuds alloués à l’instance du runtime d’intégration Azure-SSIS et lui donne pour valeur 5.

## <a name="stop-azure-ssis-ir"></a>Arrêter un runtime d’intégration Azure SSIS
Tout d’abord, arrêtez le runtime d’intégration Azure-SSIS à l’aide de l’applet de commande [Stop-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1). Cette commande libère tous ses nœuds et arrête la facturation.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
```

## <a name="reconfigure-azure-ssis-ir"></a>Reconfigurer le runtime d’intégration Azure-SSIS
Reconfigurez le runtime d’intégration Azure-SSIS à l’aide de l’applet de commande [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1). L’exemple de commande suivant augmente la taille du runtime d’intégration Azure-SSIS en le faisant passer à cinq nœuds.

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
```  

## <a name="start-azure-ssis-ir"></a>Démarrer un runtime d’intégration Azure SSIS
Ensuite, démarrez le runtime d’intégration Azure-SSIS à l’aide de l’applet de commande [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1). Cette commande a pour effet d’allouer tous ses nœuds à l’exécution des packages SSIS.   

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le runtime Azure-SSIS, voir les rubriques suivantes : 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) (Runtime d’intégration Azure-SSIS). Cet article fournit des informations conceptuelles sur les runtimes d’intégration en général, y compris sur le runtime d’intégration Azure-SSIS. 
- [Didacticiel : Déployer des packages SSIS vers Azure](tutorial-deploy-ssis-packages-azure.md). Cet article fournit des instructions détaillées pour créer un runtime d’intégration Azure-SSIS qui utilise une base de données Azure SQL pour héberger le catalogue SSIS. 
- [Guide pratique : Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel et fournit des instructions sur la façon d’utiliser Azure SQL Managed Instance (préversion privée) et d’associer le runtime d’intégration à un VNet. 
- [Attacher un runtime d’intégration Azure-SSIS à un VNet](join-azure-ssis-integration-runtime-virtual-network.md). Cet article fournit des informations conceptuelles sur la façon d’attacher un runtime d’intégration Azure-SSIS à un réseau virtuel Azure (VNet). Il décrit également les étapes nécessaires pour utiliser le portail Azure afin de configurer le réseau virtuel de sorte que le runtime d’intégration Azure-SSIS puisse le rejoindre. 
- [Surveiller le runtime d’intégration Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Cet article explique comment récupérer des informations sur un runtime d’intégration Azure-SSIS ainsi que des descriptions d’état dans les informations renvoyées. 
 
