---
title: "Démarrage rapide - Créer un registre Docker privé dans Azure avec l’interface de ligne de commande Azure"
description: "Apprenez rapidement à créer un registre de conteneurs Docker privé avec l’interface de ligne de commande Azure."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 12/07/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: a74a1ce5c9401d6445f5feec4af8d5cb771d2c64
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Créer un registre de conteneur à l’aide de l’interface de ligne de commande Azure

Azure Container Registry est un service de registre de conteneurs Docker géré utilisé pour stocker des images de conteneurs Docker privés. Ce guide décrit en détail la création d’une instance Azure Container Registry à l’aide de l’interface de ligne de commande Azure.

Ce guide de démarrage rapide nécessite d’exécuter Azure CLI 2.0.25 ou ultérieur. Exécutez `az --version` pour trouver la version. Si vous devez procéder à une installation ou une mise à niveau, consultez [Installation d’Azure CLI 2.0][azure-cli].

Docker doit également être installé en local. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create][az-group-create]. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Dans le cadre de ce guide de démarrage rapide, nous allons créer un registre *De base*. Azure Container Registry est disponible dans plusieurs références SKU, qui sont brièvement décrites dans le tableau suivant. Pour plus d’informations sur chaque référence, consultez [Références SKU de registres de conteneurs][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Créez une instance ACR à l’aide de la commande [az acr create][az-acr-create].

Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. Dans l’exemple suivant, nous utilisons le nom *myContainerRegistry007*. Mettez à jour le nom de façon à utiliser une valeur unique.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
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
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Dans le reste de ce guide de démarrage rapide, nous utilisons `<acrName>` comme espace réservé pour le nom de registre de conteneurs.

## <a name="log-in-to-acr"></a>Se connecter à l’ACR

Avant d’extraire et de transmettre des images conteneur, vous devez vous connecter à l’instance ACR. Pour ce faire, utilisez la commande [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Une fois l’opération terminée, la commande retourne un message `Login Succeeded`.

## <a name="push-image-to-acr"></a>Envoyer une image dans l’ACR

Pour envoyer une image dans un registre Azure Container Registry, vous devez tout d’abord disposer d’une image. Si vous n’avez pas encore toutes les images du conteneur local, exécutez la commande suivante pour tirer (pull) une image existante du hub Docker.

```bash
docker pull microsoft/aci-helloworld
```

Avant de pousser (push) une image vers le registre, vous devez la marquer avec le nom complet de votre serveur de connexion ACR. Exécutez la commande suivante pour obtenir le nom complet du serveur de connexion de l’instance ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Marquez l’image en utilisant la commande [docker tag][docker-tag]. Remplacez `<acrLoginServer>` par le nom du serveur de connexion de votre instance ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Enfin, utilisez la commande [docker push][docker-push] pour pousser l’image vers l’instance ACR. Remplacez `<acrLoginServer>` par le nom du serveur de connexion de votre instance ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Répertorier les images conteneur

L’exemple suivant répertorie les référentiels d’un registre :

```azurecli
az acr repository list --name <acrName> --output table
```

Sortie :

```bash
Result
----------------
aci-helloworld
```

L’exemple suivant répertorie les étiquettes sur le référentiel **aci-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Sortie :

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources, l’instance ACR et toutes les images conteneurs.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un registre Azure Container Registry avec Azure CLI. Si vous souhaitez utiliser Azure Container Registry avec des instances Azure Container Instances, passez au didacticiel Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances][container-instances-tutorial-prepare-app]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md