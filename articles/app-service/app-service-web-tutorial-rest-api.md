---
title: API RESTful avec CORS dans Azure App Service | Microsoft Docs
description: "Découvrir comment Azure App Service vous aide à héberger vos API RESTful avec prise en charge de CORS."
services: app-service\api
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: 7420e92bc929808f074e9be00dfbcb7d8476654a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="host-a-restful-api-with-cors-in-azure-app-service"></a>Héberger une API RESTful avec CORS dans Azure App Service

[Azure App Service](app-service-web-overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. De plus, App Service intègre la prise en charge du [partage des ressources cross-origin (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) pour les API RESTful. Ce didacticiel montre comment déployer une application ASP.NET Core API sur App Service avec prise en charge CORS. Vous configurez l’application à l’aide d’outils en ligne de commande, et vous la déployez à l’aide de Git. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer des ressources App Service à l’aide d’Azure CLI
> * Déployer une API RESTful sur Azure à l’aide de Git
> * Activer la prise en charge de CORS sur App Service

Vous pouvez suivre les étapes de ce didacticiel sur macOS, Linux, Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel :

* [Installer Git](https://git-scm.com/).
* [Installer .NET CoreNET](https://www.microsoft.com/net/core/).

## <a name="create-local-aspnet-core-app"></a>Créer l’application ASP.NET Core locale

Cette étape consiste à configurer le projet ASP.NET Core local. App Service prend en charge le même flux de travail pour les API écrites dans d’autres langages.

### <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Dans la fenêtre de terminal, `cd` vers un répertoire de travail.  

Exécutez la commande suivante pour cloner l’exemple de référentiel : 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

Ce référentiel contient une application créée en fonction du didacticiel suivant : [Pages d’aide d’API web ASP.NET Core à l’aide de Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio). Elle utilise un générateur de Swagger pour traiter l’[interface utilisateur Swagger](https://swagger.io/swagger-ui/) et le point de terminaison JSON de Swagger.

### <a name="run-the-application"></a>Exécution de l'application

Exécutez la commande suivante pour installer les packages requis, migrer les bases de données et démarrer l’application.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

Accédez à `http://localhost:5000/swagger` dans un navigateur pour vous familiariser avec l’interface utilisateur Swagger.

![API ASP.NET Core exécuté en local](./media/app-service-web-tutorial-rest-api/local-run.png)

Accédez à `http://localhost:5000/api/todo` pour visualiser une liste des tâches JSON.

Accédez à `http://localhost:5000` et familiarisez-vous avec l’application de navigateur. Ensuite, vous allez pointer l’application de navigateur vers une API distante dans l’App Service pour tester la fonctionnalité CORS. Le code pour l’application de navigateur se trouve dans le répertoire _wwwroot_ du référentiel.

Pour arrêter ASP.NET Core à tout moment, appuyez sur `Ctrl+C` dans le terminal.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Déployer des applications dans Azure

Dans cette étape, vous déployez votre application .NET Core connectée à SQL Database sur App Service.

### <a name="configure-local-git-deployment"></a>Configurer le déploiement Git local

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Créer une application web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure

Accédez à `http://<app_name>.azurewebsites.net/swagger` dans un navigateur et familiarisez-vous avec l’interface utilisateur Swagger.

![API ASP.NET Core exécuté dans Azure App Service](./media/app-service-web-tutorial-rest-api/azure-run.png)

Accédez à `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` pour voir le _swagger.json_ de l’API déployée.

Accédez à `http://<app_name>.azurewebsites.net/api/todo` pour voir l’API déployée en fonctionnement.

## <a name="add-cors-functionality"></a>Ajoutez des fonctionnalités CORS

Ensuite, activez la prise en charge intégrée de CORS dans App Service pour votre API.

### <a name="test-cors-in-sample-app"></a>Testez CORS dans l’exemple d’application

Dans votre référentiel local, ouvrez _wwwroot/index.html_.

Dans la ligne 51, définissez la variable `apiEndpoint` sur l’URL de l’API déployée (`http://<app_name>.azurewebsites.net`). Remplacez _\<appname >_ avec le nom de votre application dans Azure App Service.

Dans la fenêtre de votre terminal local, exécutez à nouveau l’exemple d’application.

```bash
dotnet run
```

Accédez à l’application de navigateur à `http://localhost:5000`. Ouvrez la fenêtre d’outils de développement dans votre navigateur (`Ctrl`+`Shift`+`i` dans Chrome pour Windows) et inspectez l’onglet **Console**. Vous devriez maintenant voir le message d’erreur, `No 'Access-Control-Allow-Origin' header is present on the requested resource`.

![Erreur CORS dans le navigateur client](./media/app-service-web-tutorial-rest-api/cors-error.png)

En raison de l’incompatibilité de domaine entre l’application de navigateur (`http://localhost:5000`) et la ressource à distance (`http://<app_name>.azurewebsites.net`), et du fait que votre API dans App Service n’envoie pas l’en-tête `Access-Control-Allow-Origin`, votre navigateur a empêché le chargement du contenu inter-domaines dans votre application de navigateur.

En production, votre application de navigateur aurait une URL publique au lieu de l’URL de l’hôte local, mais la façon d’activer CORS est identique pour les deux types d’URL.

### <a name="enable-cors"></a>Activez CORS 

Dans Cloud Shell, activez CORS pour votre URL de client à l’aide de la commande [`az resource update`](/cli/azure/resource#az_resource_update). Remplacez l’espace réservé _&lt;appname>_.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.cors.allowedOrigins="['http://localhost:5000']" --api-version 2015-06-01
```

Vous pouvez définir plusieurs URL de client dans `properties.cors.allowedOrigins` (`"['URL1','URL2',...]"`). Vous pouvez également activer toutes les URL client avec `"['*']"`.

### <a name="test-cors-again"></a>Testez CORS à nouveau

Actualisez l’application de navigateur à `http://localhost:5000`. Le message d’erreur dans la fenêtre **Console** a désormais disparu, et vous pouvez afficher les données de l’API déployée et interagir avec elles. Votre API distante prend désormais en charge CORS vers votre application de navigateur exécuté en local. 

![Succès de CORS dans le navigateur client](./media/app-service-web-tutorial-rest-api/cors-success.png)

Félicitations, vous exécutez une API dans Azure App Service avec prise en charge de CORS.

## <a name="app-service-cors-vs-your-cors"></a>Le CORS App Service et votre CORS

Vous pouvez utiliser vos propres utilitaires CORS au lieu de CORS App Service pour plus de souplesse. Par exemple, vous souhaitez peut-être spécifier différentes origines autorisées pour des méthodes ou des itinéraires différents. Étant donné que CORS App Service vous permet de spécifier un ensemble d’origines acceptées pour tous les itinéraires et méthodes de l’API, vous pouvez utiliser votre propre code CORS. ASP.NET Core [L’activation de demandes de Cross-Origin (CORS)](/aspnet/core/security/cors).

> [!NOTE]
> N’essayez pas d’utiliser conjointement CORS App Service et votre propre code CORS. Utilisés conjointement, CORS App Service est prioritaire et votre propre code CORS n’a aucun effet.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer des ressources App Service à l’aide d’Azure CLI
> * Déployer une API RESTful sur Azure à l’aide de Git
> * Activer la prise en charge de CORS sur App Service

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application web.

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à des applications web Azure](app-service-web-tutorial-custom-domain.md)
