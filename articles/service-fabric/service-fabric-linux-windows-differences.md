---
title: "Différences Azure Service Fabric entre Linux et Windows | Microsoft Docs"
description: "Différences entre le service Azure Service Fabric sur Linux et Windows."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 0a8dd9936268a4e4cb8b744a87111c3bbf3f062d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Différences entre Service Fabric sur Linux et Windows

Il existe certaines fonctionnalités qui sont prises en charge sur Windows, mais pas encore sur Linux. Dans le futur, les jeux de fonctionnalités seront à parité et cet écart de fonctionnalités diminuera à chaque version. Les différences suivantes existent entre les versions disponibles les plus récentes (c’est-à-dire entre la version 6.0 pour Windows et la version 6.0 pour Linux) : 

* Tous les modèles de programmation sont en version préliminaire (Java / C# Reliable Actors, services sans état Reliable et services avec état Reliable)
* Envoy (ReverseProxy) est en préversion sur Linux
* Le programme d’installation autonome n’est pas encore disponible sur Linux.
* Redirection de la console (non prise en charge dans les clusters de production Windows ou Linux)
* Le Service d’analyse d’erreur (FAS) sur Linux
* Service DNS pour les services Service Fabric (le service DNS est pris en charge pour les conteneurs sur Linux)
* Équivalents de commande de l’interface de ligne de commande de certaines commandes PowerShell (liste ci-dessous, dont la plupart ne s’appliquent qu’aux clusters autonomes)

Les outils de développement sont également différents entre Windows et Linux. Visual Studio, PowerShell, VSTS et ETW sont utilisés sur Windows, tandis que Yeoman, Eclipse, Jenkins et LTTng sont utilisés sur Linux.

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Applets de commande PowerShell ne fonctionnant pas sur un cluster Linux Service Fabric

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>Étapes suivantes
* [Préparer votre environnement de développement sur Linux](service-fabric-get-started-linux.md)
* [Prepare your development environment on OSX (Préparer votre environnement de développement sur OSX)](service-fabric-get-started-mac.md)
* [Create and deploy your first Service Fabric Java application on Linux using Yeoman (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide de Yeoman)](service-fabric-create-your-first-linux-application-with-java.md)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide du plug-in Service Fabric pour Eclipse)](service-fabric-get-started-eclipse.md)
* [Create your first Java application on Linux (Créer votre première application Java sur Linux)](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Utilisez l’interface de ligne de commande Service Fabric pour gérer vos applications](service-fabric-application-lifecycle-sfctl.md)
