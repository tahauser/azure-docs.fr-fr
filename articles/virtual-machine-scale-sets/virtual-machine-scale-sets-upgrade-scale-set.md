---
title: "Mettre à niveau un groupe de machines virtuelles identiques Azure | Documents Microsoft"
description: "Mettre à niveau un groupe de machines virtuelles identiques Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: gunegatybo
ms.openlocfilehash: fbdc9d40173a40f35eee60cadfdd258293509d53
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="upgrade-a-virtual-machine-scale-set"></a>Mettre à niveau un jeu de mise à l’échelle de machines virtuelles
Cet article décrit comment déployer une mise à jour de système d’exploitation sur un jeu de mise à l’échelle de machines virtuelles Azure sans interruption de service. Dans ce contexte, une mise à jour de système d’exploitation implique la modification de la version ou de la référence (SKU) du système d’exploitation, ou la modification de l’URI d’une image personnalisée. Une mise à jour sans interruption de service consiste à mettre à jour des machines virtuelles une par une, ou dans des groupes (par exemple, un domaine d’erreur à la fois), plutôt que toutes simultanément. En procédant de la sorte, les machines virtuelles qui ne sont pas mises à niveau peuvent continuer à opérer.

Pour éviter l’ambiguïté, nous allons distinguer quatre types de mises à jour de système d’exploitation que vous pourriez vouloir effectuer :

* Modification de la version ou de la référence (SKU) d’une image de plateforme. Par exemple, la modification d’Ubuntu version 14.04.2-LTS de 14.04.201506100 en 14.04.201507060, ou la modification d’Ubuntu 15.10/dernière référence (SKU) vers Ubuntu 16.04.0-LTS/dernière version. Ce scénario est décrit dans cet article.
* Modification de l’URI qui pointe vers une nouvelle version d’une image personnalisée que vous avez créée (**properties** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **image** > **uri**). Ce scénario est décrit dans cet article.
* Changez la référence de l’image d’un groupe identique qui a été créé à l’aide d’Azure Managed Disks.
* Application de correctifs au système d’exploitation à partir d’une machine virtuelle (par exemple, installation d’un correctif de sécurité et exécution de Windows Update). Ce scénario est pris en charge mais n’est pas traité dans cet article.

Les jeux de mise à l’échelle de machine virtuelle déployés en tant que partie d’un cluster [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) ne sont pas abordés ici. Pour plus d’informations sur la mise à jour corrective de Service Fabric, consultez [Corriger le système d’exploitation Windows dans votre cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patch-orchestration-application)

La séquence de base pour la modification de la version du système d’exploitation ou de la référence (SKU) d’une image de plateforme ou de l’URI d’une image personnalisée se présente comme suit :

1. Obtenez le modèle de jeu de mise à l’échelle de machine virtuelle.
2. Changez la version, la référence (SKU), la référence de l’image ou la valeur de l’URI dans le modèle.
3. Mettez le modèle à niveau.
4. Effectuez un appel *manualUpgrade* sur les machines virtuelles dans l’ensemble d’échelle. Cette étape est pertinente uniquement si la valeur *upgradePolicy* est défini sur **Manuel** dans votre jeu de mise à l’échelle. Si la valeur est définie sur **Automatique**, toutes les machines virtuelles sont mises à niveau en même temps, ce qui entraîne un temps d’arrêt.

Avec ces informations à l’esprit, voyons comment vous pouvez mettre à jour la version d’un groupe identique dans PowerShell et en utilisant l’API REST. Ces exemples couvrent le cas d’une image de plateforme, mais cet article fournit suffisamment d’informations pour vous permettre d’adapter ce processus à une image personnalisée.

## <a name="powershell"></a>PowerShell
Cet exemple met à jour un groupe de machines virtuelles identiques Windows vers la nouvelle version 4.0.20160229. Après la mise à jour du modèle, il effectue une mise à jour, une instance de machine virtuelle à la fois.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Si vous mettez à jour l’URI d’une image personnalisée au lieu de changer la version d’une image de plateforme, remplacez la ligne « set the new version » par une commande qui met à jour l’URI de l’image source. Par exemple, si le groupe identique a été créé sans utiliser Azure Managed Disks, la mise à jour ressemble à ceci :

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```

Si un groupe identique basé sur une image personnalisée a été créé à l’aide d’Azure Managed Disks, la référence de l’image est mise à jour. Par exemple : 

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.id = $newImageReference
```

## <a name="the-rest-api"></a>API REST
Voici quelques exemples Python qui utilisent l’API REST Azure pour déployer une mise à jour de la version du système d’exploitation. Les deux exemples utilisent la bibliothèque légère [azurerm](https://pypi.python.org/pypi/azurerm) de fonctions de wrapper de l’API REST Azure pour effectuer une opération GET sur le modèle d’ensemble d’échelle, puis une opération PUT avec un modèle mis à jour. Ils examinent également les affichages d’instances de machine virtuelle pour identifier les machines virtuelles par domaine de mise à jour.

### <a name="vmssupgrade"></a>vmssupgrade
 [Vmssupgrade](https://github.com/gbowerman/vmsstools) est un script Python utilisé pour déployer une mise à niveau de système d’exploitation sur un jeu de mise à l’échelle de machine virtuelle en cours d’exécution, un domaine de mise à jour à la fois.

![Script Vmssupgrade pour le choix de machines virtuelles ou d’un domaine de mise à jour](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Ce script vous permet de choisir des machines virtuelles spécifiques pour mettre à jour ou spécifier un domaine de mise à jour. Il prend en charge la modification d’une version d’image de plateforme ou la modification de l’URI d’une image personnalisée.

### <a name="vmsseditor"></a>Vmsseditor
[Vmsseditor](https://github.com/gbowerman/vmssdashboard) est un éditeur d’usage général pour les jeux de mise à l’échelle de machine virtuelle qui montre un état de machine virtuelle sous la forme d’une carte thermique où une ligne représente un domaine de mise à jour. Entre autres choses, vous pouvez mettre à jour le modèle pour un ensemble d’échelle avec une nouvelle version, une référence (SKU) ou une URI d’image personnalisée, puis choisir des domaines d’erreur à mettre à niveau. Lorsque vous procédez de la sorte, toutes les machines virtuelles de ce domaine de mise à jour sont mises à niveau vers le nouveau modèle. Vous pouvez également effectuer une mise à niveau propagée basée sur la taille de lot de votre choix.  

La capture d’écran suivante montre un modèle d’ensemble d’échelle pour Ubuntu 14.04-2LTS version 14.04.201507060. De nombreuses options ont été ajoutées à cet outil depuis que cette capture d’écran a été effectuée.

![Modèle Vmsseditor d’un ensemble d’échelle pour Ubuntu 14.04-2LTS](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Une fois que vous avez cliqué sur **Mettre à niveau**, puis sur **Obtenir les détails**, la mise à jour des machines virtuelles dans UD 0 commence.

![Vmsseditor montrant la progression de la mise à jour](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

