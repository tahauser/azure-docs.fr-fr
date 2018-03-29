---
title: 'Application Insights : langages, plateformes et intégrations | Microsoft Docs'
description: Langages, plateformes et intégrations disponibles pour Application Insights
services: application-insights
documentationcenter: ''
author: OlegAnaniev-MSFT
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mbullwin
ms.openlocfilehash: e172b04144afec04579de92b2393587635d3fecf
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Analyse développeur : langages, plateformes et intégrations
Ces éléments sont des implémentations [Application Insights](app-insights-overview.md) dont nous avons entendu parler, y compris par des tiers.

## <a name="languages---officially-supported-by-application-insights-team"></a>Langages : officiellement pris en charge par l’équipe Application Insights
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Pages web JavaScript](app-insights-javascript.md)
* [Node.JS](app-insights-nodejs.md)

## <a name="languages---community-supported"></a>Langages : pris en charge par la communauté
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Tout autre élément](#projects)

## <a name="platforms-and-frameworks"></a>Plates-formes et infrastructures
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET : pour les applications déjà actives](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET Core](app-insights-asp-net-core.md)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center, HockeyApp)
* [Azure Web Apps](app-insights-azure-web-apps.md)
* [Services cloud Azure](app-insights-cloudservices.md)&#151;incluant les rôles web et de travail
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center, HockeyApp)
* [J2EE](app-insights-java-get-started.md)
* [J2EE : pour les applications déjà actives](app-insights-java-live.md)
* [Application Mac OS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x) (HockeyApp)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [Spring](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Application Windows universelle](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp) (App Center, HockeyApp)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Application Windows Phone 8 et 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81) (HockeyApp)
* [Application Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps) (HockeyApp)
* [Rôles de travail, services et applications de bureau Windows](app-insights-windows-desktop.md)
* [Tout autre élément](#projects)

## <a name="logging-frameworks"></a>Frameworks de journalisation
* [Log4Net, NLog ou System.Diagnostics.Trace](app-insights-diagnostic-search.md)
* [Java, Log4J ou Logback](app-insights-java-trace-logs.md)
* [Journalisation sémantique (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) - s’intègre au [bloc applicatif de journalisation sémantique](https://msdn.microsoft.com/library/dn440729.aspx)
* [Tests de charge basés sur le cloud](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)

## <a name="content-management-systems"></a>Systèmes de gestion de contenu
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Exportation et analyse de données
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](app-insights-export-power-bi.md)

## <a name="projects"></a> Génération de votre propre kit de développement logiciel (SDK)
S’il n’existe pas encore de kit de développement logiciel (SDK) pour votre langage ou plateforme, peut-être souhaitez-vous créer un ? Examinons le code des kits de développement logiciel (SDK) existants répertoriés dans le [projet Kit de développement logiciel (SDK) Application Insights sur GitHub](https://github.com/Microsoft/AppInsights-Home).
