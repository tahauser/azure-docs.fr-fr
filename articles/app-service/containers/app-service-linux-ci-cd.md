---
title: Déploiement continu à partir d’un registre de conteneurs Docker avec Web App pour conteneurs - Azure | Microsoft Docs
description: Comment configurer le déploiement continu à partir d’un registre de conteneurs Docker dans Web App pour conteneurs.
keywords: azure app service, linux, docker, acr, oss
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: ac35dbd041de50ab8aae1a0fb4c00fe3917a7297
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Déploiement continu avec Web App pour conteneurs

Dans ce didacticiel, vous allez configurer le déploiement continu d’une image conteneur personnalisée à partir des référentiels [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) managés ou du [Docker Hub](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [Portail Azure](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>Activer la fonctionnalité de déploiement continu

Activez la fonctionnalité de déploiement continu avec [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) et en exécutant la commande suivante :

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

Dans le [portail Azure](https://portal.azure.com/), sélectionnez l’option **App Service** sur le côté gauche de la page.

Sélectionnez le nom de l’application pour laquelle vous souhaitez configurer le déploiement continu Docker Hub.

Dans la page **Conteneur Docker**, sélectionnez **Activé**, puis sélectionnez **Enregistrer** pour activer le déploiement continu.

![Capture d’écran du paramètre d’application](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Préparer l’URL du webhook

Vous pouvez obtenir l’URL du Webhook avec [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) et en exécutant la commande suivante :

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Pour l’URL du Webhook, vous devez disposer du point de terminaison suivant : `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Vous pouvez obtenir votre `publishingusername` et `publishingpwd` en téléchargeant le profil de publication de l’application web à l’aide du portail Azure.

![Capture d’écran de l’ajout de webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Appeler un webhook

### <a name="azure-container-registry"></a>Azure Container Registry

1. Sur votre page du portail du registre, sélectionnez **Webhooks**.
2. Pour créer un nouveau webhook, sélectionnez **Ajouter**. 
3. Dans le volet **Créer un webhook**, donnez un nom à votre webhook. Pour l’URI du webhook, indiquez l’URL obtenue dans la section précédente.

Veillez à définir l’étendue comme référentiel qui contient votre image conteneur.

![Capture d'écran du webhook](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

Lorsque l’image est mise à jour, l’application Web est mise à jour automatiquement avec la nouvelle image.

### <a name="docker-hub"></a>Hub Docker

Dans votre page Docker Hub, sélectionnez **Webhooks**, puis sur **CRÉER UN WEBHOOK**.

![Capture d’écran de l’ajout de webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Pour l’URL du webhook, indiquez l’URL que vous avez obtenue précédemment.

![Capture d’écran de l’ajout de webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Lorsque l’image est mise à jour, l’application Web est mise à jour automatiquement avec la nouvelle image.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure App Service sur Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Créer une application web .NET Core dans App Service sur Linux](quickstart-dotnetcore.md)
* [Créer une application Web Ruby dans App Service sur Linux](quickstart-ruby.md)
* [Déployer une application Web Docker/Go dans Web App pour conteneurs](quickstart-docker-go.md)
* [Questions fréquentes (FAQ) sur Azure App Service sur Linux](./app-service-linux-faq.md)
* [Gérer Web App pour conteneurs à l’aide d’Azure CLI](./app-service-linux-cli.md)
