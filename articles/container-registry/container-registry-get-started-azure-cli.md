---
title: "Démarrage rapide - Créer un registre Docker privé dans Azure avec l’interface de ligne de commande Azure"
description: "Apprenez rapidement à créer un registre de conteneurs Docker privé avec l’interface de ligne de commande Azure."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quicksart
ms.date: 10/16/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 5cddf0ffea256ed6d1c51d48a61ac8176d08b9cc
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Créer un registre de conteneur à l’aide de l’interface de ligne de commande Azure

Azure Container Registry est un service de registre de conteneurs Docker géré utilisé pour stocker des images de conteneurs Docker privés. Ce guide décrit en détail la création d’une instance Azure Container Registry à l’aide de l’interface de ligne de commande Azure.

Ce guide de démarrage rapide nécessite que vous exécutiez l’interface de ligne de commande Azure version 2.0.20 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli).

Docker doit également être installé en local. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Dans le cadre de ce guide de démarrage rapide, nous allons créer un registre *De base*. Azure Container Registry est disponible dans plusieurs références SKU, qui sont brièvement décrites dans le tableau suivant. Pour plus d’informations sur chaque référence, consultez [Références SKU de registres de conteneurs](container-registry-skus.md).

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Créez une instance ACR à l’aide de la commande [az acr create](/cli/azure/acr#create).

Le nom du registre **doit être unique**. Dans l’exemple suivant, nous utilisons le nom *myContainerRegistry007*. Mettez à jour le nom de façon à utiliser une valeur unique.

```azurecli
az acr create --name myContainerRegistry007 --resource-group myResourceGroup --sku Basic
```

Une fois le registre créé, la sortie ressemble à ce qui suit :

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr223140"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Dans le reste de ce guide de démarrage rapide, nous utilisons `<acrname>` comme espace réservé pour le nom de registre de conteneurs.

## <a name="log-in-to-acr"></a>Se connecter à l’ACR

Avant d’extraire et de transmettre des images conteneur, vous devez vous connecter à l’instance ACR. Pour ce faire, utilisez la commande [az acr login](/cli/azure/acr#login).

```azurecli
az acr login --name <acrname>
```

Après son exécution, la commande retourne le message « Connexion réussie ».

## <a name="push-image-to-acr"></a>Envoyer une image dans l’ACR

Pour envoyer une image dans un registre Azure Container Registry, vous devez tout d’abord disposer d’une image. Si nécessaire, exécutez la commande suivante pour extraire une image créée préalablement du hub Docker.

```bash
docker pull microsoft/aci-helloworld
```

L’image doit être étiquetée avec le nom du serveur de connexion ACR. Exécutez la commande suivante pour retourner le nom du serveur de connexion de l’instance ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Étiquetez l’image en utilisant la commande [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Remplacez *<acrLoginServer>* par le nom du serveur de connexion de votre instance ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Enfin, utilisez la commande [docker push](https://docs.docker.com/engine/reference/commandline/push/) pour envoyer l’image à l’instance ACR. Remplacez *<acrLoginServer>* par le nom du serveur de connexion de votre instance ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Répertorier les images conteneur

L’exemple suivant répertorie les référentiels d’un registre :

```azurecli
az acr repository list -n <acrname> -o table
```

Output:

```bash
Result
----------------
aci-helloworld
```

L’exemple suivant répertorie les étiquettes sur le référentiel **aci-helloworld**.

```azurecli
az acr repository show-tags -n <acrname> --repository aci-helloworld -o table
```

Output:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#delete) pour supprimer le groupe de ressources, l’instance ACR et toutes les images conteneurs.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un registre Azure Container Registry avec Azure CLI. Si vous souhaitez utiliser Azure Container Registry avec des instances Azure Container Instances, passez au didacticiel Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)