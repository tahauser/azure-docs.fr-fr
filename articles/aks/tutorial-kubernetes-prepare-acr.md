---
title: "Didacticiel Kubernetes sur Azure - Préparer un enregistrement de contrôle d’accès"
description: "Didacticiel ACS - Préparer Azure Container Registry"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b676898cca7e5c80d6ba872e76a9effdb852bfff
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Déployer et utiliser Azure Container Registry

Azure Container Registry (ACR) est un registre privé Azure pour les images de conteneur Docker. Ce didacticiel (deuxième d’une série de huit) vous aide à déployer une instance Azure Container Registry et à envoyer une image conteneur à ce dernier. Les étapes effectuées sont les suivantes :

> [!div class="checklist"]
> * Déploiement d’une instance Azure Container Registry (ACR)
> * Marquage d’une image conteneur pour ACR
> * Chargement de l’image dans ACR

Dans les didacticiels suivants, cette instance ACR est intégrée avec un cluster Kubernetes dans ACS.

## <a name="before-you-begin"></a>Avant de commencer

Dans le [didacticiel précédent][aks-tutorial-prepare-app], une image conteneur a été créée pour une application Azure Vote. Si vous n’avez pas créé l’image de l’application Azure Vote, retournez au [Didacticiel 1 : Créer des images conteneur][aks-tutorial-prepare-app].

Ce didacticiel nécessite l’exécution d’Azure CLI 2.0.27 ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="deploy-azure-container-registry"></a>Déployer Azure Container Registry

Lorsque vous déployez un registre de conteneurs Azure, il vous faut tout d’abord un groupe de ressources. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create][az-group-create]. Dans cet exemple, un groupe de ressources nommé `myResourceGroup` est créé dans la région `eastus`.

```azurecli
az group create --name myResourceGroup --location eastus
```

Créez un registre de conteneurs Azure à l’aide de la commande [az acr create][az-acr-create]. Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Dans le reste de ce didacticiel, nous utilisons `<acrName>` comme espace réservé pour le nom de registre de conteneurs.

## <a name="container-registry-login"></a>Connexion au registre de conteneurs

Utilisez la commande [az acr login][az-acr-login] pour vous connecter à l’instance ACR. Vous devez fournir le nom unique qui a été donné au Registre de conteneurs au moment de sa création.

```azurecli
az acr login --name <acrName>
```

Après son exécution, la commande retourne le message « Connexion réussie ».

## <a name="tag-container-images"></a>Marquer les images de conteneur

Pour afficher la liste des images actuelles, utilisez la commande [docker images][docker-images].

```console
docker images
```

Output:

```
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

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Une fois le marquage effectué, exécutez [docker images][docker-images] pour vérifier l’opération.

```console
docker images
```

Output:

```
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Envoyer des images au registre

Envoyez l’image `azure-vote-front` vers votre registre.

À l’aide de l’exemple suivant, remplacez le nom d’ACR loginServer par le loginServer à partir de votre environnement.

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Quelques minutes sont nécessaires pour achever l’opération.

## <a name="list-images-in-registry"></a>Créer la liste des images du registre

Pour retourner une liste d’images qui ont été déplacées dans le registre de conteneurs Azure, utilisez la commande [az acr repository list][az-acr-repository-list]. Mettez à jour la commande avec le nom d’instance ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```azurecli
Result
----------------
azure-vote-front
```

Puis, pour afficher les balises d’une image spécifique, utilisez la commande [az acr repository show-tags][az-acr-repository-show-tags].

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

Dans ce didacticiel, un Azure Container Registry a été préparé pour une utilisation dans un cluster ACS. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déploiement d’une instance Azure Container Registry
> * Marquage d’une image conteneur pour ACR
> * Chargement de l’image dans ACR

Passer à l’étape suivante du didacticiel pour en savoir plus sur le déploiement d’un cluster Kubernetes dans Azure.

> [!div class="nextstepaction"]
> [Déployer un cluster Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#create
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#show-tags
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md