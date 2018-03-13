---
title: "Démarrage rapide : Cluster Azure Kubernetes pour Linux"
description: "Découvrez rapidement comment créer un cluster Kubernetes pour des conteneurs Linux dans Azure Container Service, avec Azure CLI."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: d6eb3ed9114a597389dbe1a521e083c2cacbb724
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Déployer un cluster Azure Kubernetes pour des conteneurs Linux

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Dans ce guide de démarrage rapide, un cluster Kubernetes est déployé à l’aide de l’interface CLI d’Azure. Une application à plusieurs conteneurs composée d’un serveur web frontal et d’une instance Redis est ensuite déployée, puis exécutée sur le cluster. Ceci fait, l’application est accessible via internet. 

L’exemple d’application utilisé dans ce document est écrit en Python. Les concepts et étapes décrits dans cet article sont utilisables pour le déploiement de toute image conteneur dans un cluster Kubernetes. Le code, le fichier Dockerfile et les fichiers manifestes Kubernetes préalablement créés qui sont associés à ce projet sont disponibles sur [GitHub](https://github.com/Azure-Samples/azure-voting-app-redis.git).

![Image de la navigation vers Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

Ce guide de démarrage rapide suppose une compréhension élémentaire des concepts de Kubernetes. Pour en savoir plus, consultez la [documentation Kubernetes ]( https://kubernetes.io/docs/home/).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un groupe logique dans lequel des ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *westeurope*.

```azurecli-interactive 
az group create --name myResourceGroup --location westeurope
```

Output:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-kubernetes-cluster"></a>Créer un cluster Kubernetes

Pour créer un cluster Kubernetes dans Azure Container Service, utilisez la commande [az acs create](/cli/azure/acs#az_acs_create). L’exemple ci-après permet de créer un cluster nommé *myK8sCluster*, qui inclut un nœud maître Linux et trois nœuds agents Linux.

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8sCluster --generate-ssh-keys
```

Dans certains cas, par exemple avec une version d’évaluation limitée, un abonnement Azure dispose d’un accès limité aux ressources Azure. Si le déploiement échoue à cause d’une limitation du nombre de cœurs disponibles, réduisez le nombre d’agents par défaut en ajoutant `--agent-count 1` à la commande [az acs create](/cli/azure/acs#az_acs_create). 

Au bout de quelques minutes, la commande se termine et retourne des informations formatées Json sur le cluster. 

## <a name="connect-to-the-cluster"></a>Connexion au cluster

Pour gérer un cluster Kubernetes, utilisez [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), le client de ligne de commande Kubernetes. 

Si vous utilisez Azure CloudShell, l’outil kubectl est déjà installé. Si vous souhaitez l’installer en local, vous pouvez utiliser la commande [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

Pour configurer kubectl afin qu’il se connecte à votre cluster Kubernetes, exécutez la commande [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). Cette étape télécharge les informations d’identification et configure l’interface de ligne de commande Kubernetes pour leur utilisation.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Pour vérifier la connexion à votre cluster, utilisez la commande [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) pour retourner une liste des nœuds du cluster.

```azurecli-interactive
kubectl get nodes
```

Output:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-14ad53a1-0    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-1    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-2    Ready                      10m       v1.6.6
k8s-master-14ad53a1-0   Ready,SchedulingDisabled   10m       v1.6.6
```

## <a name="run-the-application"></a>Exécution de l'application

Un fichier manifeste Kubernetes définit un état souhaité pour le cluster, incluant les images conteneur à exécuter. Dans cet exemple, un manifeste est utilisé afin de créer tous les objets nécessaires pour l’exécution de l’application Azure Vote. 

Créez un fichier nommé `azure-vote.yml` et copiez-y le YAML suivant. Si vous travaillez dans Azure Cloud Shell, vous pouvez créer ce fichier à l’aide de vi ou de Nano comme si vous travailliez sur un système virtuel ou physique.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Utilisez la commande [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) pour exécuter l’application.

```azurecli-interactive
kubectl create -f azure-vote.yml
```

Output:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Test de l'application

Lorsque l’application est exécutée, un [service Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) est créé, qui expose le serveur frontal de l’application à internet. L’exécution de ce processus peut prendre plusieurs minutes. 

Pour surveiller la progression, utilisez la commande [kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) avec l’argument `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Au début, **EXTERNAL-IP** pour le service *azure-vote-front* apparaît *En attente*. Une fois que l’adresse IP externe est passée du statut *En attente* à *Adresse IP*, utilisez `CTRL-C` pour arrêter le processus de surveillance kubectl. 
  
```bash
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Vous pouvez désormais accéder à l’adresse IP externe pour voir l’application Azure Vote.

![Image de la navigation vers Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)  

## <a name="delete-cluster"></a>Supprimer un cluster
Lorsque vous n’avez plus besoin du cluster, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, le service de conteneur et toutes les ressources associées.

```azurecli-interactive 
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obtenir le code

Dans ce guide de démarrage rapide, les images de conteneur, créées au préalable, ont été utilisées pour créer un déploiement Kubernetes. Le code de l’application associé, Dockerfile, et le fichier manifeste Kubernetes sont disponibles sur GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé un cluster Kubernetes et vous y avez déployé une application de plusieurs conteneurs. 

Pour en savoir plus sur Azure Container Service et parcourir le code complet de l’exemple de déploiement, passez au didacticiel sur le cluster Kubernetes.

> [!div class="nextstepaction"]
> [Gérer un cluster Kubernetes ACS](./container-service-tutorial-kubernetes-prepare-app.md)
