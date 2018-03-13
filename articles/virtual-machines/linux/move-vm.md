---
title: "Déplacer une machine virtuelle Linux dans Azure | Microsoft Docs"
description: "Déplacez une machine virtuelle Linux vers un autre abonnement ou groupe de ressources Azure dans le modèle de déploiement Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 459e0d591e2279b63864a273f713e4c1df8c0858
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2017
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Déplacer une machine virtuelle Linux vers un autre abonnement ou groupe de ressources
Cet article vous guide tout au long du déplacement d’une machine virtuelle Linux entre des groupes de ressources ou des abonnements. Le déplacement d’une machine virtuelle entre abonnements peut être pratique si vous avez créé une machine virtuelle dans un abonnement personnel, et que vous souhaitez à présent la déplacer vers l’abonnement de votre entreprise.

> [!IMPORTANT]
>Vous ne pouvez pas déplacer des disques managés pour l’instant. 
>
>De nouveaux ID de ressource sont créés dans le cadre du déplacement. Une fois que la machine virtuelle a été déplacée, vous devez mettre à jour vos outils et vos scripts pour utiliser les nouveaux ID de ressource. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Utiliser l’interface CLI Azure pour déplacer une machine virtuelle


Pour pouvoir déplacer votre machine virtuelle à l’aide de l’interface CLI, vous devez vous assurer que les abonnements source et de destination se trouvent dans le même locataire. Pour vérifier que les deux abonnements ont le même ID de locataire, utilisez la commande [az account show](/cli/azure/account#az_account_show).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Si les ID clients des abonnements source et de destination ne sont pas identiques, vous devez contacter le [support technique](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) pour déplacer les ressources vers un nouveau client.

Pour déplacer avec succès une machine virtuelle, vous devez déplacer la machine virtuelle et toutes les ressources qui l’accompagnent. Utilisez la commande [azure resource list](/cli/azure/resource#az_resource_list) pour lister toutes les ressources d’un groupe de ressources et leurs ID. Cela permet de diriger la sortie de cette commande dans un fichier. Vous pouvez ainsi copier et coller les ID dans des commandes ultérieures.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Pour déplacer une machine virtuelle et ses ressources dans un autre groupe de ressources, utilisez la commande [az resource move](/cli/azure/resource#az_resource_move). L’exemple suivant montre comment déplacer une machine virtuelle et les ressources courantes nécessaires. Utilisez le paramètre **-ids** et transmettez la liste des ID des ressources à déplacer, séparée par des virgules (sans espaces).

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageacountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Si vous souhaitez déplacer la machine virtuelle et ses ressources vers un autre abonnement, ajoutez le paramètre **--destination-subscriptionId** pour spécifier l’abonnement de destination.

Vous devrez peut-être confirmer que vous souhaitez déplacer la ressource spécifiée. Tapez **Y** pour confirmer que vous souhaitez déplacer les ressources.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez déplacer de nombreux types de ressources différents entre les groupes de ressources et les abonnements. Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](../../resource-group-move-resources.md).    

