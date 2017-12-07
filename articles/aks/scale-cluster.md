---
title: "Mettre à jour un cluster Azure Container Service (ACS)"
description: "Mettez à jour un cluster Azure Container Service (ACS)."
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 299eb74686f00dc6d5eb9a1c6127aa134dcd9b77
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="scale-an-azure-container-service-aks-cluster"></a>Mettre à jour un cluster Azure Container Service (ACS)

Il est facile de mettre à l’échelle un cluster ACS vers un autre nombre de nœuds.  Sélectionnez le nombre souhaité de nœuds et exécutez la commande `az aks scale`.  En cas de diminution de l’échelle, les nœuds sont soigneusement [coordonnés et purgés](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) pour minimiser des interruptions des applications en cours d’exécution.  En cas d’augmentation d’échelle, la commande `az` attend jusqu’à ce que les nœuds soient marqués `Ready` par le cluster Kubernetes.

## <a name="scale-the-cluster-nodes"></a>Mettre à l’échelle les nœuds de cluster

Utilisez la commande `az aks scale` pour mettre à l’échelle les nœuds du cluster. L’exemple suivant met à l’échelle un cluster nommé *myK8SCluster* vers un nœud unique.

```azurecli-interactive
az aks scale --name myK8sCluster --resource-group myResourceGroup --node-count 1
```

Output:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "eastus",
  "name": "myK8sCluster",
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
        "name": "myK8sCluster",
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
    "kubernetesVersion": "1.7.7",
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

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur le déploiement et la gestion d’ACS avec les didacticiels ACS.

> [!div class="nextstepaction"]
> [Didacticiel ACS](./tutorial-kubernetes-prepare-app.md)
