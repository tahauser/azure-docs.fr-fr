---
title: Surveillance au niveau de l’application Azure Service Fabric | Microsoft Docs
description: Découvrez les événements et journaux de niveau application et service utilisés pour surveiller et diagnostiquer les clusters Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: 258aac722aa1c94ecf2cbf0524a3e4b53b8a788c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="application-and-service-level-logging"></a>Surveillance aux niveaux de l’application et du service

L’instrumentation du code est à la base de la plupart des autres aspects de la surveillance de vos services. L’instrumentation est le seul moyen de détecter les problèmes et de diagnostiquer ce qui doit être résolu. Bien qu’il soit techniquement possible de connecter un débogueur à un service de production, cette pratique n’est pas courante. Par conséquent, il est important de disposer de données d’instrumentation détaillées.

Certains produits instrumentent automatiquement votre code. Bien que ces solutions soient très efficaces, une instrumentation manuelle est presque toujours nécessaire. Au final, vous devez disposer d’assez d’informations pour déboguer l’application de manière approfondie. Ce document décrit les différentes approches d’instrumentation de votre code et explique quand privilégier telle ou telle approche.

## <a name="eventsource"></a>EventSource

Lorsque vous créez une solution Service Fabric à partir d’un modèle dans Visual Studio, une classe dérivée **EventSource** (**ServiceEventSource** ou **ActorEventSource**) est générée. Un modèle dans lequel vous pouvez ajouter des événements pour votre application ou votre service est créé. Le nom de la classe dérivée **EventSource** **doit** être unique et créé à partir de la chaîne de modèle par défaut MyCompany-&lt;solution&gt;-&lt;project&gt;. Un même nom attribué à plusieurs définitions **EventSource** peut causer des problèmes lors de l’exécution. Chaque événement défini doit avoir un identificateur unique. Si l’identificateur n’est pas unique, l’exécution échoue. Certaines organisations préaffectent des valeurs aux identificateurs afin d’éviter les conflits entre les équipes de développement. Pour plus d’informations, consultez le [blog de Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) ou la [documentation MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>Journalisation ASP.NET Core

Il est important de planifier avec soin la manière dont vous allez instrumenter votre code. Avec le plan d’instrumentation adéquat, vous pouvez éviter de déstabiliser votre base de code et d’avoir alors à réinstrumenter le code. Afin de réduire les risques, vous pouvez choisir une bibliothèque d’instrumentation telle que [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), qui fait partie de Microsoft ASP.NET Core. ASP.NET Core présente une interface [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) que vous pouvez utiliser avec le fournisseur de votre choix, tout en limitant les répercussions sur le code existant. Vous pouvez utiliser le code dans ASP.NET Core sur Windows et Linux, ainsi que dans la version complète de .NET Framework. Par conséquent, votre code d’instrumentation est normalisé.

## <a name="choosing-a-logging-provider"></a>Choix d’un fournisseur de journalisation

Si votre application repose sur des performances élevées, **EventSource** constitue généralement une bonne approche. En effet, **EventSource** utilise *généralement* moins de ressources et offre de meilleures performances que la journalisation ASP.NET Core ou que les solutions tierces disponibles.  Cela ne représente pas un problème pour de nombreux services, mais si votre service est axé sur les performances, le recours à **EventSource** peut être plus judicieux. Toutefois, pour obtenir ces avantages de journalisation structurée, **EventSource** nécessite un investissement plus important de la part de votre équipe d’ingénierie. Si possible, effectuez un prototype rapide de quelques options de journalisation, puis choisissez celui qui répond le mieux à vos besoins.

## <a name="next-steps"></a>Étapes suivantes

Une fois votre fournisseur de journalisation choisi pour l’instrumentation de vos applications et services, vos journaux et événements doivent être agrégés pour pouvoir être envoyés à une plateforme d’analyse. Pour mieux comprendre certaines des options recommandées, consultez les informations relatives à [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) et [WAD](service-fabric-diagnostics-event-aggregation-wad.md).
