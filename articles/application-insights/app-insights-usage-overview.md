---
title: "Analyse de l’usage avec Azure Application Insights | Microsoft Docs"
description: "Comprenez vos utilisateurs et ce qu’ils font avec votre application."
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 10/10/2017
ms.author: mbullwin
ms.openlocfilehash: 9f3eb14340205709b5409a3d16d631cc2d02eb32
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="usage-analysis-with-application-insights"></a>Analyse de l'utilisation avec Application Insights

Quelles sont les fonctionnalités de votre application web ou mobile les plus populaires ? Vos utilisateurs atteignent-ils leurs objectifs avec votre application ? Disparaissent-ils à des stades spécifiques, et reviennent-ils plus tard ?  [Azure Application Insights](app-insights-overview.md) vous permet d’obtenir des insights utiles sur l’utilisation de votre application. Chaque fois que vous mettez à jour votre application, vous pouvez évaluer son bon fonctionnement pour les utilisateurs. Grâce à ces informations, vous pouvez prendre des décisions basées sur des données sur les cycles de développement suivants.

## <a name="send-telemetry-from-your-app"></a>Envoyer des données de télémétrie à partir de votre application

La meilleure expérience est obtenue en installant Application Insights à la fois dans votre code serveur d’applications et dans vos pages web. Les composants client et serveur de votre application envoient la télémétrie au portail Azure pour analyse.

1. **Code serveur :** installez le module approprié pour votre [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md) ou une [autre](app-insights-platforms.md) application.

    * *Vous ne voulez pas installer de code serveur ? Vous pouvez simplement [créer une ressource Azure Application Insights](app-insights-create-new-resource.md).*

2. **Code de page Web :** ouvrez le [portail Azure](https://portal.azure.com), ouvrez la ressource Application Insights pour votre application, puis ouvrez **Mise en route &gt; Monitor and Diagnose Client-Side (Surveiller et diagnostiquer côté client)**. 

    ![Copiez le script dans l’en-tête de votre page web maître.](./media/app-insights-usage-overview/02-monitor-web-page.png)

3. **Code de l’application mobile :** utilisez le SDK App Center pour collecter les événements à partir de votre application, puis envoyer des copies de ces événements à Application Insights pour analyse en [suivant ce guide](app-insights-mobile-center-quickstart.md).

4. **Obtenir la télémétrie :** exécutez votre projet en mode débogage pendant quelques minutes, puis examinez les résultats dans le panneau Vue d’ensemble dans Application Insights.

    Publiez votre application pour surveiller les performances de votre application et découvrir ce que vos utilisateurs font avec votre application.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Inclure l’ID d’utilisateur et l’ID de session dans votre télémétrie
Pour effectuer le suivi des utilisateurs au fil du temps, Application Insights nécessite un moyen de les identifier. L’outil Événements est le seul outil d’utilisation qui ne nécessite pas d’ID d’utilisateur ni d’ID de session.

Démarrer l’envoi des ID d’utilisateur et de session à l’aide de [ce processus](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Explorer des données démographiques et des statistiques de l’utilisation
Découvrez quand des personnes utilisent votre application, les pages qui les intéressent le plus, où vos utilisateurs se trouvent, les navigateurs et les systèmes d’exploitation qu’ils utilisent. 

Les rapports Utilisateurs et sessions filtrent vos données par pages ou événements personnalisés, et les segmentent par propriétés telles que l’emplacement, l’environnement et la page. Vous pouvez également ajouter vos propres filtres.

![Utilisateurs](./media/app-insights-usage-overview/users.png)  

Aperçu des modèles intéressants appropriés dans le jeu de données.  

* Le rapport **Utilisateurs** compte le nombre d’utilisateurs uniques qui accèdent à vos pages dans les délais que vous avez choisis. Pour les applications web, les utilisateurs sont comptés avec les cookies. Si un utilisateur accède à votre site avec différents navigateurs ou ordinateurs clients, ou efface ses cookies, il est compté plusieurs fois.
* Le rapport **Sessions** compte le nombre de sessions utilisateur qui accèdent à votre site. Une session est une période d’activité d’un utilisateur, qui se termine par une période d’inactivité de plus d’une demi-heure.

[En savoir plus sur les outils Utilisateurs, Sessions et Événements](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>Affichages de page

Dans le panneau Utilisation, cliquez sur la mosaïque Pages consultées pour découvrir une répartition de vos pages les plus populaires :

![Dans le panneau Vue d'ensemble, cliquez sur le graphique des pages vues.](./media/app-insights-usage-overview/05-games.png)

L'exemple ci-dessus vient d’un site web de jeux. Dans les graphiques, nous pouvons voir instantanément :

* L'utilisation ne s'est pas améliorée au cours de la semaine écoulée. Peut-être que nous devrions envisager une optimisation pour les moteurs de recherche ?
* Tennis est la page de jeu la plus populaire. Concentrons-nous sur d’autres améliorations de cette page.
* En moyenne, les utilisateurs visitent la page Tennis environ trois fois par semaine. (Il y a près de trois fois plus de sessions que d’utilisateurs.)
* La plupart des utilisateurs visitent le site au cours de la semaine de travail aux États-Unis, et pendant les heures de travail. Il serait peut-être envisageable de fournir un bouton de masquage rapide sur la page web.
* Les [annotations](app-insights-annotations.md) sur le graphique montrent à quel moment les nouvelles versions du site web ont été déployées. Aucun des déploiements récents n’a eu d’effet notable sur l’utilisation.

Qu’en est-il si vous souhaitez examiner plus en détails le trafic vers votre site, comme le fractionnement par une propriété personnalisée que votre site envoie dans sa télémétrie d’affichage de page ?

1. Ouvrez l’outil **Événements** dans le menu de la ressource Application Insights. Cet outil vous permet d’analyser combien de pages consultées et d’événements personnalisés ont été envoyés à partir de votre application, sur la base des différentes options de filtrage, cohorte et segmentation.
2. Dans la liste déroulante « Qui a utilisé », sélectionnez « N’importe quelle page consultée ».
3. Dans la liste déroulante « Fractionner par », sélectionnez une propriété selon laquelle fractionner votre télémétrie d’affichage de page.

## <a name="retention---how-many-users-come-back"></a>Rétention - Combien d’utilisateurs reviennent ?

La rétention vous permet de comprendre la fréquence à laquelle vos utilisateurs reviennent utiliser leur application, en fonction des cohortes d’utilisateurs qui ont effectué une action pendant une période donnée. 

- Comprendre quelles fonctionnalités spécifiques font revenir les utilisateurs plus que d’autres 
- Formuler des hypothèses en fonction des données utilisateur réel 
- Déterminer si la rétention est un problème dans votre produit 

![Rétention](./media/app-insights-usage-overview/retention.png) 

Les commandes de rétention en haut vous permettent de définir des événements spécifiques et un intervalle de temps pour calculer la rétention. Le graphique au centre fournit une représentation visuelle du pourcentage de rétention globale sur l’intervalle de temps spécifié. Le graphique en bas représente la rétention sur une période de temps donnée. Ce niveau de détail vous permet de comprendre de manière plus approfondie ce que font vos utilisateurs et ce qui les peut amener à revenir.  

[En savoir plus sur l’outil de rétention](app-insights-usage-retention.md)

## <a name="custom-business-events"></a>Événements personnalisés

Pour bien comprendre ce que font les utilisateurs avec votre application, il est utile d’insérer des lignes de code pour enregistrer des événements personnalisés. Ces événements permettent de suivre toute activité, des actions détaillées des utilisateurs comme un clic sur un bouton, aux événements plus significatifs comme un de faire un achat ou de gagner à un jeu. 

Bien que les pages consultées puissent représenter des événements utiles, cela n’est en général pas le cas. Un utilisateur peut ouvrir une page produit sans acheter le produit. 

Grâce aux événements spécifiques, vous pouvez représenter la progression de vos utilisateurs sur votre site. Vous pouvez connaître leurs préférences sur les différentes options et où ils abandonnent ou rencontrent des difficultés. Grâce à ces informations, vous pouvez prendre des décisions avisées sur les priorités de vos travaux de développement en souffrance.

Les événements peuvent être enregistrés à partir du côté client de l’application :

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Ou du côté serveur :

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Vous pouvez joindre des valeurs de propriété à ces événements, afin de pouvoir filtrer ou fractionner les événements lorsque vous les étudiez dans le portail. Un ensemble standard de propriétés est également associé à chaque événement, comme des ID d’utilisateur anonymes, vous permettant ainsi de suivre la séquence d’activités d’un utilisateur.

En savoir plus sur les [événements personnalisés](app-insights-api-custom-events-metrics.md#trackevent) et les [propriétés](app-insights-api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Segmenter et traiter les événements

Dans les outils Utilisateurs, Sessions et Événements, vous pouvez segmenter et traiter des événements personnalisés par utilisateur, nom d’événement et propriétés.
![Utilisateurs](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Concevoir la télémétrie avec l’application

Lorsque vous concevez chaque fonctionnalité de votre application, prenez en compte comment vous allez mesurer son succès auprès de vos utilisateurs. Choisissez les événements à enregistrer et coder les appels de suivi de ces événements dans votre application dès le début.

## <a name="a--b-testing"></a>Test A | B
Si vous ne savez pas quelle variante de la fonctionnalité sera la plus efficace, publiez les deux versions et mettez-les à disposition de différents utilisateurs. Mesurer le taux de réussite de chaque version et créez-en une version unifiée.

Pour cette technique, vous joignez des valeurs de propriétés distinctes à toute la télémétrie envoyée par chaque version de votre application. Pour cela, vous définissez des propriétés dans le contexte TelemetryContext actif. Ces propriétés par défaut sont ajoutées à chaque message de télémétrie que l'application envoie, non seulement vos messages personnalisés, mais aussi la télémétrie standard.

Dans le portail Application Insights, filtrez et segmentez vos données sur les valeurs de propriétés, afin de comparer les différentes versions.

Pour ce faire, [configurez un initialiseur de télémétrie](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer) :

```csharp


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

Dans l’initialiseur de l’application web, par exemple Global.asax.cs :

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Tous les nouveaux TelemetryClients ajoutent automatiquement la valeur de propriété que vous spécifiez. Les événements de télémétrie individuels peuvent remplacer les valeurs par défaut.

## <a name="next-steps"></a>Étapes suivantes
   - [Utilisateurs, sessions, événements](app-insights-usage-segmentation.md)
   - [Entonnoirs](usage-funnels.md)
   - [Rétention](app-insights-usage-retention.md)
   - [Flux d’utilisateurs](app-insights-usage-flows.md)
   - [Classeurs](app-insights-usage-workbooks.md)
   - [Ajouter du contexte utilisateur](app-insights-usage-send-user-context.md)
