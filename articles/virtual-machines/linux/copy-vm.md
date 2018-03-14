---
title: "Copie d’une machine virtuelle Linux à l’aide d’Azure CLI 2.0 | Microsoft Docs"
description: "Apprenez à créer une copie de votre machine virtuelle Azure Linux à l’aide d’Azure CLI 2.0 et de Managed Disks."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 26e09f4e408b92034594215f602d5ca0ff259c5a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-and-managed-disks"></a>Créer une copie de machine virtuelle Linux à l’aide d’Azure CLI 2.0 et de disques gérés


Cet article vous explique comment créer une copie de votre machine virtuelle Azure exécutant Linux avec Azure CLI 2.0 et le modèle de déploiement Azure Resource Manager. Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Vous pouvez également [charger et créer une machine virtuelle à partir d’un disque dur virtuel](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Prérequis


-   Installer [Azure CLI 2.0](/cli/azure/install-az-cli2)

-   Connectez-vous à un compte Azure avec [az login](/cli/azure/#az_login).

-   Ayez une machine virtuelle Azure à utiliser en tant que source pour votre copie.

## <a name="step-1-stop-the-source-vm"></a>Étape 1 : Arrêter la machine virtuelle source


Libérez la machine virtuelle source à l’aide de la commande [az vm deallocate](/cli/azure/vm#az_vm_deallocate).
L’exemple suivant libère la machine virtuelle nommée **myVM** dans le groupe de ressources **myResourceGroup** :

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Étape 2 : Copier la machine virtuelle source


Pour copier une machine virtuelle, vous devez créer une copie du disque dur virtuel sous-jacent. Ce processus crée un disque dur virtuel spécialisé en tant que disque géré qui contient la même configuration et les mêmes paramètres que la machine virtuelle source.

Pour plus d’informations sur les disques gérés, consultez [Vue d’ensemble d’Azure Managed Disks](../windows/managed-disks-overview.md). 

1.  Répertoriez chaque machine virtuelle et le nom de leur disque de système d’exploitation avec la commande [az vm list](/cli/azure/vm#az_vm_list). L’exemple suivant répertorie toutes les machines virtuelles dans le groupe de ressources nommé **myResourceGroup** :
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    Le résultat ressemble à l’exemple suivant :

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Copiez le disque en créant un nouveau disque géré avec la commande [az disk create](/cli/azure/disk#az_disk_create). L’exemple suivant crée un disque nommé **myCopiedDisk** à partir du disque géré nommé **myDisk** :

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Examinez les disques gérés qui appartiennent désormais à votre groupe de ressources, à l’aide de la commande [az disk list](/cli/azure/disk#az_disk_list). L’exemple suivant répertorie les disques gérés dans le groupe de ressources nommé **myResourceGroup** :

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>Étape 3 : Configurer un réseau virtuel


Les étapes facultatives suivantes consistent à créer un nouveau réseau virtuel, un nouveau sous-réseau, une nouvelle adresse IP publique et une nouvelle carte réseau virtuelle.

Si vous copiez une machine virtuelle à des fins de dépannage ou dans le cadre de déploiements supplémentaires, vous pouvez ne pas vouloir utiliser une machine virtuelle hébergée dans un réseau virtuel existant.

Si vous souhaitez créer une infrastructure de réseau virtuel pour vos machines virtuelles copiées, exécutez les procédures suivantes. Si vous ne souhaitez pas créer de réseau virtuel, passez à [l’Étape 4 : Créer une machine virtuelle](#step-4-create-a-vm).

1.  Exécutez la commande [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) pour créer le réseau virtuel. L’exemple suivant permet de créer un réseau virtuel nommé **myVnet** et un sous-réseau nommé **mySubnet** :

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Exécutez la commande [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) pour créer une adresse IP publique. L’exemple suivant permet de créer une adresse IP publique nommée **myPublicIP** avec le nom DNS de **mypublicdns**. (Le nom DNS doit être unique, fournissez donc votre propre nom unique.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Exécutez la commande [az network nic create](/cli/azure/network/nic#az_network_nic_create) pour créer la carte réseau.
    L’exemple suivant permet de créer une carte réseau nommée **myNic** et associée au sous-réseau **mySubnet** :

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>Étape 4 : Créer une machine virtuelle

Vous pouvez désormais créer une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create).

Spécifiez le disque géré copié à utiliser en tant que disque du système d’exploitation (--attach-os-disk), tel que défini ci-dessous :

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>étapes suivantes

Pour découvrir comment utiliser l’interface Azure CLI pour gérer votre nouvelle machine virtuelle, consultez la page [Commande de l’interface de ligne de commande Azure en mode Azure Resource Manager](../azure-cli-arm-commands.md).
