---
title: "Comment redimensionner une machine virtuelle Linux avec Azure CLI 2.0 | Microsoft Docs"
description: "Comment augmenter ou diminuer les capacités d’une machine virtuelle Linux, en en modifiant la taille."
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa9861162e63714fc17d829816b25aa36e7df73b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="resize-a-linux-virtual-machine-using-cli-20"></a>Redimensionner une machine virtuelle Linux avec CLI 2.0

Après avoir approvisionné une machine virtuelle, vous pouvez le mettre à l’échelle en en modifiant la [taille][vm-sizes]. Dans certains cas, vous devez commencer par libérer la machine virtuelle. C’est le cas notamment lorsque la taille souhaitée n’est pas disponible sur le cluster matériel qui héberge la machine virtuelle. Cet article explique comment redimensionner une machine virtuelle Linux avec Azure CLI 2.0. Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](change-vm-size-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="resize-a-vm"></a>Redimensionner une machine virtuelle
Pour redimensionner une machine virtuelle, vous devez disposer de la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et vous connecter à un compte Azure avec la commande [az login](/cli/azure/#az_login).

1. Affichez la liste des tailles de machine virtuelle disponibles dans le cluster matériel qui héberge la machine virtuelle à l’aide de la commande [az vm list-vm-resize-options](/cli/azure/vm#az_vm_list_vm_resize_options). L’exemple suivant répertorie les tailles de machine virtuelle pour la machine virtuelle nommée `myVM` dans la région du groupe de ressources `myResourceGroup` :
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Si la taille de machine virtuelle souhaitée est répertoriée, redimensionnez la machine virtuelle avec la commande [az vm resize](/cli/azure/vm#az_vm_resize). L’exemple suivant redimensionne la machine virtuelle nommée `myVM` à la taille `Standard_DS3_v2` :
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    La machine virtuelle redémarre pendant le processus. Après le redémarrage, votre système d’exploitation existant et les disques de données sont remappés. Tout le contenu du disque temporaire est perdu.

3. Si la taille de machine virtuelle souhaitée n’est pas répertoriée, vous devez d’abord libérer la machine virtuelle avec la commande [az vm deallocate](/cli/azure/vm#az_vm_deallocate). Ce processus permet de redimensionner ensuite la machine virtuelle à n’importe quelle taille disponible dans la région, puis de la redémarrer. Les étapes suivantes consistent à libérer, redimensionner, puis démarrer la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Le fait de libérer la machine virtuelle libère également toutes les adresses IP dynamiques affectées à la machine virtuelle. Les disques de données et du système d’exploitation ne sont pas affectés.

## <a name="next-steps"></a>Étapes suivantes
Pour une évolutivité supplémentaire, exécutez plusieurs instances de machine virtuelle et augmentez leur taille. Pour plus d’informations, consultez [Mise à l’échelle automatique des machines Linux dans un groupe de machines virtuelles à échelle identique][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
