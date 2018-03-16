---
title: "Créer une fonction déployée à partir de GitHub dans Azure | Microsoft Docs"
description: "Créez une application de fonction et déployez le code de fonction à partir d’un référentiel GitHub à l’aide d’Azure Functions."
services: functions
ms.service: functions
keywords: 
ms.devlang: azurecli
author: syntaxc4
ms.author: cfowler
ms.date: 01/09/2018
ms.topic: sample
ms.custom: mvc
ms.openlocfilehash: 720ead07aa35540f61f1dc8e15b79ac9d58669be
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-function-app-in-azure-that-is-deployed-from-github"></a>Créer une application de fonction déployée à partir de GitHub dans Azure

Cet exemple de script Azure Functions crée une application de fonction à l’aide du [plan de consommation](../functions-scale.md#consumption-plan), avec ses ressources associées. Le script configure également votre code de fonction pour le déploiement continu à partir d’un référentiel GitHub. 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

Dans cet exemple, vous aurez besoin des éléments suivants :

* Un référentiel GitHub avec du code de fonction, pour lequel vous disposez d’autorisations d’administration.
* Un [jeton d’accès personnel](https://help.github.com/articles/creating-an-access-token-for-command-line-use) pour votre compte GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous préférez utiliser Azure CLI localement, vous devez installer et utiliser la version 2.0 ou une version ultérieure. Pour déterminer la version Azure CLI, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script

Cet exemple crée une Function App Azure et déploie le code de fonction à partir de GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github-continuous/deploy-function-app-with-function-github-continuous.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Chaque commande du tableau renvoie à une documentation spécifique. Ce script utilise les commandes suivantes :

| Commande | Notes |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Crée un plan App Service. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_delete) |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config) | Associe une Function App à un référentiel Git ou Mercurial. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).
