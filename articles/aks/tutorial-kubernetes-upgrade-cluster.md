---
title: Didacticiel Kubernetes sur Azure - Mettre à jour un cluster
description: Didacticiel Kubernetes sur Azure - Mettre à jour un cluster
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 9c63bf2204f1e18cda6bfc80d54b01240193833f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-upgrade-kubernetes-in-azure-container-service-aks"></a>Didacticiel : Mettre à niveau Kubernetes dans Azure Container Service (AKS)

Un cluster Azure Container Service (AKS) peut être mis à niveau à l’aide d’Azure CLI. Pendant le processus de mise à niveau, les nœuds Kubernetes sont soigneusement [coordonnés et purgés][kubernetes-drain] afin de limiter les perturbations pour les applications en cours d’exécution.

Dans ce didacticiel (le huitième d’une série de huit), un cluster Kubernetes est mis à niveau. Les tâches que vous effectuez sont les suivantes :

> [!div class="checklist"]
> * Identifier les versions Kubernetes actuelles et disponibles
> * Mettre à niveau les nœuds Kubernetes
> * Valider une mise à niveau réussie

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. L’application a ensuite été exécutée sur le cluster Kubernetes.

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur][aks-tutorial-prepare-app].


## <a name="get-cluster-versions"></a>Obtenir les versions du cluster

Avant la mise à niveau d’un cluster, utilisez la commande `az aks get-upgrades` pour vérifier les versions de Kubernetes qui sont disponibles pour la mise à niveau.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Ici, vous pouvez voir la version actuelle du nœud (`1.7.9`) ainsi que les versions de mise à jour disponibles sous la colonne des mises à jour.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------------------
default  myResourceGroup  1.7.9            1.7.9              1.7.12, 1.8.1, 1.8.2, 1.8.6, 1.8.7
```

## <a name="upgrade-cluster"></a>Mettre à niveau un cluster

Utilisez la commande `az aks upgrade` pour mettre à niveau les nœuds du cluster. Les exemples suivants mettent à jour le cluster vers la version `1.8.2`.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

Output:

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myAKSCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.8.2",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>Valider la mise à niveau

Vous pouvez maintenant vérifier si la mise à niveau a réussi avec la commande `az aks show`.

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Output:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez mis à niveau Kubernetes dans un cluster AKS. Les tâches suivantes ont été accomplies :

> [!div class="checklist"]
> * Identifier les versions Kubernetes actuelles et disponibles
> * Mettre à niveau les nœuds Kubernetes
> * Valider une mise à niveau réussie

Suivez ce lien pour en savoir plus sur AKS.

> [!div class="nextstepaction"]
> [Vue d’ensemble d’AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md