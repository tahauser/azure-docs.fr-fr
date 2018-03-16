---
title: "Charger ou copier une machine virtuelle Linux personnalisée avec Azure CLI 2.0 | Microsoft Docs"
description: "Charger ou copier une machine virtuelle personnalisée en utilisant le modèle de déploiement Resource Manager et Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/06/2017
ms.author: cynthn
ms.openlocfilehash: 49e6f4a1222d11d096577265e3e7c7a30169bf81
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Créer une machine virtuelle Linux à partir d’un disque personnalisé avec Azure CLI 2.0

<!-- rename to create-vm-specialized -->

Cet article vous explique comment charger un disque dur virtuel (VHD) personnalisé ou copier un disque VHD existant dans Azure et comment créer des machines virtuelles Linux à partir du disque personnalisé. Vous pouvez installer et de configurer une distribution Linux selon vos besoins, puis d’utiliser ce disque dur virtuel pour créer rapidement une machine virtuelle Azure.

Si vous souhaitez créer plusieurs machines virtuelles à partir de votre disque personnalisé, créez une image à partir de votre machine virtuelle ou d’un disque dur virtuel. Pour plus d’informations, consultez la page [Créer une image personnalisée d’une machine virtuelle Azure à l’aide de l’interface de ligne de commande](tutorial-custom-images.md).

Deux options s'offrent à vous :
* [Charger un disque dur virtuel](#option-1-upload-a-specialized-vhd)
* [Copier une machine virtuelle Azure existante](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>Commandes rapides

Lorsque vous créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm#az_vm_create) à partir d’un disque spécialisé ou personnalisé, vous **attachez** le disque (--attach-os-disk) au lieu de spécifier une image personnalisée ou marketplace (--image). L’exemple suivant crée une machine virtuelle nommée *myVM* à l’aide du disque géré nommé *myManagedDisk* créé à partir de votre disque dur virtuel personnalisé :

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Configuration requise
Pour effectuer les étapes suivantes, vous avez besoin des éléments suivants :

* Une machine virtuelle Linux qui a été préparée pour une utilisation dans Azure. La section [Préparation de la machine virtuelle](#prepare-the-vm) de cet article explique où trouver les informations spécifiques de distribution pour l’installation de l’agent Linux Azure (waagent), qui sont requises pour que la machine virtuelle fonctionne correctement dans Azure et pour que vous puissiez vous y connecter via SSH.
* Le fichier VHD d’une [distribution Linux approuvée par Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) existante (ou consultez [les informations relatives aux distributions non approuvées](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) sur un disque virtuel au format VHD. Plusieurs outils permettent de créer une machine virtuelle et un disque dur virtuel :
  * Installez et configurez [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), en veillant à utiliser VHD comme format d’image. Vous pouvez [convertir une image](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) à l’aide de la commande **qemu-img convert** si nécessaire.
  * Vous pouvez également utiliser Hyper-V [sur Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [sur Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Azure ne prend pas en charge le nouveau format VHDX. Lorsque vous créez une machine virtuelle, spécifiez le format de disque dur virtuel (VHD). Si nécessaire, vous pouvez convertir des disques VHDX au format VHD à l’aide de [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou de l’applet de commande PowerShell [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx). De plus, Azure ne prend pas en charge le chargement de disques durs virtuels dynamiques. Vous devez convertir ces disques en disques durs virtuels statiques avant le chargement. Vous pouvez utiliser des outils tels que les [utilitaires de disque dur virtuel Azure pour GO](https://github.com/Microsoft/azure-vhd-utils-for-go) pour convertir les disques dynamiques au cours du processus de téléchargement vers Azure.
> 
> 


* Assurez-vous que vous avez installé la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et que vous êtes connecté à un compte Azure avec la commande [az login](/cli/azure/reference-index#az_login).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple *myResourceGroup*, *mystorageaccount* et *mydisks*.

<a id="prepimage"></a>

## <a name="prepare-the-vm"></a>Préparation de la machine virtuelle

Azure prend en charge diverses distributions de Linux (voir [Distributions Linux approuvées](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Les articles suivants indiquent comment préparer les diverses distributions de Linux prises en charge dans Azure :

* [Distributions CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES et openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Autres - Distributions non approuvées](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Consultez également les [notes d’installation Linux](create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils généraux sur la préparation d’images Linux pour Azure.

> [!NOTE]
> Le [contrat de niveau de service de la plateforme Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) s’applique aux machines virtuelles exécutant Linux uniquement lorsqu’une des distributions approuvées est utilisée avec les détails de configuration définis sous la rubrique « Versions prises en charge » de la section [Linux dans les distributions approuvées par Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Option 1 : charger un disque dur virtuel

Vous pouvez charger un disque dur virtuel personnalisé s’exécutant sur un ordinateur local ou que vous avez exporté à partir d’un autre cloud. Pour utiliser le disque dur virtuel pour créer une machine virtuelle Azure, vous devez le charger dans un compte de stockage et créer un disque géré à partir de ce disque. 

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de charger votre disque personnalisé et de créer des machines virtuelles, vous devez créer un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create).

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* : [Vue d’ensemble d’Azure Managed Disks](../windows/managed-disks-overview.md)
```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Créez un compte de stockage pour votre disque personnalisé et vos machines virtuelles avec la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create). 

L’exemple suivant crée un compte de stockage nommé *mystorageaccount* dans le groupe de ressources que vous avez créé :

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Répertorier les clés de compte de stockage
Azure génère deux clés d’accès de 512 bits pour chaque compte de stockage. Ces clés d’accès sont utilisées lors de l’authentification auprès du compte de stockage, par exemple pour effectuer des opérations d’écriture. Découvrez plus d’informations sur la [gestion de l’accès au stockage ici](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Vous affichez les clés d’accès avec [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list).

Affichez les clés d’accès au compte de stockage que vous avez créé :

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

Le résultat ressemble à ce qui suit :

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Prenez note de l’élément **key1**, car vous allez l’utiliser pour interagir avec votre compte de stockage au cours des étapes suivantes.

### <a name="create-a-storage-container"></a>Créer un conteneur de stockage
De la même façon que vous créez des répertoires différents pour organiser de manière logique votre système de fichiers local, vous créez des conteneurs dans un compte de stockage afin d’organiser vos disques. Un compte de stockage peut contenir un certain nombre de conteneurs. Créez un conteneur avec la commande [az storage container create](/cli/azure/storage/container#az_storage_container_create).

L’exemple suivant permet de créer un conteneur nommé *mydisks* :

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Charger le disque dur virtuel
Chargez votre disque personnalisé avec la commande [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload). Téléchargez et stockez votre disque personnalisé en tant qu’objet blob de pages.

Spécifiez votre clé d’accès, le conteneur que vous avez créé au cours de l’étape précédente, puis le chemin d’accès au disque personnalisé sur votre ordinateur local :

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
Le chargement du disque dur virtuel peut prendre un certain temps.

### <a name="create-a-managed-disk"></a>Créer un disque géré


Créez un disque géré à partir du disque dur virtuel à l’aide de la commande [az disk create](/cli/azure/disk#az_disk_create). L’exemple suivant crée un disque géré nommé *myManagedDisk* à partir du disque dur virtuel que vous avez chargé dans votre conteneur et votre compte de stockage nommés :

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Option 2 : copier une machine virtuelle existante

Vous pouvez également créer la machine virtuelle personnalisée dans Azure, puis copier le disque du système d’exploitation et l’attacher à une nouvelle machine virtuelle pour créer une autre copie. Cela convient pour un test, mais si vous souhaitez utiliser une machine virtuelle Azure existante comme modèle pour plusieurs nouvelles machines virtuelles, mieux vaut créer une **image** à la place. Pour plus d’informations sur la création d’une image à partir d’une machine virtuelle Azure existante, consultez la page [Créer une image personnalisée d’une machine virtuelle Azure à l’aide de l’interface de ligne de commande](tutorial-custom-images.md)

### <a name="create-a-snapshot"></a>Créer un instantané

Cet exemple crée l’instantané d’une machine virtuelle nommée *myVM* dans le groupe de ressources *myResourceGroup* et crée un instantané nommé *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Créer le disque géré

Créez un disque géré à partir de l’instantané.

Obtenez l’ID de l’instantané. Dans cet exemple, l’instantané est nommé *osDiskSnapshot* et se trouve dans le groupe de ressources *myResourceGroup*.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Créez le disque géré. Dans cet exemple, nous allons créer un disque géré nommé *myManagedDisk* à partir de notre instantané, qui représente 128 Go de stockage standard.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Création de la machine virtuelle

Maintenant, créez votre machine virtuelle avec [az vm create](/cli/azure/vm#az_vm_create) et attachez le disque géré en tant que disque du système d’exploitation (--attach-os-disk). L’exemple suivant crée une machine virtuelle nommée *myNewVM* à l’aide du disque géré créé à partir du disque dur virtuel chargé :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Vous devriez pouvoir utiliser SSH pour vous connecter à la machine virtuelle en utilisant les informations d’identification de la machine virtuelle source. 

## <a name="next-steps"></a>Étapes suivantes
Après avoir préparé et chargé votre disque virtuel personnalisé, vous pouvez découvrir plus d’informations sur [l’utilisation de Resource Manager et des modèles](../../azure-resource-manager/resource-group-overview.md). Vous pouvez également [ajouter un disque de données](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) à vos nouvelles machines virtuelles. Si vous avez besoin d’accéder à des applications qui s’exécutent sur vos machines virtuelles, veillez à [ouvrir les ports et points de terminaison](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

