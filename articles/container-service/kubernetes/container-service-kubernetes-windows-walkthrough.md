---
title: "Démarrage rapide : Cluster Azure Kubernetes pour Windows"
description: "Découvrez rapidement comment créer un cluster Kubernetes pour des conteneurs Windows dans Azure Container Service avec l’interface de ligne de commande Azure."
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: a7bb330657d14ac42cddf2e20fbb2e5a5b2a589b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-kubernetes-cluster-for-windows-containers"></a>Déployer un cluster Azure Kubernetes pour des conteneurs Windows

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide explique en détail comment utiliser Azure CLI pour déployer un cluster [Kubernetes](https://kubernetes.io/docs/home/) dans [Azure Container Service](../container-service-intro.md). Une fois le cluster déployé, vous vous y connectez avec l’outil en ligne de commande Kubernetes `kubectl`, et vous déployez votre premier conteneur Windows.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Le support des conteneurs Windows sur Kubernetes dans Azure Container Service est en préversion. 
>

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un groupe logique dans lequel des ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Créer un cluster Kubernetes
Créez un cluster Kubernetes dans Azure Container Service avec la commande [az acs create](/cli/azure/acs#az_acs_create). 

L’exemple ci-après crée un cluster nommé *myK8sCluster* avec un nœud maître Linux et deux nœuds agents Windows. Cet exemple crée les clés SSH requises pour la connexion au maître Linux. Cet exemple utilise le nom d’utilisateur administratif *azureuser* et le mot de passe *myPassword12* sur les nœuds Windows. Mettez à jour ces valeurs avec quelque chose d’approprié pour votre environnement. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

Au bout de quelques minutes, la commande s’achève et vous fournit des informations sur votre déploiement.

## <a name="install-kubectl"></a>Installer kubectl

Pour vous connecter au cluster Kubernetes depuis l’ordinateur client, utilisez l’outil [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), le client de ligne de commande Kubernetes. 

Si vous utilisez Azure CloudShell, l’outil `kubectl` est déjà installé. Si vous souhaitez l’installer en local, vous pouvez utiliser la commande [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

L’exemple Azure CLI ci-après installe `kubectl` sur votre système. Sur Windows, exécutez cette commande en tant qu’administrateur.

```azurecli-interactive 
az acs kubernetes install-cli
```


## <a name="connect-with-kubectl"></a>Se connecter avec kubectl

Pour configurer le client `kubectl` afin qu’il se connecte à votre cluster Kubernetes, exécutez la commande [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). L’exemple ci-après télécharge la configuration de votre cluster Kubernetes.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Pour vérifier la connexion à votre cluster à partir de votre machine, essayez d’exécuter la commande suivante :

```azurecli-interactive
kubectl get nodes
```

`kubectl` répertorie les nœuds maître et agents.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```

## <a name="deploy-a-windows-iis-container"></a>Déployer un conteneur Windows IIS

Vous pouvez exécuter un conteneur Docker à l’intérieur d’un *pod* Kubernetes, qui contient un ou plusieurs conteneurs. 

Cet exemple de base utilise un fichier JSON pour spécifier un conteneur Microsoft Internet Information Server (IIS), puis crée le pod à l’aide de la commande `kubctl apply`. 

Créez un fichier local nommé `iis.json` et copiez-y le texte suivant. Ce fichier demande à Kubernetes d’exécuter IIS sur Windows Server 2016 Nano Server, à l’aide d’une image conteneur publique provenant de [Docker Hub](https://hub.docker.com/r/microsoft/iis/). Le conteneur utilise le port 80, mais au début il n’est accessible que dans le réseau du cluster.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "microsoft/iis:nanoserver",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

Pour démarrer le pod, tapez :
  
```azurecli-interactive
kubectl apply -f iis.json
```  

Pour suivre le déploiement, tapez :
  
```azurecli-interactive
kubectl get pods
```

Pendant que le pod est en cours de déploiement, l’état présente la valeur `ContainerCreating`. Il peut s’écouler quelques minutes avant que le conteneur prenne l’état `Running`.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>Afficher la page d’accueil IIS

Pour exposer le pod avec une adresse IP publique, tapez la commande suivante :

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

Avec cette commande, Kubernetes crée un service et une règle Azure Load Balancer avec une adresse IP publique pour ce service. 

Exécutez la commande suivante pour afficher l’état du service.

```azurecli-interactive
kubectl get svc
```

L’adresse IP affichée initialement apparaît en tant que `pending`. Après quelques minutes, l’adresse IP externe du pod `iis` est définie :
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

Vous pouvez utiliser un navigateur web de votre choix pour visualiser la page d’accueil IIS par défaut à l’adresse IP externe :

![Image de la navigation vers IIS](./media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## <a name="delete-cluster"></a>Supprimer un cluster
Lorsque vous n’avez plus besoin du cluster, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, le service de conteneur et toutes les ressources associées.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Étapes suivantes

Par le biais de ce guide de démarrage rapide, vous avez déployé un cluster Kubernetes, vous vous êtes connecté avec `kubectl` et vous avez déployé un pod avec un conteneur IIS. Pour plus d’informations sur Azure Container Service, passez au didacticiel Kubernetes.

> [!div class="nextstepaction"]
> [Gérer un cluster Kubernetes ACS](container-service-tutorial-kubernetes-prepare-app.md)
