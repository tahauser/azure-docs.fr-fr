---
title: "Déplacer une ressource de machine virtuelle Windows dans Azure | Microsoft Docs"
description: "Déplacement d’une machine virtuelle Windows vers un autre abonnement ou groupe de ressources Azure dans le modèle de déploiement Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: f4b739fd34cc0c85d47b97b7b42a70eb7f5f5ac7
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2017
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Déplacement d’une machine virtuelle Windows vers un autre abonnement ou groupe de ressources Azure
Cet article vous guide tout au long du déplacement d’une machine virtuelle Windows entre des groupes de ressources ou des abonnements. Le déplacement entre abonnements peut être pratique si, à l’origine, vous avez créé une machine virtuelle dans un abonnement personnel, et que vous souhaitez à présent la déplacer vers l’abonnement de votre entreprise afin de poursuivre votre travail.

> [!IMPORTANT]
>Vous ne pouvez pas déplacer des disques managés pour l’instant. 
>
>De nouveaux ID de ressource sont créés dans le cadre du déplacement. Une fois que la machine virtuelle a été déplacée, vous devez mettre à jour vos outils et vos scripts pour utiliser les nouveaux ID de ressource. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Utilisation de PowerShell pour déplacer une machine virtuelle

Pour déplacer une machine virtuelle vers un autre groupe de ressources, vous devez vous assurer que vous déplacez également toutes les ressources dépendantes. Pour utiliser l’applet de commande Move-AzureRMResource, vous avez besoin de l’ID de ressource de chacune des ressources. Vous pouvez obtenir une liste des ID de ressource à l’aide de l’applet de commande [Find-AzureRMResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
 Find-AzureRMResource -ResourceGroupNameContains <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Pour déplacer une machine virtuelle, nous devons déplacer plusieurs ressources. Nous pouvons utiliser la sortie de Find-AzureRMResource pour créer une liste séparée par des virgules des ID de ressource et la transmettre à [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) pour les déplacer vers la destination. 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Pour déplacer les ressources vers un autre abonnement, incluez le paramètre **DestinationSubscriptionId** . 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Vous devrez confirmer que vous souhaitez déplacer les ressources spécifiées. 

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez déplacer de nombreux types de ressources différents entre les groupes de ressources et les abonnements. Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](../../resource-group-move-resources.md).    

