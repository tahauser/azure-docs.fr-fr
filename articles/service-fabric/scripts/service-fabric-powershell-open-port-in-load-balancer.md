---
title: Exemple de script Azure PowerShell - Ouvrir le port d’application dans l’équilibreur de charge | Microsoft Docs
description: Exemple de script Azure PowerShell - Ouvrir un port dans l’équilibrage de charge Azure pour une application de Service Fabric.
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
ms.openlocfilehash: 1edaca9c354a7b65b47213c7970e823aee3cbe87
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Ouvrir un port d’application dans l’équilibreur de charge Azure

Une application Service Fabric s’exécutant dans Azure se trouve derrière l’équilibrage de charge Azure. Cet exemple de script ouvre un port dans un équilibreur de charge Azure pour qu’une application Service Fabric puisse communiquer avec des clients externes. Personnalisez les paramètres selon vos besoins. Si votre cluster se trouve dans un groupe de sécurité réseau, pensez à [ajouter une règle de groupe de sécurité réseau entrant](service-fabric-powershell-add-nsg-rule.md) pour autoriser le trafic entrant.

Si nécessaire, installez le module Service Fabric PowerShell avec le [Kit de développement logiciel (SDK) Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtient une ressource Azure.  |
| [Get-AzureRmLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) | Obtient l’équilibreur de charge Azure. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) | Ajoute une configuration de sonde pour un équilibreur de charge.|
| [Get-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/get-azurermloadbalancerprobeconfig) | Obtient une configuration de sonde pour un équilibreur de charge. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) | Ajoute une configuration de règle pour un équilibreur de charge. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Définit l’état visé d’un équilibrage de charge. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell pour Azure Service Fabric sur la page [Exemples Azure PowerShell](../service-fabric-powershell-samples.md).
