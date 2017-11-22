---
title: "Didacticiel Kubernetes sur Azure - Déployer un cluster | Microsoft Docs"
description: "Didacticiel ACS - Déployer un cluster"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Docker, conteneurs, micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 69dea4ab748d88d18cf01dc9b3fc1bdddd562681
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Déployer un cluster Azure Container Service (ACS)

Kubernetes fournit une plateforme distribuée destinée aux applications en conteneur. Avec Azure Container Service, l’approvisionnement d’un cluster Kubernetes prêt pour la production est une opération simple et rapide. Dans ce didacticiel (troisième d’une série de huit), un cluster Kubernetes est déployé dans ACS. Les étapes terminées sont les suivantes :

> [!div class="checklist"]
> * Déploiement d’un cluster ACS Kubernetes
> * Installation de l’interface de ligne de commande Kubernetes (kubectl)
> * Configuration de kubectl

Dans les didacticiels suivants, l’application Azure Vote est déployée sur le cluster, puis mise à l’échelle et mise à jour. En outre, Operations Management Suite est configuré pour la surveillance du cluster Kubernetes.

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une image conteneur a été créée et chargée dans une instance Azure Container Registry. Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./tutorial-kubernetes-prepare-app.md).

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Activation de la préversion d’AKS pour votre abonnement Azure
Tant que AKS est en préversion, la création de nouveaux clusters exige un indicateur de fonctionnalité dans votre abonnement. Vous pouvez demander cette fonctionnalité pour les abonnements que vous souhaitez utiliser, quel qu’en soit le nombre. Utilisez la commande `az provider register` pour inscrire le fournisseur AKS :

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

Une fois celui-ci inscrit, vous êtes prêt à créer un cluster Kubernetes avec AKS.

## <a name="create-kubernetes-cluster"></a>Créer un cluster Kubernetes

L’exemple suivant crée un cluster nommé `myK8sCluster` dans le groupe de ressources `myResourceGroup`. Vous avez créé le groupe de ressources au [tutoriel précédent](./tutorial-kubernetes-prepare-acr.md).

```azurecli
az aks create --resource-group myResourceGroup --name myK8sCluster --node-count 1 --generate-ssh-keys
```

Au bout de quelques minutes, le déploiement se termine et retourne des informations au format JSON concernant le déploiement ACS.

## <a name="install-the-kubectl-cli"></a>Installer l’interface de ligne de commande kubectl

Pour vous connecter au cluster Kubernetes à partir de votre ordinateur client, utilisez [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), le client de ligne de commande Kubernetes.

Si vous utilisez Azure CloudShell, l’outil kubectl est déjà installé. Si vous souhaitez l’installer localement, exécutez la commande suivante :

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Se connecter avec kubectl

Pour configurer kubectl afin qu’il se connecte à votre cluster Kubernetes, exécutez la commande suivante :

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Pour vérifier la connexion à votre cluster, exécutez la commande [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```azurecli
kubectl get nodes
```

Output:

```
NAME                          STATUS    AGE       VERSION
k8s-myk8scluster-36346190-0   Ready     49m       v1.7.7
```

À l’issue du didacticiel, vous disposez d’un cluster ACS prêt pour les charges de travail. Dans les didacticiels suivants, une application à plusieurs conteneurs est déployée sur ce cluster, mise à l’échelle, mise à jour et surveillée.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, un cluster Kubernetes a été déployé dans ACS. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déploiement d’un cluster ACS Kubernetes
> * Installation de l’interface de ligne de commande Kubernetes (kubectl)
> * Configuration de kubectl

Passez au didacticiel suivant pour en savoir plus sur l’exécution de l’application sur le cluster.

> [!div class="nextstepaction"]
> [Déployer une application dans Kubernetes](./tutorial-kubernetes-deploy-application.md)
