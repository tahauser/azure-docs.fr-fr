---
title: "Exemple de script Azure CLI - Créer un compte Batch - abonnement utilisateur | Documents Microsoft"
description: "Exemple de script Azure CLI - Créer un compte Batch en mode abonnement utilisateur"
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
ms.openlocfilehash: 0923512d62a1efe31e08377b6c074df2e0618552
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>Exemple CLI : créer un compte Batch en mode abonnement utilisateur

Ce script crée un compte Azure Batch en mode abonnement utilisateur. Un compte qui alloue des nœuds de calcul dans votre abonnement doit être authentifié via un jeton Azure Active Directory. Les nœuds de calcul alloués sont pris en compte dans le quota de processeurs virtuels (cœur) de votre abonnement. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.20 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qui lui sont associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az role assignment create](/cli/azure/role#az_role_assignment_create) | Crée une nouvelle attribution de rôle pour un utilisateur, un groupe ou un principal de service. |
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Crée un coffre de clés. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | Met à jour la stratégie de sécurité du coffre de clés spécifié. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Crée le compte Batch.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI.  |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
