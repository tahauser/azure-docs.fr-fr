---
title: "Charger un disque Linux personnalisé avec Azure CLI 2.0 | Microsoft Docs"
description: "Créer et charger un disque dur virtuel vers Azure en utilisant le modèle de déploiement Resource Manager et Azure CLI 2.0"
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
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 258c2a5bbce1f15c78690cb01dc9b66fef4bb8f5
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Charger et créer une machine virtuelle Linux à partir d’un disque personnalisé avec Azure CLI 2.0
Cet article indique comment charger un disque dur virtuel dans un compte de stockage Azure avec Azure CLI 2.0 et comment créer des machines virtuelles Linux à partir de ce disque personnalisé. Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](upload-vhd-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Cette fonctionnalité vous permet d’installer et de configurer une distribution Linux selon vos besoins, puis d’utiliser ce disque dur virtuel pour créer rapidement des machines virtuelles Azure.

Cette rubrique utilise des comptes de stockage pour les disques durs virtuels finaux, mais vous pouvez également effectuer ces étapes avec [des disques gérés](upload-vhd.md). 

## <a name="quick-commands"></a>Commandes rapides
Si vous avez besoin d’accomplir rapidement cette tâche, la section suivante décrit les commandes de base permettant de charger un disque dur virtuel dans Azure. Pour obtenir plus d’informations et davantage de contexte pour chaque étape, lisez la suite de ce document, [à partir de cette section](#requirements).

Assurez-vous que vous avez installé la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et que vous êtes connecté à un compte Azure avec la commande [az login](/cli/azure/reference-index#az_login).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `mydisks`.

Tout d’abord, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `WestUs` :

```azurecli
az group create --name myResourceGroup --location westus
```

Créez un compte de stockage qui contiendra vos disques virtuels avec [az storage account create](/cli/azure/storage/account#az_storage_account_create). L’exemple qui suit permet de créer un compte de stockage nommé `mystorageaccount` :

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Répertoriez les clés d’accès pour votre compte de stockage avec [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list). Notez la valeur de `key1` :

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Créez un conteneur dans votre compte de stockage à l’aide de la clé de stockage que vous venez d’obtenir avec [az storage container create](/cli/azure/storage/container#az_storage_container_create). L’exemple suivant crée un conteneur nommé `mydisks` à l’aide de la valeur de clé de stockage de `key1` :

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Enfin, chargez votre disque dur virtuel vers le conteneur que vous avez créé avec [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload). Spécifiez le chemin d’accès local à votre disque dur virtuel sous `/path/to/disk/mydisk.vhd` :

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Spécifiez l’URI de votre disque (`--image`) à l’aide de la commande [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée `myVM` à l’aide du disque virtuel que vous avez téléchargé :

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Le compte de stockage de destination doit être celui sur lequel vous avez chargé votre disque virtuel. Vous devez également spécifier tous les paramètres supplémentaires nécessaires à la commande **az vm create**, ou répondre aux invites correspondantes, notamment le réseau virtuel, l’adresse IP publique, le nom d’utilisateur et les clés SSH. Vous pouvez en savoir plus sur les [paramètres de l’interface de ligne de commande Resource Manager](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Configuration requise
Pour effectuer les étapes suivantes, vous avez besoin des éléments suivants :

* **Un système d’exploitation Linux installé dans un fichier .vhd** : installez une [distribution Linux approuvée par Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou consultez les [informations relatives aux distributions non approuvées](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) sur un disque virtuel au format VHD. Plusieurs outils permettent de créer une machine virtuelle et un disque dur virtuel :
  * Installez et configurez [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), en veillant à utiliser VHD comme format d’image. Vous pouvez [convertir une image](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) à l’aide de `qemu-img convert` si nécessaire.
  * Vous pouvez également utiliser Hyper-V [sur Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [sur Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Azure ne prend pas en charge le nouveau format VHDX. Lorsque vous créez une machine virtuelle, spécifiez le format de disque dur virtuel (VHD). Si nécessaire, vous pouvez convertir des disques VHDX au format VHD à l’aide de [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou de l’applet de commande PowerShell [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). De plus, Azure ne prend pas en charge le chargement de disques durs virtuels dynamiques. Vous devez convertir ces disques en disques durs virtuels statiques avant le chargement. Vous pouvez utiliser des outils tels que les [utilitaires de disque dur virtuel Azure pour GO](https://github.com/Microsoft/azure-vhd-utils-for-go) pour convertir les disques dynamiques au cours du processus de téléchargement vers Azure.
> 
> 

* Les machines virtuelles créées à partir de votre disque personnalisé doivent se trouver au sein du même compte de stockage que le disque lui-même.
  * Créer un compte de stockage et un conteneur pour stocker les machines virtuelles créées et votre disque personnalisé
  * Après avoir créé toutes vos machines virtuelles, vous pouvez supprimer votre disque en toute sécurité.

Assurez-vous que vous avez installé la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et que vous êtes connecté à un compte Azure avec la commande [az login](/cli/azure/reference-index#az_login).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `mydisks`.

<a id="prepimage"></a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Préparation du disque pour le chargement
Azure prend en charge diverses distributions de Linux (voir [Distributions Linux approuvées](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Les articles suivants indiquent comment préparer les diverses distributions de Linux prises en charge dans Azure :

* **[Distributions CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES et openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Autres - Distributions non approuvées](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Consultez également les **[notes d’installation Linux](create-upload-generic.md#general-linux-installation-notes)** pour obtenir d’autres conseils généraux sur la préparation d’images Linux pour Azure.

> [!NOTE]
> Le [contrat de niveau de service de la plateforme Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) s’applique aux machines virtuelles exécutant Linux uniquement lorsqu’une des distributions approuvées est utilisée avec les détails de configuration définis sous la rubrique « Versions prises en charge » de la section [Linux dans les distributions approuvées par Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Les groupes de ressources regroupent de manière logique l’ensemble des ressources Azure afin d’assurer la prise en charge de vos machines virtuelles, par exemple le stockage et la mise en réseau virtuel. Pour plus d’informations sur les groupes de ressources, consultez [Présentation des groupes de ressources](../../azure-resource-manager/resource-group-overview.md). Avant de charger votre disque personnalisé et de créer des machines virtuelles, vous devez créer un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create).

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westus` :

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Créez un compte de stockage pour votre disque personnalisé et vos machines virtuelles avec la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create). Toutes les machines virtuelles avec des disques non gérés que vous créez à partir de votre disque personnalisé doivent être placées dans le même compte de stockage que ce disque. 

L’exemple suivant crée un compte de stockage nommé `mystorageaccount` dans le groupe de ressources que vous avez créé :

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Répertorier les clés de compte de stockage
Azure génère deux clés d’accès de 512 bits pour chaque compte de stockage. Ces clés d’accès sont utilisées lors de l’authentification auprès du compte de stockage, par exemple pour effectuer des opérations d’écriture. Découvrez plus d’informations sur la [gestion de l’accès au stockage ici](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Vous affichez les clés d’accès avec [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list).

Affichez les clés d’accès au compte de stockage que vous avez créé :

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
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
Prenez note de l’élément `key1` , car vous allez l’utiliser pour interagir avec votre compte de stockage au cours des étapes suivantes.

## <a name="create-a-storage-container"></a>Créer un conteneur de stockage
De la même façon que vous créez des répertoires différents pour organiser de manière logique votre système de fichiers local, vous créez des conteneurs dans un compte de stockage afin d’organiser vos disques. Un compte de stockage peut contenir un certain nombre de conteneurs. Créez un conteneur avec la commande [az storage container create](/cli/azure/storage/container#az_storage_container_create).

L’exemple suivant permet de créer un conteneur nommé `mydisks` :

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Charger un disque dur virtuel
Chargez votre disque personnalisé avec la commande [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload). Téléchargez et stockez votre disque personnalisé en tant qu’objet blob de pages.

Spécifiez votre clé d’accès, le conteneur que vous avez créé au cours de l’étape précédente, puis le chemin d’accès au disque personnalisé sur votre ordinateur local :

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Création de la machine virtuelle
Pour créer une machine virtuelle avec des disques non gérés, spécifiez l’URI de votre disque (`--image`) avec la commande [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée `myVM` à l’aide du disque virtuel que vous avez téléchargé :

Spécifiez le paramètre `--image` avec la commande [az vm create](/cli/azure/vm#az_vm_create) pour pointer vers votre disque personnalisé. Vérifiez que le paramètre `--storage-account` correspond au compte de stockage dans lequel se trouve le disque personnalisé. Vous n’avez pas à utiliser le même conteneur que le disque personnalisé pour stocker vos machines virtuelles. Veillez à créer des conteneurs supplémentaires de la même manière que dans les étapes précédentes avant de charger votre disque personnalisé.

L’exemple suivant crée une machine virtuelle nommée `myVM` à partir de votre disque personnalisé :

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Vous devez encore spécifier tous les paramètres supplémentaires nécessaires à la commande **az vm create**, ou répondre aux invites correspondantes, notamment le nom d’utilisateur et les clés SSH.


## <a name="resource-manager-template"></a>Modèle Resource Manager
Les modèles Azure Resource Manager sont des fichiers JSON (JavaScript Objet Notation) qui définissent l’environnement que vous souhaitez générer. Les modèles sont répartis parmi différents fournisseurs de ressources, tels que les fournisseurs de calcul ou réseau. Vous pouvez utiliser les modèles existants ou écrire les vôtres. Découvrez plus d’informations sur [l’utilisation des modèles et de Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Dans le fournisseur `Microsoft.Compute/virtualMachines` de votre modèle, vous disposez d’un nœud `storageProfile` qui contient les détails de configuration de votre machine virtuelle. Les deux principaux paramètres à modifier sont les URI `image` et `vhd` qui pointent vers votre disque personnalisé et le disque virtuel de votre nouvelle machine virtuelle. Voici un exemple de JSON pour l’utilisation d’un disque personnalisé :

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Vous pouvez utiliser [ce modèle existant pour créer une machine virtuelle à partir d’une image personnalisée](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou obtenir plus d’informations sur [la création de vos propres modèles Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

Une fois que vous disposez d’un modèle configuré, utilisez [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create) pour créer vos machines virtuelles. Spécifiez l’URI de votre modèle JSON avec le paramètre `--template-uri` :

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Si vous disposez d’un fichier JSON stocké localement sur votre ordinateur, vous pouvez utiliser le paramètre `--template-file` à la place :

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Étapes suivantes
Après avoir préparé et chargé votre disque virtuel personnalisé, vous pouvez découvrir plus d’informations sur [l’utilisation de Resource Manager et des modèles](../../azure-resource-manager/resource-group-overview.md). Vous pouvez également [ajouter un disque de données](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) à vos nouvelles machines virtuelles. Si vous avez besoin d’accéder à des applications qui s’exécutent sur vos machines virtuelles, veillez à [ouvrir les ports et points de terminaison](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

