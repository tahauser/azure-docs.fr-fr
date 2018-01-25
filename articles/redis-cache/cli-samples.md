---
title: Exemples de CLI du Cache Redis Azure | Microsoft Docs
description: Exemples de CLI pour le Cache Redis Azure.
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: wesmc
ms.openlocfilehash: 4450a8f7f16e3503626c9ee0ca125fc2b1ed9052
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Exemples de CLI pour le Cache Redis Azure

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de l’interface de ligne de commande Azure.

| | |
|---|---|
|**Créer un cache**||
| [Créer un cache](./scripts/create-cache.md) | Crée un groupe de ressources et un Cache Redis Azure de niveau de base. |
| [Créer un cache Premium avec clustering](./scripts/create-premium-cache-cluster.md) | Crée un groupe de ressources et un cache de niveau Premium avec activation du clustering.|
| [Obtenir les détails du cache](./scripts/show-cache.md) | Obtient les détails d’une instance du Cache Redis Azure, dont l’état d’approvisionnement. |
| [Obtenir le nom d’hôte, les ports et les clés](./scripts/cache-keys-ports.md) | Obtient le nom d’hôte, les ports et les clés pour une instance du Cache Redis Azure. |
|**Application web avec cache**||
| [Connecter une application web à un cache redis](./../app-service/scripts/app-service-cli-app-service-redis.md) | Crée une application web Azure et un Cache Redis, puis ajoute les détails de connexion Redis aux paramètres d’application. |
|**Supprimer un cache**||
| [Supprimer un cache](./scripts/delete-cache.md) | Supprime une instance du Cache Redis Azure  |
| | |

Pour plus d’informations sur Azure CLI 2.0, voir [Installer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) et [Prise en main d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).