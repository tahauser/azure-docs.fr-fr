---
title: Azure Application Insights pour ASP.NET Core | Microsoft Docs
description: "Surveiller la disponibilité, les performances et l’utilisation.des applications Web."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: e9fb3e68db66449d9ca3b43e6974910cb9477e62
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights pour ASP.NET Core

Azure Application Insights assure une surveillance approfondie de votre application web jusqu’au niveau du code. Azure Application Insights vous permet d’analyser facilement la disponibilité, les performances et l’utilisation. De plus, vous pouvez rapidement identifier et diagnostiquer les erreurs dans votre application sans attendre qu’un utilisateur ne les signale.

Cet article vous explique comment créer un exemple d’application de [pages Razor](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) ASP.NET Core dans Visual Studio et démarrer la surveillance avec Azure Application Insights.

## <a name="prerequisites"></a>configuration requise

- Kit de développement logiciel (SDK) NET Core 2.0.0 ou ultérieur.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) version 15.3 ou ultérieur avec la charge de travail Développement ASP.NET et web.

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Créer un projet ASP.NET Core dans Visual Studio

1. Cliquez avec le bouton droit et démarrez **Visual Studio 2017** en tant qu’administrateur.
2. Sélectionnez **Fichier** > **Nouveau** > **Projet** (Ctrl-Shift-N).

   ![Capture d’écran du menu Fichier Nouveau Projet de Visual Studio](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. Développez **Visual C#** > sélectionnez **.NET Core** > **Application web ASP.NET Core**. Renseignez **Nom** > **Nom de solution** > cochez **Créer un référentiel Git**.

   ![Capture d’écran de l’assistant Fichier Nouveau Projet de Visual Studio](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. Sélectionnez **.Net Core** > **ASP.NET Core 2.0** **Application web** > **OK**.

    ![Capture d’écran du menu Fichier Nouveau Projet Sélection de Visual Studio](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>Ajouter Application Insights Telemetry

1. Sélectionnez **Projet** > **Ajouter Application Insights Telemetry...** (Sinon, cliquez avec le bouton droit sur **Services connectés** > Ajouter un service connecté.)

    ![Capture d’écran du menu Fichier Nouveau Projet Sélection de Visual Studio](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. Sélectionnez **Démarrer gratuitement**.

    ![Capture d’écran du menu Fichier Nouveau Projet Sélection de Visual Studio](./media/app-insights-asp-net-core/0005-start-free.png)

3. Sélectionnez des options appropriées pour **Abonnement** > **Ressource** > et décidez d’autoriser ou non la collecte de données au-delà de 1 Go par mois > **S’inscrire**.

    ![Capture d’écran du menu Fichier Nouveau Projet Sélection de Visual Studio](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>Modifications apportées à votre projet

Application Insights a une très faible surcharge. Pour passer en revue les modifications apportées à votre projet en ajoutant Application Insights Telemetry :

Sélectionnez **Vue** > **Team Explorer** (Ctrl+\, Ctrl+M) > **Projet** > **Modifications**

- Quatre modifications au total :

  ![Capture d’écran des fichiers modifiés par l’ajout d’Application Insights](./media/app-insights-asp-net-core/0007-changes.png)

- Un nouveau fichier est créé :

   **ConnectedService.json**

  ![Capture d’écran des fichiers modifiés par l’ajout d’Application Insights](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- Trois fichiers sont modifiés :

  **appsettings.json**

   ![Capture d’écran des fichiers modifiés par l’ajout d’Application Insights](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![Capture d’écran des fichiers modifiés par l’ajout d’Application Insights](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.cs**

   ![Capture d’écran des fichiers modifiés par l’ajout d’Application Insights](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>Transactions synthétiques avec PowerShell

Pour générer du trafic test, vous pouvez démarrer votre application, puis cliquer sur les liens manuellement. Cependant, il est généralement utile de créer un transaction synthétique simple dans PowerShell.

1. Exécutez votre application en cliquant sur IIS Express. ![Capture d’écran de l’icône IIS Express de Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Copiez l’URL de la barre d’adresses du navigateur. Elle est au format http://localhost:{numéro de port aléatoire}

   ![Capture d’écran de la barre d’adresses URL du navigateur](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Exécutez la boucle PowerShell suivante pour créer 100 transactions synthétiques pour votre application test. Modifiez le numéro de port après **localhost:** pour qu’il corresponde à l’URL copiée à l’étape précédente.

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Ouvrir le portail Application Insights

Après avoir exécuté PowerShell comme expliqué dans la section précédente, démarrez Application Insights pour voir les transactions et confirmer que les données sont collectées. 

Dans le menu Visual Studio, sélectionnez **Projet** > **Application Insights** > **Ouvrir le portail Application Insights**

   ![Capture d’écran de la vue d’ensemble Application Insights](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> Dans la capture d’écran exemple ci-dessus **Flux temps réel**, **Temps de chargement de la page consultée** et **Requêtes échouées** ne sont pas collectés pour le moment. La section suivante vous guide pour les ajouter. Si vous collectez déjà **Flux temps réel** et **Temps de chargement de la page**, suivez uniquement les étapes pour **Requêtes échouées**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Collecter Requêtes échouées, Flux temps réel et Temps de chargement de la page

### <a name="failed-requests"></a>Demandes ayant échoué

Techniquement, les **Requêtes échouées** sont collectées, mais aucun échec ne s’est produit pour le moment. Pour accélérer le processus, vous pouvez ajouter une exception personnalisée au projet existant pour simuler une exception réelle. Si votre application est toujours en cours d’exécution dans Visual Studio, **arrêtez le débogage** (MAJ + F5) avant de continuer.

1. Dans **l’Explorateur de solutions** > développez **Pages** > **About.cshtml** > ouvrez **About.cshtml.cs**.

   ![Capture d’écran de l’Explorateur de solutions de Visual Studio](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. Ajoutez une Exception sous ``Message=`` > Enregistrer la modification dans le fichier.

   ```C#
   throw new Exception("Test Exception");
   ```

   ![Capture d’écran du code d’exception](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>Flux temps réel

Pour accéder à la fonctionnalité Flux temps réel d’Application Insights avec la mise à jour ASP.NET Core pour les packages NuGet **Microsoft.ApplicationInsights.AspNetCore 2.2.0**.

Dans Visual Studio, sélectionnez **Projet** > **Gérer les packages NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > Version **2.2.0** > **Mise à jour**.

  ![Capture d’écran du Gestionnaire de package NuGet](./media/app-insights-asp-net-core/0017-update-nuget.png)

Plusieurs invites de confirmation apparaissent. Lisez-les et acceptez-les le cas échéant.

### <a name="page-view-load-time"></a>Temps de chargement de la page consultée

1. Dans Visual Studio, accédez à **l’Explorateur de solutions** > **Pages** > deux fichiers doivent être modifiés : **_Layout.cshtml** et **_ ViewImports.cshtml**

2. Dans **_ViewImports.cshtml**, ajoutez :

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![Capture d’écran de la modification du code de _ViewImports.cshtml](./media/app-insights-asp-net-core/00018-view-imports.png)

3. Dans **Layout.cshtml**, ajoutez la ligne ci-dessous avant la balise ``</head>``, mais avant les autres scripts.

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![Capture d’écran de la modification du code de layout.cshtml](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Tester Requêtes échouées, Temps de chargement de la page, Flux temps réel

Maintenant que vous avez terminé les étapes précédentes, faites des tests et vérifiez que tout fonctionne.

1. Exécutez votre application en cliquant sur IIS Express. ![Capture d’écran de l’icône IIS Express de Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Accédez à la page **À propos de** pour déclencher l’exception de test. (Si vous êtes en mode débogage, vous devez cliquer sur **Continuer** dans Visual Studio avant que l’exception soit récupérée par Application Insights.)

3. Réexécutez le script de transaction PowerShell simulé précédemment (vous devrez peut-être ajuster le numéro de port dans le script.)

4. Si la vue d’ensemble d’Application Insights n’est pas encore ouverte, à partir du menu Visual Studio, sélectionnez **Projet** > **Application Insights** > **Ouvrir le portail Application Insights**. 

   > [!TIP]
   > Si vous ne voyez pas encore le nouveau trafic, vérifiez **l’intervalle de temps** et cliquez sur **Actualiser**.

   ![Capture d’écran de la fenêtre de vue d’ensemble](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Sélectionnez Flux temps réel.

   ![Capture d’écran de Flux de métriques en temps réel](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Si votre script PowerShell est toujours en cours d’exécution, vous devez voir les métriques en temps réel. S’il est arrêté, réexécutez le script en laissant Flux temps réel ouvert.)

## <a name="app-insights-sdk-comparison"></a>Comparaison des Kits de développement logiciel (SDK) d’Application Insights

Le groupe de produits Application Insights a beaucoup travaillé pour s’approcher autant que possible de la parité de fonctionnalité entre le [SDK .NET Framework complet ](https://github.com/Microsoft/ApplicationInsights-dotnet) et le SDK .Net Core. La version 2.2.0 du [SDK ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore) pour Application Insights a largement comblé l’écart de fonctionnalité.

Pour en savoir plus sur les différences et les compromis entre [.NET et .NET Core](https://docs.microsoft.com/en-us/dotnet/standard/choosing-core-framework-server).

   | Comparaison des SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Métriques temps réel**      | **+** |**-** | **+** |
   | **Canal de télémétrie du serveur** | **+** |**-** | **+**|
   |**Échantillonnage adaptatif**| **+** | **-** | **+**|
   | **Appels de dépendance SQL**     | **+** |**-** | **+**|
   | **Compteurs de performance*** | **+** | **-**| **-**|

Dans ce contexte, les _compteurs de performance_ font référence aux [compteurs de performance côté serveur](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters) telles que le processeur, la mémoire et l’utilisation du disque.

## <a name="open-source-sdk"></a>Kit de développement logiciel (SDK) open source
[Lire et contribuer au code](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Étapes suivantes
* [Explorez les flux d’utilisateurs](app-insights-usage-flows.md) pour comprendre comment les utilisateurs naviguent dans l’application.
* [Utilisez l’API](app-insights-api-custom-events-metrics.md) pour envoyer vos propres événements et mesures pour obtenir une vue plus détaillée des performances et de l’utilisation de votre application.
* [Les tests de disponibilité](app-insights-monitor-web-app-availability.md) vérifient votre application en permanence dans le monde entier.