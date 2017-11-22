---
title: "Didacticiel Kubernetes sur Azure - Mettre à jour un cluster | Microsoft Docs"
description: "Didacticiel Kubernetes sur Azure - Mettre à jour un cluster"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Docker, conteneurs, micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d0193e891c6c41687f1aaa3a8033bc71e85b10c3
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="upgrade-kubernetes-in-azure-container-service-aks"></a>Mettre à niveau Kubernetes dans Azure Container Service (AKS)

Un cluster Azure Container Service (AKS) peut être mis à niveau à l’aide d’Azure CLI. Pendant le processus de mise à niveau, les nœuds Kubernetes sont soigneusement [coordonnés et purgés](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) pour limiter les perturbations pour les applications en cours d’exécution.

Dans ce didacticiel (le huitième d’une série de huit), un cluster Kubernetes est mis à niveau. Les tâches que vous effectuez sont les suivantes :

> [!div class="checklist"]
> * Identifier les versions Kubernetes actuelles et disponibles
> * Mettre à niveau les nœuds Kubernetes
> * Valider une mise à niveau réussie

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans une image conteneur, l’image a été chargée dans Azure Container Registry et un cluster Kubernetes a été créé. L’application a ensuite été exécutée sur le cluster Kubernetes.

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./tutorial-kubernetes-prepare-app.md).


## <a name="get-cluster-versions"></a>Obtenir les versions du cluster

Avant la mise à niveau d’un cluster, utilisez la commande `az aks get-versions` pour vérifier les versions de Kubernetes qui sont disponibles pour la mise à niveau.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Vous pouvez voir ici que la version actuelle du nœud est `1.7.7`, et que les versions `1.7.9`, `1.8.1` et `1.8.2` sont disponibles.

```
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myAKSCluster     1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

## <a name="upgrade-cluster"></a>Mettre à niveau un cluster

Utilisez la commande `az aks upgrade` pour mettre à niveau les nœuds du cluster. Les exemples suivants mettent à jour le cluster vers la version `1.8.2`.

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
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

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

Output:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez mis à niveau Kubernetes dans un cluster AKS. Les tâches suivantes ont été accomplies :

> [!div class="checklist"]
> * Identifier les versions Kubernetes actuelles et disponibles
> * Mettre à niveau les nœuds Kubernetes
> * Valider une mise à niveau réussie

Suivez ce lien pour en savoir plus sur AKS.

> [!div class="nextstepaction"]
> [Vue d’ensemble d’AKS](./intro-kubernetes.md)