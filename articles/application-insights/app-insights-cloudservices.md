---
title: Application Insights pour Azure Cloud Services | Microsoft Docs
description: "Surveillance efficace de vos rôles Web et de travail avec Application Insights"
services: application-insights
documentationcenter: 
keywords: WAD2AI, Azure Diagnostics
author: mrbullwinkle
manager: carmonm
editor: alancameronwills
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.devlang: na
ms.tgt_pltfrm: ibiza
ms.topic: get-started-article
ms.workload: tbd
ms.date: 05/05/2017
ms.author: mbullwin
ms.openlocfilehash: dd35ef2239469be6ecf478f44a7e97389e5c5dd3
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights pour Services cloud Azure
[Les applications de service cloud Microsoft Azure](https://azure.microsoft.com/services/cloud-services/) peuvent être surveillées par [Application Insights][start] pour la disponibilité, les performances, les défaillances et l’utilisation en combinant les données des kits de développement logiciel (SDK) d’Application Insights avec les données [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) de vos services cloud. Avec les retours que vous obtenez sur les performances et l’efficacité de votre application dans la nature, vous pouvez prendre des décisions avisées sur la direction de la conception de chaque cycle de développement.

![Exemple](./media/app-insights-cloudservices/sample.png)

## <a name="before-you-start"></a>Avant de commencer
Vous devez disposer des éléments suivants :

* Un abonnement à [Microsoft Azure](http://azure.com). Connectez-vous avec un compte Microsoft, que vous pouvez avoir pour Windows, XBox Live ou d’autres services cloud de Microsoft. 
* Microsoft Azure Tools 2.9 ou version ultérieure
* Developer Analytics Tools 7.10 ou version ultérieure

## <a name="quick-start"></a>Démarrage rapide
Le moyen le plus simple et le plus rapide pour surveiller votre service cloud avec Application Insights consiste à choisir cette option quand vous publiez votre service dans Azure.

![Exemple](./media/app-insights-cloudservices/azure-cloud-application-insights.png)

Cette option instrumente votre application au moment de l’exécution, ce qui vous donne toute la télémétrie dont vous avez besoin pour surveiller les demandes, les exceptions et les dépendances dans votre rôle web, ainsi que les compteurs de performances de vos rôles de travail. Les traces de diagnostics générées par votre application sont également envoyées à Application Insights.

Si tout ceci vous suffit, vous avez terminé ! Les étapes suivantes consistent à [afficher les métriques de votre application](app-insights-metrics-explorer.md), à [interroger vos données avec Analytics](app-insights-analytics.md) et éventuellement à configurer un [tableau de bord](app-insights-dashboards.md). Vous souhaiterez peut-être configurer des [tests de disponibilité](app-insights-monitor-web-app-availability.md) et [ajouter du code à vos pages web](app-insights-javascript.md) pour surveiller les performances dans le navigateur.

Mais vous pouvez aussi obtenir plus d’options :

* Envoyer les données de différents composants et différentes configurations de builds à des ressources distinctes.
* Ajouter une télémétrie personnalisée à partir de votre application.

Si ces options vous intéressent, lisez la suite.

## <a name="sample-application-instrumented-with-application-insights"></a>Exemple d’application instrumentée avec Application Insights
Examinons l’ [exemple d’application](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) dans lequel Application Insights est ajouté à un service cloud avec deux rôles de travail hébergés dans Azure. 

Les informations suivantes vous indiqueront comment adapter votre propre projet de service cloud de la même façon.

## <a name="plan-resources-and-resource-groups"></a>Planifier des ressources et des groupes de ressources
Les données de télémétrie de votre application sont stockées, analysées et affichées dans une ressource Azure de type Application Insights. 

Chaque ressource appartient à un groupe de ressources. Les groupes de ressources servent à gérer les coûts, à accorder l’accès aux membres d’équipe et à déployer des mises à jour dans une transaction unique et coordonnée. Par exemple, vous pouvez [écrire un script pour déployer](../azure-resource-manager/resource-group-template-deploy.md) un service cloud Azure et ses ressources de surveillance Application Insights en une seule opération.

### <a name="resources-for-components"></a>Ressources pour les composants
Le modèle recommandé consiste à créer une ressource distincte pour chaque composant de votre application, autrement dit chaque rôle web et rôle de travail. Vous pouvez analyser chaque composant séparément, mais vous pouvez aussi créer un [tableau de bord](app-insights-dashboards.md) qui réunit les principaux graphiques de tous les composants, pour pouvoir les comparer et les surveiller ensemble. 

Un autre modèle consiste à envoyer les données de télémétrie de plusieurs rôles à la même ressource, mais en [ajoutant une propriété de dimension à chaque élément de télémétrie](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer) qui identifie son rôle source. Dans ce modèle, les graphiques de métriques telles que les exceptions affichent normalement une agrégation des décomptes des différents rôles, mais vous pouvez segmenter le graphique en fonction de l’identificateur de rôle si nécessaire. Vous pouvez également filtrer les recherches par la même dimension. Cette solution facilite quelque peu l’affichage simultané de tous les éléments, mais elle peut aussi entraîner une certaine confusion entre les rôles.

La télémétrie de navigateur est généralement comprise dans la même ressource que son rôle web côté serveur.

Placez les ressources Application Insights pour les différents composants dans un groupe de ressources. Ainsi, il est plus facile de les gérer ensemble. 

### <a name="separating-development-test-and-production"></a>Séparation du développement, des tests et de la production
Si vous développez des événements personnalisés pour votre prochaine fonctionnalité alors que la version précédente est active, vous devez envoyer la télémétrie de développement à une ressource Application Insights distincte. Sinon, vous aurez des difficultés à trouver votre télémétrie de test parmi tout le trafic provenant du site actif.

Pour éviter cette situation, créez des ressources distinctes pour chaque configuration de build ou « tampon » (développement, test, production...) de votre système. Placez les ressources pour chaque configuration de build dans un groupe de ressources distinct. 

Pour envoyer la télémétrie aux ressources appropriées, vous pouvez configurer le SDK Application Insights pour qu’il récupère une clé d’instrumentation différente en fonction de la configuration de build. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Création d’une ressource Application Insights pour chaque rôle
Si vous avez décidé de créer une ressource distincte pour chaque rôle (et éventuellement un ensemble distinct pour chaque configuration de build), il est plus facile de les créer dans le portail Application Insights. (Si vous créez beaucoup de ressources, vous pouvez [automatiser le processus](app-insights-powershell.md).)

1. Dans le [portail Azure][portal], créez une ressource Application Insights. Choisissez ASP.NET comme type d’application. 

    ![Cliquez sur Nouveau > Application Insights](./media/app-insights-cloudservices/01-new.png)
2. Notez que chaque ressource est identifiée par une clé d’instrumentation. Vous en aurez peut-être besoin plus tard si vous souhaitez configurer ou vérifier manuellement la configuration du SDK.

    ![Cliquez sur Propriétés, sélectionnez la clé et appuyez sur Ctrl + C](./media/app-insights-cloudservices/02-props.png) 

## <a name="set-up-azure-diagnostics-for-each-role"></a>Configurer Diagnostics Azure pour chaque rôle
Définissez cette option pour surveiller votre application avec Application Insights. Pour les rôles web, vous bénéficierez d’alertes, de diagnostics et d’une surveillance des performances, ainsi que de l’analyse de l’utilisation. Pour d’autres rôles, vous pouvez rechercher et surveiller des diagnostics Azure tels que le redémarrage, les compteurs de performances et les appels à System.Diagnostics.Trace. 

1. Dans l’Explorateur de solutions Visual Studio, sous &lt;votre_service_cloud&gt;, Rôles, ouvrez les propriétés de chaque rôle.
2. Dans **Configuration**, activez **Envoyer des données de diagnostic à Application Insights** et sélectionnez la ressource Application Insights appropriée créée précédemment.

Si vous avez décidé d’utiliser une ressource Application Insights distincte pour chaque configuration de build, sélectionnez d’abord la configuration.

![Dans les propriétés de chaque rôle Azure, configurer Application Insights](./media/app-insights-cloudservices/configure-azure-diagnostics.png)

Cela a pour effet d’insérer vos clés d’instrumentation Application Insights dans les fichiers nommés `ServiceConfiguration.*.cscfg`. ([Exemple de code](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)).

Si vous souhaitez faire varier le niveau des informations de diagnostics envoyées à Application Insights, vous pouvez le faire en [modifiant directement les fichiers `.cscfg`](app-insights-azure-diagnostics.md).

## <a name="sdk"></a>Installation du Kit de développement logiciel (SDK) dans chaque projet
Cette option ajoute la capacité à ajouter une télémétrie métier personnalisée à n’importe quel rôle, pour pouvoir analyser plus en détail l’utilisation et les performances de votre application.

Dans Visual Studio, configurez le Kit de développement logiciel (SDK) Application Insights pour chaque projet d’application cloud.

1. **Rôles Web** : cliquez avec le bouton droit sur le projet et sélectionnez **Configurer Application Insights** ou **Ajouter la télémétrie Application Insights**.

2. **Rôles de travail** : 
 * cliquez avec le bouton droit sur le projet et sélectionnez **Gérer les packages NuGet**.
 * Ajoutez [Application Insights pour les serveurs Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Recherchez « Application Insights »](./media/app-insights-cloudservices/04-ai-nuget.png)

3. Configurez le Kit SDK pour envoyer des données à la ressource Application Insights.

    Dans une fonction de démarrage appropriée, définissez la clé d’instrumentation à partir du paramètre de configuration dans le fichier .cscfg :
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    Faites cela pour chaque rôle de votre application. Consultez les exemples :
   
   * [Rôle Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
   * [Rôle de travail](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
   * [Pour les pages Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 
4. Définissez le fichier ApplicationInsights.config pour que celui-ci soit toujours copié dans le répertoire de sortie. 
   
    (Dans le fichier .config, vous verrez des messages vous demandant de placer la clé d'instrumentation là. Toutefois, pour les applications cloud, il est préférable de la définir à partir du fichier .cscfg. Ceci garantit que le rôle est identifié correctement dans le portail.)

#### <a name="run-and-publish-the-app"></a>Exécution et publication de l’application
Exécutez votre application et connectez-vous à Azure. Ouvrez les ressources Application Insights que vous avez créées pour voir apparaître les points de données individuels dans [Recherche](app-insights-diagnostic-search.md) et les données agrégées dans [Metrics Explorer](app-insights-metrics-explorer.md). 

Ajoutez des données de télémétrie supplémentaires (voir les sections ci-dessous), puis publiez votre application pour obtenir un retour en direct sur le diagnostic et l’utilisation. 

#### <a name="no-data"></a>Pas de données ?
* Ouvrez la vignette [Rechercher][diagnostic] pour afficher les événements individuels.
* Utilisez l'application en ouvrant différentes pages pour générer des données de télémétrie.
* Attendez quelques secondes, puis cliquez sur Actualiser.
* Consultez la rubrique [Résolution des problèmes][qna].

## <a name="view-azure-diagnostic-events"></a>Affichage des événements Azure Diagnostics
Emplacement des informations [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) dans Application Insights :

* Les compteurs de performances s’affichent comme mesures personnalisées. 
* Les journaux des événements Windows s’affichent comme traces et événements personnalisés.
* Les journaux des applications, les journaux ETW et tous les journaux d’infrastructure des diagnostics s’affichent comme traces.

Pour visualiser les compteurs de performances et le nombre d’événements, ouvrez [Metrics Explorer](app-insights-metrics-explorer.md) et ajoutez un nouveau graphique :

![Données de diagnostics Azure](./media/app-insights-cloudservices/23-wad.png)

Utilisez [Search](app-insights-diagnostic-search.md) ou une [requête Analytics](app-insights-analytics-tour.md) pour effectuer des recherches dans les différents journaux de suivi envoyés par Azure Diagnostics. Par exemple, supposons que vous disposez d’une exception non prise en charge qui a provoqué un incident et le recyclage d’un rôle. Cette information s’affiche dans le canal Application du Journal des événements Windows. Vous pouvez utiliser Search pour rechercher l’erreur du Journal des événements Windows et obtenir la trace de pile complète pour l’exception. Cela vous aidera à identifier l’origine du problème.

![Recherche de diagnostics Azure](./media/app-insights-cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Données de télémétrie supplémentaires
Les sections ci-dessous expliquent comment obtenir des données de télémétrie supplémentaires à partir de différents aspects de votre application.

## <a name="track-requests-from-worker-roles"></a>Suivi des demandes des rôles de travail
Dans les rôles Web, le module des requêtes collecte automatiquement les données relatives aux requêtes HTTP. Consultez l’ [exemple MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole) pour obtenir des exemples vous indiquant comment remplacer le comportement de regroupement par défaut. 

Vous pouvez capturer les performances des appels aux rôles de travail en effectuant le suivi de la même façon que les requêtes HTTP. Dans Application Insights, le type de demande de télémétrie mesure une unité de travail côté serveur nommée, pouvant être chronométrée et réussir ou échouer indépendamment. Même si les requêtes HTTP sont automatiquement capturées par le Kit SDK, vous pouvez insérer votre propre code pour effectuer le suivi des requêtes aux rôles de travail.

Consultez les deux exemples de rôles de travail instrumentés pour signaler des requêtes : [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) et [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Exceptions
Consultez la rubrique [Analyse des exceptions dans Application Insights](app-insights-asp-net-exceptions.md) pour découvrir comment collecter des exceptions non gérées depuis différents types d'applications Web.

L’exemple du rôle Web dispose de contrôleurs MVC5 et API Web 2. Les exceptions non gérées à partir de deux sont capturées avec les gestionnaires suivants :

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) configuré [ici](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) pour les contrôleurs MVC5
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) configuré [ici](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) pour les contrôleurs de l’API Web 2

Pour les rôles de travail, il existe deux façons d’effectuer le suivi des exceptions :

* TrackException(ex)
* Si vous avez ajouté le package NuGet de l’écouteur de suivi Application Insights, vous pouvez utiliser **System.Diagnostics.Trace** pour journaliser les exceptions. [Exemple de code.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## <a name="performance-counters"></a>Compteurs de performance
Les compteurs suivants sont collectés par défaut :

    * \Processus(??APP_WIN32_PROC??)\% Temps processeur
    * \Memory\Octets disponibles
    * \.Exceptions .NET CLR(??APP_CLR_PROC??)\# Nombre d'exceptions levées/s
    * \Processus(??APP_WIN32_PROC??)\Octets privés
    * \Processus(??APP_WIN32_PROC??)\Nombre d’octets de données E/S par s
    * \Processor(_Total)\% temps processeur

Pour les rôles web, ces compteurs sont également collectés :

    * \Applications ASP.NET(??APP_W3SVC_PROC??)\Demandes/s
    * \Applications ASP.NET (??APP_W3SVC_PROC??)\Durée d’exécution de la demande
    * \Applications ASP.NET (??APP_W3SVC_PROC??)\Demandes dans la file d’attente d’application

Vous pouvez spécifier des compteurs personnalisés ou d’autres compteurs de performances Windows en modifiant ApplicationInsights.config [comme dans cet exemple](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Compteurs de performances](./media/app-insights-cloudservices/OLfMo2f.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Télémétrie liée aux rôles de travail
Elle représente une riche expérience de diagnostic, lorsque vous pouvez découvrir ce qui a provoqué l’échec de la demande ou bien une forte latence. Grâce aux rôles Web, le Kit SDK définit automatiquement la corrélation entre les télémétries connexes. Pour les rôles de travail, vous pouvez utiliser un initialiseur de télémétrie personnalisée pour définir un attribut de contexte Operation.Id commun pour toutes les télémétries afin d’y parvenir. Ceci vous permet de découvrir très rapidement si le problème de latence/d'échec était dû à une dépendance ou à votre code ! 

Voici comment procéder :

* Définissez l'ID de corrélation dans un CallContext, comme indiqué [ici](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). Dans ce cas, nous utilisons l'ID de la demande comme id de corrélation
* Ajoutez une implémentation personnalisée de TelemetryInitializer, qui définit l’Operation.Id sur le correlationId mentionné ci-dessus. Voici un exemple : [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* Ajoutez l'initialiseur de télémétrie personnalisée. Effectuez cette opération dans le fichier ApplicationInsights.config ou dans le code mentionné [ici](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)

Et voilà ! L’expérience du portail est déjà intégrée pour vous permettre de découvrir très rapidement toutes les télémétries qui lui sont associées :

![Télémétrie corrélée](./media/app-insights-cloudservices/bHxuUhd.png)

## <a name="client-telemetry"></a>Télémétrie client
[Ajoutez le Kit de développement logiciel (SDK) JavaScript à vos pages web][client] pour obtenir des données de télémétrie basées sur votre navigateur, comme le nombre d’affichages de vos pages, les délais de chargement de vos pages, les exceptions de script, mais aussi les données de télémétrie personnalisées que vous pouvez rédiger dans les scripts de vos pages.

## <a name="availability-tests"></a>Tests de disponibilité
[Configurez les tests web][availability] pour vous assurer que votre application est bien active.

## <a name="display-everything-together"></a>Afficher tous les éléments ensemble
Pour obtenir une vue d’ensemble de votre système, vous pouvez rassembler les principaux graphiques de surveillance sur un même [tableau de bord](app-insights-dashboards.md). Vous pouvez par exemple épingler le décompte des demandes et des échecs de chaque rôle. 

Si votre système utilise d’autres services Azure tels que Stream Analytics, incluez également leurs graphiques de surveillance. 

Si vous avez une application mobile cliente, insérez du code pour envoyer des événements personnalisés quand des opérations utilisateur clés se produisent, et créez un [pont HockeyApp](app-insights-hockeyapp-bridge-app.md). Créez des requêtes dans [Analytics](app-insights-analytics.md) pour afficher le nombre d’événements, et épinglez-les au tableau de bord.

## <a name="example"></a>Exemple
[L'exemple](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) analyse un service qui dispose d’un rôle Web et de deux rôles de travail.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exception « méthode introuvable » lors de l’exécution dans Services cloud Azure
Avez-vous effectué une génération pour .NET 4.6 ? 4.6 n’est pas automatiquement pris en charge dans les rôles Azure Cloud Services. [Installez 4.6 sur chaque rôle](../cloud-services/cloud-services-dotnet-install-dotnet.md) avant d’exécuter votre application.

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>étapes suivantes
* [Configuration de l’envoi de diagnostics Azure à Application Insights](app-insights-azure-diagnostics.md)
* [Automatiser la création des ressources Application Insights](app-insights-powershell.md)
* [Automatiser les diagnostics Azure](app-insights-powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md 
