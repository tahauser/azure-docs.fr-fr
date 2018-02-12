---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: d5dfe6ab6d53d450f7c75d376d630558ee03f698
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
Créer une [application web](../articles/app-service/app-service-web-overview.md) dans le plan App Service `myAppServicePlan`

Dans Cloud Shell, vous pouvez utiliser la commande [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Dans l’exemple suivant, remplacez *\<app_name>* par un nom d’application unique (les caractères autorisés sont `a-z`, `0-9` et `-`). 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

Une fois l’application web créée, Azure CLI affiche des informations similaires à celles de l’exemple suivant :

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Vous avez créé une application web vide, avec le déploiement Git activé.

> [!NOTE]
> L’URL du Git distant est indiquée dans la propriété `deploymentLocalGitUrl`, avec le format `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Enregistrez cette URL, car vous en aurez besoin ultérieurement.
>

Accédez à la nouvelle application web.

```
http://<app_name>.azurewebsites.net
```

Voici à quoi doit ressembler votre nouvelle application web :
