---
title: Surveillance dans Microsoft Azure | Microsoft Docs
description: Les choix lorsque vous souhaitez analyser quelque chose dans Microsoft Azure. Azure Monitor, Application Insights et Log Analytics
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: robb
ms.openlocfilehash: c34211e0c55c10defaa32f1e0a2195514ff3ae5f
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="overview-of-monitoring-in-microsoft-azure"></a>Vue d’ensemble de l’analyse dans Microsoft Azure
Cet article fournit une vue d’ensemble des outils et services impliqués dans la surveillance holistique de Microsoft Azure. S’applique à :
- Utilisation de services Azure pour surveiller l’infrastructure et les applications Azure
- Utilisation de services Azure pour surveiller l’infrastructure et les applications hybrides et non hybrides Azure
- Utilisation de services non Azure pour surveiller l’infrastructure et les applications Azure

Cet article aborde les différents produits et services disponibles et leur fonctionnement ensemble. Il peut vous aider à déterminer les outils les plus appropriés pour vous dans différents cas.  

## <a name="why-use-azures-monitoring-services"></a>Pourquoi utiliser les services de surveillance Azure ?

Les problèmes de performances dans votre application cloud peuvent affecter votre entreprise. Avec plusieurs composants interconnectés et de nouvelles versions fréquentes, des dégradations peuvent se produire à tout moment. Et si vous développez une application, vos utilisateurs découvrent généralement des problèmes que vous n’avez pas trouvés dans le test. Vous devez prendre connaissance de ces problèmes immédiatement et disposer d’outils pour diagnostiquer et résoudre les problèmes. En outre, les problèmes dans votre application peuvent provenir de l’infrastructure sous-jacente sur laquelle ces applications s’exécutent. Il est donc primordial d’avoir une vue holistique de votre infrastructure et de votre application pour surveiller votre environnement Azure. Microsoft Azure propose une gamme d’outils permettant d’identifier et de résoudre ces problèmes.

## <a name="how-do-i-monitor-my-azure-environment"></a>Comment surveiller mon environnement Azure ?

Il existe toute une gamme d’outils pour surveiller votre environnement Azure, du code d’application en cours d’exécution sur Azure aux services et à l’infrastructure hébergeant votre application. Ces outils fonctionnent ensemble pour offrir une surveillance complète du cloud, et incluent :

-   **Azure Monitor** - service Azure qui fonctionne comme un pipeline consolidé pour toutes les données de surveillance provenant des services Azure. Il vous donne accès à des mesures de performances et des événements qui décrivent le fonctionnement de l’infrastructure Azure et de tous les services Azure que vous utilisez. Azure Monitor est un pipeline de données de surveillance pour votre environnement Azure, qui transmet les données directement dans Log Analytics et dans des outils tiers afin d’obtenir un aperçu de ces données et de les combiner aux données provenant de ressources sur site ou de cloud.

-   **Application Insights** - service Azure permettant une surveillance des performances de l’application et une analyse de l’utilisateur. Il analyse le code que vous avez écrit et les applications que vous avez déployées dans Azure, sur site, ou dans d’autres clouds. En instrumentant votre application avec le Kit de développement logiciel (SDK) Application Insights, vous pouvez obtenir l’accès à toute une gamme de données, notamment les temps de réponse des dépendances, les suivis d’exception, le débogage des instantanés et les profils d’exécution. Il fournit de puissants outils pour analyser les données de télémétrie de l’application lors du développement et de l’exploitation de votre application. Il est étroitement intégré dans Visual Studio pour vous permettre d’accéder directement aux lignes de code du problème et de résoudre celui-ci, et il analyse également l’utilisation de vos applications par le client afin d’en informer les chefs de produit.

-   **Log Analytics** - anciennement appelé OMS Log Analytics, il s’agit d’un service Azure qui reçoit les données des journaux et des mesures en provenance des services Azure (via Azure Monitor), des machines virtuelles Azure, de l’infrastructure sur site ou sur d’autres clouds, et qui permet des recherches de journal ainsi qu’une analyse de ces données. Il fournit des outils riches pour analyser les données de plusieurs sources, en acceptant des requêtes complexes sur tous les journaux et en pouvant générer des alertes de façon proactive pour des conditions spécifiées.  Vous pouvez même collecter des données personnalisées dans son référentiel central pour les interroger et les visualiser. Vous pouvez également tirer parti des solutions intégrées de Log Analytics pour obtenir immédiatement des informations détaillées sur la sécurité et les fonctionnalités de votre infrastructure.

## <a name="accessing-monitoring-in-the-azure-portal"></a>Accès à la surveillance dans le portail Azure
Tous les services de surveillance Azure sont maintenant disponibles dans un seul volet de l’interface utilisateur. Pour en savoir plus sur l’accès à cette zone, consultez [Prise en main d’Azure Monitor](monitoring-get-started.md). 

Vous pouvez également accéder à des fonctions de surveillance pour des ressources Azure spécifiques en mettant en surbrillance ces ressources et en explorant leurs options de surveillance. 

## <a name="examples-of-when-to-use-which-tool"></a>Exemples d’utilisation de l’outil 

Les sections suivantes montrent quelques scénarios de base et outils devant être utilisés ensemble. 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>Scénario 1 : correction des erreurs dans une application Azure en développement   

**La meilleure option consiste à utiliser conjointement Application Insights, Azure Monitor et Visual Studio**

Azure offre désormais toute la puissance du débogueur Visual Studio dans le cloud. Configurez Azure Monitor pour envoyer la télémétrie à Application Insights. Permettez à Visual Studio d’inclure le kit de développement logiciel Application Insights dans votre application. Une fois dans Application Insights, vous pouvez utiliser la mise en correspondance d’applications pour découvrir visuellement les parties de votre application en cours d’exécution qui sont intègres et celles qui ne le sont pas. Corrigez les parties qui ne sont pas intègres, les erreurs et les exceptions déjà disponibles pour exploration. Vous pouvez utiliser les diverses analyses dans Application Insights pour approfondir la question. Si vous n’êtes pas sûr de l’erreur, vous pouvez utiliser le débogueur de Visual Studio pour effectuer le suivi dans le code et identifier plus précisément un problème. 

Pour plus d’informations, consultez [Surveillance d’applications web](../application-insights/app-insights-azure-web-apps.md) et reportez-vous à la table des matières sur la gauche pour obtenir des instructions sur les différents types d’applications et de langages.  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>Scénario 2 : déboguer une application web Azure .NET pour les erreurs qui apparaissent uniquement en production 

> [!NOTE]
> Ces fonctionnalités sont en version préliminaire. 

**La meilleure option consiste à utiliser Application Insights et si possible de Visual Studio pour une expérience de débogage complète.**

Utilisez le débogueur d’instantanés d’Application Insights pour déboguer votre application. Lorsqu’un certain seuil d’erreur se produit avec des composants de production, le système capture automatiquement les données de télémétrie de Windows dans des « instantanés ». La quantité capturée est sûre pour une solution cloud de production, car elle est suffisamment petite pour ne pas affecter les performances, mais suffisamment significative pour permettre le traçage.  Le système peut capturer plusieurs instantanés. Vous pouvez examiner un moment donné dans le portail Azure ou utiliser Visual Studio pour l’expérience complète. Avec Visual Studio, les développeurs peuvent parcourir cet instantané comme s’ils effectuaient le débogage en temps réel. Les paramètres, la mémoire, les variables locales et les trames sont tous disponibles. Les développeurs doivent avoir accès à ces données de production via un [rôle RBAC](../active-directory/role-based-access-built-in-roles.md).  

Pour plus d’informations, consultez [Débogage d’instantané](../application-insights/app-insights-snapshot-debugger.md). 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>Scénario 3 : déboguer une application Windows Azure qui utilise des conteneurs ou microservices 

**Identique au scénario 1. Utiliser conjointement Application Insights, Azure Monitor et Visual Studio ensemble**

Application Insights prend également en charge la collecte des données de télémétrie des processus qui s’exécutent dans des conteneurs et microservices (Kubernetes, Docker, Azure Service Fabric). Pour plus d’informations, [regardez cette vidéo sur le débogage des conteneurs et microservices](https://go.microsoft.com/fwlink/?linkid=848184). 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>Scénario 4 : corriger les problèmes de performances dans votre application Azure

Le [profileur d’Application Insights](../application-insights/app-insights-profiler.md) est conçu pour vous aider à résoudre ces types de problèmes. Vous pouvez identifier et résoudre les problèmes de performances pour les applications qui s’exécutent dans App Service (Web Apps, Logic Apps, Mobile Apps, API Apps, Function Apps) et d’autres ressources de calcul comme les machines virtuelles, les jeux de mise à l’échelle de machines virtuelles, les services de cloud computing et Service Fabric. 

> [!NOTE]
> La fonctionnalité de profilage de machines virtuelles, de jeux de mise à l’échelle de machines virtuelles, de services cloud et de Service Fabric est en version préliminaire.   

De plus, vous êtes proactivement informé par courrier électronique de certains types d’erreurs, comme les temps de chargement de page lents, par l’outil de détection intelligente.  Vous n’avez pas besoin d’effectuer de configuration pour cet outil. Pour plus d’informations, consultez [Détection intelligente - anomalies de performances](../application-insights/app-insights-proactive-performance-diagnostics.md).



## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur

* [Azure Monitor dans une vidéo de l’Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Prise en main d’Azure Monitor](monitoring-get-started.md)
* [Azure Diagnostic](../azure-diagnostics.md) si vous tentez de diagnostiquer des problèmes dans votre service cloud, machine virtuelle, jeux de mise à l’échelle de machine virtuelle ou application Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) si vous essayez de diagnostiquer des problèmes dans votre application web App Service.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) et la solution de surveillance de production [Operations Management Suite](https://www.microsoft.com/oms/)
