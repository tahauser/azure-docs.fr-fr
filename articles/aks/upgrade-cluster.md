---
title: "Mise à jour d’un cluster Azure Container Service (AKS)"
description: "Mise à jour d’un cluster Azure Container Service (AKS)"
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 9b94f858aa896eaa93430a12cd74e12d9bf02008
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Mise à jour d’un cluster Azure Container Service (AKS)

Service de conteneur Azure (ACS) facilite effectuer des tâches de gestion courantes, y compris la mise à niveau Kubernetes clusters.

## <a name="upgrade-an-aks-cluster"></a>Mettre à niveau un cluster ACS

Avant la mise à niveau d’un cluster, utilisez la commande `az aks get-upgrades` pour vérifier les versions de Kubernetes qui sont disponibles pour la mise à niveau.

```azurecli-interactive
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Output:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------------------
default  myResourceGroup  1.7.9            1.7.9              1.7.12, 1.8.1, 1.8.2, 1.8.6, 1.8.7
```

Nous avons trois versions disponibles pour la mise à niveau : 1.7.9, 1.8.1 et 1.8.2. Nous pouvons utiliser la commande `az aks upgrade` pour mettre à niveau vers la dernière version disponible.  Pendant le processus de mise à niveau, les nœuds sont soigneusement [coordonnés et purgés][kubernetes-drain] afin de limiter les perturbations pour les applications en cours d’exécution.  Avant d’effectuer une mise à niveau de cluster, assurez-vous de disposer d’une capacité de calcul supplémentaire suffisante pour gérer votre charge de travail au fur et à mesure que des noeuds de cluster sont ajoutés et supprimés.

```azurecli-interactive
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

Vous pouvez maintenant vérifier si la mise à niveau a réussi avec la commande `az aks show`.

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Output:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur le déploiement et la gestion d’ACS avec les didacticiels ACS.

> [!div class="nextstepaction"]
> [Didacticiel ACS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md