---
title: Profiler des applications web ASP.NET Core Azure Linux avec Application Insights Profiler | Microsoft Docs
description: Vue d’ensemble du concept et didacticiel pas à pas pour l’activer
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 63a7ceacffe1ee33227d3a8272dda7de7b3b1135
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profiler des applications web ASP.NET Core Azure Linux avec Application Insights Profiler

Actuellement, cette fonctionnalité est uniquement disponible en tant que préversion

Découvrez combien de temps vous consacrez à chaque méthode de votre application web en production quand vous utilisez [Application Insights](app-insights-overview.md). Profiler est désormais disponible pour les applications web ASP.NET Core hébergées dans Linux sur App Services. Ce guide fournit des instructions détaillées sur la façon dont les traces du profileur peuvent être collectées pour les applications web ASP.NET core Linux.

Après avoir effectué cette procédure pas à pas, votre application collecte des traces du profileur semblables à la capture d’écran ci-dessous. Dans cet exemple, la trace du profileur indique qu’une requête web particulière est lente, car la plupart du temps est passé en attente. Le chemin d’accès réactif dans le code qui a ralenti l’application est précédé de l’icône flamme. Cet exemple montre que la méthode `About` dans `HomeController` a ralenti l’application web, car elle appelait `Thread.Sleep`.

![Traces du profileur](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="pre-requisites"></a>Conditions préalables
Les instructions ci-dessous doivent être appliquées à tous les environnements de développement Windows, Linux et Mac :

* Installez [.NET Core SDK 2.1.2 ou une version ultérieure](https://www.microsoft.com/net/download/windows/build)
* Installez Git suivant les instructions sur [Démarrage rapide - Installation de Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

## <a name="setup-project-locally"></a>Configurer le projet localement

1. Ouvrez une invite de commandes sur votre machine. Les instructions ci-dessous sont valables pour tous les environnements de développement Windows, Linux et Mac.

2. Créez une application web ASP.NET Core MVC
    ```
    dotnet new mvc -n LinuxProfilerTest
    ```
3. Modifiez le répertoire dans l’invite de commandes vers le dossier racine du projet

4. Ajoutez le package Nuget pour collecter les traces du profileur.
    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```
5. Ajoutez une ligne de code pour retarder de quelques secondes de façon aléatoire dans HomeController.cs

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```
6. Enregistrez et validez vos modifications dans le référentiel local

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-azure-app-service-for-hosting-your-project"></a>Créer un Azure App Service pour héberger votre projet
1. Créez un environnement App Services Linux

    ![Créez les App Services Linux](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Créez les informations d’identification de déploiement. Prenez note de votre mot de passe, car vous en aurez besoin plus tard lors du déploiement de votre application.

    ![Créez les informations d’identification de déploiement](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Choisissez une option de déploiement. Configurez un référentiel Git local dans l’application web en suivant les instructions sur le portail Azure. Un référentiel Git est créé automatiquement.

    ![Configurez le référentiel Git](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

Plus d’options de déploiement sont disponibles [ici](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)

## <a name="deploy-your-project"></a>Déployez votre projet

1. Dans l’invite de commandes, accédez au dossier racine de votre projet. Ajoutez le référentiel distant Git de façon à pointer vers celui d’App Services :

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```
    * Utilisez le nom d’utilisateur de l’étape « créez les informations d’identification de déploiement ».
    * Utilisez le nom de l’application de l’étape « créez le service de l’application ».

2. Déployez le projet en envoyant les modifications vers Azure

    ```
    git push azure master
    ```
Vous devez voir des informations semblables à ce qui suit :

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Ajouter Application Insights pour contrôler vos applications web
1. [Création d’une ressource Application Insights](./app-insights-create-new-resource.md)
2. Copiez l’iKey de la ressource Application Insights et définissez les paramètres suivants dans vos App services

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Définissez les paramètres de l’application](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    La modification des paramètres de l’application redémarre automatiquement le site. Une fois que les nouveaux paramètres sont appliqués, le profileur commence à s’exécuter immédiatement pendant 2 minutes. Ensuite, il s’exécute pendant 2 minutes toutes les heures.

3. Générer du trafic vers votre site web. Vous pouvez actualiser la page ```About``` du site plusieurs fois.

4. Attendez 2 à 5 minutes que les événements soit agrégés à Application Insights.

5. Accédez au volet de niveau de performance d’Application Insights dans le portail Azure. Vous verrez des traces du profileur disponibles en bas à droite.

    ![Affichage des traces](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Problèmes connus

### <a name="enable-button-in-profiler-configuration-pane-does-not-work"></a>Le bouton Activer dans le volet Configuration du profileur ne fonctionne pas
**Si vous hébergez votre application à l’aide de App Services Linux, il est inutile de réactiver le profileur dans le volet de niveau de performance du portail Application Insights. Inclure le package NuGet dans le projet et définir l’iKey Application Insights dans les paramètres des applications suffit pour activer le profileur**.

Si vous suivez le workflow d’activation du [Profileur Application Insights pour Windows](./app-insights-profiler.md) pour cliquer sur **Activer** dans le volet Configurer le profileur, vous recevez un message d’erreur car le bouton tente d’installer la version de l’agent du profileur dans l’environnement Linux.

Nous travaillons sur la résolution de ce problème dans le cadre de l’expérience d’activation.

![Vous n’êtes pas obligé d’activer le profileur à nouveau dans le volet de niveau de performance pour faire fonctionner le profileur sur App Services Linux](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>Étapes suivantes
Si vous utilisez des conteneurs personnalisés hébergés par App Services, suivez les instructions de la page [Activer le profileur de service pour l’application ASP.NET Core en conteneur](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) pour activer Application Insights Profiler.

Si vous avez des problèmes ou des suggestions, veuillez les signaler sur le référentiel GitHub : [ApplicationInsights-Profiler-AspNetCore: Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues)
