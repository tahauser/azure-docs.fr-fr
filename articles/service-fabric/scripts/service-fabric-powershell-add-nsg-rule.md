---
title: "Exemple de script Azure PowerShell - Ajouter une règle de groupe de sécurité réseau | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Ajouter une règle de groupe de sécurité réseau pour autoriser le trafic entrant sur un port spécifique."
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
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: fd3c648ee63c45bef305658832a4d31dfdb213be
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="add-an-inbound-network-security-group-rule"></a>Ajouter une règle de groupe de sécurité réseau de trafic entrant

Cet exemple de script crée une règle de groupe de sécurité réseau visant à autoriser le trafic entrant sur le port 8081.  Le script obtient la ressource `Microsoft.Network/networkSecurityGroups` où se trouve le cluster, crée une règle de configuration de sécurité réseau et met à jour le groupe de sécurité réseau. Personnalisez les paramètres selon vos besoins.

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtient la ressource `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)| Obtient le groupe de sécurité réseau à partir du nom.|
|[Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)| Ajoute une configuration de règle de sécurité réseau à un groupe de sécurité réseau. |
|[Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)| Définit l’état de l’objectif d’un groupe de sécurité réseau.|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).
