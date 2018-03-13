---
title: "Exemple de script Azure CLI - Créer un compte Batch - Service Batch | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer un compte Batch dans le mode de service Batch"
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
ms.openlocfilehash: e8e8e475c1fe32346dde39e187a007ec7f62a2f3
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>Exemple CLI : Créer un compte Batch dans le mode de service Batch

Ce script crée un compte Azure Batch dans le mode de service Batch et indique de quelle manière les différentes propriétés du compte peuvent être interrogées et mises à jour. Lorsque vous créez un compte Batch dans le mode de service Batch par défaut, ses nœuds de calcul sont attribués en interne par le service Batch. Les nœuds de calcul alloués sont soumis à un quota de processeur virtuel (principal) distinct, et le compte peut être authentifié via les informations d’identification de la clé partagée ou par un jeton Azure Active Directory.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.20 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

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
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Crée un compte de stockage. |
| [az batch account set](/cli/azure/batch/account#az_batch_account_set) | Met à jour les propriétés du compte Batch.  |
| [az batch account show](/cli/azure/batch/account#az_batch_account_show) | Récupère les détails relatifs au compte Batch spécifié.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az_batch_account_keys_list) | Récupère les clés d’accès du compte Batch spécifié.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI.  |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure/overview).
