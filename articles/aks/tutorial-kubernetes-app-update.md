---
title: "Didacticiel Kubernetes sur Azure - Mettre à jour une application | Microsoft Docs"
description: "Didacticiel AKS - Mettre à jour une application"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Docker, conteneurs, micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e788a982d2580e90309df977c8e2e1cb22daadaf
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="update-an-application-in-azure-container-service-aks"></a>Mettre à jour une application dans Azure Container Service (AKS)

Après avoir déployé une application dans Kubernetes, vous pouvez la mettre à jour en spécifiant une nouvelle image conteneur ou une nouvelle version de l’image. Cette mise à jour se fait alors étape par étape, afin que seulement une partie du déploiement soit mise à jour simultanément. Cette mise à jour progressive permet à l’application de poursuivre son exécution pendant la mise à jour. Elle fournit également un mécanisme de restauration en cas d’échec du déploiement. 

Dans ce didacticiel (le sixième d’une série de huit), l’exemple de l’application Azure Vote est mis à jour. Les tâches que vous effectuez sont les suivantes :

> [!div class="checklist"]
> * Mise à jour du code de l’application frontale
> * Création d’une image conteneur mise à jour
> * Envoi (push) de l’image conteneur à Azure Container Registry
> * Déploiement de l’image conteneur mise à jour

Dans les didacticiels suivants, Operations Management Suite est configuré pour la surveillance du cluster Kubernetes.

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. L’application a ensuite été exécutée sur le cluster Kubernetes. 

Un référentiel d’application a également été cloné, ce qui inclut le code source de l’application et un fichier Docker Compose précréé utilisé dans ce didacticiel. Vérifiez que vous avez cloné le référentiel et que vous avez modifié des répertoires dans le répertoire cloné. Vous trouverez à l’intérieur un répertoire nommé `azure-vote` et un fichier nommé `docker-compose.yml`.

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Mettre à jour l’application

Pour ce didacticiel, une modification est apportée à l’application et l’application mise à jour est déployée sur le cluster Kubernetes. 

Le code source de l’application se trouve à l’intérieur du répertoire `azure-vote`. Ouvrez le fichier `config_file.cfg` avec un éditeur de code ou de texte. Dans cet exemple, on utilise `vi` .

```console
vi azure-vote/azure-vote/config_file.cfg
```

Modifiez les valeurs de `VOTE1VALUE` et `VOTE2VALUE`, puis enregistrez le fichier.

```console
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Enregistrez et fermez le fichier.

## <a name="update-container-image"></a>Créer une image conteneur

Utilisez [docker-compose](https://docs.docker.com/compose/) pour recréer l’image de l’application frontale et exécuter l’application mise à jour. L’argument `--build` est utilisé pour indiquer à Docker Compose de recréer l’image d’application.

```console
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Tester l’application localement

Accédez à http://localhost:8080 pour afficher l’application en cours d’exécution.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Marquer et envoyer des images

Marquez l’image `azure-vote-front` avec le loginServer du registre de conteneurs. 

Obtenez le nom du serveur de connexion à l’aide de la commande [az acr list](/cli/azure/acr#list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilisez [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) pour ajouter une balise à l’image. Remplacez `<acrLoginServer>` par le nom de votre serveur de connexion Azure Container Registry ou par votre nom d’hôte de registre public. Notez également que la version de l’image est mise à jour à `redis-v2`.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Utilisez [docker push](https://docs.docker.com/engine/reference/commandline/push/) pour charger l’image dans le Registre. Remplacez `<acrLoginServer>` par votre nom de serveur de connexion Azure Container Registry.

```console
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Déployer l’application mise à jour

Pour garantir une disponibilité maximale, vous devez exécuter plusieurs instances du pod d’application. Vérifiez cette configuration avec la commande [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```
kubectl get pod
```

Output:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Si vous n’avez qu’un POD exécutant l’image azure-vote-front, mettez à l’échelle le déploiement `azure-vote-front`.


```azurecli
kubectl scale --replicas=3 deployment/azure-vote-front
```

Pour mettre à jour l’application, utilisez la commande [kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set). Mettez à jour `<acrLoginServer>` avec le nom du serveur de connexion ou le nom d’hôte de votre registre de conteneurs.

```azurecli
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Pour surveiller le déploiement, utilisez la commande [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get). À mesure que l’application mise à jour est déployée, vos pods sont terminés et recréés avec la nouvelle image conteneur.

```azurecli
kubectl get pod
```

Output:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Tester l’application mise à jour

Obtenez l’adresse IP externe du service `azure-vote-front`.

```azurecli
kubectl get service azure-vote-front
```

Accédez à l’adresse IP pour voir l’application mise à jour.

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons mis à jour une application et nous avons déployé cette mise à jour sur un cluster Kubernetes. Les tâches suivantes ont été accomplies :

> [!div class="checklist"]
> * Mise à jour du code de l’application frontale
> * Création d’une image conteneur mise à jour
> * Envoi (push) de l’image conteneur à Azure Container Registry
> * Déploiement de l’application mise à jour

Passez au didacticiel suivant pour en savoir plus sur la surveillance de Kubernetes avec Operations Management Suite.

> [!div class="nextstepaction"]
> [Effectuer le monitoring de Kubernetes avec Log Analytics](./tutorial-kubernetes-monitor.md)