---
title: "Créer une application Ruby et la déployer sur App Service sur Linux | Microsoft Docs"
description: "Apprenez à créer des applications Ruby avec App Service sur Linux."
keywords: azure app service, linux, oss, ruby
services: app-service
documentationcenter: 
author: SyntaxC4
manager: cfowler
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: db3086724c22e485e2a9a69c36a990fc5b8016a9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-ruby-app-in-app-service-on-linux"></a>Créer une application Ruby dans App Service sur Linux

[App Service sur Linux](app-service-linux-intro.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce guide de démarrage rapide vous montre comment créer une application Ruby on Rails de base puis la déployer dans Azure Web App sur Linux.

![Hello-world](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Installez Ruby 2.4.1 ou version ultérieure</a>
* <a href="https://git-scm.com/" target="_blank">Installez Git</a>

## <a name="download-the-sample"></a>Téléchargez l’exemple

Dans une fenêtre de terminal, exécutez la commande ci-après pour cloner le référentiel de l’exemple d’application sur votre ordinateur local :

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Exécuter l’application localement

Exécutez le serveur Rails afin que l’application fonctionne. Passez au répertoire *hello-world* et démarrez le serveur avec la commande `rails server`.

```bash
cd hello-world\bin
rails server
```

À l’aide de votre navigateur web, accédez à `http://localhost:3000` pour tester l’application en local.

![Hello-world](./media/quickstart-ruby/hello-world.png)

## <a name="modify-app-to-display-welcome-message"></a>Modification de l’application pour afficher un message d’accueil

Modifiez l’application afin qu’elle affiche un message d’accueil. Tout d’abord, vous devez configurer un itinéraire en modifiant le fichier *~/workspace/ruby-docs-hello-world/config/routes.rb* pour qu’il inclue un itinéraire nommé `hello`.

  ```ruby
  Rails.application.routes.draw do
      #For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
      root 'application#hello'
  end
  ```

Modifiez le contrôleur de l’application de sorte pour qu’il renvoie le message au format HTML au navigateur. 

Ouvrez *~/workspace/hello-world/app/controllers/application_controller.rb* pour le modifier. Modifiez la classe `ApplicationController` pour qu’elle ressemble à l’exemple de code suivant :

  ```ruby
  class ApplicationController > ActionController :: base
    protect_from_forgery with: :exception
    def hello
      render html: "Hello, world from Azure Web App on Linux!"
    end
  end
  ```

Votre application est désormais configurée. À l’aide de votre navigateur web, accédez à `http://localhost:3000` pour vérifier la page d’accueil racine.

![Hello World configurée](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

## <a name="create-a-ruby-web-app-on-azure"></a>Créer une application web Ruby sur Azure

Un groupe de ressources est obligatoire pour contenir les ressources nécessaires pour votre application web. Pour créer un groupe de ressources, utilisez la commande [`az group create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create).

```azurecli-interactive
az group create --location westeurope --name myResourceGroup
```

Utilisez la commande [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) pour créer un plan App Service pour votre application web.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --is-linux
```

Ensuite, utilisez la commande [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) pour créer l’application web qui utilise le plan de service nouvellement créé. Notez que le runtime est défini sur `ruby|2.3`. N’oubliez pas de remplacer `<app name>` par un nom d’application unique.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> \
--runtime "ruby|2.3" --deployment-local-git
```

La sortie de la commande révèle des informations sur l’application web qui vient d’être créée, ainsi que sur l’URL de déploiement. Elle doit être semblable à l’exemple suivant. Copiez l’URL pour une utilisation ultérieure dans ce didacticiel.

```bash
https://<deployment user name>@<app name>.scm.azurewebsites.net/<app name>.git
```

Une fois l’application web créée, une page **Vue d’ensemble** est disponible à l’affichage. Naviguez vers cet emplacement. La page de démarrage suivante s’affiche :

![Page de démarrage](./media/quickstart-ruby/splash-page.png)


## <a name="deploy-your-application"></a>Déployer votre application

Exécutez les commandes suivantes pour déployer l’application locale sur votre site web Azure :

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

Vérifiez que les opérations de déploiement à distance réussissent. Les commandes produisent une sortie semblable au texte suivant :

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

Une fois le déploiement terminé, redémarrez votre application web pour que le déploiement prenne effet en utilisant la commande [`az webapp restart`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_restart), comme présenté ici :

```azurecli-interactive
az webapp restart --name <app name> --resource-group myResourceGroup
```

Accédez à votre site et vérifiez les résultats.

```bash
http://<app name>.azurewebsites.net
```

![application web mise à jour](./media/quickstart-ruby/hello-world-updated.png)

> [!NOTE]
> Si vous tentez de parcourir les résultats du site pendant que l’application redémarre, vous recevrez une erreur HTTP `Error 503 Server unavailable`. Le redémarrage complet peut prendre quelques minutes.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [Build a Ruby and MySQL web app in Azure App Service on Linux](tutorial-ruby-mysql-app.md) (Créer une application web Ruby et MySQL dans Azure App Service sur Linux)
