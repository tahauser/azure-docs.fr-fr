---
title: "Analyse d’événements Azure Service Fabric avec Azure Application Insights | Microsoft Docs"
description: "Découvrez l’analyse et la visualisation d’événements à l’aide d’Application Insights pour la surveillance et le diagnostic de clusters Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 479e486dca432020d5fcbaf98971a9803888bf98
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Analyse et visualisation d’événements avec Application Insights

Azure Application Insights est une plateforme extensible pour la surveillance et le diagnostic d’application. Elle inclut un puissant outil d’analyse et de requête, un tableau de bord et des visualisations personnalisables, ainsi que d’autres options incluant l’alerte automatisée. Il s’agit de la plateforme recommandée pour la surveillance et le diagnostic des services et applications Service Fabric.

## <a name="setting-up-application-insights"></a>Configuration d'Application Insights

### <a name="creating-an-ai-resource"></a>Création d’une ressource AI (Application Insights)

Pour créer une ressource AI, rendez-vous sur la Place de marché Azure, puis recherchez « Application Insights ». Elle doit s’afficher comme première solution (elle se trouve sous la catégorie « Web + mobile »). Cliquez sur **Créer** quand vous consultez la ressource appropriée (vérifiez que le chemin correspond celui indiqué dans l’image ci-dessous).

![Nouvelle ressource Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/create-new-ai-resource.png)

Vous devez remplir certaines informations pour approvisionner correctement la ressource. Dans le champ *Type d’application*, utilisez « Application web ASP.NET » si vous comptez utiliser l’un des modèles de programmation Service Fabric ou publier une application .NET dans le cluster. Utilisez « Général » si vous souhaitez déployer des conteneurs et ses exécutables invités. En général, utilisez par défaut « Application web ASP.NET » pour maintenir vos options ouvertes par la suite. Vous pouvez définir le nom de votre choix. De plus, le groupe de ressources et l’abonnement peuvent être modifiés après le déploiement de la ressource. Il est recommandé que votre ressources AI se trouve dans le même groupe de ressources que votre cluster. Si vous voulez obtenir des informations supplémentaires, consultez [Création d’une ressource Application Insights dans Azure](../application-insights/app-insights-create-new-resource.md).

Vous avez besoin de la clé d’instrumentation AI pour configurer AI avec votre outil d’agrégation d’événements. Une fois votre ressource AI configurée (ce qui prend quelques minutes après la validation du déploiement), accédez-y et recherchez la section **Propriétés** dans la barre de navigation de gauche. Un nouveau panneau s’ouvre, affichant une *CLÉ D’INSTRUMENTATION*. Si vous devez modifier l’abonnement ou le groupe de la ressource, cela peut également être effectué ici.

### <a name="configuring-ai-with-wad"></a>Configuration de AI avec WAD

>[!NOTE]
>Cette configuration s’applique uniquement aux clusters Windows pour le moment.

Il existe deux méthodes principales permettant d’envoyer des données à Azure AI à partir de WAD, ce qui nécessite d’ajouter un récepteur AI à la configuration de WAD, comme détaillé dans [cet article](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Ajouter une clé d’instrumentation AI lors de la création d’un cluster dans le portail Azure

![Ajout d’une clé AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Lors de la création d’un cluster, si le diagnostic est « Activé » », un champ facultatif permettant d’entrer une clé d’instrumentation Application Insights s’affiche. Si vous collez votre clé iKey AI ici, le récepteur AI est automatiquement configuré dans le modèle Resource Manager utilisé pour déployer votre cluster.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Ajouter le récepteur AI au modèle Resource Manager

Dans l’élément « WadCfg » du modèle Resource Manager, ajoutez un récepteur (« Sink ») en incluant les deux modifications suivantes :

1. Ajoutez la configuration du récepteur directement une fois que la déclaration de la `DiagnosticMonitorConfiguration` est terminée :

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Incluez le récepteur dans la `DiagnosticMonitorConfiguration` en ajoutant la ligne suivante dans la `DiagnosticMonitorConfiguration` de `WadCfg` (juste avant de déclarer les `EtwProviders`) :

    ```json
    "sinks": "applicationInsights"
    ```

Dans les deux extraits de code ci-dessus, le nom « applicationInsights » a été utilisé pour décrire le récepteur. Cela n’est pas obligatoire, et tant que le nom du récepteur est inclus dans l’élément « sinks », vous pouvez définir n’importe quelle chaîne comme nom.

Actuellement, les journaux du cluster s’affichent sous forme de traces dans la visionneuse du journal d’AI. Étant donné que les traces provenant du niveau plateforme sont de type « Informations », vous pouvez également envisager de modifier la configuration du récepteur pour envoyer uniquement des journaux de type « Critique » ou « Erreur ». Pour ce faire, ajoutez « Channels » à votre récepteur, comme illustré dans [cet article](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Si vous utilisez une clé iKey AI incorrecte dans le portail ou dans votre modèle Resource Manager, vous devrez modifier manuellement la clé et mettre à jour/redéployer le cluster. 

### <a name="configuring-ai-with-eventflow"></a>Configuration de AI avec EventFlow

Si vous utilisez EventFlow pour agréger des événements, veillez à importer le package NuGet `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`. Les éléments suivants doivent être inclus dans la section *outputs* du fichier *eventFlowConfig.json* :

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        // (replace the following value with your AI resource's instrumentation key)
        "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
]
```

Veillez à apporter les modifications obligatoires dans vos filtres, ainsi qu’à inclure toutes les autres entrées (avec leurs packages NuGet respectifs).

## <a name="aisdk"></a>SDK AI (Application Insights)

Il est généralement recommandé d’utiliser EventFlow et WAD comme solutions d’agrégation, car ils permettent d’adopter une approche plus modulaire du diagnostic et de l’analyse. Par exemple si vous voulez modifier vos sorties d’EventFlow, aucune modification de votre instrumentation réelle n’est nécessaire, mais juste une simple modification de votre fichier de configuration. Toutefois, si vous décidez d’investir dans Application Insights et qu’il est peu probable que vous changiez de plateforme, vous devez envisager d’utiliser le nouveau SDK d’AI pour agréger les événements et les envoyer à AI. Cela signifie que vous n’aurez plus à configurer EventFlow pour envoyer vos données à AI, mais simplement à installer le package NuGet Service Fabric d’Application Insights. Des informations supplémentaires sur le package sont disponibles [ici](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

La rubrique [Prise en charge d’Application Insights pour les microservices et les conteneurs](https://azure.microsoft.com/en-us/blog/app-insights-microservices/) vous présente quelques-unes des nouvelles fonctionnalités en cours de développement (encore actuellement en version bêta), qui vous permettent de bénéficier d’options de surveillance prêtes à l’emploi enrichies avec AI. Ces fonctionnalités incluent le suivi de dépendance (utilisé dans la création d’une AppMap de l’ensemble de vos services et applications dans un cluster et la communication entre eux), ainsi qu’une meilleure corrélation des suivis provenant de vos services (ce qui facilite l’identification d’un problème dans le flux de travail d’une application ou d’un service).

Si vous effectuez un développement dans .NET, que vous utiliserez probablement certains des modèles de programmation de Service Fabric et que vous souhaitez utiliser AI comme plateforme pour la visualisation et l’analyse des données d’événement et de journal, nous vous recommandons d’utiliser l’itinéraire du SDK AI comme flux de travail de surveillance et de diagnostic. Lisez [ceci](../application-insights/app-insights-asp-net-more.md) et [cela](../application-insights/app-insights-asp-net-trace-logs.md) pour commencer avec l’utilisation d’AI pour collecter et afficher vos journaux.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Navigation dans la ressource AI dans le portail Azure

Une fois que vous avez configuré AI comme sortie pour vos événements et journaux, les informations doivent commencer à s’afficher dans votre ressource AI au bout de quelques minutes. Accédez à la ressource AI, ce qui vous fait accéder au tableau de bord correspondant. Cliquez sur **Rechercher** dans la barre des tâches AI pour voir les dernières traces reçues et pour pouvoir les filtrer.

*Metrics Explorer* est un outil utile pour la création de tableaux de bord personnalisés basés sur les indicateurs de performance que vos applications, vos services et votre cluster peuvent signaler. Consultez [Exploration des indicateurs de performance dans Application Insights](../application-insights/app-insights-metrics-explorer.md) pour configurer vous-mêmes quelques graphiques basés sur les données que vous collectez.

Si vous cliquez sur **Analyse**, vous accédez au portail Application Insights Analytics, où vous pouvez interroger des événements et des traces avec une plus grande étendue et plus de choix. Vous pouvez en apprendre plus à ce sujet dans [Analytics dans Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des alertes dans AI](../application-insights/app-insights-alerts.md) pour être averti des changements de performances ou d’utilisation
* La [détection intelligente dans Application Insights](../application-insights/app-insights-proactive-diagnostics.md) effectue une analyse proactive de la télémétrie envoyée à AI pour vous avertir d’éventuels problèmes de performances
