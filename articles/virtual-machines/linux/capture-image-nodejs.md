---
title: "Capturer une machine virtuelle Linux Azure à utiliser en tant que modèle | Microsoft Docs"
description: "Apprenez à capturer et à généraliser l’image d’une machine virtuelle Azure sous Linux créée avec le modèle de déploiement Azure Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: f990a0da0be7f10dc16aa2e5a6320b456cfffed1
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2017
---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Capturer une machine virtuelle Linux exécutée sur Azure
Suivez les étapes décrites dans cet article pour généraliser et capturer votre machine virtuelle Linux Azure dans le modèle de déploiement Resource Manager. Lorsque vous généralisez la machine virtuelle, vous supprimez les informations personnelles de votre compte et vous préparez la machine virtuelle pour l’utiliser en tant qu’image. Ensuite, vous capturez une image généralisée du disque dur virtuel du système d’exploitation, les disques durs virtuels des disques de données attachés et un [modèle Resource Manager](../../azure-resource-manager/resource-group-overview.md) pour les nouveaux déploiements de machines virtuelles. Cet article explique comment capturer une image de machine virtuelle avec Azure CLI 1.0 pour une machine virtuelle utilisant des disques non gérés. Vous pouvez également [capturer une machine virtuelle utilisant Azure Managed Disks à l’aide d’Azure CLI 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Les disques gérés sont traités par la plateforme Azure et ne nécessitent pas de préparation ou d’emplacement pour les stocker. Pour plus d’informations, voir la page [Azure Managed Disks overview](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Vue d’ensemble d’Azure Managed Disks). 

Pour créer des machines virtuelles à l’aide de l’image, configurez les ressources réseau de chaque nouvelle machine virtuelle et utilisez le modèle (un fichier JSON ou JavaScript Object Notation) pour déployer à partir des images de disque dur virtuel capturées. De cette façon, vous pouvez répliquer une machine virtuelle avec sa configuration logicielle actuelle, comme vous utilisez des images dans Azure Marketplace.

> [!TIP]
> Si vous souhaitez créer une copie de votre machine virtuelle Linux existante avec son état spécialisé pour la sauvegarde ou le débogage, consultez [Créer une copie d’une machine virtuelle Linux sur Azure](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Si vous souhaitez charger un disque dur virtuel Linux à partir d’une machine virtuelle locale, consultez [Chargement et création d’une machine virtuelle à partir d’une image de disque personnalisée](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#before-you-begin) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

## <a name="before-you-begin"></a>Avant de commencer
Assurez-vous de satisfaire les prérequis suivants :

* **Machine virtuelle Azure créée dans le modèle de déploiement Resource Manager** : si vous n’avez pas créé une machine virtuelle Linux, vous pouvez utiliser le [portail](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [l’interface CLI Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou les [modèles Resource Manager](create-ssh-secured-vm-from-template.md). 
  
    Configurez la machine virtuelle en fonction de vos besoins. Par exemple, [ajoutez des disques de données](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), appliquez des mises à jour et installez des applications. 
* **Interface CLI Azure** : installez [l’interface CLI Azure](../../cli-install-nodejs.md) sur un ordinateur local.

## <a name="step-1-remove-the-azure-linux-agent"></a>Étape 1 : supprimer l’agent Linux Azure
Commencez par exécuter la commande **waagent** avec le paramètre **deprovision** sur la machine virtuelle Linux. Cette commande supprime les fichiers et les données pour préparer la machine virtuelle à la généralisation. Pour plus d’informations, consultez le [Guide d’utilisation de l’agent Linux Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

1. Connectez-vous à votre machine virtuelle Linux en utilisant un client SSH.
2. Dans la fenêtre SSH, tapez la commande suivante :
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Exécutez uniquement cette commande sur une machine virtuelle que vous souhaitez capturer en tant qu’image. Cela ne garantit pas que l’image est exempte de toute information sensible ou qu’elle convient pour la redistribution.
 
3. Tapez **y** pour continuer. Vous pouvez ajouter le paramètre **-force** pour éviter cette étape de confirmation.
4. Une fois la commande terminée, tapez **exit**. Cette étape ferme le client SSH.

## <a name="step-2-capture-the-vm"></a>Étape 2 : capturer la machine virtuelle
Utilisez l’interface CLI Azure pour généraliser et capturer la machine virtuelle. Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les noms de paramètre sont par exemple **myResourceGroup**, **myVnet** et **myVM**.

1. À partir de l’ordinateur local, ouvrez l’interface CLI Azure et [connectez-vous à votre abonnement Azure](/cli/azure/authenticate-azure-cli). 
2. Assurez-vous que vous êtes en mode Gestionnaire de ressources.
   
    ```azurecli
    azure config mode arm
    ```
3. Arrêtez la machine virtuelle dont vous avez déjà annulé l’approvisionnement à l’aide de la commande suivante :
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. Généralisez la machine virtuelle en exécutant la commande suivante :
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. Exécutez maintenant la commande **azure vm capture** qui capture la machine virtuelle. Dans l’exemple suivant, les disques durs virtuels image sont capturés avec des noms commençant par **MyVHDNamePrefix**, et l’option **-t** spécifie un chemin d’accès au modèle **MyTemplate.json**. 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > Les fichiers d’image de disque dur virtuel sont créés par défaut dans le même compte de stockage utilisé par la machine virtuelle d’origine. Utilisez le *même compte de stockage* pour stocker les disques durs virtuels des nouvelles machines virtuelles créées à partir de l’image. 

6. Pour rechercher l’emplacement d’une image capturée, ouvrez le modèle JSON dans un éditeur de texte. Dans **storageProfile**, recherchez **l’uri** de **l’image** située dans le conteneur **system**. Par exemple, l’URI de l’image du disque du système d’exploitation est semblable à `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Étape 3 : créer une machine virtuelle à partir de l’image capturée
Maintenant, utilisez l’image avec un modèle pour créer une machine virtuelle Linux. Ces étapes vous montrent comment utiliser l’interface CLI Azure et le modèle de fichier JSON capturé pour créer la machine virtuelle dans un nouveau réseau virtuel.

### <a name="create-network-resources"></a>Créer des ressources réseau
Pour utiliser le modèle, vous devez d’abord configurer un réseau virtuel et une carte réseau pour votre nouvelle machine virtuelle. Nous vous recommandons de créer un groupe de ressources pour ces ressources à l’emplacement de stockage de votre image de machine virtuelle. Exécutez des commandes comme suit en remplaçant les noms de vos ressources et de l’emplacement Azure approprié (« centralus » dans ces commandes) :

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>Obtenir l’ID de la carte d’interface réseau
Pour déployer une machine virtuelle à l’aide du fichier JSON que vous avez enregistré pendant la capture, vous avez besoin de l’ID de la carte d’interface réseau. Obtenez-le en exécutant la commande suivante :

```azurecli
azure network nic show myResourceGroup1 myNIC
```

**L’ID** du résultat ressemble à `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`.

### <a name="create-a-vm"></a>Créer une machine virtuelle
Exécutez maintenant la commande suivante pour créer votre machine virtuelle à partir de l’image de machine virtuelle capturée. Utilisez le paramètre **-f** pour spécifier le chemin d’accès au modèle de fichier JSON enregistré.

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

Dans la sortie de commande, vous êtes invité à fournir un nouveau nom de machine virtuelle, le nom d’utilisateur administrateur et un mot de passe et l’ID de la carte d’interface réseau que vous avez créée précédemment.

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

L’exemple suivant montre ce que vous voyez en cas de déploiement réussi :

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>Vérifier le déploiement
Maintenant, exécutez le SSH sur la machine virtuelle que vous avez créée pour vérifier le déploiement et lancez le déploiement et le démarrage de l’utilisation de la nouvelle machine virtuelle. Pour vous connecter via SSH, recherchez l’adresse IP de la machine virtuelle que vous avez créée en exécutant la commande suivante :

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

L’adresse IP publique est répertoriée dans la sortie de commande. Par défaut, vous vous connectez à la VM Linux par SSH sur le port 22.

## <a name="create-additional-vms"></a>Créer des machines virtuelles supplémentaires
Utilisez le modèle et l’image capturée pour déployer des machines virtuelles supplémentaires à l’aide de la procédure de la section précédente. Les autres options de création de machines virtuelles à partir de l’image incluent l’utilisation d’un modèle de démarrage rapide ou l’exécution de la commande **azure vm create**.

### <a name="use-the-captured-template"></a>Utiliser le modèle capturé
Pour utiliser l’image capturée et le modèle, suivez cette procédure (détaillée dans la section précédente) :

* Assurez-vous que votre image de machine virtuelle se trouve dans le compte de stockage qui héberge le disque dur virtuel de votre machine virtuelle.
* Copiez le modèle de fichier JSON et spécifiez un nom unique pour le disque du système d’exploitation du (ou des) disque(s) dur(s) virtuel(s) de la nouvelle machine virtuelle. Par exemple, dans **storageProfile**, sous **vhd**, dans **uri**, spécifiez un nom unique pour le disque dur virtuel **osDisk**, similaire à `https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
* Créez une carte d’interface réseau dans un réseau virtuel identique ou différent.
* À l’aide du modèle de fichier JSON modifié, créez un déploiement dans le groupe de ressources dans lequel vous configurez le réseau virtuel.

### <a name="use-a-quickstart-template"></a>Utilisation d’un modèle de démarrage rapide
Si vous souhaitez que le réseau soit automatiquement configuré lorsque vous créez une machine virtuelle à partir de l’image, vous pouvez spécifier ces ressources dans un modèle. Par exemple, consultez le [modèle 101-vm-from-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) à partir de GitHub. Ce modèle crée un ordinateur virtuel à partir de votre image personnalisée et des ressources virtuelles réseau nécessaires, de l’adresse IP publique et des ressources de carte réseau. Pour une démonstration de l’utilisation du modèle dans le portail Azure, consultez [Comment créer une machine virtuelle à partir d’une image personnalisée à l’aide d’un modèle ARM](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/)(en anglais).

### <a name="use-the-azure-vm-create-command"></a>Utilisation de la commande azure vm create
En général, il est plus facile d’utiliser un modèle Resource Manager pour créer une machine virtuelle à partir de l’image. Toutefois, vous pouvez créer la machine virtuelle de façon *impérative* à l’aide de la commande **azure vm create** avec le paramètre **-Q** (**--image-urn**). Si vous utilisez cette méthode, vous devez également transmettre le paramètre **-d** (**--os-disk-vhd**) pour spécifier l’emplacement du fichier .vhd du système d’exploitation pour la nouvelle machine virtuelle. Ce fichier doit se trouver dans le conteneur de disques durs virtuels du compte de stockage sur lequel se trouve le fichier d’image du disque dur virtuel. La commande copie le disque dur virtuel pour la nouvelle machine virtuelle automatiquement sur le conteneur de disques durs virtuels **vhds**.

Avant d’exécuter **azure vm create** avec l’image, procédez comme suit :

1. Créez un groupe de ressources ou identifiez un groupe de ressources existant pour le déploiement.
2. Créez une ressource d’adresse IP publique et une ressource de la carte réseau pour la nouvelle machine virtuelle. Pour savoir comment créer un réseau virtuel, une adresse IP publique et une carte réseau à l’aide de l’interface CLI, voir plus haut dans cet article. (**azure vm create** peut également créer une carte d’interface réseau, mais vous devez transmettre des paramètres supplémentaires pour un réseau virtuel et un sous-réseau.)

Ensuite, exécutez une commande transmettant des URI vers le nouveau fichier de disque dur virtuel de système d’exploitation et l’image existante. Dans cet exemple, une machine virtuelle de taille Standard_A1 est créée dans la région Est des États-Unis.

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

Pour obtenir d’autres options de commande supplémentaires, exécutez `azure help vm create`.

## <a name="next-steps"></a>Étapes suivantes
Pour gérer vos machines virtuelles à l’aide de l’interface de ligne de commande, consultez les tâches décrites dans [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](create-ssh-secured-vm-from-template.md).

