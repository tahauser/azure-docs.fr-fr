---
title: "Didacticiel Kubernetes sur Azure – Mise à l’échelle d’une application"
description: "Didacticiel ACS - Mise à l’échelle d’une application"
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 9eba0de054b06233f2de7fb375010b4b40c6937f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="scale-application-in-azure-container-service-aks"></a>Mettre à jour une application dans Azure Container Service (ACS)

Si vous avez suivi les didacticiels, vous disposez d’un cluster Kubernetes opérationnel dans ACS, et avez déployé l’application Azure Voting.

Dans ce didacticiel (cinquième d’une série de huit), vous allez augmenter le nombre de pods dans l’application et essayer la mise à l’échelle automatique des pods. Vous allez également apprendre à mettre à l’échelle le nombre de nœuds de machine virtuelle Azure afin de modifier la capacité du cluster pour l’hébergement des charges de travail. Les tâches accomplies sont les suivantes :

> [!div class="checklist"]
> * Mettre à l’échelle les nœuds Azure Kubernetes
> * Mise à l’échelle manuelle des pods Kubernetes
> * Configuration de la mise à l’échelle automatique des pods qui exécutent le front-end de l’application

Dans les didacticiels suivants, l’application Azure Vote est mise à jour et Operations Management Suite est configuré pour la surveillance du cluster Kubernetes.

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. L’application a ensuite été exécutée sur le cluster Kubernetes.

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./tutorial-kubernetes-prepare-app.md).

## <a name="scale-aks-nodes"></a>Mettre à l’échelle nœuds ACS

Si vous avez créé votre cluster Kubernetes à l’aide des commandes dans le didacticiel précédent, le cluster comporte un nœud. Vous pouvez ajuster le nombre de nœuds manuellement si vous prévoyez davantage ou moins de charges de travail de conteneur sur votre cluster.

L’exemple suivant permet d’augmenter le nombre de nœuds à trois dans le cluster Kubernetes nommé *myK8sCluster*. Quelques minutes sont nécessaires pour exécuter la commande.

```azurecli
az aks scale --resource-group=myResourceGroup --name=myK8SCluster --node-count 3
```

Le résultat ressemble à ce qui suit :

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myK8sCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="manually-scale-pods"></a>Mettre à l’échelle des pods manuellement

Jusqu’à maintenant, le front-end Azure Vote et l’instance de Redis ont été déployés, chacun avec un réplica unique. À des fins de vérification, exécutez la commande [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```azurecli
kubectl get pods
```

Output:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Modifiez manuellement le nombre de pods dans le déploiement `azure-vote-front` à l’aide de la commande [kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale). Cet exemple augmente le nombre à 5.

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

Exécutez [kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) pour vérifier que Kubernetes crée les pods. Au bout d’une minute environ, les pods supplémentaires sont en cours d’exécution :

```azurecli
kubectl get pods
```

Output:

```
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


```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Pour voir l’état de la mise à l’échelle automatique, exécutez la commande suivante :

```azurecli
kubectl get hpa
```

Output:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Au bout de quelques minutes, avec une charge minimale sur l’application Azure Vote, le nombre de réplicas de pods descend automatiquement à 3.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé différentes fonctionnalités de mise à l’échelle dans votre cluster Kubernetes. Les tâches traitées ont inclus :

> [!div class="checklist"]
> * Mise à l’échelle manuelle des pods Kubernetes
> * Configuration de la mise à l’échelle automatique des pods qui exécutent le front-end de l’application
> * Mettre à l’échelle les nœuds Azure Kubernetes

Passez au didacticiel suivant pour en savoir plus sur la mise à jour d’une application dans Kubernetes.

> [!div class="nextstepaction"]
> [Mettre à jour une application dans Kubernetes](./tutorial-kubernetes-app-update.md)