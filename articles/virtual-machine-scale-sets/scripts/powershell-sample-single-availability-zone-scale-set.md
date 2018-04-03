---
title: 'Exemples Azure PowerShell : groupe identique dans une zone unique | Microsoft Docs'
description: Exemples Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4e4f6d26c119a113bab0220828c847f438e7bc7a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>Créer un groupe de machines virtuelles identiques dans une zone unique à l’aide de PowerShell
Ce script permet de créer un groupe de machines virtuelles identiques exécutant Windows Server 2016 dans une zone de disponibilité unique. Une fois que vous avez exécuté le script, vous pouvez accéder à la machine virtuelle via RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1 "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Exécutez la commande suivante pour supprimer le groupe de ressources, le groupe identique et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script
Ce script a recours aux commandes suivantes pour créer le déploiement. Chaque élément du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Crée un objet de configuration qui définit le sous-réseau du réseau virtuel. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Crée un réseau virtuel et un sous-réseau. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crée une adresse IP publique statique. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Crée un objet de configuration qui définit l’équilibreur de charge frontal, y compris l’adresse IP publique. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Crée un objet de configuration qui définit l’équilibreur de charge principal. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Crée un équilibreur de charge basé sur les objets de configuration frontaux et principaux. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Crée une sonde d’intégrité d’équilibreur de charge afin de surveiller le trafic sur le port TCP 80. Si deux échecs consécutifs se produisent à 15 secondes d’intervalle, le point de terminaison est considéré comme défectueux. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Crée un objet de configuration qui définit les règles de l’équilibreur de charge pour répartir le trafic sur le port TCP 80 du pool frontal jusqu’au pool principal. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Met à jour l’équilibreur de charge avec la sonde d’intégrité et les règles de l’équilibreur de charge. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Crée un objet de configuration qui définit une règle de groupe de sécurité réseau pour autoriser le trafic sur le port TCP 80. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Crée un groupe de sécurité réseau et une règle. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Met à jour le sous-réseau du réseau virtuel à associer au groupe de sécurité réseau. Toutes les instances de machine virtuelle connectées au sous-réseau héritent des règles de groupe de sécurité réseau. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | Met à jour le réseau virtuel avec la configuration du sous-réseau et du groupe de sécurité réseau. |
| [New-AzureRmVmssIpConfig](/powershell/module/azurerm.compute/new-azurermvmssipconfig) | Crée un objet de configuration qui définit l’adresse IP du groupe de machines virtuelles identiques pour connecter les instances de machine virtuelle au pool principal de l’équilibreur de charge et au pool NAT entrant.
| [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) | Crée un objet de configuration qui définit le nombre d’instances de machine virtuelle dans un groupe identique, la taille des instances de machine virtuelle, et la stratégie de mise à niveau. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | Crée un objet de configuration qui définit l’image de machine virtuelle à utiliser pour les instances de machine virtuelle. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | Crée un profil de configuration qui définit le nom de l’instance de machine virtuelle et les informations d’identification de l’utilisateur. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | Crée un objet de configuration qui définit la carte d’interface de réseau virtuel pour chaque instance de machine virtuelle. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Regroupe tous les objets de configuration afin de créer le groupe de machines virtuelles identiques. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Obtient des informations relatives à un groupe de machines virtuelles identiques. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Ajoute une extension de machine virtuelle pour le script personnalisé afin d’installer une application web de base. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Met à jour le modèle de groupe de machines virtuelles identiques pour appliquer l’extension de machine virtuelle. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Obtient des informations sur l’adresse IP publique assignée qui est utilisée par l’équilibreur de charge. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources et toutes les ressources contenues. |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de groupes de machines virtuelles identiques dans la [documentation relative aux groupes de machines virtuelles identiques Azure](../powershell-samples.md).