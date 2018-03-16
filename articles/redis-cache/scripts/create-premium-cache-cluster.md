---
title: "Exemple de script Azure CLI - Créer un Cache Redis Azure Premium avec clustering | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer un Cache Redis Azure Premium avec clustering"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: fb97d19d50ac9845c2495bd167a9cd30805d464b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-premium-azure-redis-cache-with-clustering"></a>Créer un Cache Redis Azure Premium avec clustering

Ce scénario explique comment créer un Cache Redis Azure Premium de 6 Go avec clustering activé et deux partitions.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources et un Cache Redis Premium avec clustering activé. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az redis create](https://docs.microsoft.com/cli/azure/redis#az_redis_create) | Créez une instance de Cache Redis. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous pouvez trouver des exemples supplémentaires de scripts CLI de cache Redis Azure dans la [documentation du cache Redis Azure](../cli-samples.md).