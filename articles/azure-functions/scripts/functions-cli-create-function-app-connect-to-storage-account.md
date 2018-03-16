---
title: "Créer une fonction Azure qui se connecte à un Stockage Azure | Documents Microsoft"
description: "Exemple de script Azure CLI - Créer une fonction Azure qui se connecte à un Stockage Azure"
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: cbe7bf95574ca7a77d666981691da05357ce9a0d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-function-app-that-connects-to-an-azure-storage-account"></a>Créer une application de fonction se connectant à un compte de stockage Azure

Cet exemple de script Azure Functions crée une application de fonction et connecte la fonction à un compte de stockage Azure. Le paramètre de l’application créée contenant la connexion peut être utilisé avec une [liaison ou un déclencheur de stockage](..\functions-bindings-storage-blob.md). 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous utilisez l’interface de ligne de commande en local, veillez à exécuter Azure CLI 2.0 ou une version plus récente. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script

Cet exemple crée une Function App Azure et ajoute la chaîne de connexion de stockage à un paramètre d’application.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]


## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, exécutez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées :

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/reference-index#az_login) | Connectez-vous à Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crée un groupe de ressources avec un emplacement. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | Créez un compte de stockage. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Crée une Function App. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Nettoyer |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).
