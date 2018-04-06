---
title: Échantillonnage de données de télémétrie dans Azure Application Insights | Microsoft Docs
description: Comment maintenir sous contrôle le volume de télémétrie.
services: application-insights
documentationcenter: windows
author: vgorbenko
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: mbullwin
ms.openlocfilehash: 300b9b7786c17972c5c48df7e5b6d28491adc095
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="sampling-in-application-insights"></a>Échantillonnage dans Application Insights


L’échantillonnage est une fonctionnalité dans [Azure Application Insights](app-insights-overview.md). Il s’agit de la méthode recommandée pour réduire le trafic et le stockage des données de télémétrie, tout en conservant une analyse statistiquement correcte des données d’application. Le filtre sélectionne les éléments associés afin que vous puissiez naviguer entre les éléments lorsque vous effectuez un diagnostic.
Lorsque les données de mesure apparaissent sur le portail, elles sont renormalisées pour tenir compte de l’échantillonnage, afin de réduire tout effet sur les statistiques.

L’échantillonnage réduit les coûts du trafic et des données, et vous aide à éviter les limitations.

## <a name="in-brief"></a>En bref :
* L’échantillonnage conserve 1 enregistrement sur *n* et ignore le reste. Par exemple, il peut conserver 1 événement sur 5, soit un taux d’échantillonnage de 20 %. 
* L’échantillonnage s’effectue automatiquement si votre application envoie de nombreuses données de télémétrie, dans les applications de serveur web ASP.NET.
* Vous pouvez également définir l’échantillonnage manuellement, sur la page de tarifs du portail, dans le fichier .config du Kit de développement logiciel (SDK) ASP.NET ou dans le fichier ApplicationInsights.xml du Kit SDK Java, pour réduire également le trafic réseau.
* Si vous consignez des événements personnalisés et que vous souhaitez vous assurer qu’un ensemble d’événements soit conservé ou ignoré conjointement, faites en sorte qu’ils aient la même valeur OperationId.
* Le diviseur d’échantillonnage *n* est signalé dans chaque enregistrement de la propriété `itemCount`, qui dans la recherche s’affiche sous le nom convivial « nombre de demandes » ou « nombre d’événements ». Lorsque l’échantillonnage n’est pas en cours d’utilisation, `itemCount==1`.
* Si vous écrivez des requêtes Analytics, vous devez [tenir compte de l’échantillonnage](app-insights-analytics-tour.md#counting-sampled-data). En particulier, au lieu de compter simplement les enregistrements, vous devez utiliser `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Types d’échantillonnage
Il existe trois autres méthodes d’échantillonnage :

* **échantillonnage adaptatif** ajuste automatiquement le volume de données de télémétrie envoyées à partir du Kit de développement logiciel (SDK) dans votre application ASP.NET. À partir du SDK v 2.0.0-beta3, il s’agit de la méthode d’échantillonnage par défaut. L’échantillonnage adaptatif n’est disponible que pour la télémétrie ASP.NET côté serveur. 
* **L’échantillonnage à débit fixe** réduit le volume de données de télémétrie envoyées par votre serveur ASP.NET ou Java et par le navigateur de vos utilisateurs. Vous définissez le débit. Le client et le serveur synchronisent leur échantillonnage de façon à ce que vous puissiez naviguer entre les demandes et les affichages de pages associés dans Search.
* **L’échantillonnage d’ingestion** fonctionne dans le portail Azure. Il ignore certaines données de télémétrie provenant de votre application, à une fréquence d’échantillonnage que vous définissez. Le trafic des données de télémétrie reçu de votre application n’est pas réduit, mais cela vous permet de respecter votre quota mensuel. L’avantage principal de l’échantillonnage d’ingestion est que vous pouvez définir la fréquence d’échantillonnage sans avoir à redéployer votre application et qu’il fonctionne uniformément pour tous les clients et serveurs. 

Si un échantillonnage de débit adaptatif ou fixe est en cours d’utilisation, l’échantillonnage d’ingestion est désactivé.

## <a name="ingestion-sampling"></a>échantillonnage d’ingestion
Cette forme d’échantillonnage fonctionne au niveau où les données de télémétrie issues de votre serveur web, des navigateurs et des appareils atteignent le point de terminaison du service Application Insights. Bien qu’elle ne réduise pas le trafic des données de télémétrie envoyées à partir de votre application, elle réduit la quantité traitée et conservée (et facturée) par Application Insights.

Utilisez ce type d’échantillonnage si votre application dépasse souvent son quota mensuel et que vous ne pouvez recourir à aucun type d’échantillonnage basé sur le Kit de développement logiciel (SDK). 

Définissez le taux d’échantillonnage dans le panneau Quota + tarification :

![Dans le panneau Vue d’ensemble de l’application, cliquez sur Paramètres, Quota + tarification, Échantillons conservés, sélectionnez un taux d’échantillonnage, puis cliquez sur Mettre à jour.](./media/app-insights-sampling/04.png)

Comme d’autres types d’échantillonnage, l’algorithme conserve les éléments de télémétrie associés. Par exemple, quand vous inspectez les données de télémétrie dans Search, vous pouvez trouver la demande liée à une exception spécifique. Les données de mesure telles que les taux de demandes et le taux d’exceptions sont correctement conservées.

Les points de données ignorés par l’échantillonnage ne sont disponibles dans aucune fonctionnalité Application Insights, par exemple l’ [exportation continue](app-insights-export-telemetry.md).

L’échantillonnage d’ingestion ne fonctionne pas pendant qu’une opération d’échantillonnage adaptatif ou taux fixe basé sur un Kit de développement logiciel (SDK) est en cours d’exécution. Notez que l’échantillonnage adaptatif est activé par défaut lorsque le Kit de développement logiciel (SDK) ASP.NET est activé dans Visual Studio ou à l’aide de Status Monitor et que l’échantillonnage d’ingestion est désactivé. Si le taux d’échantillonnage dans le Kit de développement logiciel (SDK) est inférieur à 100 %, le taux d’échantillonnage d’ingestion que vous avez défini est ignoré.

> [!WARNING]
> La valeur affichée sur la vignette indique la valeur que vous définissez pour l’échantillonnage d’ingestion. Il ne représente pas le taux d’échantillonnage réel si l’échantillonnage du kit de développement logiciel (SDK) est effectué.
> 
> 

## <a name="adaptive-sampling-at-your-web-server"></a>Échantillonnage adaptatif sur votre serveur web
L’échantillonnage adaptatif est disponible pour le Kit de développement logiciel (SDK) Application Insights pour ASP.NET version 2.0.0-beta3 et ultérieures et est activé par défaut. 

L’échantillonnage adaptatif a une incidence sur le volume de données de télémétrie envoyées à partir de votre application de serveur web au point de terminaison de service Application Insights. Le volume est automatiquement maintenu en deçà d’un débit de trafic maximal spécifié.

Il ne fonctionne pas quand les volumes de données de télémétrie sont bas ; ainsi, une application en mode débogage ou un site web faiblement utilisé ne sont pas affectés.

Pour atteindre le volume cible, certaines des données de télémétrie générées sont ignorées. Toutefois, comme d’autres types d’échantillonnage, l’algorithme conserve les éléments de télémétrie associés. Par exemple, quand vous inspectez les données de télémétrie dans Search, vous pouvez trouver la demande liée à une exception spécifique. 

Les données de mesure telles que le taux de demandes et le taux d’exceptions sont ajustées pour compenser le taux d’échantillonnage, afin qu’elles affichent des valeurs approximativement correctes dans Metrics Explorer.

### <a name="update-nuget-packages"></a>Mettre à jour les packages NuGet ###

Mettez à jour les packages NuGet de votre projet vers la dernière *préversion* d’Application Insights. Dans Visual Studio, cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, sélectionnez Gérer les packages NuGet, cochez **Inclure la version préliminaire** et recherchez Microsoft.ApplicationInsights.Web. 

### <a name="configuring-adaptive-sampling"></a>Configuration de l’échantillonnage adaptatif ###

Dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), vous pouvez ajuster plusieurs paramètres dans le nœud `AdaptiveSamplingTelemetryProcessor`. Les chiffres indiqués correspondent aux valeurs par défaut :

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Taux cible que l’algorithme adaptatif tente d’atteindre **sur chaque hôte du serveur**. Si votre application web s’exécute sur plusieurs hôtes, réduisez cette valeur afin de ne pas dépasser votre débit de trafic cible sur le portail Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Intervalle auquel le taux actuel de télémétrie est réévalué. L’évaluation est effectuée sous forme de moyenne mobile. Vous souhaiterez peut-être raccourcir cet intervalle si vos données de télémétrie sont soumises à des pics soudains.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    En cas de modification du pourcentage d’échantillonnage, délai avant que le pourcentage d’échantillonnage puisse de nouveau être réduit pour capturer moins de données.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    En cas de modification du pourcentage d’échantillonnage, délai avant que le pourcentage d’échantillonnage puisse de nouveau être augmenté pour capturer plus de données.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Lors des variations du pourcentage d’échantillonnage, valeur minimale autorisée.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Lors des variations du pourcentage d’échantillonnage, valeur maximale autorisée.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Lors du calcul de la moyenne mobile, poids affecté à la valeur la plus récente. Utilisez une valeur inférieure ou égale à 1. Plus les valeurs sont petites, moins l’algorithme est réactif en cas de modifications brusques.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Valeur affectée lorsque l’application vient de démarrer. Ne diminuez pas cette valeur pendant le débogage. 

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Une liste délimitée par des points-virgules des types que vous ne souhaitez pas voir échantillonnés. Les types reconnus sont : Dependency, Event, Exception, PageView, Request et Trace. Toutes les instances des types spécifiés sont transmises ; les types qui ne sont pas spécifiés sont échantillonnés.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Une liste délimitée par des points-virgules des types que vous souhaitez échantillonner. Les types reconnus sont : Dependency, Event, Exception, PageView, Request et Trace. Les types spécifiés sont échantillonnés ; toutes les instances des autres types sont toujours transmises.


**Pour désactiver** l’échantillonnage adaptatif, supprimez le nœud AdaptiveSamplingTelemetryProcessor de applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Solution alternative : configurer l’échantillonnage adaptatif dans le code
Au lieu de définir le paramètre d’échantillonnage dans le fichier .config, vous pouvez définir ces valeurs par programmation. Cela vous permet de spécifier une fonction de rappel qui est appelée chaque fois que le taux d’échantillonnage est réévalué. Par exemple, vous pouvez utiliser cette fonction pour savoir quel est le taux d’échantillonnage utilisé.

Supprimer le nœud `AdaptiveSamplingTelemetryProcessor` du fichier .config.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([En savoir plus sur les processeurs de télémétrie](app-insights-api-filtering-sampling.md#filtering).)

<a name="other-web-pages"></a>

## <a name="sampling-for-web-pages-with-javascript"></a>Échantillonnage pour les pages web avec JavaScript
Vous pouvez configurer des pages Web pour l’échantillonnage à débit fixe à partir de n’importe quel serveur. 

Quand vous [configurez les pages web pour Application Insights](app-insights-javascript.md), modifiez l’extrait de code JavaScript que vous obtenez à partir du portail Application Insights. (Dans les applications ASP.NET, l’extrait de code passe généralement dans _Layout.cshtml.)  Insérez une ligne de type `samplingPercentage: 10,` avant la clé d’instrumentation :

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

Pour le pourcentage d’échantillonnage, choisissez un pourcentage proche de 100/N, où N est un nombre entier.  L’échantillonnage ne prend actuellement en charge aucune autre valeur.

Si vous avez activé l’échantillonnage à débit fixe sur le serveur, les clients et le serveur se synchronisent de façon à ce que, dans Recherche, vous puissiez naviguer entre les demandes et les affichages de pages associés.

## <a name="fixed-rate-sampling-for-aspnet-and-java-web-sites"></a>Échantillonnage à débit fixe pour les sites web ASP.NET et Java
L’échantillonnage à débit fixe réduit le trafic envoyé depuis votre serveur web et les navigateurs web. À la différence de l’échantillonnage adaptatif, il réduit les données de télémétrie à un débit fixe choisi par vos soins. En outre, il synchronise l’échantillonnage client et serveur afin que les éléments associés soient conservés ; par exemple, quand vous examinez un affichage de page dans Recherche, vous pouvez trouver sa demande associée.

L’algorithme d’échantillonnage conserve les éléments associés. Pour chaque événement de requête HTTP, la requête et ses événements associés sont ignorés ou transmis ensemble. 

Dans Metrics Explorer, les taux tels que le nombre de demandes et d’exceptions sont multipliés par un facteur pour compenser le taux d’échantillonnage, afin qu’ils soient approximativement corrects.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Configurer l’échantillonnage à débit fixe avec ASP.NET ###

1. **Mettez à jour les packages NuGet de votre projet** vers la dernière version *préliminaire* d’Application Insights. Dans Visual Studio, cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, sélectionnez Gérer les packages NuGet, cochez **Inclure la version préliminaire** et recherchez Microsoft.ApplicationInsights.Web. 
2. **Désactivez l’échantillonnage adaptatif** : dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), supprimez ou commentez le nœud `AdaptiveSamplingTelemetryProcessor`.
   
    ```xml
   
    <TelemetryProcessors>
   
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->

    ```

3. **Activez le module d’échantillonnage à débit fixe.** Ajoutez cet extrait de code à [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Configurer l’échantillonnage à débit fixe avec Java ###

1. Téléchargez et configurez votre application web avec la dernière version du [Kit SDK Java Application Insights](app-insights-java-get-started.md).

2. **Activez le module d’échantillonnage à débit fixe** en ajoutant l’extrait de code suivant au fichier ApplicationInsights.xml.

```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type = "FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name = "SamplingPercentage" value = "50" />
            </Processor>
        </BuilrInProcessors>
    <TelemetryProcessors/>
```

3. Vous pouvez inclure ou exclure certains types de données de télémétrie de l’échantillonnage en utilisant les balises suivantes au sein de la balise Processor « FixedRateSamplingTelemetryProcessor » :
```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
```
Voici les types de données de télémétrie qu’il est possible d’inclure ou d’exclure de l’échantillonnage : Dependency, Event, Exception, PageView, Request et Trace.

> [!NOTE]
> Pour le pourcentage d’échantillonnage, choisissez un pourcentage proche de 100/N, où N est un nombre entier.  L’échantillonnage ne prend actuellement en charge aucune autre valeur.
> 
> 

### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Solution alternative : activez l’échantillonnage à taux fixe dans le code serveur
Au lieu de définir le paramètre d’échantillonnage dans le fichier .config, vous pouvez définir ces valeurs par programmation. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([En savoir plus sur les processeurs de télémétrie](app-insights-api-filtering-sampling.md#filtering).)

## <a name="when-to-use-sampling"></a>Quand utiliser l’échantillonnage ?
L’échantillonnage adaptatif est automatiquement activé si vous utilisez la version 2.0.0-beta3 du Kit de développement logiciel (SDK) ASP.NET, ou une version ultérieure. Quelle que soit la version du SDK que vous utilisez, vous pouvez activer l’échantillonnage d’ingestion pour autoriser Application Insights à échantillonner les données collectées.

Par défaut, aucun échantillonnage n’est activé dans le Kit SDK Java, qui ne gère à l’heure actuelle que l’échantillonnage à débit fixe. L’échantillonnage adaptatif n’est pas pris en charge dans le Kit SDK Java.

En général, pour la plupart des applications de taille petite à moyenne, vous n’avez pas besoin d’échantillonnage. Pour obtenir les informations de diagnostic les plus utiles et les statistiques les plus précises, le mieux est de collecter les données sur toutes vos activités utilisateur. 

Les principaux avantages de l’échantillonnage sont les suivants :

* Si le service Application Insights supprime (« limite ») des points de données lorsque votre application envoie un taux de télémétrie très élevé dans un court laps de temps. 
* Pour respecter le [quota](app-insights-pricing.md) de points de données pour votre niveau tarifaire. 
* Pour réduire le trafic réseau généré par la collecte de données de télémétrie. 

### <a name="which-type-of-sampling-should-i-use"></a>Quel type d’échantillonnage dois-je utiliser ?
**Utiliser l’échantillonnage d’ingestion dans les situations suivantes :**

* Vous dépassez souvent votre quota mensuel de données de télémétrie.
* Vous utilisez une version du Kit SDK qui ne prend pas en charge l’échantillonnage, par exemple, une version d’ASP.NET antérieure à la version 2.
* Vous obtenez un volume élevé de données de télémétrie des navigateurs web de vos utilisateurs.

**Utilisez l’échantillonnage à débit fixe si :**

* Vous utilisez la version 2.0.0 ou une version ultérieure du Kit SDK Application Insights pour les services web ASP.NET ou la version 2.0.1 ou une version ultérieure du Kit SDK Java, et
* Vous voulez disposer d’un échantillonnage synchronisé entre le client et le serveur afin de pouvoir naviguer entre les événements connexes sur le client et le serveur (tels que les affichages de pages et les requêtes http) lorsque vous étudiez des événements dans [Search](app-insights-diagnostic-search.md).
* Vous êtes sûr du pourcentage d’échantillonnage approprié pour votre application. Il doit être suffisamment élevé pour obtenir des mesures précises, mais inférieur au pourcentage engendrant le dépassement de votre quota de tarification et des limites de limitation. 

**Utilisez l’échantillonnage adaptatif :**

Si les conditions d’utilisation des autres formes d’échantillonnage ne s’appliquent pas, nous vous recommandons l’échantillonnage adaptatif. Cette option est activée par défaut dans le Kit de développement logiciel (SDK) du serveur ASP.NET, version 2.0.0-beta3 ou ultérieure. Elle ne réduit le trafic que si un débit minimal donné est atteint ; ainsi, les sites peu utilisés ne sont pas affectés.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Comment savoir si l’échantillonnage est utilisé ?
Pour découvrir le taux d’échantillonnage réel, indépendamment de l’endroit où il a été appliqué, utilisez une [requête Analytics](app-insights-analytics.md) telle que celle-ci :

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

Pour chaque enregistrement conservé, `itemCount` indique le nombre d’enregistrements d’origine qu’il représente, soit 1 + le nombre d’enregistrements précédents ignorés. 

## <a name="how-does-sampling-work"></a>Comment fonctionne l’échantillonnage ?
La fonctionnalité d’échantillonnage à débit fixe du Kit SDK ASP.NET à partir de la version 2.0.0 et du Kit SDK Java à partir de la version 2.0.1. L’échantillonnage adaptatif est une fonctionnalité du Kit SDK dans ASP.NET version 2.0.0 et ultérieures. L’échantillonnage d’ingestion est une fonctionnalité du service Application Insights et peut fonctionner si le Kit de développement logiciel (SDK) n’effectue pas d’échantillonnage. 

L’algorithme d’échantillonnage sélectionne les éléments de télémétrie à supprimer et ceux à conserver (qu’ils se trouvent dans le Kit de développement logiciel ou le service Application Insights). La décision d’échantillonnage est fondée sur plusieurs règles visant à préserver l’intégrité de tous les points de données reliés entre eux, en conservant dans Application Insights une expérience de diagnostic qui demeure exploitable et fiable, même avec un jeu de données réduit. En cas d’échec d’une requête, par exemple, si votre application envoie d’autres éléments de télémétrie (tels que les exceptions et les traces enregistrées à partir de cette requête), l’échantillonnage ne fractionnera ni cette requête ni les autres éléments de télémétrie. Il les conservera ou supprimera ensemble. C’est pourquoi, lorsque vous examinez les détails de la requête dans Application Insights, la requête s’affichera toujours avec les éléments de télémétrie qui lui sont associés. 

Pour les applications qui définissent « l’utilisateur » (autrement dit, les applications Web les plus courantes), la décision d’échantillonnage est basée sur le hachage de l’identifiant utilisateur, ce qui signifie que tous les éléments de télémétrie associés à n’importe quel utilisateur spécifique seront soit conservés soit supprimés. Pour les types d’applications qui ne définissent pas les utilisateurs (tels que les services Web), la décision d’échantillonnage repose sur l’identifiant d’opération de la requête. Enfin, pour les éléments de télémétrie qui ne sont associés ni à un identifiant utilisateur ni à un identifiant d’opération (par exemple, pour des éléments de télémétrie signalés à partir de threads asynchrones sans contexte http), l’échantillonnage capturera simplement un pourcentage d’éléments de télémétrie de chaque type. 

Lorsque les données de télémétrie vous sont restituées, le service Application Insights ajuste les mesures en fonction du même pourcentage d’échantillonnage que celui utilisé au moment de la collecte, de manière à compenser les points de données manquants. Par conséquent, lorsqu’ils consultent les données de télémétrie dans Application Insights, les utilisateurs constatent des approximations correctes d’un point de vue statistique et très proches des chiffres réels.

La précision de l’approximation dépend en grande partie du pourcentage d’échantillonnage configuré. Elle est également plus précise pour les applications qui gèrent un grand nombre de requêtes globalement similaires générées par un grand nombre d’utilisateurs. En revanche, pour les applications qui ne sont pas soumises à une charge importante, l’échantillonnage n’est pas nécessaire, car ces applications peuvent généralement envoyer toutes leurs données de télémétrie sans dépasser leur quota ni entraîner de perte de données liée à une limitation de la bande passante. 

> [!WARNING]
> Application Insights n’échantillonne pas les exemples de données de télémétrie de type métriques et sessions. La précision réduite n’est pas du tout souhaitable pour ces types de données de télémétrie.
> 

### <a name="adaptive-sampling"></a>échantillonnage adaptatif
L’échantillonnage adaptatif ajoute un composant qui contrôle la vitesse de transmission actuelle à partir du Kit de développement logiciel (SDK) et ajuste le pourcentage d’échantillonnage afin de le maintenir en dessous du taux maximal cible. L’ajustement est recalculé à intervalles réguliers en fonction d’une moyenne mobile de la vitesse de transmission sortante.

## <a name="sampling-and-the-javascript-sdk"></a>Échantillonnage et kit de développement logiciel JavaScript
Le kit de développement logiciel (SDK) côté client (JavaScript) participe à l’échantillonnage à taux fixe parallèlement au kit de développement logiciel (SDK) côté serveur. Les pages instrumentées envoient uniquement les données de télémétrie côté client provenant des utilisateurs que le SDK côté serveur a décidé d’échantillonner. Cette logique est conçue pour préserver l’intégrité de la session utilisateur côté client et côté serveur. En partant de n’importe quel élément de télémétrie dans Application Insights, vous retrouverez donc tous les autres éléments de télémétrie associés à l’utilisateur ou à la session en question. 

*Mes données de télémétrie côté client et côté serveur n’affichent pas les échantillons coordonnés que vous décrivez ci-dessus.*

* Vérifiez que vous avez activé l’échantillonnage à débit fixe à la fois sur le serveur et sur le client.
* Assurez-vous que vous utilisez bien le kit de développement logiciel version 2.0 ou ultérieure.
* Vérifiez que vous définissez le même pourcentage d’échantillonnage dans le client et dans le serveur.

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)
*Pourquoi l’échantillonnage ne permet-il pas simplement de « collecter X % de chaque type de télémétrie » ?*

* Bien que cette approche de l’échantillonnage puisse générer des approximations métriques d’une très haute précision, elle ne permettrait pas de mettre en corrélation les données de diagnostic par utilisateur, par session et par requête, ce qui est essentiel pour l’établissement de diagnostics. L’échantillonnage est donc plus efficace lorsqu’il revient à « collecter tous les éléments de télémétrie pour X % des utilisateurs de l’application » ou à « collecter tous les éléments de télémétrie pour X % des requêtes de l’application ». Pour les éléments de télémétrie qui ne sont pas associés aux requêtes (dans le cas, par exemple, d’un traitement asynchrone en arrière-plan), la solution consiste à « collecter X % de tous les éléments de chaque type de télémétrie ». 

*Le pourcentage d’échantillonnage peut-il évoluer au fil du temps ?*

* Oui, l’échantillonnage adaptatif modifie progressivement le pourcentage d’échantillonnage en fonction du volume de données de télémétrie constaté.

*Si j’utilise l’échantillonnage à débit fixe, comment déterminer le pourcentage d’échantillonnage optimal pour mon application ?*

* Pour cela, vous pouvez commencer par l’échantillonnage adaptatif, identifier le taux obtenu (voir la question précédente) et passer à l’échantillonnage à taux fixe en utilisant ce taux. 
  
    Autrement, vous devez procéder par tâtonnements. Analysez votre utilisation actuelle des données de télémétrie dans Application Insights, observez les limitations qui s’appliquent, puis estimez le volume de données de télémétrie collectées. Ces trois entrées, combinées au niveau tarifaire sélectionné, vous indiquent dans quelle mesure vous devrez réduire le volume de données de télémétrie collectées. Toutefois, une augmentation du nombre d’utilisateurs ou toute autre modification au niveau du volume des données de télémétrie peut invalider votre estimation.

*Que se passe-t-il si je configure le pourcentage d’échantillonnage à un niveau trop faible ?*

* Un pourcentage d’échantillonnage excessivement faible (échantillonnage trop agressif) a pour effet de réduire la précision des approximations lorsqu’Application Insights tente de compenser la visualisation des données pour tenir compte de la réduction du volume de données. Il peut également affecter l’expérience de diagnostic puisque l’échantillonnage peut inclure certaines requêtes rarement sujettes à des problèmes d’échec ou de ralentissement.

*Que se passe-t-il si je configure un pourcentage d’échantillonnage trop élevé ?*

* Si vous configurez un pourcentage d’échantillonnage trop élevé (c’est-à-dire pas assez agressif), le volume de données de télémétrie collectées n’est pas suffisamment réduit. Vous risquez de perdre des données de télémétrie sous l’effet de la limitation de bande passante et de supporter des coûts plus élevés que prévu pour l’utilisation d’Application Insights en raison des frais de dépassement.

*Sur quelles plates-formes puis-je utiliser l’échantillonnage ?*

* L’échantillonnage d’ingestion peut se produire automatiquement pour toute télémétrie au-dessus d’un certain volume, si le Kit de développement logiciel (SDK)n’effectue pas d’échantillonnage. Cela peut être le cas si, par exemple, vous utilisez une version antérieure du Kit SDK ASP.NET ou du Kit SDK Java (1.0.10 ou antérieure).
* Si vous utilisez le Kit de développement logiciel (SDK) ASP.NET version 2.0.0 ou une version ultérieure (hébergée dans Azure ou sur votre propre serveur), vous obtenez l’échantillonnage adaptatif par défaut, mais vous pouvez basculer à l’échantillonnage à taux fixe, comme décrit ci-dessus. Avec l’échantillonnage à taux fixe, le Kit de développement logiciel (SDK) du navigateur se synchronise automatiquement pour échantillonner les événements connexes. 
* Si vous utilisez le Kit SDK Java version 2.0.1 ou ultérieure, vous pouvez configurer ApplicationInsights.xml de façon à activer l’échantillonnage à débit fixe. L’échantillonnage est désactivé par défaut. Avec l’échantillonnage à taux fixe, le Kit de développement logiciel (SDK) du navigateur se synchronise automatiquement pour échantillonner les événements connexes.

*Je souhaite que certains événements rares soient toujours affichés. Comment faire en sorte qu’ils soient disponibles hors du module d’échantillonnage ?*

* Initialisez une instance distincte de TelemetryClient avec une nouvelle instance TelemetryConfiguration (et non la valeur Active par défaut). Utilisez cette instance pour envoyer vos événements rares.

## <a name="next-steps"></a>Étapes suivantes
* [filtrage](app-insights-api-filtering-sampling.md) peut fournir un contrôle plus strict de ce que le Kit de développement logiciel (SDK) envoie.

