---
title: "Exemple CLI - Surveiller - Mettre à l’échelle -Base de données Azure SQL | Microsoft Docs"
description: "Exemple de script Azure CLI permettant de surveiller et mettre à l’échelle une base de données Azure SQL"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune, mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 12/14/2017
ms.author: janeng
ms.openlocfilehash: 741c066d62364e34b788883bfc96fba1ea3507c3
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2017
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Utiliser CLI pour surveiller et mettre à l’échelle une base de données SQL

Cet exemple de script Azure CLI met à l’échelle une base de données Azure SQL vers un autre niveau de performance après avoir déterminé la taille de la base de données. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> Utilisez [az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list) pour obtenir la liste des opérations effectuées sur la base de données et [az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel) pour annuler une opération de mise à jour sur la base de données.

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az_sql_server_create) | Crée un serveur logique qui héberge une base de données. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_show_usage) | Affiche les données de taille d’une base de données. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_update) | Met à jour les propriétés de la base de données (par exemple, le niveau de performances ou le niveau de service) ou déplace une base de données vers, hors ou entre des pools élastiques. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI SQL Database sur la page [Documentation Azure SQL Database](../sql-database-cli-samples.md).
