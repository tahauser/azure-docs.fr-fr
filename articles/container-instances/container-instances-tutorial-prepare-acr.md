---
title: "Didacticiel Azure Container Instances - Préparer Azure Container Registry"
description: "Didacticiel Azure Container Instances - Préparer Azure Container Registry"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 6e69b7a3540ce90743f9dd75664b118751f4a63b
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Déployer et utiliser Azure Container Registry

Il s’agit de la deuxième partie d’un didacticiel en trois parties. À l’[étape précédente](container-instances-tutorial-prepare-app.md), nous avons créé une image conteneur pour une application web simple écrite en [Node.js](http://nodejs.org). Dans ce didacticiel, vous envoyez cette image à Azure Container Registry. Si vous n’avez pas créé l’image de conteneur, retournez au [Didacticiel 1 : Créer une image conteneur](container-instances-tutorial-prepare-app.md).

Azure Container Registry est un registre privé Azure pour les images conteneur Docker. Ce didacticiel explique comment déployer une instance Azure Container Registry et lui envoyer une image conteneur. Les étapes accomplies sont les suivantes :

> [!div class="checklist"]
> * Déploiement d’une instance Azure Container Registry
> * Balisage d’image conteneur pour Azure Container Registry
> * Chargement d’image dans Azure Container Registry

Dans les didacticiels suivants vous déploierez le conteneur de votre Registre privé vers Azure Container Instances.

## <a name="before-you-begin"></a>Avant de commencer

Ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.21 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli).

Pour terminer ce didacticiel, il vous faut un environnement de développement Docker. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell n’inclut pas les composants Docker requis pour effectuer chaque étape de ce didacticiel. Par conséquent, nous recommandons une installation locale de l’interface Azure CLI et de l’environnement de développement Docker.

## <a name="deploy-azure-container-registry"></a>Déployer Azure Container Registry

Lorsque vous déployez un registre de conteneurs Azure, il vous faut tout d’abord un groupe de ressources. Un groupe de ressources Azure est une collection logique dans laquelle des ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Dans cet exemple, un groupe de ressources nommé *myResourceGroupVM* est créé dans la région *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Créez un registre de conteneurs Azure à l’aide de la commande [az acr create](/cli/azure/acr#create). Le nom du registre de conteneurs **doit être unique** dans Azure et doit contenir de 5 à 50 caractères alphanumériques. Remplacez `<acrName>` par un nom unique pour votre registre :

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Par exemple, pour créer un registre de conteneurs Azure nommé *mycontainerregistry082* :

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

Dans le reste de ce didacticiel, nous utilisons `<acrName>`. Ce nom correspond au registre de conteneurs que vous avez choisi.

## <a name="container-registry-login"></a>Connexion au registre de conteneurs

Vous devez vous connecter à votre instance ACR avant de lui envoyer des images. Utilisez la commande [az acr login](/cli/azure/acr#az_acr_login) pour terminer l’opération. Vous devez fournir le nom unique qui a été donné au registre de conteneurs au moment de sa création.

```azurecli
az acr login --name <acrName>
```

Une fois l’opération terminée, la commande retourne un message `Login Succeeded`.

## <a name="tag-container-image"></a>Baliser l’image de conteneur

Pour pouvoir déployer une image conteneur à partir d’un registre privé, vous devez la baliser avec le `loginServer` nom du registre.

Pour afficher une liste des images en cours, utilisez la commande `docker images`.

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Pour obtenir le nom de loginServer, exécutez la commande suivante. Remplacez `<acrName>` par le nom de votre registre de conteneurs.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Exemple de sortie :

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Balisez l’image *aci-tutorial-app* à l’aide du loginServer de votre registre de conteneurs. En outre, ajoutez `:v1` à la fin du nom de l’image. Cette balise indique le numéro de version de l’image. Remplacez `<acrLoginServer>` par le résultat de la commande `az acr show` que vous venez d’exécuter.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Une fois marqué, exécutez `docker images` pour vérifier l’opération.

```bash
docker images
```

Output:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Envoyer l’image à Azure Container Registry

Envoyez l’image *aci-tutorial-app* vers le registre à l’aide de la commande `docker push`. Remplacez `<acrLoginServer>` par le nom complet du serveur de connexion que vous obtenez à l’étape précédente.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

L’opération `push` doit prendre quelques secondes à quelques minutes selon votre connexion Internet, et la sortie est semblable à ce qui suit :

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

Pour retourner une liste d’images qui ont été déplacées dans le registre de conteneurs Azure, utilisez la commande [az acr repository list](/cli/azure/acr/repository#list). Mettez à jour la commande avec le nom du registre de conteneurs.

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```azurecli
Result
----------------
aci-tutorial-app
```

Puis, pour afficher les balises d’une image spécifique, utilisez la commande [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Output:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons préparé un Azure Container Registry pour une utilisation avec Azure Container Instances, et nous avons envoyé l’image conteneur. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déploiement d’une instance Azure Container Registry
> * Marquage d’une image conteneur pour Azure Container Registry
> * Chargement d’une image dans Azure Container Registry

Passez au didacticiel suivant pour en savoir plus sur le déploiement du conteneur sur Azure à l’aide d’Azure Container Instances.

> [!div class="nextstepaction"]
> [Déployer des conteneurs sur Azure Container Instances](./container-instances-tutorial-deploy-app.md)
