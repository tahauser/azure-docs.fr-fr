---
title: "Déploiement continu à partir d’un Registre de conteneurs Docker avec Web App pour conteneurs- Azure | Microsoft Docs"
description: "Comment configurer le déploiement continu à partir d’un Registre de conteneurs Docker dans Web App pour conteneurs."
keywords: azure app service, linux, docker, acr, oss
services: app-service
documentationcenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: e61c767ada31fc32e28bfd9a2a4e843e9ca88053
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Déploiement continu avec Web App pour conteneurs

Dans ce didacticiel, vous allez configurer le déploiement continu d’une image de conteneur personnalisée à partir des référentiels [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) gérés ou du [hub Docker](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com)

## <a name="enable-container-continuous-deployment-feature"></a>Activer la fonctionnalité de déploiement continu de conteneur

Vous pouvez activer la fonctionnalité de déploiement continu avec [l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) et en exécutant la commande suivante

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

Dans le **[portail Azure](https://portal.azure.com/)**, cliquez sur l’option **App Service** sur le côté gauche de la page.

Cliquez sur le nom de l’application pour laquelle vous souhaitez configurer le déploiement continu Docker Hub.

Dans **Conteneur Docker**, sélectionnez Activé, puis cliquez sur Enregistrer pour activer le déploiement continu.

![insérer une image de Paramètres de l’application](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-webhook-url"></a>Préparer l’URL du webhook

Vous pouvez obtenir l’URL du Webhook avec [l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) et en exécutant la commande suivante

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Pour l’URL du Webhook, vous devez disposer du point de terminaison suivant : `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Vous pouvez obtenir votre `publishingusername` et `publishingpwd` en téléchargeant le profil de publication de l’application web à l’aide du portail Azure.

![insérer une image de l’ajout du webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-web-hook"></a>Ajouter un webhook

### <a name="azure-container-registry"></a>Azure Container Registry

Dans le panneau de portail de votre registre, cliquez sur **Webhooks** et créez un nouveau webhook en cliquant sur **Ajouter**. Dans le panneau **Create webhook** (Créer un webhook), donnez un nom à votre webhook. Pour l’URI du webhook, vous devez fournir l’URL obtenue à l’**étape 3**.

Veillez à définir l’étendue comme référentiel qui contient votre image de conteneur.

![insérer une image de webhook](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

Lorsque l’image est mise à jour, l’application web est mise à jour automatiquement avec la nouvelle image.

### <a name="docker-hub"></a>Hub Docker

Dans votre page Docker Hub, cliquez sur **Webhooks**, puis sur **Créer un Webhook**.

![insérer une image de l’ajout du webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Pour l’URL du webhook, vous devez fournir l’URL obtenue à l’**étape 3**.

![insérer une image de l’ajout du webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Lorsque l’image est mise à jour, l’application web est mise à jour automatiquement avec la nouvelle image.

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est-ce qu’Azure App Service sur Linux ?](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Utiliser .NET Core dans Azure App Service sur Linux](quickstart-dotnetcore.md)
* [Utiliser Ruby dans Azure App Service sur Linux](quickstart-ruby.md)
* [Guide pratique pour utiliser une image Docker personnalisée pour Web App pour conteneurs](quickstart-docker-go.md)
* [Forum aux questions sur Azure App Service Web App for Containers](./app-service-linux-faq.md)
* [Gérer Web App for Containers à l’aide d’Azure CLI 2.0](./app-service-linux-cli.md)
