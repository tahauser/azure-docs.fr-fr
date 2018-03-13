---
title: "Exemple de script Azure CLI - Exécution d’une tâche Batch | Microsoft Docs"
description: "Exemple de script Azure CLI - Exécution d’une tâche avec Batch"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: a782bed39720ac8f4b62b082dd43a2604faf1ca6
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Exemple CLI : Exécution d’un travail et de tâches avec Azure Batch

Ce script crée une tâche Batch et ajoute une série de tâches au travail. Il explique également comment surveiller un travail et ses tâches. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.20 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

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
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | Crée un pool de nœuds de calcul.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_create) | Crée un travail Batch.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_create) | Ajoute une tâche au travail Batch spécifié.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_set) | Met à jour les propriétés d’un travail Batch.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_show) | Récupère les détails relatifs au travail Batch spécifié.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_show) | Récupère les détails relatifs à une tâche du travail Batch spécifié.  |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure/overview).