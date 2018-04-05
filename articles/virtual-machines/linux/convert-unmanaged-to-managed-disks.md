---
title: Convertir les disques non gérés d’une machine virtuelle Linux dans Azure en disques gérés - Azure Managed Disks | Microsoft Docs
description: Conversion des disques non gérés d’une machine virtuelle Linux en disques gérés à l’aide d’Azure CLI 2.0 dans le modèle de déploiement Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 12/15/2017
ms.author: rogarana
ms.openlocfilehash: a3a2bbc15dd94ef09755d34a20e69c97854416b3
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Convertir les disques non gérés d’une machine virtuelle Linux en disques gérés

Si vos machines virtuelles Linux existantes utilisent des disques non gérés, vous pouvez les convertir pour qu’elles utilisent [Azure Managed Disks](../linux/managed-disks-overview.md). Ce processus convertit le disque du système d’exploitation ainsi que tous les autres disques de données attachés.

Cet article explique comment convertir des machines virtuelles avec Azure CLI. Si vous devez installer ou mettre à niveau l’interface, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Avant de commencer
* Consultez les [questions fréquentes (FAQ) sur la migration vers Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Convertir des machines virtuelles à instance unique
Cette section explique comment convertir vos machines virtuelles Azure à instance unique à partir de disques non gérés vers des disques gérés. (Si vos machines virtuelles sont dans un groupe à haute disponibilité, voir la section suivante.) Vous pouvez utiliser ce processus pour convertir les machines virtuelles qui utilisent des disques non gérés premium (SSD) afin qu’elles utilisent des disques gérés premium, ou qui utilisent des disques non gérés standard (HDD) afin qu’elles utilisent des disques gérés standard.

1. Libérez la machine virtuelle à l’aide de [az vm deallocate](/cli/azure/vm#az_vm_deallocate). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Convertissez la machine virtuelle afin qu’elle utilise des disques gérés à l’aide de la commande [az vm convert](/cli/azure/vm#az_vm_convert). Le processus suivant convertit la machine virtuelle nommée `myVM`, y compris le disque du système d’exploitation et tous les disques de données :

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Démarrez la machine virtuelle une fois la conversion vers les disques gérés effectuée à l’aide de la commande [az vm start](/cli/azure/vm#az_vm_start). L’exemple suivant démarre la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Convertir des machines virtuelles dans un groupe à haute disponibilité

Si les machines virtuelles que vous souhaitez convertir pour utiliser des disques gérés se trouvent dans un groupe à haute disponibilité, vous devez tout d’abord convertir ce dernier en groupe à haute disponibilité géré.

Toutes les machines virtuelles dans le groupe à haute disponibilité doivent être libérées avant de convertir le groupe à haute disponibilité. Prévoyez de convertir toutes les machines virtuelles vers des disques gérés une fois que le groupe à haute disponibilité a lui-même été converti en groupe à haute disponibilité géré. Vous pouvez ensuite démarrer toutes les machines virtuelles et poursuivre un fonctionnement normal.

1. Répertoriez toutes les machines virtuelles contenues dans un groupe à haute disponibilité à l’aide de la commande [az vm availability-set list](/cli/azure/vm/availability-set#az_vm_availability_set_list). L’exemple suivant répertorie toutes les machines virtuelles dans le groupe à haute disponibilité nommé `myAvailabilitySet` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Libérez toutes les machines virtuelles à l’aide de la commande [az vm deallocate](/cli/azure/vm#az_vm_deallocate). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Convertissez le groupe à haute disponibilité à l’aide de la commande [az vm availability-set convert](/cli/azure/vm/availability-set#az_vm_availability_set_convert). L’exemple suivant convertit le groupe à haute disponibilité nommé `myAvailabilitySet` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Convertissez toutes les machines virtuelles vers des disques gérés à l’aide de la commande [az vm convert](/cli/azure/vm#az_vm_convert). Le processus suivant convertit la machine virtuelle nommée `myVM`, y compris le disque du système d’exploitation et tous les disques de données :

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Démarrez toutes les machines virtuelles une fois la conversion vers des disques gérés effectuée à l’aide de la commande [az vm start](/cli/azure/vm#az_vm_start). L’exemple suivant démarre la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les options de stockage, voir la page [Vue d’ensemble d’Azure Managed Disks](../windows/managed-disks-overview.md).
