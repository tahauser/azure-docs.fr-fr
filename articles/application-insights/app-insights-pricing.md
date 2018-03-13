---
title: "Gestion de la tarification et du volume de données Azure Application Insights | Microsoft Docs"
description: "Gérer les volumes de données de télémétrie et surveiller les coûts dans Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mbullwin
ms.openlocfilehash: 95c5195ac2ea832586211cce37eb2094e06eaf03
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Gérer la tarification et le volume de données dans Application Insights


La tarification pour [Azure Application Insights][start] est basée sur le volume de données par application. Une faible utilisation pendant le développement ou dans une petite application est susceptible d’être gratuite, compte tenu du quota mensuel de 1 Go de données de télémétrie accordé.

Chaque ressource d’Application Insights est facturée comme un service distinct et s’ajoute à votre facture d’abonnement à Azure.

Il existe deux plans de tarification. Le plan par défaut est nommé De base. Vous pouvez opter pour le plan Entreprise, facturé à la journée, qui donne accès à certaines fonctionnalités supplémentaires telles que [l’exportation continue](app-insights-export-telemetry.md).

Si vous avez des questions sur la tarification d’Application Insights, publiez une question dans notre [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights). 

## <a name="the-price-plans"></a>Les plans tarifaires

Consultez la [page de tarification d’Application Insights][pricing] pour connaître les prix actuels dans votre devise.

### <a name="basic-plan"></a>Plan De base

Le plan De base est la valeur par défaut lorsqu’une ressource Application Insights est créée et est suffisante pour la plupart des clients.

* Dans le plan De base, vous êtes facturé en fonction du volume de données : nombre d’octets de données de télémétrie reçus par Application Insights. Le volume de données est mesuré comme la taille du package de données JSON non compressé reçu par Application Insights de la part de votre application.
Pour [les données tabulaires importées dans Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), le volume de données est mesuré en tant que taille non compressée des fichiers envoyés à Application Insights.  
* Votre premier 1 Go pour chaque application est gratuit. Si vous expérimentez ou développez simplement, vous n'aurez probablement pas à payer.
* Les données des [Flux de métriques temps réel](app-insights-live-stream.md) ne sont pas comptabilisées dans la tarification.
* L[’exportation continue](app-insights-export-telemetry.md) est disponible moyennent des frais supplémentaires par Go dans le plan De base.

### <a name="enterprise-plan"></a>Plan Entreprise

* Dans le plan Entreprise, votre application peut utiliser toutes les fonctionnalités d’Application Insights. L[’exportation continue](app-insights-export-telemetry.md) et 

le [connecteur Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) sont disponibles sans frais supplémentaires dans le plan Entreprise.
* Vous payez pour chaque nœud qui envoie des données de télémétrie pour n'importe quelle application dans le plan Entreprise. 
 * Un *nœud* correspond à une machine serveur virtuelle ou physique ou à une instance de rôle Platform-as-a-Service qui héberge votre application.
 * Les ordinateurs de développement, les navigateurs clients et les appareils mobiles ne sont pas comptés comme nœuds.
 * Si votre application comporte plusieurs composants qui envoient des données de télémétrie, comme un service web et un Worker back-end, ces composants sont comptés séparément.
 * Les données [Flux de métriques en temps réel](app-insights-live-stream.md) ne sont pas comptabilisées dans la tarification.* Dans un abonnement, vos frais sont calculés par nœud et non par application. Si vous avez cinq nœuds envoyant des données de télémétrie pour 12 applications, les frais sont calculés pour cinq nœuds.
* Bien que les frais indiqués soient par mois, vous êtes facturé uniquement pour toutes les heures dans lesquelles un nœud envoie des données de télémétrie à partir d’une application. Le tarif horaire est le prix mensuel indiqué / 744 (le nombre d’heures dans un mois de 31 jours).
* Une allocation de volume de données de 200 Mo par jour est accordée pour chaque nœud détecté (avec une granularité par heure). L’allocation des données inutilisées n'est pas reportée d’un jour à l’autre.
 * Lorsque vous choisissez l’option de tarification Entreprise, chaque abonnement reçoit un volume quotidien de données selon le nombre de nœuds qui envoient des données de télémétrie aux ressources Application Insights dans cet abonnement. Par conséquent, si vous avez 5 nœuds qui envoient des données toute la journée, une allocation groupée de 1 Go est appliquée à toutes les ressources Application Insights dans cet abonnement. Le fait que certains nœuds envoient plus de données que d’autres a peu d'importance, car les données incluses sont partagées entre tous les nœuds. Si, un jour donné, les ressources d’Application Insights reçoivent plus de données que le volume quotidien alloué pour cet abonnement, les frais de données de dépassement par Go s’appliquent. 
 * Le volume de données quotidien alloué est calculé comme suit : nombre d’heures quotidiennes (UTC) pendant lesquelles chaque nœud envoie des données de télémétrie, divisé par 24 fois 200 Mo. Par conséquent, si 4 nœuds envoient des données de télémétrie pendant 15 heures sur les 24 heures de la journée, les données incluses pour le jour en question sont calculées comme suit : ((4 x 15)/24) x 200 Mo = 500 Mo. Au prix de 2,30 USD par Go pour le dépassement de données, les frais s'élèvent à 1,15 USD si les nœuds envoient 1 Go de données dans cette journée.
 * Notez que le volume quotidien alloué pour le plan Entreprise n’est pas partagé avec les applications pour lesquelles vous avez choisi l’option De base et le volume alloué inutilisé n’est pas reporté de jour en jour. 
* Voici quelques exemples de détermination du nombre de nœuds distincts :
| Scénario                               | Nombre total de nœuds quotidien |
|:---------------------------------------|:----------------:|
| 1 application utilise 3 instances d'Azure App Service et 1 serveur virtuel | 4 |
| 3 applications s’exécutant sur 2 machines virtuelles et les ressources Application Insights pour ces applications sont dans le même abonnement et dans le plan Entreprise | 2 | 
| 4 applications dont les ressources Application Insights sont dans le même abonnement. Chaque application exécute 2 instances pendant 16 heures creuses et 4 instances pendant 8 heures de pointe. | 13.33 | 
| Services cloud avec 1 rôle de travail et 1 rôle web, chacune exécutant 2 instances | 4 | 
| Cluster de 5 nœuds Service Fabric exécutant 50 micro-services, chaque micro-service exécutant 3 instances | 5.|

* La méthode de comptage de nœuds précise varie selon le Kit de développement logiciel (SDK) Application Insights que votre application utilise. 
  * Dans les versions du Kit de développement logiciel 2.2 et ultérieures, le [Kit de développement logiciel (SDK) principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) ou [le Kit de développement logiciel (SDK) web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) Application Insights signale chaque hôte d’application en tant que nœud, par exemple le nom d’ordinateur pour le serveur physique et les hôtes de machine virtuelle ou le nom d’instance dans le cas de services cloud.  La seule exception concerne les applications qui utilisent uniquement [.NET Core](https://dotnet.github.io/) et le Kit de développement logiciel (SDK) principal Application Insights, auquel cas un seul nœud sera signalé pour tous les hôtes, car le nom d’hôte n’est pas disponible. 
  * Pour les versions antérieures du Kit de développement logiciel (SDK), le [Kit de développement logiciel (SDK) web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se comporte comme les nouvelles versions du Kit de développement logiciel (SDK). Toutefois, le [Kit de développement logiciel (SDK) principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) ne signale qu’un seul nœud, quel que soit le nombre d’hôtes d’application actuel. 
  * Notez que, si votre application utilise le Kit de développement logiciel (SDK) pour définir l’instance de rôle (roleInstance) sur une valeur personnalisée, cette même valeur est utilisée par défaut pour déterminer le nombre de nœuds. 
  * Si vous utilisez une nouvelle version du Kit de développement logiciel (SDK) avec une application exécutée à partir d’ordinateurs clients ou d'appareils mobiles, il est possible que le nombre de nœuds retourné soit très important (en raison du grand nombre d’ordinateurs clients ou d'appareils mobiles). 

### <a name="multi-step-web-tests"></a>Tests web à plusieurs étapes

Des frais supplémentaires sont facturés pour les [tests web à plusieurs étapes](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Cela vaut pour les tests web qui exécutent une séquence d’actions. 

Aucun frais supplémentaire n'est facturé pour les tests Ping sur une seule page. Les données de télémétrie des tests Ping et des tests à plusieurs étapes sont facturées, ainsi que d’autres données de télémétrie de votre application.
 
## <a name="operations-management-suite-subscription-entitlement"></a>Droit d’abonnement à Operations Management Suite

Comme [annoncé récemment](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), les clients qui achètent Microsoft Operations Management Suite E1 et E2 peuvent d’obtenir gratuitement Application Insights Enterprise comme composant supplémentaire. Plus précisément, chaque unité Operations Management Suite E1 et E2 inclut un droit à 1 nœud du plan Entreprise d’Application Insights. Comme indiqué ci-dessus, chaque nœud Application Insights inclut l’intégration de 200 Mo de données maximum par jour (en plus de l’intégration des données Log Analytics), avec une période de rétention des données de 90 jours, sans coût supplémentaire. 

> [!NOTE]
> Pour pouvoir bénéficier de ce droit, vos ressources Application Insights doivent figurer dans le plan de tarification Entreprise. Ce droit s’applique uniquement sous forme de nœuds, et les ressources Application Insights du plan De base ne pourront donc pas en bénéficier. Notez que ce droit n’apparaîtra pas dans les coûts estimés affichés dans le panneau Fonctionnalités + tarification. 
>
 
## <a name="review-pricing-plans-and-estimate-costs"></a>Passer en revue les plans de tarification et estimer les coûts

Application Insights permet de comprendre facilement les plans tarifaires disponibles et d’estimer les coûts en fonction des modèles d’utilisation récente. Commencez par ouvrir le panneau **Fonctionnalités + tarification** dans la ressource Application Insights du portail Azure :

![Choisissez Tarification.](./media/app-insights-pricing/01-pricing.png)

**a.** Consultez votre volume de données pour le mois. Cela inclut toutes les données reçues et conservées (après tous les [échantillonnages](app-insights-sampling.md) provenant de votre serveur, de vos applications clientes et des tests de disponibilité).

**b.** Les [tests web multiétapes](app-insights-monitor-web-app-availability.md#multi-step-web-tests) font l’objet d’une facturation distincte. (Cela n’inclut pas les tests de disponibilité simples, qui sont compris dans les frais de volume de données.)

**c.** Activer le plan Entreprise.

**d.** Cliquez pour accéder aux options de gestion des données pour afficher le volume de données pour le dernier mois et définir une limite quotidienne ou un échantillonnage d’ingestion.

Les frais liés à Application Insights sont ajoutés à votre facture Azure. Les informations relatives à votre facture Azure s’affichent dans la section Facturation du portail Azure ou sur le [portail de facturation Azure](https://account.windowsazure.com/Subscriptions). 

![Dans le menu latéral, choisissez Facturation.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Débit de données
Le volume d’envoi de données est limité de trois façons :

* **Échantillonnage :** ce mécanisme permet de réduire la quantité de données de télémétrie envoyées à partir de votre serveur et de vos applications clientes, avec une distorsion minimale des métriques. C’est le principal outil dont vous disposez pour ajuster la quantité de données. Découvrez plus en détail les [fonctionnalités d’échantillonnage](app-insights-sampling.md). 
* **Limite quotidienne :** au moment de créer une ressource Application Insights à partir du portail Azure, cette limite est définie à 100 Go/jour. La valeur par défaut au moment de créer une ressource Application Insights à partir de Visual Studio est faible (seulement 32,3 Mo/jour) et vise uniquement à faciliter les tests. Dans ce cas, l’idée est que l’utilisateur augmente la limite quotidienne avant de déployer l’application en production. La limite maximale est de 1 000 Go/jour, à moins que vous en ayez demandé une plus élevée pour les besoins d’une application à fort trafic. Définissez la limite maximale avec précaution en partant du principe que vous ne devez **jamais atteindre la limite maximale**. Vous perdriez en effet le reste de données de la journée et ne pourriez pas surveiller votre application. Pour la modifier, servez-vous du panneau Limite quotidienne de volume, qui est lié au panneau Gestion du volume de données (voir ci-dessous). Notez que certains types d’abonnement disposent d’un crédit qui ne peut pas être utilisé pour Application Insights. Si l’abonnement impose une limite de dépense, le panneau Limite quotidienne indique comment la supprimer et permet d’augmenter la limite quotidienne au-delà de 32,3 Mo/jour.  
* **Limitation :** ce paramètre limite le débit de données à 32 000 événements par seconde, en moyenne sur 1 minute. 


*Que se passe-t-il si mon application dépasse le taux limite ?*

* Le volume de données que votre application envoie est évalué à chaque minute. S’il dépasse le taux par seconde moyen de plus d’une minute, le serveur refuse des demandes. Le Kit de développement logiciel (SDK) met les données en mémoire tampon et essaie ensuite de renvoyer, en diffusant un pic pendant plusieurs minutes. Si votre application envoie régulièrement des données au-delà du taux limite, certaines données sont supprimées. (les kits de développement logiciel (SDK) ASP.NET, Java et JavaScript essaient de renvoyer de cette manière ; les autres SDK suppriment parfois simplement les données limitées). En cas de limitation, vous en êtes informé par un avertissement.

*Comment déterminer la quantité de données envoyées par mon application ?*

* Ouvrez le panneau **Gestion du volume de données** pour afficher le graphique Volume de données quotidien. 
* Ou dans Metrics Explorer, ajoutez un nouveau graphique et sélectionnez la mesure **Volume du point de données** . Basculez sur le regroupement et regroupez par **Type de données**.

## <a name="to-reduce-your-data-rate"></a>Pour réduire le débit de données
Voici quelques opérations possibles pour réduire le volume de données :

* Utilisez l’ [échantillonnage](app-insights-sampling.md). Cette technologie réduit le débit de données sans fausser vos mesures et sans impacter la capacité à naviguer entre des éléments associés dans la recherche. Dans les applications serveurs, elle s’applique automatiquement.
* [Limitez le nombre d’appels Ajax qui peuvent être signalés](app-insights-javascript.md#detailed-configuration) dans chaque affichage de page, ou désactivez les rapports Ajax.
* Désactivez les modules de collecte dont vous n'avez pas besoin en [modifiant ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Par exemple, vous pouvez décider que les compteurs de performances ou les données de dépendance ne sont pas essentiels.
* Diviser vos données de télémétrie pour séparer les clés d’instrumentation. 
* Procédez à la pré-agrégation des métriques. Si vous avez placé des appels de TrackMetric dans votre application, vous pouvez réduire le trafic en utilisant la surcharge qui accepte votre calcul de la moyenne et de l’écart type d’un lot de mesures. Une autre possibilité consiste à utiliser un [package de pré-agrégation](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="managing-the-maximum-daily-data-volume"></a>Gestion du volume maximal quotidien de données

Vous pouvez utiliser le plafond de volume quotidien pour limiter les données collectées, mais, s’il est atteint, vous perdrez toute la télémétrie envoyée par votre application pour le reste de la journée. Il est **déconseillé** de laisser votre application atteindre le plafond quotidien, car vous ne pouvez pas suivre l’intégrité et les performances de votre application une fois que ce plafond est atteint. 

Utilisez plutôt [l’Échantillonnage](app-insights-sampling.md) pour ajuster le volume de données au niveau souhaité, et le plafond quotidien seulement en dernier recours, dans le cas où votre application se mettrait subitement à envoyer des volumes de télémétrie largement supérieurs. 

Pour modifier le plafond quotidien, cliquez sur **Gestion du volume de données** dans la section Configurer de votre ressource Application Insights, puis sur **Plafond quotidien**.

![Ajustement de la limite du volume quotidien des données de télémétrie](./media/app-insights-pricing/daily-cap.png) 

## <a name="sampling"></a>échantillonnage
[Sampling](app-insights-sampling.md) est une méthode vous permettant de réduire la fréquence d’envoi des données de télémétrie à votre application, tout en conservant la capacité à trouver des événements connexes lors des recherches de diagnostic, ainsi que le décompte des événements corrects. 

L’échantillonnage est un moyen efficace de réduire les coûts et de respecter votre quota mensuel. L’algorithme d’échantillonnage conserve les éléments associés à la télémétrie, afin que, lorsque vous utilisez la recherche par exemple, vous puissiez trouver la demande liée à une exception spécifique. L’algorithme conserve également le décompte correct. Cela vous permet de voir les valeurs correctes des taux de demandes, des taux d’exception et des autres compteurs dans Metric Explorer.

Il existe plusieurs formes d’échantillonnage.

* [L’échantillonnage adaptatif](app-insights-sampling.md) est la méthode par défaut pour le kit de développement logiciel (SDK) ASP.NET. Il s’ajuste automatiquement au volume de données de télémétrie envoyées par votre application. Il fonctionne automatiquement dans le Kit de développement logiciel (SDK) de votre application web, afin de réduire le trafic de télémétrie sur le réseau. 
* *échantillonnage d’ingestion* est une méthode alternative qui fonctionne au niveau où les données de télémétrie issues de votre application entrent dans le service Application Insights. Il n’affecte pas le volume de télémétrie envoyé depuis votre application, mais il réduit le volume conservé par le service. Vous pouvez l’utiliser pour réduire le quota utilisé par les données de télémétrie provenant des navigateurs et d’autres kits de développement logiciel (SDK).

Pour définir l’échantillonnage d’ingestion, définissez le contrôle dans le panneau Tarification :

![Dans le panneau Quotas et tarification, cliquez sur la vignette Exemples et sélectionnez une fraction d’échantillonnage.](./media/app-insights-pricing/04.png)

> [!WARNING]
> Le panneau d’échantillonnage de données contrôle uniquement la valeur d’échantillonnage de l’ingestion. Il ne reflète pas le taux d’échantillonnage qui est appliqué par le Kit de développement logiciel (SDK) Application Insights dans votre application. Si les données de télémétrie entrantes ont déjà été échantillonnées dans le kit de développement logiciel (SDK), l’échantillonnage d’ingestion n’est pas appliqué.
> 

Pour découvrir le taux d’échantillonnage réel, indépendamment de l’endroit où il a été appliqué, utilisez une [requête Analytics](app-insights-analytics.md) telle que celle-ci :

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

Pour chaque enregistrement conservé, `itemCount` indique le nombre d’enregistrements d’origine qu’il représente, soit 1 + le nombre d’enregistrements précédents ignorés. 


## <a name="automation"></a>Automatisation

Vous pouvez utiliser le service de gestion de ressources Azure pour écrire un script de définition de plan tarifaire. [Découvrez comment](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Synthèse des limites
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Échantillonnage](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

