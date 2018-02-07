---
title: "Créer une application web ASP.NET Core dans Azure | Microsoft Docs"
description: "Découvrez comment exécuter des applications web dans Azure App Service en déployant l’application web ASP.NET par défaut."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/14/2017
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: 698f23507da0707a4612f8d33fe7e2995429f361
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Créer une application web ASP.NET Core dans Azure

> [!NOTE]
> Cet article explique comment déployer une application sur App Service sous Windows. Pour déployer une application App Service sur _Linux_, consultez [Créer et déployer une application web .NET Core sur App Service sur Linux](./containers/quickstart-dotnetcore.md).
>

[Azure Web Apps](app-service-web-overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques.  Ce guide de démarrage rapide montre comment déployer votre première application web ASP.NET Core sur Azure Web Apps. Lorsque vous aurez terminé, vous disposerez d’un groupe de ressources constitué d’un plan App Service et d’une application web Azure avec une application web déployée.

> [!NOTE]
> Si vous souhaitez savoir comment créer et déployer une application web ASP.NET Framework, consultez [cet article](app-service-web-get-started-dotnet-framework.md). 
>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>configuration requise

Pour suivre ce didacticiel :

* Installez <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> avec les charges de travail suivantes :
    - **Développement web et ASP.NET**
    - **Développement Azure**

    ![Développement web et ASP.NET et Développement Azure (sous Web et cloud)](media/app-service-web-tutorial-dotnet-sqldatabase/workloads.png)

## <a name="create-an-aspnet-core-web-app"></a>Créez une application web ASP.NET Core

Dans Visual Studio, créez un projet en sélectionnant **Fichier > Nouveau > Projet**. 

Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C# > Web > Application web ASP.NET Core**.

Nommez l’application _myFirstAzureWebApp_, puis sélectionnez **OK**.
   
![Boîte de dialogue Nouveau projet](./media/app-service-web-get-started-dotnet/new-project.png)

Vous pouvez déployer n’importe quel type d’application web ASP.NET Core dans Azure. Pour ce guide de démarrage rapide, sélectionnez le modèle **Application Web** et vérifiez que l’authentification est définie sur **Aucune authentification**.
      
Sélectionnez **OK**.

![Boîte de dialogue New ASP.NET Project](./media/app-service-web-get-started-dotnet/razor-pages-aspnet-dialog.png)

Une fois le projet ASP.NET Core créé, la page d’accueil ASP.NET Core s’affiche et fournit de nombreux liens vers des ressources pour vous aider à commencer. 

![Page d’accueil](./media/app-service-web-get-started-dotnet/aspnet-core-welcome-page.png)

Dans le menu, sélectionnez **Déboguer > Exécuter sans débogage** pour exécuter l’application web localement.

![Exécuter l’application localement](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-to-azure"></a>Publication dans Azure

Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **myFirstAzureWebApp**, puis sélectionnez **Publier**.

![Publier à partir de l’Explorateur de solutions](./media/app-service-web-get-started-dotnet/right-click-publish.png)

Assurez-vous que **Microsoft Azure App Service** est sélectionné, puis sélectionnez **Publier**.

![Publier à partir de la page de présentation du projet](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

Cette opération affiche la boîte de dialogue **Créer App Service**, qui vous permet de créer toutes les ressources Azure nécessaires pour exécuter l’application web ASP.NET Core dans Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Dans la boîte de dialogue **Créer App Service**, sélectionnez **Ajouter un compte**, puis connectez-vous à votre abonnement Azure. Si vous êtes déjà connecté, sélectionnez le compte qui contient l’abonnement souhaité dans la liste déroulante.

> [!NOTE]
> Si vous êtes déjà connecté, ne sélectionnez pas encore **Créer**.
>
>
   
![Connexion à Azure](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

En regard de **Groupe de ressources**, sélectionnez **Nouveau**.

Nommez le groupe de ressources **myResourceGroup**, puis sélectionnez **OK**.

## <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

En regard de **Plan App Service**, sélectionnez **Nouveau**. 

Dans la boîte de dialogue **Configurer le plan App Service**, utilisez les paramètres spécifiés dans la table sous la capture d’écran ci-après.

![Créer un plan App Service](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

| Paramètre | Valeur suggérée | DESCRIPTION |
|-|-|-|
|Plan App Service| myAppServicePlan | Nom du plan App Service. |
| Lieu | Europe de l'Ouest | Centre de données dans lequel l’application web est hébergée. |
| Taille | Gratuit | Le [niveau tarifaire](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) détermine les fonctionnalités d’hébergement. |

Sélectionnez **OK**.

## <a name="create-and-publish-the-web-app"></a>Créer et publier l’application web

Dans **Nom de l’application web**, tapez un nom d’application unique (les caractères valides sont `a-z`, `0-9` et `-`) ou acceptez le nom unique généré automatiquement. L’URL de l’application web est `http://<app_name>.azurewebsites.net`, où `<app_name>` correspond au nom de votre application web.

Sélectionnez **Créer** pour commencer à créer les ressources Azure.

![Configurer le nom de l’application web](./media/app-service-web-get-started-dotnet/web-app-name.png)

Une fois que l’Assistant a terminé, il publie l’application web ASP.NET Core sur Azure, puis il lance l’application dans le navigateur par défaut.

![Application web ASP.NET publiée dans Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

Le nom de l’application web spécifié à [l’étape de création et de publication](#create-and-publish-the-web-app) est utilisé en tant que préfixe d’URL au format `http://<app_name>.azurewebsites.net`.

Félicitations, votre application web ASP.NET Core s’exécute en temps réel dans Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Mise à jour de l’application et redéploiement

Dans **l’Explorateur de solutions**, ouvrez _Pages/Index.cshtml_.

Recherchez la balise HTML `<div id="myCarousel" class="carousel slide" data-ride="carousel" data-interval="6000">` vers le début, puis remplacez la totalité de l’élément par le code suivant :

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Pour effectuer un redéploiement dans Azure, cliquez avec le bouton droit sur le projet **myFirstAzureWebApp** dans **l’Explorateur de solutions**, puis sélectionnez **Publier**.

Dans la page de publication, sélectionnez **Publier**.

Une fois la publication terminée, Visual Studio lance un navigateur en accédant à l’URL de l’application web.

![Application web ASP.NET mise à jour dans Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-web-app"></a>Gérer l’application web Azure

Accédez au <a href="https://portal.azure.com" target="_blank">Portail Azure</a> pour gérer l’application web.

Dans le menu de gauche, sélectionnez **App Services**, puis sélectionnez le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/app-service-web-get-started-dotnet/access-portal.png)

Vous voyez apparaître la page Vue d’ensemble de votre application web. Ici, vous pouvez également des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). 

![Panneau App Service sur le portail Azure](./media/app-service-web-get-started-dotnet/web-app-blade.png)

Le menu de gauche fournit différentes pages vous permettant de configurer votre application. 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [Build a .NET Core and SQL Database web app in Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md) (Créer une application web .NET Core et SQL Database dans Azure App Service)
