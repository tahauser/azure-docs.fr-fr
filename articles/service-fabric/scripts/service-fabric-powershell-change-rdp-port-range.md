---
title: Exemple de script Azure PowerShell - Changer la plage de ports RDP | Microsoft Docs
description: Exemple de script Azure PowerShell - Changer la plage de ports RDP d’un cluster déployé
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 83fb6cc03f605a60b06f31fa6ddd82cd4e3e899e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="update-the-rdp-port-range-values"></a>Mettre à jour les valeurs de la plage de ports RDP

Cet exemple de script change les valeurs de la plage de ports RDP sur les machines virtuelles de nœud de cluster une fois que le cluster a été déployé.  Azure PowerShell est utilisé afin que les machines virtuelles sous-jacentes ne s’interrompent pas.  Le script obtient la ressource `Microsoft.Network/loadBalancers` dans le groupe de ressources du cluster et met à jour les valeurs `inboundNatPools.frontendPortRangeStart` et `inboundNatPools.frontendPortRangeEnd`. Personnalisez les paramètres selon vos besoins.

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtient la ressource `Microsoft.Network/loadBalancers`. |
|[Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource)|Met à jour la ressource `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure Service Fabric sur la page [Azure PowerShell Samples](../service-fabric-powershell-samples.md) (Exemples Azure PowerShell).
