---
title: "Envoi d’ID de contexte utilisateur pour activer les expériences d’utilisation dans Azure Application Insights | Microsoft Docs"
description: "Suivez les déplacements des utilisateurs dans votre service en affectant une chaîne d’ID unique et permanente à chacun d’eux dans Application Insights."
services: application-insights
documentationcenter: 
author: abgreg
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: article
ms.date: 08/02/2017
ms.author: mbullwin
ms.openlocfilehash: fe4481cf851fc021b3073a6d9d6254f546218785
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
#  <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Envoyer des ID de contexte utilisateur pour activer les expériences d’utilisation dans Azure Application Insights

## <a name="tracking-users"></a>Suivi des utilisateurs

Application Insights vous permet de surveiller et suivre vos utilisateurs par le biais d’un ensemble d’outils d’utilisation du produit : 
* [Utilisateurs, sessions, événements](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [Entonnoirs](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [Rétention](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* Cohortes
* [Classeurs](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Afin d’assurer le suivi des actions d’un utilisateur au fil du temps, Application Insights nécessite un ID pour chaque utilisateur ou chaque session. Incluez les ID suivants dans chaque vue personnalisée de page ou d’événement personnalisé.
- Utilisateurs, Entonnoirs, Rétention et Cohortes : incluez l’ID d’utilisateur.
- Sessions : incluez l’ID de session.

Si votre application est intégrée dans le [Kit de développement logiciel (SDK) JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), l’ID d’utilisateur est suivi automatiquement.

## <a name="choosing-user-ids"></a>Choix d’ID d’utilisateur

Les ID d’utilisateur doivent être conservés d’une session utilisateur à l’autre pour permettre de suivre le comportement des utilisateurs au fil du temps. Il existe différents moyens de conserver l’ID.
- Définition d’un utilisateur dont vous disposez déjà dans votre service.
- Si le service a accès à un navigateur, il peut transmettre au navigateur un cookie contenant un ID. L’ID est conservé tant que le cookie reste dans le navigateur de l’utilisateur.
- Si nécessaire, vous pouvez utiliser un nouvel ID à chaque session, mais les résultats sur les utilisateurs seront limités. Par exemple, vous ne pourrez pas voir l’évolution du comportement de l’utilisateur au fil du temps.

L’ID doit être un identificateur unique ou une autre chaîne assez complexe pour permettre l’identification spécifique de chaque utilisateur. Par exemple, il peut s’agir d’un nombre aléatoire long.

Si l’ID contient des informations personnelles sur l’utilisateur, cette valeur ne peut pas être envoyée à Application Insights en tant qu’ID d’utilisateur. Vous pouvez envoyer un ID en tant qu’[ID d’utilisateur authentifié](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), mais cela ne satisfait pas les critères requis pour l’ID d’utilisateur dans les scénarios d’usage.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>Applications ASP.NET : définition du contexte utilisateur dans ITelemetryInitializer

Créez un initialiseur de télémétrie, comme décrit en détail [ici](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer), puis définissez Context.User.Id et Context.Session.Id.

Cet exemple configure l’ID d’utilisateur en tant qu’identificateur expirant à la fin de la session. Si possible, utilisez un ID d’utilisateur qui se conserve d’une session à l’autre.

```csharp

    using System;
    using System.Web;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that sets the user ID.
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            // For a full experience, track each user across sessions. For an incomplete view of user 
            // behavior within a session, store user ID on the HttpContext Session.
            // Set the user ID if we haven't done so yet.
            if (HttpContext.Current.Session["UserId"] == null)
            {
                HttpContext.Current.Session["UserId"] = Guid.NewGuid();
            }

            // Set the user id on the Application Insights telemetry item.
            telemetry.Context.User.Id = (string)HttpContext.Current.Session["UserId"];

            // Set the session id on the Application Insights telemetry item.
            telemetry.Context.Session.Id = HttpContext.Current.Session.SessionID;
        }
      }
    }
```

## <a name="next-steps"></a>Étapes suivantes
- Pour activer les expériences d’utilisation, commencez à envoyer des [événements personnalisés](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou des [affichages de page](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Si vous envoyez déjà des événements personnalisés ou des affichages de page, explorez les outils d’utilisation pour savoir comment les utilisateurs emploient votre service.
    * [Vue d’ensemble de l’utilisation](app-insights-usage-overview.md)
    * [Utilisateurs, Sessions et Événements](app-insights-usage-segmentation.md)
    * [Entonnoirs](usage-funnels.md)
    * [Rétention](app-insights-usage-retention.md)
    * [Classeurs](app-insights-usage-workbooks.md)
