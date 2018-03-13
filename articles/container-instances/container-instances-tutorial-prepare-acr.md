---
title: "Didacticiel Azure Container Instances - Préparer Azure Container Registry"
description: "Didacticiel Azure Container Instances (partie 2 sur 3) - Préparer Azure Container Registry"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 94ecba44b8281460da4518c146aab814d2eaa850
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Déployer et utiliser Azure Container Registry

Il s’agit de la deuxième partie d’un didacticiel en trois parties. À l’[étape précédente](container-instances-tutorial-prepare-app.md), nous avons créé une image conteneur pour une application web simple écrite dans [Node.js][nodejs]. Dans ce didacticiel, vous envoyez cette image à Azure Container Registry. Si vous n’avez pas créé l’image de conteneur, retournez au [Didacticiel 1 : Créer une image conteneur](container-instances-tutorial-prepare-app.md).

Azure Container Registry est un registre privé Azure pour les images conteneur Docker. Ce didacticiel explique comment déployer une instance Azure Container Registry et lui envoyer une image conteneur.

Dans ce deuxième article de la série, vous allez apprendre à :

> [!div class="checklist"]
> * Déployer une instance Azure Container Registry
> * Baliser une image conteneur pour votre Azure Container Registry
> * Charger l’image dans votre registre

Dans ce dernier article de la série, vous allez déployer le conteneur de votre registre privé vers Azure Container Instances.

## <a name="before-you-begin"></a>Avant de commencer

Ce didacticiel nécessite l’exécution d’Azure CLI 2.0.23 ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez procéder à une installation ou une mise à niveau, consultez [Installation d’Azure CLI 2.0][azure-cli-install].

Pour terminer ce didacticiel, il vous faut un environnement de développement Docker installé localement. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

Azure Cloud Shell n’inclut pas les composants Docker requis pour effectuer chaque étape de ce didacticiel. Vous devez installer Azure CLI et l’environnement de développement Docker sur votre ordinateur local pour pouvoir réaliser les étapes de ce didacticiel.

## <a name="deploy-azure-container-registry"></a>Déployer Azure Container Registry

Lorsque vous déployez un registre de conteneurs Azure, il vous faut tout d’abord un groupe de ressources. Un groupe de ressources Azure est une collection logique dans laquelle des ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create][az-group-create]. Dans cet exemple, un groupe de ressources nommé *myResourceGroupVM* est créé dans la région *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Créez un registre de conteneurs Azure à l’aide de la commande [az acr create][az-acr-create]. Le nom du registre de conteneurs doit être unique dans Azure et contenir de 5 à 50 caractères alphanumériques. Remplacez `<acrName>` par un nom unique pour votre registre :

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Par exemple, pour créer un registre de conteneurs Azure nommé *mycontainerregistry082* :

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

Dans le reste de ce didacticiel, nous utilisons `<acrName>`. Ce nom correspond au registre de conteneurs que vous avez choisi.

## <a name="container-registry-login"></a>Connexion au registre de conteneurs

Vous devez vous connecter à votre instance Azure Container Registry avant de lui envoyer des images. Utilisez la commande [az acr login][az-acr-login] pour terminer l’opération. Vous devez fournir le nom unique que vous avez fourni au registre de conteneurs au moment de sa création.

```azurecli
az acr login --name <acrName>
```

Une fois l’opération terminée, la commande retourne un message `Login Succeeded`.

## <a name="tag-container-image"></a>Baliser l’image de conteneur

Pour pouvoir déployer une image conteneur à partir d’un registre privé, vous devez la baliser avec le nom de `loginServer` du registre.

Pour afficher la liste des images actuelles, utilisez la commande [docker images][docker-images].

```bash
docker images
```

Sortie :

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Pour obtenir le nom de loginServer, exécutez la commande [az acr show][az-acr-show]. Remplacez `<acrName>` par le nom de votre registre de conteneurs.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Exemple de sortie :

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Balisez l’image *aci-tutorial-app* à l’aide du loginServer de votre registre de conteneurs. En outre, ajoutez `:v1` à la fin du nom de l’image. Cette balise indique le numéro de version de l’image. Remplacez `<acrLoginServer>` par le résultat de la commande [az acr show][az-acr-show] que vous venez d’exécuter.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Une fois marqué, exécutez `docker images` pour vérifier l’opération.

```bash
docker images
```

Sortie :

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Envoyer l’image à Azure Container Registry

Envoyez l’image *aci-tutorial-app* vers le registre à l’aide de la commande [docker push][docker-push]. Remplacez `<acrLoginServer>` par le nom complet du serveur de connexion que vous obtenez à l’étape précédente.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

L’opération `push` peut prendre de quelques secondes à quelques minutes selon votre connexion Internet, et la sortie est semblable à ce qui suit :

```bash
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

Pour retourner une liste d’images qui ont été déplacées dans le registre de conteneurs Azure, utilisez la commande [az acr repository list][az-acr-repository-list]. Mettez à jour la commande avec le nom du registre de conteneurs.

```azurecli
az acr repository list --name <acrName> --output table
```

Sortie :

```azurecli
Result
----------------
aci-tutorial-app
```

Puis, pour afficher les balises d’une image spécifique, utilisez la commande [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Sortie :

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez préparé un Azure Container Registry pour une utilisation avec Azure Container Instances, et envoyé l’image conteneur vers le registre. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déploiement d’une instance Azure Container Registry
> * Marquage d’une image conteneur pour Azure Container Registry
> * Chargement d’une image dans Azure Container Registry

Passez au didacticiel suivant pour en savoir plus sur le déploiement du conteneur sur Azure à l’aide d’Azure Container Instances.

> [!div class="nextstepaction"]
> [Déployer des conteneurs sur Azure Container Instances](./container-instances-tutorial-deploy-app.md)

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
