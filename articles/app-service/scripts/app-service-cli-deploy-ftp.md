---
title: "Exemple de script Azure CLI - Créer une application web et déployer des fichiers par FTP | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer une application web et déployer des fichiers par FTP"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
tags: azure-service-management
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: sample
ms.topic: sample
ms.date: 12/12/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: d8b4cc9c80f1e9e3743e615c205972d027eb726f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-web-app-and-deploy-files-with-ftp"></a>Créer une application web et déployer des fichiers par FTP

Cet exemple de script crée une application web dans App Service avec les ressources associées, puis déploie une page HTML statique par FTP. Pour le chargement FTP, le script utilise [cURL](https://en.wikipedia.org/wiki/CURL) à titre d’exemple. Vous pouvez utiliser l’outil FTP de votre choix pour charger vos fichiers.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez utiliser Azure CLI 2.0 ou version ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-ftp/deploy-ftp.sh "Create a web app and deploy files with FTP")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script 

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | Crée un plan App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) | Crée une application web Azure. |
| [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment?view=azure-cli-latest#az_webapp_deployment_list_publishing_profiles) | Affiche les détails des profils de déploiement d’applications web disponibles. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../app-service-cli-samples.md).
