---
title: "Exemple de script Azure CLI - Obtenir les détails d’un cache Redis Azure | Microsoft Docs"
description: "Exemple de script Azure CLI - Obtenir les détails d’un cache Redis Azure"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 00ae8e00d456d4660cadf645d4521173d254bbd7
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="get-details-of-an-azure-redis-cache"></a>Obtenir les détails d’un cache Redis Azure

Dans ce scénario, vous allez apprendre à récupérer les détails d’une instance de cache Redis Azure, y compris son état d’approvisionnement.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Redis Cache")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour récupérer les détails d’une instance du cache Redis Azure. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Récupérer les détails d’une instance du cache Redis Azure. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous pouvez trouver des exemples supplémentaires de scripts CLI de cache Redis Azure dans la [documentation du cache Redis Azure](../cli-samples.md).