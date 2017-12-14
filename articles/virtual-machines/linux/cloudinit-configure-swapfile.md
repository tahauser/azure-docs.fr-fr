---
title: "Utiliser cloud-init pour configurer un fichier d’échange sur une machine virtuelle Linux | Microsoft Docs"
description: "Comment utiliser cloud-init pour configurer un fichier d’échange dans une machine virtuelle Linux lors de la création avec Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: a8ccec0dc8ff100c5d067cd50f2a6fa8cb4871fb
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Utiliser cloud-init pour configurer un fichier d’échange sur une machine virtuelle Linux
Cet article vous explique comment utiliser [cloud-init](https://cloudinit.readthedocs.io) pour configurer le fichier d’échange sur diverses distributions Linux. Le fichier d’échange est généralement configuré par l’Agent Linux (WALA) en fonction des distributions qui en ont besoin.  Ce document décrit les processus de création du fichier d’échange à la demande au moment de l’approvisionnement à l’aide de cloud-init.  Pour plus d’informations sur le fonctionnement de cloud-init en mode natif dans Azure et sur les versions de Linux prises en charge, consultez [Présentation de cloud-init](using-cloud-init.md).

## <a name="create-swapfile-for-ubuntu-based-images"></a>Créer un fichier d’échange pour des images basées sur Ubuntu
Par défaut sur Azure, les images de la galerie d’Ubuntu ne créent pas de fichiers d’échange. Pour activer la configuration du fichier d’échange au moment de l’approvisionnement de la machine virtuelle à l’aide de cloud-init, veuillez consulter le [document sur AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) sur le wiki Ubuntu.

## <a name="create-swapfile-for-redhat-and-centos-based-images"></a>Créer le fichier d’échange pour les images basées sur RedHat et CentOS

Dans l’interpréteur de commandes actuel, créez un fichier nommé *cloud_init_swapfile.txt* et collez la configuration suivante. Pour cet exemple, créez le fichier dans Cloud Shell et non sur votre ordinateur local. Vous pouvez utiliser l’éditeur de votre choix. Entrez `sensible-editor cloud_init_swapfile.txt` pour créer le fichier et afficher la liste des éditeurs disponibles. Choisissez le n°1 pour utiliser l’éditeur **nano**. Vérifiez que l’intégralité du fichier cloud-init est copiée correctement, en particulier la première ligne.  

```yaml
#cloud-config
disk_setup:
ephemeral0:
table_type: gpt
layout: [66, [33,82]]
overwrite: True
fs_setup:
- device: ephemeral0.1
filesystem: ext4
- device: ephemeral0.2
filesystem: swap
mounts:
- ["ephemeral0.1", "/mnt"]
- ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Avant de déployer cette image, vous devez créer un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maintenant, créez une machine virtuelle avec [az vm create](/cli/azure/vm#create) et spécifiez le fichier cloud-init avec `--custom-data cloud_init_swapfile.txt` comme suit :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Vérifier que le fichier d’échange a été créé
Établissez une connexion SSH à l’adresse IP publique de votre machine virtuelle indiquée dans la sortie de la commande précédente. Entrez votre propre **publicIpAddress** comme suit :

```bash
ssh <publicIpAddress>
```

Une fois que vous avez appliqué le protocole SSH à la machine virtuelle, vérifiez si le fichier d’échange a été créé.

```bash
swapon -s
```

La sortie de cette commande doit ressembler à ceci :

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Si vous disposez d’une image Azure existante qui a un fichier d’échange configuré et que vous souhaitez modifier la configuration du fichier d’échange pour de nouvelles images, vous devez supprimer le fichier d’échange existant. Veuillez consulter « Customize Images to provision by cloud-init» (Personnaliser des images à approvisionner par cloud-init) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des exemples cloud-init supplémentaires de modifications de configuration, consultez les rubriques suivantes :
 
- [Use cloud-init to add a user to a Linux VM in Azure](cloudinit-add-user.md) (Utiliser cloud-init pour ajouter un utilisateur à une machine virtuelle dans Azure)
- [Use cloud-init to update and install packages in a Linux VM in Azure](cloudinit-update-vm.md) (Utiliser cloud-init pour mettre à jour et installer des packages dans une machine virtuelle Linux dans Azure)
- [Use cloud-init to set hostname for a Linux VM in Azure](cloudinit-update-vm-hostname.md) (Utiliser cloud-init pour définir un nom d’hôte pour une machine virtuelle Linux dans Azure) 
- [Comment personnaliser une machine virtuelle Linux au premier démarrage](tutorial-automate-vm-deployment.md)