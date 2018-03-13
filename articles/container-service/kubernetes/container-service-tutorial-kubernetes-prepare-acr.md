---
title: "Didacticiel Azure Container Service - Préparer l’ACR"
description: "Didacticiel Azure Container Service - Préparer l’ACR"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f2520c79d5d5b1dcf13147b64bd98df78e6e2f37
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Déployer et utiliser Azure Container Registry

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Azure Container Registry (ACR) est un registre privé Azure pour les images de conteneur Docker. Ce didacticiel (le deuxième d’une série de sept) vous aide à déployer une instance Azure Container Registry et à envoyer une image conteneur à ce dernier. Les étapes effectuées sont les suivantes :

> [!div class="checklist"]
> * Déploiement d’une instance Azure Container Registry (ACR)
> * Marquage d’une image conteneur pour ACR
> * Chargement de l’image dans ACR

Dans les didacticiels suivants, cette instance ACR est intégrée à un cluster Azure Container Service Kubernetes. 

## <a name="before-you-begin"></a>Avant de commencer

Dans le [didacticiel précédent](./container-service-tutorial-kubernetes-prepare-app.md), une image conteneur a été créée pour une application Azure Vote. Si vous n’avez pas créé l’image de l’application Azure Vote, retournez au [Didacticiel 1 : Créer des images conteneur](./container-service-tutorial-kubernetes-prepare-app.md).

Ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Déployer Azure Container Registry

Lorsque vous déployez un registre de conteneurs Azure, il vous faut tout d’abord un groupe de ressources. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Dans cet exemple, un groupe de ressources nommé `myResourceGroup` est créé dans la région `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Créez un registre de conteneurs Azure à l’aide de la commande [az acr create](/cli/azure/acr#az_acr_create). Le nom d’un registre de conteneurs **doit être unique**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Dans le reste de ce didacticiel, nous utilisons `<acrname>` comme espace réservé pour le nom de registre de conteneurs.

## <a name="container-registry-login"></a>Connexion au registre de conteneurs

Utilisez la commande [az acr login](https://docs.microsoft.com/cli/azure/acr#az_acr_login) pour vous connecter à l’instance ACR. Vous devez fournir le nom unique qui a été donné au Registre de conteneurs au moment de sa création.

```azurecli
az acr login --name <acrName>
```

Après son exécution, la commande retourne le message « Connexion réussie ».

## <a name="tag-container-images"></a>Marquer les images de conteneur

Pour afficher la liste des images actuelles, utilisez la commande [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Chaque image conteneur doit être marquée avec le nom de serveur de connexion du registre. Cette balise est utilisée pour l’acheminement lors de l’envoi des images de conteneur dans un registre d’images.

Pour obtenir le nom de loginServer, exécutez la commande suivante.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Maintenant, marquez l’image `azure-vote-front` avec le loginServer du registre de conteneurs. En outre, ajoutez `:v1` à la fin du nom de l’image. Cette balise indique la version de l’image.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Une fois l’image marquée, exécutez [docker images] (https://docs.docker.com/engine/reference/commandline/images/) pour vérifier l’opération.

```bash
docker images
```

Output:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Envoyer des images au registre

Envoyez l’image `azure-vote-front` vers votre registre. 

À l’aide de l’exemple suivant, remplacez le nom d’ACR loginServer par le loginServer à partir de votre environnement.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Quelques minutes sont nécessaires pour achever l’opération.

## <a name="list-images-in-registry"></a>Créer la liste des images du registre

Pour retourner une liste d’images qui ont été déplacées dans le registre de conteneurs Azure, utilisez la commande [az acr repository list](/cli/azure/acr/repository#az_acr_repository_list). Mettez à jour la commande avec le nom d’instance ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```azurecli
Result
----------------
azure-vote-front
```

Puis, pour afficher les balises d’une image spécifique, utilisez la commande [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Output:

```azurecli
Result
--------
v1
```

Au terme de ce didacticiel, l’image conteneur est stockée dans une instance privée Azure Container Registry. Dans les didacticiels suivants, cette image est déployée à partir d’ACR vers un cluster Kubernetes.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, un registre de conteneurs Azure a été préparé pour une utilisation dans un cluster ACS Kubernetes. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déploiement d’une instance Azure Container Registry
> * Marquage d’une image conteneur pour ACR
> * Chargement de l’image dans ACR

Passer à l’étape suivante du didacticiel pour en savoir plus sur le déploiement d’un cluster Kubernetes dans Azure.

> [!div class="nextstepaction"]
> [Déployer un cluster Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md)