---
title: 'Démarrage rapide : Cluster Azure Kubernetes pour Linux'
description: Découvrez rapidement comment créer un cluster Kubernetes pour des conteneurs Linux dans ACS avec Azure CLI.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: quickstart
ms.date: 03/14/2018
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 2167754d832eb9be3add9875a8189780e1785988
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-deploy-an-azure-container-service-aks-cluster"></a>Démarrage rapide : Déployer un cluster Azure Container Service (AKS)

Dans ce guide de démarrage rapide, un cluster AKS est déployé à l’aide de l’interface de ligne de commande Azure. Une application de plusieurs conteneurs composée d’un serveur web frontal et d’une instance Redis est alors exécutée sur le cluster. Ceci fait, l’application est accessible via internet.

![Image de la navigation vers Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

Ce guide de démarrage rapide suppose une compréhension élémentaire des concepts de Kubernetes. Pour en savoir plus, consultez la [documentation Kubernetes][kubernetes-documentation].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce démarrage rapide nécessite que vous exécutiez la version 2.0.27 minimum d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="enabling-aks-preview"></a>Activation de la préversion d’AKS

Assurez-vous que les fournisseurs de services Azure requis sont activés à l’aide de la commande `az provider register`. 

```azurecli-interactive
az provider register -n Microsoft.Network
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
az provider register -n Microsoft.ContainerService
```

Une fois celui-ci inscrit, vous êtes prêt à créer un cluster Kubernetes avec AKS.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create][az-group-create]. Un groupe de ressources Azure est un groupe logique dans lequel des ressources Azure sont déployées et gérées.
Lorsque vous créez un groupe de ressources, vous êtes invité à spécifier un emplacement. Il s’agit de l’emplacement où résideront vos ressources dans Azure. Il n’y a que quelques emplacements disponibles, car ACS est en préversion. Ces emplacements sont les suivants `eastus, westeurope, centralus, canadacentral, canadaeast`.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Output:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Créer un cluster ACS

Utilisez la commande [az aks create][az-aks-create] pour créer un cluster AKS. L’exemple suivant crée un cluster à un nœud nommé *myAKSCluster*.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Au bout de quelques minutes, la commande se termine et retourne des informations formatées JSON sur le cluster.

## <a name="connect-to-the-cluster"></a>Connexion au cluster

Pour gérer un cluster Kubernetes, utilisez [kubectl][kubectl], le client de ligne de commande Kubernetes.

Si vous utilisez Azure Cloud Shell, l’outil kubectl est déjà installé. Pour l’installer en local, utilisez la commande [az aks install-cli][az-aks-install-cli].


```azurecli
az aks install-cli
```

Pour configurer kubectl afin qu’il se connecte à votre cluster Kubernetes, utilisez la commande [az aks get-credentials][az-aks-get-credentials]. Cette étape télécharge les informations d’identification et configure l’interface de ligne de commande Kubernetes pour leur utilisation.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pour vérifier la connexion à votre cluster, utilisez la commande [kubectl get][kubectl-get] pour retourner une liste des nœuds du cluster. Notez que cela peut mettre quelques minutes à apparaître.

```azurecli-interactive
kubectl get nodes
```

Output:

```
NAME                          STATUS    ROLES     AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     agent     2m        v1.7.7
```

## <a name="run-the-application"></a>Exécution de l'application

Un fichier manifeste Kubernetes définit un état souhaité pour le cluster, incluant les images conteneur à exécuter. Dans cet exemple, un manifeste est utilisé afin de créer tous les objets nécessaires pour l’exécution de l’application Azure Vote. L’image fournie est un exemple d’application, mais vous pouvez en savoir plus la [création d’image](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-app) et le [déploiement vers Azure Container Registry](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-acr) pour utiliser la vôtre.

Créez un fichier nommé `azure-vote.yaml` et copiez-y le code YAML suivant. Si vous travaillez dans Azure Cloud Shell, vous pouvez créer ce fichier à l’aide de vi ou de Nano comme si vous travailliez sur un système virtuel ou physique. Si vous travaillez en local, vous pouvez utiliser Visual Studio Code pour créer ce fichier en exécutant la commande `code azure-vote.yaml`.

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

Utilisez la commande [kubectl create][kubectl-create] pour exécuter l’application.

```azurecli-interactive
kubectl create -f azure-vote.yaml
```

Output:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Test de l'application

Lorsque l’application est exécutée, un [service Kubernetes][kubernetes-service] est créé, qui expose le serveur frontal de l’application à Internet. L’exécution de ce processus peut prendre plusieurs minutes.

Pour surveiller la progression, utilisez la commande [kubectl get service][kubectl-get] avec l’argument `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Au début, *EXTERNAL-IP* pour le service *azure-vote-front* apparaît *En attente*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Une fois que l’adresse *EXTERNAL-IP* est passée du statut *En attente* à *Adresse IP*, utilisez `CTRL-C` pour arrêter le processus de surveillance kubectl.

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Vous pouvez désormais accéder à l’adresse IP externe pour voir l’application Azure Vote.

![Image de la navigation vers Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Supprimer un cluster

Lorsque vous n’avez plus besoin du cluster, vous pouvez utiliser la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources, le service de conteneur et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obtenir le code

Dans ce guide de démarrage rapide, les images de conteneur, créées au préalable, ont été utilisées pour créer un déploiement Kubernetes. Le code de l’application associé, Dockerfile, et le fichier manifeste Kubernetes sont disponibles sur GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé un cluster Kubernetes et vous y avez déployé une application de plusieurs conteneurs.

Pour en savoir plus sur ACS et parcourir le code complet de l’exemple de déploiement, passez au didacticiel sur le cluster Kubernetes.

> [!div class="nextstepaction"]
> [Didacticiel ACS][aks-tutorial] :

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az_aks_browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az aks install-cli]: /cli/azure/aks?view=azure-cli-latest#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md

