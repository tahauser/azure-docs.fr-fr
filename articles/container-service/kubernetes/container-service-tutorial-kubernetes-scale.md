---
title: "Didacticiel Azure Container Service - Mettre à l’échelle une application"
description: "Didacticiel Azure Container Service - Mettre à l’échelle une application"
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 36c5586f79cf127ec069fd3c6ef95dd073fdbdb6
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="scale-kubernetes-pods-and-kubernetes-infrastructure"></a>Mettre à l’échelle des pods Kubernetes et l’infrastructure Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Si vous avez suivi les didacticiels, vous disposez d’un cluster Kubernetes opérationnel dans Azure Container Service et vous avez déployé l’application Azure Vote. 

Dans ce didacticiel (issu d’une série de sept didacticiels), vous allez augmenter le nombre de pods dans l’application et essayer la mise à l’échelle automatique des pods. Vous allez également apprendre à mettre à l’échelle le nombre de nœuds d’agents de machine virtuelle Azure afin de modifier la capacité du cluster pour l’hébergement des charges de travail. Les tâches accomplies sont les suivantes :

> [!div class="checklist"]
> * Mise à l’échelle manuelle des pods Kubernetes
> * Configuration de la mise à l’échelle automatique des pods qui exécutent le front-end de l’application
> * Mettre à l’échelle les nœuds d’agents Azure Kubernetes

Dans les didacticiels suivants, l’application Azure Vote est mise à jour et Operations Management Suite est configuré pour la surveillance du cluster Kubernetes.

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. L’application a ensuite été exécutée sur le cluster Kubernetes. 

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="manually-scale-pods"></a>Mettre à l’échelle des pods manuellement

Jusqu’à maintenant, le front-end Azure Vote et l’instance de Redis ont été déployés, chacun avec un réplica unique. À des fins de vérification, exécutez la commande [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```azurecli-interactive
kubectl get pods
```

Sortie :

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Modifiez manuellement le nombre de pods dans le déploiement `azure-vote-front` à l’aide de la commande [kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale). Cet exemple augmente le nombre à 5.

```azurecli-interactive
kubectl scale --replicas=5 deployment/azure-vote-front
```

Exécutez [kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) pour vérifier que Kubernetes crée les pods. Au bout d’une minute environ, les pods supplémentaires sont en cours d’exécution :

```azurecli-interactive
kubectl get pods
```

Sortie :

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Mettre à l’échelle les pods automatiquement

Kubernetes prend en charge la [mise à l’échelle automatique des pods horizontaux](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) pour ajuster le nombre de pods dans un déploiement en fonction de l’utilisation du processeur ou d’autres métriques. 

Pour utiliser la mise à l’échelle automatique, vos pods doivent avoir des demandes et limites de processeur définies. Dans le déploiement `azure-vote-front`, le conteneur frontal demande 0,25 processeur, avec une limite de 0,5 processeur. Les paramètres s’apparentent aux suivants :

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

L’exemple suivant utilise la commande [kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) pour mettre automatiquement à l’échelle le nombre de pods dans le déploiement `azure-vote-front`. Ici, si l’utilisation du processeur dépasse 50 %, le nombre de pods augmente jusqu’à un maximum de 10.


```azurecli-interactive
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Pour voir l’état de la mise à l’échelle automatique, exécutez la commande suivante :

```azurecli-interactive
kubectl get hpa
```

Sortie :

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Au bout de quelques minutes, avec une charge minimale sur l’application Azure Vote, le nombre de réplicas de pods descend automatiquement à 3.

## <a name="scale-the-agents"></a>Mettre à l’échelle les agents

Si vous avez créé votre cluster Kubernetes à l’aide des commandes par défaut dans le didacticiel précédent, il comporte trois nœuds agents. Vous pouvez ajuster le nombre d’agents manuellement si vous prévoyez davantage ou moins de charges de travail de conteneur sur votre cluster. Utilisez la commande [az acs scale](/cli/azure/acs#az_acs_scale), puis spécifiez le nombre d’agents avec le paramètre `--new-agent-count`.

L’exemple suivant permet d’augmenter le nombre de nœuds agents à 4 dans le cluster Kubernetes nommé *myK8sCluster*. Quelques minutes sont nécessaires pour exécuter la commande.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

La sortie de la commande indique le nombre de nœuds agents dans la valeur de `agentPoolProfiles:count` :

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé différentes fonctionnalités de mise à l’échelle dans votre cluster Kubernetes. Les tâches traitées ont inclus :

> [!div class="checklist"]
> * Mise à l’échelle manuelle des pods Kubernetes
> * Configuration de la mise à l’échelle automatique des pods qui exécutent le front-end de l’application
> * Mettre à l’échelle les nœuds d’agents Azure Kubernetes

Passez au didacticiel suivant pour en savoir plus sur la mise à jour d’une application dans Kubernetes.

> [!div class="nextstepaction"]
> [Mettre à jour une application dans Kubernetes](./container-service-tutorial-kubernetes-app-update.md)

