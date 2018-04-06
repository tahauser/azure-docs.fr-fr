---
title: Gérer des groupes de machines virtuelles identiques à l’aide d’Azure PowerShell | Microsoft Docs
description: Des applets de commande Azure PowerShell communes pour gérer des groupes de machines virtuelles identiques, par exemple, pour démarrer et arrêter une instance ou modifier la capacité du groupe identique.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: c463dd26c106b3178becc977a8afd742220d7973
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Gérer un groupe de machines virtuelles identique à l’aide d’Azure PowerShell
Tout au long du cycle de vie du groupe de machines virtuelles identiques, vous devrez peut-être exécuter une ou plusieurs tâches de gestion. En outre, vous souhaiterez peut-être créer des scripts pour automatiser les diverses tâches liées au cycle de vie. Cet article décrit en détail certaines des applets de commande Azure PowerShell courantes qui vous permettent d’effectuer ces tâches.

Pour effectuer ces tâches de gestion, vous aurez besoin du dernier module Azure PowerShell. Pour en savoir plus, voir [Prise en main d’Azure PowerShell](/powershell/azure/get-started-azureps). Si vous avez besoin de créer un groupe de machines virtuelles identiques, vous pouvez [Créer un groupe identique avec Azure PowerShell](quick-create-powershell.md).


## <a name="view-information-about-a-scale-set"></a>Afficher des informations sur un groupe identique
Pour afficher les informations générales sur un groupe identique, utilisez [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). L’exemple suivant obtient des informations sur le groupe identique nommé *myScaleSet* dans le groupe de ressources *myResourceGroup*. Entrez vos propres noms, comme suit :

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Afficher les machines virtuelles d’un groupe identique
Pour afficher une liste d’instances de machine virtuelle dans un groupe identique, utilisez [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). L’exemple suivant répertorie toutes les instances de machine virtuelle dans le groupe identique nommé *myScaleSet* et dans le groupe de ressources *myResourceGroup*. Indiquez vos propres valeurs pour ces noms :

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Pour afficher des informations supplémentaires sur une instance spécifique de la machine virtuelle, ajoutez le paramètre `-InstanceId` à [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) et spécifiez une instance à afficher. L’exemple suivant affiche des informations sur l’instance de machine virtuelle *0* dans le groupe identique nommé *myScaleSet* et le groupe de ressources *myResourceGroup*. Entrez vos propres noms, comme suit :

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Modifier la capacité d’un groupe identique
Les commandes précédentes montraient plus d’informations sur votre groupe identique et les instances de machine virtuelle. Pour augmenter ou diminuer le nombre d’instances dans le groupe identique, vous pouvez modifier la capacité. Le groupe identique crée ou supprime automatiquement le nombre requis de machines virtuelles, puis configure les machines virtuelles recevoir le trafic d’application.

Commencez par créer un objet de groupe identique avec [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss), puis spécifiez une nouvelle valeur pour `sku.capacity`. Pour appliquer la modification de la capacité, utilisez [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). L’exemple suivant met à jour *myScaleSet* dans le groupe de ressources *myResourceGroup* avec une capacité de *5* instances. Indiquez vos propres valeurs comme suit :

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Il faut quelques minutes pour mettre à jour de la capacité de votre groupe identique. Si vous réduisez la capacité d’un groupe identique, les machines virtuelles dont les ID d’instance sont les plus élevés sont supprimées en premier.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Arrêter et démarrer des machines virtuelles dans un groupe identique
Pour arrêter une ou plusieurs machines virtuelles dans un groupe identique, utilisez [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). Le paramètre `-InstanceId` vous permet de spécifier une ou plusieurs machines virtuelles à arrêter. Si vous ne spécifiez pas d’ID d’instance, toutes les machines virtuelles dans le groupe identique sont arrêtées. Pour arrêter plusieurs machines virtuelles, séparez chaque ID d’instance par une virgule.

L’exemple suivant arrête l’instance de machine virtuelle *0* dans le groupe identique nommé *myScaleSet* et le groupe de ressources *myResourceGroup*. Fournissez vos valeurs comme suit :

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Par défaut, les machines virtuelles arrêtées sont libérées et n’entraînent pas de frais de calcul. Si vous souhaitez que la machine virtuelle reste dans un état configuré lors de l’arrêt, ajoutez le paramètre `-StayProvisioned` à la commande précédente. Les machines virtuelles arrêtées qui conservent leur configuration peuvent occasionner des frais de calcul standard.


### <a name="start-vms-in-a-scale-set"></a>Démarrer les machines virtuelles d’un groupe identique
Pour démarrer une ou plusieurs machines virtuelles dans un groupe identique, utilisez [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). Le paramètre `-InstanceId` vous permet de spécifier une ou plusieurs machines virtuelles à démarrer. Si vous ne spécifiez pas d’ID d’instance, toutes les machines virtuelles dans le groupe identique sont démarrées. Pour démarrer plusieurs machines virtuelles, séparez chaque ID d’instance par une virgule.

L’exemple suivant démarre l’instance de machine virtuelle *0* dans le groupe identique nommé *myScaleSet* et le groupe de ressources *myResourceGroup*. Fournissez vos valeurs comme suit :

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Redémarrer les machines virtuelles d’un groupe identique
Pour redémarrer une ou plusieurs machines virtuelles dans un groupe identique, utilisez [Restart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). Le paramètre `-InstanceId` vous permet de spécifier une ou plusieurs machines virtuelles à redémarrer. Si vous ne spécifiez pas d’ID d’instance, toutes les machines virtuelles dans le groupe identique sont redémarrées. Pour redémarrer plusieurs machines virtuelles, séparez chaque ID d’instance par une virgule.

L’exemple suivant redémarre l’instance de machine virtuelle *0* dans le groupe identique nommé *myScaleSet* et le groupe de ressources *myResourceGroup*. Fournissez vos valeurs comme suit :

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Supprimer des machines virtuelles à partir d’un groupe identique
Pour supprimer une ou plusieurs machines virtuelles dans un groupe identique, utilisez [Remove-AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). Le paramètre `-InstanceId` vous permet de spécifier une ou plusieurs machines virtuelles à supprimer. Si vous ne spécifiez pas d’ID d’instance, toutes les machines virtuelles dans le groupe identique sont supprimées. Pour supprimer plusieurs machines virtuelles, séparez chaque ID d’instance par une virgule.

L’exemple suivant supprime l’instance de machine virtuelle *0* dans le groupe identique nommé *myScaleSet* et le groupe de ressources *myResourceGroup*. Fournissez vos valeurs comme suit :

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Étapes suivantes
D’autres tâches courantes pour les groupes identiques sont le [déploiement d’une application](virtual-machine-scale-sets-deploy-app.md) et la [mise à niveau d’instances de machine virtuelle](virtual-machine-scale-sets-upgrade-scale-set.md). Vous pouvez également utiliser Azure PowerShell pour [configurer des règles de mise à l’échelle automatique](virtual-machine-scale-sets-autoscale-overview.md).
