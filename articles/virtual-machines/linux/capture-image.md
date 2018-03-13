---
title: "Capturer l’image d’une machine virtuelle Linux dans Azure à l’aide de l’interface CLI 2.0 | Microsoft Docs"
description: "Capturez une image d’une machine virtuelle Azure à utiliser pour les déploiements de masse à l’aide de l’interface Azure CLI 2.0."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 3cbc25099b99499a6186e57c155d195e75bd61bf
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Créer une image d’une machine virtuelle ou d’un disque dur virtuel

<!-- generalize, image - extended version of the tutorial-->

Pour créer plusieurs copies d’une machine virtuelle à utiliser dans Azure, capturez une image de la machine virtuelle ou du disque dur virtuel du système d’exploitation. Pour créer une image, vous devez supprimer les informations de compte personnel. Cela vous permet ainsi d’effectuer plusieurs déploiements de façon plus sécurisée. Les étapes suivantes vous permettent de déprovisionner une machine virtuelle actuelle, de la libérer et de créer une image. Vous pouvez utiliser cette image pour créer des machines virtuelles dans n’importe quel groupe de ressources de votre abonnement.

Si vous souhaitez créer une copie de votre machine virtuelle Linux actuelle à des fins de sauvegarde ou de débogage, ou charger un disque dur virtuel Linux spécialisé à partir d’une machine virtuelle locale, consultez [Charger et créer une machine virtuelle Linux à partir d’une image de disque personnalisée](upload-vhd.md).  

Vous pouvez également utiliser **Packer** pour créer votre configuration personnalisée. Pour plus d’informations sur l’utilisation de Packer, consultez [Comment utiliser Packer pour créer des images de machines virtuelles Linux dans Azure](build-image-with-packer.md).


## <a name="before-you-begin"></a>Avant de commencer
Assurez-vous de satisfaire les prérequis suivants :

* Vous avez besoin d’une machine virtuelle Azure créée avec le modèle de déploiement Resource Manager à l’aide de disques managés. Si vous n’avez pas créé de machine virtuelle Linux, vous pouvez utiliser le [portail](quick-create-portal.md), l’interface [Azure CLI](quick-create-cli.md) ou les [modèles Resource Manager](create-ssh-secured-vm-from-template.md). Configurez la machine virtuelle en fonction de vos besoins. Par exemple, [ajoutez des disques de données](add-disk.md), appliquez des mises à jour et installez des applications. 

* Vous devez également disposer de la dernière version d’[Azure CLI 2.0](/cli/azure/install-az-cli2) et vous connecter à un compte Azure avec la commande [az login](/cli/azure/#az_login).

## <a name="quick-commands"></a>Commandes rapides

Pour une version simplifiée de cette rubrique, pour tester, évaluer ou découvrir les machines virtuelles dans Azure, consultez [Créer une image personnalisée d’une machine virtuelle Azure à l’aide de l’interface CLI](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Étape 1 : Déprovisionnement de la machine virtuelle
Déprovisionnez la machine virtuelle à l’aide de l’agent de machine virtuelle Azure pour supprimer les fichiers et les données propres à la machine. Utilisez la commande `waagent` avec le paramètre *-deprovision+user* sur votre machine virtuelle Linux source. Pour plus d’informations, consultez le [Guide d’utilisateur de l’agent Linux Azure](../windows/agent-user-guide.md).

1. Connectez-vous à votre machine virtuelle Linux en utilisant un client SSH.
2. Dans la fenêtre SSH, tapez la commande suivante :
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > Exécutez uniquement cette commande sur une machine virtuelle que vous souhaitez capturer en tant qu’image. Cela ne garantit pas que l’image est exempte de toute information sensible ou qu’elle convient pour la redistribution. Le paramètre *+user* supprime également le dernier compte d’utilisateur configuré. Si vous souhaitez conserver les informations d’identification de compte dans la machine virtuelle, utilisez simplement *-deprovision* pour conserver le compte utilisateur.
 
3. Tapez **y** pour continuer. Vous pouvez ajouter le paramètre **-force** pour éviter cette étape de confirmation.
4. Une fois la commande terminée, tapez **exit**. Cette étape ferme le client SSH.

## <a name="step-2-create-vm-image"></a>Étape 2 : Créer une image de machine virtuelle
Utilisez l’interface Azure CLI 2.0 pour marquer la machine virtuelle comme étant généralisée et capturer l’image. Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple *myResourceGroup*, *myVnet* et *myVM*.

1. Libérez la machine virtuelle dont vous avez annulé le déploiement à l’aide de la commande [az vm deallocate](/cli//azure/vm#deallocate). L’exemple suivant libère la machine virtuelle nommée *myVM* dans le groupe de ressources nommé *myResourceGroup* :
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Définissez l’état de la machine virtuelle sur généralisé avec [az vm generalize](/cli//azure/vm#generalize). L’exemple suivant marque comme généralisée la machine virtuelle nommée *myVM* dans le groupe de ressources nommé *myResourceGroup* :
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Créez à présent une image de la ressource de machine virtuelle à l’aide de la commande [az image create](/cli/azure/image#az_image_create). L’exemple suivant crée une image nommée *myImage* dans le groupe de ressources nommé *myResourceGroup* à l’aide de la ressource de machine virtuelle nommée *myVM* :
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > L’image est créée dans le même groupe de ressources que votre machine virtuelle source. Vous pouvez créer des machines virtuelles dans n’importe quel groupe de ressources de votre abonnement à partir de cette image. En matière de gestion, peut-être voudrez-vous créer un groupe de ressources spécifique pour vos ressources de machine virtuelle et vos images.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Étape 3 : créer une machine virtuelle à partir de l’image capturée
Créez une machine virtuelle à l’aide de l’image que vous avez créée en utilisant la commande [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVMDeployed* à partir de l’image nommée *myImage* :

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Création de la machine virtuelle dans un autre groupe de ressources 

Vous pouvez créer des machines virtuelles à partir d’une image dans n’importe quel groupe de ressources de votre abonnement. Pour créer une machine virtuelle dans un groupe de ressources différent de celui de l’image, indiquez l’ID de ressource complet de votre image. Utilisez la commande [az image list](/cli/azure/image#az_image_list) pour afficher une liste d’images. Le résultat ressemble à l’exemple suivant :

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

L’exemple suivant utilise la commande [az vm create](/cli/azure/vm#az_vm_create) pour créer une machine virtuelle dans un groupe de ressources différent de celui de l’image source en spécifiant l’ID de ressource d’image :

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Étape 4 : Vérifier le déploiement

Maintenant, exécutez le SSH sur la machine virtuelle que vous avez créée pour vérifier le déploiement et lancez le déploiement et le démarrage de l’utilisation de la nouvelle machine virtuelle. Pour vous connecter via le protocole SSH, recherchez l’adresse IP ou le nom de domaine complet de votre machine virtuelle à l’aide de la commande [az vm show](/cli/azure/vm#az_vm_show) :

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez créer plusieurs machines virtuelles à partir de votre image de machine virtuelle source. Si vous devez apporter des modifications à votre image : 

- Créez une machine virtuelle à partir de votre image.
- Apportez les mises à jour ou modifications de configuration requises.
- Suivez de nouveau ces étapes pour annuler le déploiement, libérer, généraliser et créer une image.
- Utilisez cette nouvelle image pour les déploiements futurs. Si vous le souhaitez, supprimez l’image d’origine.

Pour plus d’informations sur la gestion de vos machines virtuelles avec l’interface de ligne de commande, consultez [Azure CLI 2.0](/cli/azure/overview).
