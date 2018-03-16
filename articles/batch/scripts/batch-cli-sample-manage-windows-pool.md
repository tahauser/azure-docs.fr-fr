---
title: Exemple de script Azure CLI - Pool Windows dans Batch | Microsoft Docs
description: "Exemple de script Azure CLI - Créer et gérer un Pool Windows dans Batch"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 0d9c757925b26e9f46afbd075184f853efa25dc3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>Exemple CLI : Créer et gérer un pool Windows dans Azure Batch

Ces scripts montrent certaines des commandes disponibles dans l’interface Azure CLI pour créer et gérer un pool de nœuds de calcul Windows dans Azure Batch. Un pool Windows peut être configuré de deux manières, soit via une configuration de services cloud, soit via une configuration de machine virtuelle. Cet exemple montre comment créer un pool Windows avec la configuration de services cloud.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.20 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qui lui sont associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Crée le compte Batch. |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI. |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | Crée un pool de nœuds de calcul.  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_set) | Met à jour les propriétés d’un pool.  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | Active la mise à l’échelle automatique sur un pool et applique une formule.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_show) | Affiche les propriétés d’un pool.  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | Désactive la mise à l’échelle automatique sur un pool. |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).
