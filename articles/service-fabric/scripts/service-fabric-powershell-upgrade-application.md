---
title: "Exemple de script Azure PowerShell - Mettre à niveau une application Service Fabric | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Mettre à niveau une application Service Fabric."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 889e1bbb71f6eaa1871556b3b9a7da1c28cf16ee
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="upgrade-a-service-fabric-application"></a>Mettre à niveau une application Service Fabric

Cet exemple de script met à niveau une instance d’application Service Fabric en cours d’exécution vers la version 1.3.0. Le script copie le nouveau package d’application dans le magasin d’images d’un cluster, inscrit le type d’application et supprime le package d’application inutile.  Le script démarre une mise à niveau surveillée et vérifie en continu l’état de la mise à niveau jusqu’à la fin ou la restauration de la mise à niveau. Personnalisez les paramètres selon vos besoins. 

Si nécessaire, installez le module Service Fabric PowerShell avec le [Kit de développement logiciel (SDK) Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Récupère toutes les applications du cluster Service Fabric ou une application en particulier.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Récupère l’état d’une mise à niveau d’application Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Récupère les types d’applications Service Fabric enregistrés sur le cluster Service Fabric. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Annule l’enregistrement d’un type d’application Service Fabric.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Copie un package d’application Service Fabric dans le magasin d’images.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Enregistre un type d’application Service Fabric. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Met à niveau une application Service Fabric vers la version spécifiée du type d’application. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Supprime un package d’application Service Fabric du magasin d’images.|


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Service Fabric PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Vous trouverez des exemples supplémentaires de scripts PowerShell pour Azure Service Fabric sur la page [Exemples Azure PowerShell](../service-fabric-powershell-samples.md).
