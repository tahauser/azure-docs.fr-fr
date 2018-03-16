---
title: "Exemple de script Azure CLI - Obtenir le nom d’hôte, les ports et les clés pour le Cache Redis Azure | Microsoft Docs"
description: "Exemple de script Azure CLI - Obtenir le nom d’hôte, les ports et les clés pour une instance du Cache Redis Azure"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 9a2a6a7ed4bb56f93c965a5bfd24b21368117c7b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>Obtenir le nom d’hôte, les ports et les clés pour le Cache Redis Azure

Ce scénario décrit comment récupérer le nom d’hôte, les ports et les clés utilisés pour se connecter à une instance du Cache Redis Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Redis Cache")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour récupérer le nom d’hôte, les clés et les ports d’une instance du Cache Redis Azure. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Récupérer les détails d’une instance du Cache Redis Azure. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Récupérer les clés d’accès pour une instance du Cache Redis Azure. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous pouvez trouver des exemples supplémentaires de scripts CLI de cache Redis Azure dans la [documentation du cache Redis Azure](../cli-samples.md).