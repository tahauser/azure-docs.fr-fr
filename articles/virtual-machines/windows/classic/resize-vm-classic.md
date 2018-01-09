---
title: "Redimensionner une machine virtuelle Windows dans le modèle de déploiement Classic - Azure | Microsoft Docs"
description: "Redimensionnez une machine virtuelle Windows créée avec le modèle de déploiement classique à l’aide d’Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e3038215-001c-406e-904d-e0f4e326a4c7
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 4277bc8394c7ba140291e9dc776162e87deab96b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Redimensionner une machine virtuelle Windows créée avec le modèle de déploiement classique
Cet article vous montre comment redimensionner une machine virtuelle Windows créée avec le modèle de déploiement classique à l’aide d’Azure PowerShell.

En ce qui concerne la possibilité de redimensionner une machine virtuelle, deux concepts contrôlent la plage de tailles disponibles pour redimensionner la machine virtuelle. Le premier est la région dans laquelle votre machine virtuelle est déployée. La liste des tailles de machines virtuelles disponibles par région se trouve sous l’onglet Services de la page web des régions Azure. Le deuxième concept est le matériel physique qui héberge actuellement votre machine virtuelle. Les serveurs physiques hébergeant des machines virtuelles sont regroupés dans des clusters de matériel physique commun. La méthode de modification de la taille d’une machine virtuelle est différente selon que la nouvelle taille souhaitée de la machine virtuelle est ou non prise en charge par le cluster matériel qui l’héberge actuellement.

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Vous pouvez également [redimensionner une machine virtuelle créée avec le modèle de déploiement Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="add-your-account"></a>Ajouter votre compte
Vous devez configurer Azure PowerShell pour qu’il fonctionne avec des ressources Azure classiques. Suivez les étapes ci-dessous pour configurer Azure PowerShell de sorte qu’il gère les ressources classiques.

1. À l’invite PowerShell, tapez `Add-AzureAccount`, puis cliquez sur **Entrée**. 
2. Tapez l’adresse de messagerie associée à votre abonnement Azure, puis cliquez sur **Continuer**. 
3. Tapez le mot de passe de votre compte. 
4. Cliquez sur **Se connecter**. 

## <a name="resize-in-the-same-hardware-cluster"></a>Redimensionner au sein du même cluster matériel
Pour redimensionner une machine virtuelle à une taille disponible dans le cluster matériel qui héberge la machine virtuelle, procédez comme suit.

1. Exécutez la commande PowerShell suivante pour lister les tailles de machines virtuelles disponibles dans le cluster matériel qui héberge le service cloud contenant la machine virtuelle.
   
    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```
2. Exécutez les commandes suivantes pour redimensionner la machine virtuelle.
   
    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Redimensionner sur un nouveau cluster matériel
Pour redimensionner une machine virtuelle à une taille non disponible dans le cluster matériel qui l’héberge, le service cloud et toutes les machines virtuelles du service cloud doivent être recréés. Chaque service cloud est hébergé sur un cluster matériel unique ; par conséquent, toutes les machines virtuelles du service cloud doivent avoir une taille prise en charge sur un cluster matériel. La procédure suivante explique comment redimensionner une machine virtuelle en supprimant, puis en recréant le service cloud.

1. Exécutez la commande PowerShell suivante pour lister les tailles de machines virtuelles disponibles dans la région. 
   
    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```
2. Prenez note de l’ensemble des paramètres de configuration de toutes les machines virtuelles du service cloud qui contient la machine virtuelle à redimensionner. 
3. Supprimez toutes les machines virtuelles du service cloud en sélectionnant l’option Conserver les disques pour chacune.
4. Recréez la machine virtuelle à redimensionner à la taille souhaitée.
5. Recréez toutes les autres machines virtuelles qui étaient dans le service cloud en utilisant une taille de machine virtuelle disponible dans le cluster matériel qui héberge maintenant le service cloud.

Vous trouverez [ici](https://github.com/Azure/azure-vm-scripts) un exemple de script de suppression et de recréation d’un service cloud avec une nouvelle taille de machines virtuelles. 

## <a name="next-steps"></a>Étapes suivantes
* [Redimensionnez une machine virtuelle créée avec le modèle de déploiement Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

