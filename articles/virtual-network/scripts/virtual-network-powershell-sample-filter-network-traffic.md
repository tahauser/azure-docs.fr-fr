---
title: Exemple de script Azure PowerShell - Filtrer le trafic réseau de machine virtuelle | Microsoft Docs
description: Exemple de script Azure PowerShell - Filtrer le trafic réseau de machine virtuelle entrant et sortant
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 13089e0194b62a1f2f7a7c592d3ff21af8495dd1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>Filtrer le trafic réseau de machine virtuelle entrant et sortant

Cet exemple de script permet de créer un réseau virtuel avec des sous-réseaux frontaux et principaux. Le trafic réseau entrant vers le sous-réseau frontal est limité à HTTP et HTTPS, tandis que le trafic sortant vers Internet à partir du sous-réseau principal n’est pas autorisé. Après avoir exécuté le script, vous disposez d’une machine virtuelle avec deux cartes réseau. Chacune est connectée à un sous-réseau différent.

Vous pouvez exécuter le script à partir d’Azure [Cloud Shell](https://shell.azure.com/powershell) ou à partir d’une installation PowerShell locale. Si vous utilisez PowerShell en local, vous devez exécuter le module AzureRM PowerShell version 5.4.1 ou version ultérieure pour les besoins de ce script. Pour trouver la version installée, exécutez `Get-Module -ListAvailable AzureRM`. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/filter-network-traffic/filter-network-traffic.ps1  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées, exécutez la commande suivante :

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, un réseau virtuel et les groupes de sécurité réseau. Chaque commande figurant dans le tableau suivant lie à une documentation spécifique :

| Commande | Notes |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Crée un objet de configuration de sous-réseau. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Crée un réseau virtuel et un sous-réseau frontal Azure. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Crée des règles de sécurité à attribuer à un groupe de sécurité réseau. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) |Crée des règles NSG qui autorisent ou bloquent des ports spécifiques sur des sous-réseaux donnés. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Associe les groupes de sécurité réseau à des sous-réseaux. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crée une adresse IP publique pour accéder à la machine virtuelle à partir d’Internet. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Crée des interfaces réseau virtuelles et les attache aux sous-réseaux frontaux et principaux du réseau virtuel. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Crée une configuration de machine virtuelle. Cette configuration inclut des informations telles que le nom de la machine virtuelle, le système d’exploitation et les informations d’identification d’administration. La configuration est utilisée lors de la création de machines virtuelles. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Création d’une machine virtuelle |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources et toutes les ressources contenues. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell pour réseau virtuel dans [Exemples PowerShell pour réseau virtuel](../powershell-samples.md).