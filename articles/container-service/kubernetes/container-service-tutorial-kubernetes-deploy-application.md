---
title: Didacticiel Azure Container Service - Déployer une application
description: Didacticiel Azure Container Service - Déployer une application
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7119976ada00e10ebeadf6fcff2daf125f439c17
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="run-applications-in-kubernetes"></a>Exécuter des applications dans Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Dans ce didacticiel (le quatrième d’une série de sept), un exemple d’application est déployé dans un cluster Kubernetes. Les étapes effectuées sont les suivantes :

> [!div class="checklist"]
> * Mise à jour des fichiers manifeste Kubernetes
> * Exécuter une application dans Kubernetes
> * Test de l'application

Dans les didacticiels suivants, la taille des instances de cette application est augmentée, l’application est mise à jour et Log Analytics est configuré pour la surveillance du cluster Kubernetes.

Ce didacticiel suppose une compréhension élémentaire des concepts de Kubernetes. Pour en savoir plus, consultez la [documentation Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. 

Pour effectuer ce didacticiel, vous avez besoin du fichier manifeste Kubernetes `azure-vote-all-in-one-redis.yml`. Ce fichier a été téléchargé avec le code source de l’application dans un didacticiel précédent. Vérifiez que vous avez cloné le référentiel et que vous avez modifié des répertoires dans le référentiel cloné.

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Mettre à jour le fichier manifeste

Dans ce didacticiel, Azure Container Registry (ACR) a été utilisé pour stocker une image conteneur. Avant d’exécuter l’application, le nom de serveur de connexion ACR doit être mis à jour dans le fichier manifeste Kubernetes.

Obtenez le nom du serveur de connexion ACR à l’aide de la commande [az acr list](/cli/azure/acr#az_acr_list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Le fichier manifeste a été précréé avec le nom de serveur de connexion `microsoft`. Ouvrez le fichier avec un éditeur de texte. Dans cet exemple, le fichier est ouvert avec `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Remplacez `microsoft` par le nom de serveur de connexion ACR. Cette valeur se trouve sur la ligne **47** du fichier manifeste.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Enregistrez et fermez le fichier.

## <a name="deploy-application"></a>Déployer l’application

Utilisez la commande [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) pour exécuter l’application. Cette commande analyse le fichier manifeste et crée les objets Kubernetes définis.

```azurecli-interactive
kubectl create -f azure-vote-all-in-one-redis.yml
```

Output:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Tester l’application

Un [service Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) est créé et expose l’application à Internet. Ce processus peut prendre plusieurs minutes. 

Pour surveiller la progression, utilisez la commande [kubectl get service](https://review.docs.microsoft.com/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) avec l’argument `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Au début, l’**adresse IP externe** pour le service `azure-vote-front` apparaît comme `pending`. Une fois que l’adresse IP externe est passée du statut `pending` à `IP address`, utilisez `CTRL-C` pour arrêter le processus de surveillance kubectl.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Pour afficher l’application, accédez à l’adresse IP externe.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, l’application Azure Vote a été déployée sur un cluster Kubernetes Azure Container Service. Les tâches accomplies sont les suivantes :  

> [!div class="checklist"]
> * Téléchargement des fichiers manifeste Kubernetes
> * Exécution de l’application dans Kubernetes
> * Test de l’application

Passez au didacticiel suivant pour en savoir plus sur la mise à l’échelle d’une application Kubernetes et de l’infrastructure Kubernetes sous-jacente. 

> [!div class="nextstepaction"]
> [Mettre à l’échelle l’application et l’infrastructure Kubernetes](./container-service-tutorial-kubernetes-scale.md)