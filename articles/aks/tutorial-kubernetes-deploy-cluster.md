---
title: "Didacticiel Kubernetes sur Azure – Déployer un cluster"
description: "Didacticiel ACS - Déployer un cluster"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e0d5bd57a40fca837ead42e691e1fa0c802dc013
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Déployer un cluster Azure Container Service (ACS)

Kubernetes fournit une plateforme distribuée destinée aux applications en conteneur. Avec Azure Container Service, l’approvisionnement d’un cluster Kubernetes prêt pour la production est une opération simple et rapide. Dans ce didacticiel (troisième d’une série de huit), un cluster Kubernetes est déployé dans ACS. Les étapes effectuées sont les suivantes :

> [!div class="checklist"]
> * Déploiement d’un cluster ACS Kubernetes
> * Installation de l’interface de ligne de commande Kubernetes (kubectl)
> * Configuration de kubectl

Dans les didacticiels suivants, l’application Azure Vote est déployée sur le cluster, puis mise à l’échelle et mise à jour. En outre, Operations Management Suite est configuré pour la surveillance du cluster Kubernetes.

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une image conteneur a été créée et chargée dans une instance Azure Container Registry. Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur][aks-tutorial-prepare-app].

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Activation de la préversion d’AKS pour votre abonnement Azure
Tant que AKS est en préversion, la création de nouveaux clusters exige un indicateur de fonctionnalité dans votre abonnement. Vous pouvez demander cette fonctionnalité pour les abonnements que vous souhaitez utiliser, quel qu’en soit le nombre. Utilisez la commande `az provider register` pour inscrire le fournisseur AKS :

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

Une fois celui-ci inscrit, vous êtes prêt à créer un cluster Kubernetes avec AKS.

## <a name="create-kubernetes-cluster"></a>Créer un cluster Kubernetes

L’exemple suivant crée un cluster nommé `myAKSCluster` dans le groupe de ressources `myResourceGroup`. Vous avez créé le groupe de ressources au [tutoriel précédent][aks-tutorial-prepare-acr].

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Au bout de quelques minutes, le déploiement se termine et retourne des informations au format JSON concernant le déploiement ACS.

## <a name="install-the-kubectl-cli"></a>Installer l’interface de ligne de commande kubectl

Pour vous connecter au cluster Kubernetes à partir de votre ordinateur client, utilisez [kubectl][kubectl], le client de ligne de commande Kubernetes.

Si vous utilisez Azure CloudShell, l’outil kubectl est déjà installé. Si vous souhaitez l’installer localement, exécutez la commande suivante :

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Se connecter avec kubectl

Pour configurer kubectl afin qu’il se connecte à votre cluster Kubernetes, exécutez la commande suivante :

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myAKSCluster
```

Pour vérifier la connexion à votre cluster, exécutez la commande [kubectl get nodes][kubectl-get].

```azurecli
kubectl get nodes
```

Sortie :

```
NAME                          STATUS    AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     49m       v1.7.7
```

À l’issue du didacticiel, vous disposez d’un cluster ACS prêt pour les charges de travail. Dans les didacticiels suivants, une application à plusieurs conteneurs est déployée sur ce cluster, mise à l’échelle, mise à jour et surveillée.

## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, un cluster Kubernetes a été déployé dans ACS. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déploiement d’un cluster ACS Kubernetes
> * Installation de l’interface de ligne de commande Kubernetes (kubectl)
> * Configuration de kubectl

Passez au didacticiel suivant pour en savoir plus sur l’exécution de l’application sur le cluster.

> [!div class="nextstepaction"]
> [Déployer une application dans Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md