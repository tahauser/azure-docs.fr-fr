---
title: Supprimer un cluster Azure Container Service (AKS)
description: "Supprimez un cluster AKS avec l’interface CLI ou le portail Azure."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 78056288f45616eda427f8e708efc679f8a5202c
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="delete-an-azure-container-service-aks-cluster"></a>Supprimer un cluster Azure Container Service (AKS)

Lorsque vous supprimez un cluster Azure Container Service, le groupe de ressources dans lequel le cluster a été déployé est conservé. Cependant, toutes les ressources AKS sont supprimées. Ce document montre comment supprimer un cluster AKS à l’aide de l’interface Azure CLI et du portail Azure. 

Outre la suppression du cluster, le groupe de ressources dans lequel il a été déployé peut être supprimé, ce qui supprime également le cluster AKS.

## <a name="azure-cli"></a>Azure CLI

Utilisez la commande [az aks delete][az-aks-delete] pour supprimer le cluster AKS.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Les options suivantes sont disponibles avec la commande `az aks delete`.

| Argument | Description | Obligatoire |
|---|---|:---:|
| `--name``-n` | Nom de ressource pour le cluster managé. | Oui |
| `--resource-group``-g` | Nom du groupe de ressources Azure Container Service. | Oui |
| `--no-wait` | Ne pas attendre la fin de l’opération de longue durée. | no |
| `--yes``-y` | Ne pas demander de confirmation. | no |

## <a name="azure-portal"></a>Portail Azure

Dans le portail Azure, recherchez le groupe de ressources contenant la ressource Azure Container Service (AKS), sélectionnez la ressource, puis cliquez sur **Supprimer**. Vous êtes invité à confirmer l’opération de suppression.

![Supprimer un cluster AKS dans le portail](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete