---
title: Didacticiel Azure Container Instances - Préparer Azure Container Registry
description: Didacticiel Azure Container Instances (partie 2 sur 3) - Préparer Azure Container Registry
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6e5a6a64e6d7c53bb4ea84de646812c962469d4f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Didacticiel : Déployer et utiliser Azure Container Registry

Il s’agit de la deuxième partie d’un didacticiel en trois parties. Dans la [première partie](container-instances-tutorial-prepare-app.md) du didacticiel, vous avez créé une image de conteneur Docker pour une application web Node.js. Dans ce didacticiel, vous envoyez cette image à Azure Container Registry. Si vous n’avez pas encore créé l’image de conteneur, retournez au [Didacticiel 1 : Créer une image conteneur](container-instances-tutorial-prepare-app.md).

Azure Container Registry est votre registre Docker privé dans Azure. Dans ce didacticiel, vous créez une instance Azure Container Registry dans votre abonnement, puis envoyez l’image de conteneur créée précédemment à cette instance. Dans ce deuxième article de la série, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une instance Azure Container Registry
> * Baliser une image conteneur pour votre Azure Container Registry
> * Charger l’image dans votre registre

Dans ce dernier article de la série, vous allez déployer le conteneur de votre registre privé vers Azure Container Instances.

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="create-azure-container-registry"></a>Créer un registre de conteneurs Azure

Avant de créer votre registre de conteneurs, vous avez besoin d’un *groupe de ressources* vers lequel le déployer. Un groupe de ressources est une collection logique dans laquelle toutes les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create][az-group-create]. Dans l’exemple suivant, un groupe de ressources nommé *myResourceGroup* est créé dans la région *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Une fois que vous avez créé le groupe de ressources, créez un registre de conteneurs Azure à l’aide de la commande [az acr create][az-acr-create]. Le nom du registre de conteneurs doit être unique dans Azure et contenir de 5 à 50 caractères alphanumériques. Remplacez `<acrName>` par un nom unique pour votre registre :

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

Voici un exemple de sortie pour un nouveau registre de conteneurs Azure nommé *mycontainerregistry082* (tronquée ici) :

```console
$ az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
...
{
  "adminUserEnabled": true,
  "creationDate": "2018-03-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
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

Dans le reste de ce didacticiel, `<acrName>` désigne l’espace réservé du nom du registre des conteneurs que vous choisissez dans cette étape.

## <a name="log-in-to-container-registry"></a>Se connecter au registre de conteneurs

Vous devez vous connecter à votre instance Azure Container Registry avant de lui envoyer des images. Utilisez la commande [az acr login][az-acr-login] pour terminer l’opération. Vous devez fournir le nom unique que vous avez choisi pour le registre de conteneurs au moment de sa création.

```azurecli
az acr login --name <acrName>
```

Une fois l’opération terminée, la commande renvoie `Login Succeeded` :

```console
$ az acr login --name mycontainerregistry082
Login Succeeded
```

## <a name="tag-container-image"></a>Baliser l’image de conteneur

Pour envoyer une image de conteneur sur un registre privé comme Azure Container Registry, vous devez d’abord étiqueter l’image avec le nom complet du serveur de connexion du registre.

Tout d’abord, obtenez le nom complet du serveur de connexion pour votre registre de conteneurs Azure. Exécutez la commande [az acr show][az-acr-show] suivante et remplacez `<acrName>` par le nom du registre que vous venez de créer :

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Par exemple, si votre registre est nommé *mycontainerregistry082* :

```console
$ az acr show --name mycontainerregistry082 --query loginServer --output table
Result
------------------------
mycontainerregistry082.azurecr.io
```

Maintenant, affichez la liste de vos images locales avec la commande [images Docker][docker-images] :

```bash
docker images
```

Vous devriez voir l’image *aci-didacticiel-app* que vous avez créé dans le [didacticiel précédent](container-instances-tutorial-prepare-app.md) en même temps que toutes les autres images vous avez sur votre ordinateur :

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Balisez l’image *aci-tutorial-app* à l’aide du loginServer de votre registre de conteneurs. Ajoutez également la balise `:v1` à la fin du nom d’image pour indiquer le numéro de version de l’image. Remplacez `<acrLoginServer>` par le résultat de la commande [az acr show][az-acr-show] que vous avez exécuté précédemment.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Exécutez `docker images` de nouveau pour vérifier l’opération de marquage :

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Envoyer l’image à Azure Container Registry

Maintenant que vous avez étiqueté l’image *aci-tutorial-app* avec le nom complet du serveur de connexion de votre registre privé, vous pouvez l’envoyer au registre avec la commande [docker push][docker-push]. Remplacez `<acrLoginServer>` par le nom complet du serveur de connexion que vous avez obtenu à l’étape précédente.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

L’opération `push` peut prendre de quelques secondes à quelques minutes selon votre connexion Internet, et la sortie est semblable à ce qui suit :

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Énumérer les images dans Azure Container Registry

Pour vérifier que l’image que vous venez d’envoyer est en effet dans le registre de conteneurs Azure, listez les images dans votre registre avec la commande [az acr repository list][az-acr-repository-list]. Remplacez `<acrName>` par le nom de votre registre de conteneurs.

```azurecli
az acr repository list --name <acrName> --output table
```

Par exemple : 

```console
$ az acr repository list --name mycontainerregistry082 --output table
Result
----------------
aci-tutorial-app
```

Pour afficher les *balises* d’une image spécifique, utilisez la commande [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Le résultat ressemble à ce qui suit :

```console
$ az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez préparé un registre de conteneurs Azure pour une utilisation avec Azure Container Instances, et envoyé une image conteneur vers le registre. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déploiement d’une instance Azure Container Registry
> * Marquage d’une image conteneur pour Azure Container Registry
> * Chargement d’une image dans Azure Container Registry

Passez au didacticiel suivant pour découvrir comment déployer le conteneur sur Azure à l’aide d’Azure Container Instances :

> [!div class="nextstepaction"]
> [Déployer un conteneur sur Azure Container Instances](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
