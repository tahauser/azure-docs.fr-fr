---
title: "Créer votre première fonction sur Linux à l’aide d’Azure CLI (préversion) | Microsoft Docs"
description: "Apprenez à créer votre première fonction Azure exécutée sur une image Linux par défaut à l’aide d’Azure CLI."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 49931155339660fc7a0a39f5b60dc9443374b8b0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Créer votre première fonction exécutée sur Linux à l’aide d’Azure CLI (préversion)

Azure Functions vous permet d’héberger vos fonctions sur Linux dans un conteneur Azure App Service par défaut. Vous pouvez également [apporter votre propre conteneur personnalisé](functions-create-function-linux-custom-image.md). Cette fonctionnalité est actuellement en version préliminaire et requiert le [runtime Functions 2.0](functions-versions.md), qui est également en version préliminaire.

Cette rubrique de démarrage rapide vous guide tout au long de votre utilisation d’Azure Functions avec l’interface de ligne de commande Azure, pour créer votre première application de fonction sur Linux hébergée dans le conteneur App Service par défaut. Le code de la fonction lui-même est déployé sur l’image à partir d’un dépôt d’exemples GitHub.    

Les étapes suivantes sont prises en charge sur un ordinateur Mac, Windows ou Linux. 

## <a name="prerequisites"></a>Prérequis 

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

+ Un abonnement Azure actif.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, Azure CLI version 2.0.21 ou ultérieure est indispensable pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version qui est à votre disposition. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Créer un plan App Service Linux

Pour l’instant, l’hébergement Linux pour Functions est uniquement pris en charge dans un plan App Service. L’hébergement dans un plan Consommation n’est pas encore pris en charge. Pour en savoir plus sur l’hébergement, consultez [Comparaison des plans d’hébergement Azure Functions](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Créer une application de fonction sur Linux

Vous devez disposer d’une application de fonction pour héberger l’exécution de vos fonctions sur Linux. L’application de fonction fournit un environnement pour l’exécution de votre code de fonction. Elle vous permet de regrouper les fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources. Créez une application de fonction à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az_functionapp_create) dans le cadre d’un plan App Service Linux. 

Dans la commande suivante, indiquez un nom d’application de fonction unique là où se trouve l’espace réservé `<app_name>`, et le nom du compte de stockage pour `<storage_name>`. La valeur `<app_name>` est utilisée en tant que domaine DNS par défaut pour la Function App. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Le paramètre _deployment-source-url_ est un dépôt d’exemples dans GitHub qui contient une fonction « Hello World » déclenchée par HTTP.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
```
Après la création de l’application de fonction et son déploiement, Azure CLI affiche des informations semblables à celles de l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

`myAppServicePlan` étant un plan Linux, l’image docker intégrée est utilisée pour créer le conteneur qui exécute l’application de fonction sur Linux. 

>[!NOTE]  
>Actuellement, le dépôt d’exemples comprend deux fichiers de script, [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) et [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment). Le fichier .deployment indique au processus de déploiement d’utiliser deploy.sh comme [script de déploiement personnalisé](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). Dans la préversion courante, les scripts sont nécessaires pour déployer l’application de fonction sur une image Linux.  

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
