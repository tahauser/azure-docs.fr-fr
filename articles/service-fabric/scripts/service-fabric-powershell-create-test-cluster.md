---
title: "Exemple de script Azure PowerShell - Créer un cluster Service Fabric | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Créer un cluster Service Fabric de test à trois nœuds"
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/29/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: fd94a5dd9630cc65dedc180cdfd7aafea83c4866
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-three-node-test-service-fabric-cluster"></a>Créer un cluster Service Fabric de test à trois nœuds

Cet exemple de script crée un cluster Service Fabric de test à trois nœuds sécurisé par un certificat X.509. La configuration de cluster à trois nœuds est prise en charge pour le développement/test, car elle permet d’effectuer des mises à niveau en toute sécurité et de faire face aux défaillances d’un nœud (tant que ces deux événements ne sont pas simultanés). Un cluster de production a besoin d’au moins cinq nœuds pour être résistant à des défaillances simultanées.  

La commande crée un certificat auto-signé et le charge dans un nouveau coffre de clés, créé dans le même groupe de ressources que le cluster. Le certificat est également copié dans un répertoire local.  Définissez le paramètre *-OS* pour choisir la version de Windows ou Linux qui s’exécute sur les nœuds de cluster.  Personnalisez les paramètres selon vos besoins.

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview), puis exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-test-cluster/create-test-cluster.ps1 "Create a test Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, le cluster et toutes les ressources associées.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | Crée un cluster Service Fabric. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure Service Fabric sur la page [Azure PowerShell Samples](../service-fabric-powershell-samples.md) (Exemples Azure PowerShell).
