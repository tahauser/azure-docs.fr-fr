---
title: "Disques de données associés de groupes de machines virtuelles identiques Azure | Microsoft Docs"
description: "Découvrez comment utiliser des disques de données associés à des groupes de machines virtuelles identiques"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: 88d4012145172bcd393070904980898d9923ea1c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Groupes de machines virtuelles identiques Azure et disques de données associés
Les [groupes de machines virtuelles identiques](/azure/virtual-machine-scale-sets/) Azure prennent désormais en charge les machines virtuelles avec des disques de données associés. Les disques de données peuvent être définis dans le profil de stockage pour des groupes identiques qui ont été créés avec des disques gérés Azure. Auparavant, les seules options de stockage associées directement disponibles avec les machines virtuelles dans des groupes identiques étaient le lecteur du système d’exploitation et les lecteurs temporaires.

> [!NOTE]
>  Lorsque vous créez un groupe identique dans lequel des disques de données associés sont définis, vous devez toujours monter et formater les disques à partir d’une machine virtuelle pour les utiliser (comme pour les machines virtuelles Azure autonomes). Une méthode pratique pour effectuer ce processus consiste à utiliser une extension de script personnalisé qui appelle un script standard pour partitionner et formater tous les disques de données sur une machine virtuelle.

## <a name="create-a-scale-set-with-attached-data-disks"></a>Créer un groupe identique avec des disques de données associés
Une méthode simple pour créer un groupe identique avec des disques associés consiste à utiliser la commande [az vmss create](/cli/azure/vmss#az_vmss_create). L’exemple suivant crée un groupe de ressources Azure et un groupe identique de 10 machines virtuelles Ubuntu, chacune ayant 2 disques de données associés, de 50 et 100 Go respectivement.

```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

La commande [az vmss create](/cli/azure/vmss#az_vmss_create) inclut par défaut des valeurs de configuration si vous ne les spécifiez pas. Pour voir les options disponibles que vous pouvez remplacer, essayez :

```bash
az vmss create --help
```

Une autre méthode de création d’un groupe identique avec des disques de données associés consiste à définir un groupe identique dans un modèle Azure Resource Manager, à inclure une section _dataDisks_ dans le _storageProfile_ et à déployer le modèle. Les disques de 50 Go et 100 Go de l’exemple précédent seraient définis comme dans l’exemple de modèle suivant :

```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```

Vous pouvez voir un exemple complet et prêt à déployer d’un modèle de groupe identique avec un disque associé défini ici : [https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data).

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>Ajouter un disque de données à un groupe identique existant
> [!NOTE]
>  Vous pouvez uniquement associer des disques de données à un groupe identique qui a été créé avec des [disques gérés Azure](./virtual-machine-scale-sets-managed-disks.md).

Vous pouvez ajouter un disque de données à un groupe de machines virtuelles identiques à l’aide de la commande _az vmss disk attach_ d’Azure CLI. Assurez-vous de spécifier un numéro d’unité logique (LUN) qui n’est pas déjà utilisé. L’exemple de CLI suivant ajoute un lecteur de 50 Go au numéro d’unité logique (LUN) 3 :

```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```

L’exemple PowerShell suivant ajoute un lecteur de 50 Go au numéro d’unité logique (LUN) 3 :

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myvmssrg -VMScaleSetName myvmss
$vmss = Add-AzureRmVmssDataDisk -VirtualMachineScaleSet $vmss -Lun 3 -Caching 'ReadWrite' -CreateOption Empty -DiskSizeGB 50 -StorageAccountType StandardLRS
Update-AzureRmVmss -ResourceGroupName myvmssrg -Name myvmss -VirtualMachineScaleSet $vmss
```

> [!NOTE]
> Des tailles de machine virtuelle différentes présentent des limites différentes sur les nombres de lecteurs associés qu’elles peuvent prendre en charge. Vérifiez la [caractéristique de taille de la machine virtuelle](../virtual-machines/windows/sizes.md) avant d’ajouter un nouveau disque.

Vous pouvez également ajouter un disque en ajoutant une nouvelle entrée à la propriété _dataDisks_ dans le _storageProfile_ de définition d’un groupe identique et en appliquant la modification. Pour tester, trouvez la définition d’un groupe identique existant dans l’[Explorateur de ressources Azure](https://resources.azure.com/). Sélectionnez _Modifier_ et ajoutez un nouveau disque à la liste des disques de données, comme illustré dans l’exemple suivant :

```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```

Puis sélectionnez _PUT_ pour appliquer les modifications à votre groupe identique. Cet exemple doit fonctionner tant que vous utilisez une taille de machine virtuelle qui prend en charge plus de deux disques de données associés.

> [!NOTE]
> Lorsque vous apportez une modification à une définition de groupe identique comme l’ajout ou la suppression d’un disque de données, elle s’applique à toutes les machines virtuelles qui sont créées, mais elle s’applique aux machines virtuelles existantes uniquement si la propriété _upgradePolicy_ est définie sur « Automatique ». Si elle est définie sur « Manuel », vous devez appliquer manuellement le nouveau modèle aux machines virtuelles existantes. Pour cela, dans le portail, utilisez la commande PowerShell _Update-AzureRmVmssInstance_ ou la commande _az vmss update-instances_ de la CLI.

## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Ajout de disques de données pré-remplis à un groupe identique existant 
> En ajoutant des disques à un modèle de groupe identique existant, le disque est donc toujours vide à la création. Ce scénario inclut aussi les nouvelles instances créées par le groupe identique. Ce comportement se produit car la définition du groupe identique contient un disque de données vide. Afin de créer des disques de données préremplis pour un modèle de groupe identique existant, vous pouvez choisir une des deux options suivantes :

* Copier des données à partir de la machine virtuelle de l’instance 0 vers les disques de données dans les autres machines virtuelles en exécutant un script personnalisé.
* Créer une image gérée avec le disque du système d’exploitation et le disque de données (avec les données requises), et créer un nouveau groupe identique avec l’image. De cette manière, chaque nouvelle machine virtuelle créée dispose d’un disque de données dans la définition du groupe identique. Étant donné que cette définition fait référence à une image avec un disque de données qui dispose de données personnalisées, chaque machine virtuelle sur le groupe identique comporte ces modifications.

> Vous trouverez ici comme créer une image personnalisée : [Créer une image gérée d’une machine virtuelle généralisée dans Azure](/azure/virtual-machines/windows/capture-image-resource/) 

> L’utilisateur doit capturer la machine virtuelle de l’instance 0 qui possède les données requises, puis utiliser le VHD pour définir l’image.

## <a name="removing-a-data-disk-from-a-scale-set"></a>Supprimer un disque de données d’un groupe identique
Vous pouvez supprimer un disque de données d’un groupe de machines virtuelles identiques à l’aide de la commande _az vmss disk detach_ d’Azure CLI. Par exemple, la commande suivante supprime le disque défini au niveau du numéro d’unité logique (LUN) 2 :
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
De même, vous pouvez également supprimer un disque d’un groupe identique en supprimant une entrée de la propriété _dataDisks_ dans le _storageProfile_ et en appliquant la modification. 

## <a name="additional-notes"></a>Remarques supplémentaires
La prise en charge des disques gérés Azure et des disques de données associés de groupe identique ests disponible dans les versions [ _2016-04-30-preview_ ](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) et ultérieures de l’APi Microsoft.Compute.API.

Dans l’implémentation initiale de la prise en charge des disques associés pour les groupes identiques, vous ne pouvez pas associer ou dissocier des disques de données à/de machines virtuelles individuelles dans un groupe identique.

La prise en charge du portail Azure pour les disques de données associés dans des groupes identiques est initialement limitée. Selon vos besoins, vous pouvez utiliser des modèles Azure, la CLI, PowerShell, des kits de développement logiciel (SDK) et l’API REST pour gérer les disques associés.


