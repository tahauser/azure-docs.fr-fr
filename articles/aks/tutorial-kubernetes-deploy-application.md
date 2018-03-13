---
title: "Didacticiel Kubernetes sur Azure - Déployer une application"
description: "Didacticiel ACS - Déployer une application"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0639a2b7e71878103542d3e037040f8a7444976f
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="run-applications-in-azure-container-service-aks"></a>Exécuter des applications dans Azure Container Service (ACS)

Dans ce didacticiel (quatrième d’une série de huit), un exemple d’application est déployé dans un cluster Kubernetes. Les étapes effectuées sont les suivantes :

> [!div class="checklist"]
> * Mise à jour des fichiers manifeste Kubernetes
> * Exécuter une application dans Kubernetes
> * Test de l'application

Dans les didacticiels suivants, cette application est mise à l’échelle et Operations Management Suite est configuré pour la surveillance du cluster Kubernetes.

Ce didacticiel suppose une compréhension élémentaire des concepts de Kubernetes. Pour en savoir plus, consultez la [documentation Kubernetes][kubernetes-documentation].

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. 

Pour effectuer ce didacticiel, vous avez besoin du fichier manifeste Kubernetes `azure-vote-all-in-one-redis.yaml`. Ce fichier a été téléchargé avec le code source de l’application dans un didacticiel précédent. Vérifiez que vous avez cloné le référentiel et que vous avez modifié des répertoires dans le référentiel cloné.

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur][aks-tutorial-prepare-app].

## <a name="update-manifest-file"></a>Mettre à jour le fichier manifeste

Dans ce didacticiel, Azure Container Registry (ACR) a été utilisé pour stocker une image conteneur. Avant d’exécuter l’application, le nom de serveur de connexion ACR doit être mis à jour dans le fichier manifeste Kubernetes.

Obtenez le nom du serveur de connexion ACR à l’aide de la commande [az acr list][az-acr-list].

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Le fichier manifeste a été précréé avec le nom de serveur de connexion `microsoft`. Ouvrez le fichier avec un éditeur de texte. Dans cet exemple, le fichier est ouvert avec `vi`.

```console
vi azure-vote-all-in-one-redis.yaml
```

Vous pouvez également, si vous travaillez sur Windows, utiliser Visual Studio Code.

```console
code azure-vote-all-in-one-redis.yaml
```

Remplacez `microsoft` par le nom de serveur de connexion ACR. Cette valeur se trouve sur la ligne **47** du fichier manifeste.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Le code ci-dessus devient alors.

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Enregistrez et fermez le fichier.

## <a name="deploy-application"></a>Déployer l’application

Utilisez la commande [kubectl create][kubectl-create] pour exécuter l’application. Cette commande analyse le fichier manifeste et crée les objets Kubernetes définis.

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yaml
```

Output:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Tester l’application

Un [service Kubernetes][kubernetes-service] est créé et expose l’application à Internet. Ce processus peut prendre plusieurs minutes. 

Pour surveiller la progression, utilisez la commande [kubectl get service][kubectl-get] avec l’argument `--watch`.

```azurecli
kubectl get service azure-vote-front --watch
```

Au début, *EXTERNAL-IP* pour le service *azure-vote-front* apparaît *En attente*.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Une fois que l’adresse *EXTERNAL-IP* est passée du statut *En attente* à *Adresse IP*, utilisez `CTRL-C` pour arrêter le processus de surveillance kubectl. 

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Pour afficher l’application, accédez à l’adresse IP externe.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Si l’application ne s’est pas chargée, il y a peut-être un problème d’autorisation avec votre registre d’images.

Suivez ces étapes pour [autoriser l’accès via un secret Kubernetes](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, l’application de vote Azure a été déployée sur un cluster Kubernetes dans ACS. Les tâches accomplies sont les suivantes :  

> [!div class="checklist"]
> * Téléchargement des fichiers manifeste Kubernetes
> * Exécution de l’application dans Kubernetes
> * Test de l’application

Passez au didacticiel suivant pour en savoir plus sur la mise à l’échelle d’une application Kubernetes et de l’infrastructure Kubernetes sous-jacente. 

> [!div class="nextstepaction"]
> [Mettre à l’échelle l’application et l’infrastructure Kubernetes][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
