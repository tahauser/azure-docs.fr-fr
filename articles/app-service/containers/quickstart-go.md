---
title: "Déployer une application Go dans Azure Web App for Containers | Microsoft Docs"
description: "Comment déployer une image Docker exécutant une application Go pour Azure Web Apps for Containers."
keywords: azure app service, application web, go, docker, conteneur
services: app-service
author: sptramer
manager: cfowler
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: sttramer
ms.custom: mvc
ms.openlocfilehash: dbe4d7bc6f5f1d83a079993c9616206fd82a1b9d
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="deploy-a-go-app-in-azure-web-app-for-containers"></a>Déployer une application Go dans Azure Web App for Containers

App Service fournit des piles d’applications prédéfinies sur Linux avec la prise en charge de versions spécifiques, comme PHP 7.0 et Node.js 4.5. Vous pouvez également utiliser une image Docker personnalisée pour exécuter votre application web sur une pile d’applications qui n’est pas encore définie dans Azure. Ce démarrage rapide montre comment prendre un conteneur Docker existant avec une application Go et l’exécuter sur Azure App Service. Vous allez créer l’application web à l’aide d’[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-the-container"></a>Créer une application Web App pour le conteneur

Créez une [application web](../app-service-web-overview.md) dans le plan App Service `myAppServicePlan` avec la commande [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). N’oubliez pas de remplacer `<app name>` par un nom d’application globalement unique.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/appservice-go-quickstart
```

Dans la commande précédente, `--deployment-container-image-name` pointe vers l’image publique Docker Hub [microsoft/appservice-go-quickstart](https://hub.docker.com/r/microsoft/appservice-go-quickstart).

Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="get-data-from-the-apps-endpoint"></a>Obtenir des données à partir du point de terminaison de l’application

À l’aide de la commande `curl`, contactez le point de terminaison API REST fourni par l’application du conteneur.

```bash
curl -X GET http://<app_name>.azurewebsites.net:8080/hello
```

```output
Hello world!
```

**Félicitations !** Vous avez déployé une image Docker personnalisée exécutant une application Go dans Azure Web Apps for Containers.

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [Utiliser une image Docker personnalisée](tutorial-custom-docker-image.md)
