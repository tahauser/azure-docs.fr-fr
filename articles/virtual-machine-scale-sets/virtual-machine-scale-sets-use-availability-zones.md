---
title: "Créer un groupe identique Azure qui utilise les zones de disponibilité (préversion) | Microsoft Docs"
description: "Découvrir comment créer des groupes identiques de machines virtuelles Azure qui utilisent les zones de disponibilité pour augmenter la redondance contre les pannes"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: iainfou
ms.openlocfilehash: 310fdc68d3eb662906053d2bc0c45e6cfa18d4da
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Créer un groupe identique de machines virtuelles qui utilise les zones de disponibilité (préversion)
Pour protéger vos groupes identiques de machines virtuelles contre les défaillances au niveau du centre de données, vous pouvez créer un groupe identique dans une zone de disponibilité. Les régions Azure qui prennent en charge les zones de disponibilité comportent au minimum trois zones distinctes, chacune avec leurs propres source d’alimentation, réseau et système de refroidissement. Pour plus d'informations, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md).

Pour utiliser les zones de disponibilité, votre groupe identique doit être créé dans une [région Azure prise en charge](../availability-zones/az-overview.md#regions-that-support-availability-zones). Vous pouvez créer un groupe identique qui utilise des zones de disponibilité avec l’une des méthodes suivantes :

- [Portail Azure](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Modèles Microsoft Azure Resource Manager](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Utilisation du portail Azure
Le processus de création d’un groupe identique qui utilise une zone de disponibilité est identique à celui décrit dans [l’article de prise en main](virtual-machine-scale-sets-create-portal.md). Lorsque vous sélectionnez une région Azure prise en charge, vous pouvez créer un groupe identique dans une des zones disponibles, comme indiqué dans l’exemple suivant :

![Créer un groupe identique dans une zone de disponibilité unique](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

Le groupe identique et les ressources prises en charge, notamment l’équilibreur de charge Azure et l’adresse IP publique, sont créés dans la même zone que vous spécifiez.


## <a name="use-the-azure-cli-20"></a>Utiliser Azure CLI 2.0
Le processus de création d’un groupe identique qui utilise une zone de disponibilité est identique à celui décrit dans [l’article de prise en main](virtual-machine-scale-sets-create-cli.md). Pour utiliser les zones de disponibilité, vous devez créer votre groupe identique dans une région Azure prise en charge. Ajoutez le paramètre `--zones` à la commande [az vmss create](/cli/azure/vmss#az_vmss_create), puis spécifiez la zone à utiliser (par exemple, zone *1*, *2* ou *3*). L’exemple suivant crée un groupe identique nommé *myScaleSet* dans la zone *1* :

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Quelques minutes sont nécessaires pour créer et configurer l’ensemble des ressources et machines virtuelles du groupe identique dans la zone que vous spécifiez.


## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell
Le processus de création d’un groupe identique qui utilise une zone de disponibilité est identique à celui décrit dans [l’article de prise en main](virtual-machine-scale-sets-create-powershell.md). Pour utiliser les zones de disponibilité, vous devez créer votre groupe identique dans une région Azure prise en charge. Ajoutez le paramètre `-Zone` à la commande [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig), puis spécifiez la zone à utiliser (par exemple, zone *1*, *2* ou *3*). L’exemple suivant crée une configuration de groupe identique nommée *vmssConfig* dans *Est des États-Unis 2*, zone *1* :

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Pour créer le groupe identique réel, suivez les étapes supplémentaires, comme indiqué dans [l’article de prise en main](virtual-machine-scale-sets-create-powershell.md).


## <a name="use-azure-resource-manager-templates"></a>Utiliser les modèles Azure Resource Manager
Le processus de création d’un groupe identique qui utilise une zone de disponibilité est identique à celui décrit dans l’article de prise en main pour [Linux](virtual-machine-scale-sets-create-template-linux.md) ou [Windows](virtual-machine-scale-sets-create-template-windows.md). Pour utiliser les zones de disponibilité, vous devez créer votre groupe identique dans une région Azure prise en charge. Ajoutez la propriété `zones` au type de ressource *Microsoft.Compute/virtualMachineScaleSets* dans votre modèle, puis spécifiez la zone à utiliser (par exemple, zone *1*, *2* ou *3*). L’exemple suivant crée un groupe identique Linux nommé *myScaleSet* dans *Est des États-Unis 2*, zone *1* :

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Pour créer le groupe identique réel, suivez les étapes supplémentaires, comme indiqué dans l’article de prise en main pour [Linux](virtual-machine-scale-sets-create-template-linux.md) ou [Windows](virtual-machine-scale-sets-create-template-windows.md)


## <a name="next-steps"></a>étapes suivantes
Maintenant que vous avez créé un groupe identique dans une zone de disponibilité, vous pouvez apprendre à [déployer des applications sur des groupes identiques de machines virtuelles](virtual-machine-scale-sets-deploy-app.md) ou à [utiliser la mise à l’échelle automatique avec des groupes identiques de machines virtuelles](virtual-machine-scale-sets-autoscale-overview.md).
