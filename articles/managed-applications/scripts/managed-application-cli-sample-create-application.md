---
title: "Exemple de script Azure CLI - Déployer une application managée | Microsoft Docs"
description: "Exemple de script Azure CLI - Déployer une définition d’application managée"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 3090284a89cfeb1fe8b6446e55e2731e1155167f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Déployer une application managée pour le catalogue de services avec Azure CLI

Ce script déploie une définition d’application managée à partir du catalogue de services. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise la commande suivante pour déployer l’application managée. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_create) | Créer une application managée. Fournir les paramètres et l’ID de définition pour le modèle. |


## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](../overview.md).
* Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).
