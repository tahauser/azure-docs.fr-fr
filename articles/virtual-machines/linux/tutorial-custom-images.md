---
title: "Créer des images de machine virtuelle personnalisées avec l’interface de ligne de commande Azure | Microsoft Docs"
description: "Didacticiel : créez une image de machine virtuelle personnalisée à l’aide de l’interface de ligne de commande Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 297faeb56ac2d4743bfe5887e369be066e91fbd3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>Créer une image personnalisée d’une machine virtuelle Azure à l’aide de l’interface de ligne de commande

Les images personnalisées sont comme des images de la Place de marché, sauf que vous les créez vous-même. Les images personnalisées peuvent être utilisées pour amorcer des configurations comme le préchargement des applications, les configurations d’application et d’autres configurations de système d’exploitation. Ce didacticiel explique comment créer votre propre image personnalisée d’une machine virtuelle Azure. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Annuler le déploiement de machines virtuelles et généraliser des machines virtuelles
> * Créer une image personnalisée
> * Créer une machine virtuelle à partir d’une image personnalisée
> * Répertorier toutes les images dans votre abonnement
> * Supprimer une image


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel exige que vous exécutiez Azure CLI version 2.0.4 ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Avant de commencer

Les étapes ci-dessous expliquent comment prendre une machine virtuelle existante et la transformer en une image personnalisée réutilisable que vous pouvez utiliser pour créer de nouvelles instances de machines virtuelles.

Pour exécuter l’exemple dans ce didacticiel, vous devez disposer d’une machine virtuelle. Si nécessaire, cet [exemple de script](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) peut en créer une pour vous. Au cours du didacticiel, remplacez les noms du groupe de ressources et de la machine virtuelle si nécessaire.

## <a name="create-a-custom-image"></a>Créer une image personnalisée

Pour créer une image de machine virtuelle, vous devez préparer la machine virtuelle en annulant l’approvisionnement, en la libérant et en la marquant comme généralisée. Une fois la machine virtuelle préparée, vous pouvez créer une image.

### <a name="deprovision-the-vm"></a>Annuler l’approvisionnement de la machine virtuelle 

L’annulation de l’approvisionnement généralise la machine virtuelle en supprimant les informations spécifiques à la machine. Cette généralisation permet de déployer plusieurs machines virtuelles à partir d’une image unique. Au cours de l’annulation de l’approvisionnement, le nom d’hôte est réinitialisé sur *localhost.localdomain*. Les clés d’hôte SSH, les configurations de serveur de noms, le mot de passe racine et les baux DHCP mis en cache sont également supprimés.

Pour annuler l’approvisionnement de la machine virtuelle, utilisez l’agent de machine virtuelle Azure (waagent). L’agent de machine virtuelle Azure est installé sur la machine virtuelle et gère l’approvisionnement et l’interaction avec le contrôleur de structure Azure. Pour plus d’informations, consultez le [Guide d’utilisateur de l’agent Linux Azure](agent-user-guide.md).

Connectez-vous à votre machine virtuelle à l’aide du protocole SSH et exécutez la commande pour annuler l’approvisionnement de la machine virtuelle. Avec l’argument `+user`, le dernier compte d’utilisateur approvisionné et les données associées sont également supprimés. Remplacez l’exemple d’adresse IP par l’adresse IP publique de votre machine virtuelle.

Utilisez une clé SSH sur la machine virtuelle.
```bash
ssh azureuser@52.174.34.95
```
Annulez l’approvisionnement de la machine virtuelle.

```bash
sudo waagent -deprovision+user -force
```
Fermez la session SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Libérer la machine virtuelle et la marquer comme généralisée

Pour créer une image, la machine virtuelle doit être libérée. Libérez la machine virtuelle à l’aide de [az vm deallocate](/cli//azure/vm#deallocate). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Enfin, définissez l’état de la machine virtuelle comme généralisé avec [az vm generalize](/cli//azure/vm#generalize) afin que la plateforme Azure sache que la machine virtuelle a été généralisée. Vous pouvez uniquement créer une image à partir d’une machine virtuelle généralisée.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Création de l’image

Créez à présent une image de la machine virtuelle à l’aide de [az image create](/cli//azure/image#create). L’exemple suivant crée une image nommée *myimage* à partir d’une machine virtuelle nommée *myimage*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Créer des machines virtuelles à partir de l’image

Maintenant que vous avez une image, vous pouvez créer une ou plusieurs nouvelles machines virtuelles à partir de l’image à l’aide de [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVMfromImage* à partir de l’image nommée *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="image-management"></a>Gestion d’image 

Voici quelques exemples de tâches de gestion d’image courantes et comment les exécuter à l’aide d’Azure CLI.

Répertoriez toutes les images par nom dans un format de tableau.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

Supprimez une image. Cet exemple supprime l’image nommée *myOldImage* à partir du groupe *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous montré comment créer une image de machine virtuelle. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Annuler le déploiement de machines virtuelles et généraliser des machines virtuelles
> * Créer une image personnalisée
> * Créer une machine virtuelle à partir d’une image personnalisée
> * Répertorier toutes les images dans votre abonnement
> * Supprimer une image

Pour découvrir les machines virtuelles hautement disponibles, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [How to use availability sets](tutorial-availability-sets.md) (Comment utiliser des groupes à haute disponibilité).

