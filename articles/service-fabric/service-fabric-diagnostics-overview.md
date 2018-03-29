---
title: Présentation de la surveillance et des diagnostics Azure Service Fabric | Microsoft Docs
description: Découvrez la surveillance et les diagnostics pour les clusters, applications et services Azure Service Fabric.
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
ms.date: 01/10/2018
ms.author: dekapur
ms.openlocfilehash: f784576547f0d85a825ad9dd107c6c84cd261092
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Surveillance et diagnostics pour Azure Service Fabric

Cet article fournit une vue d’ensemble de la configuration du monitoring et des diagnostics pour des applications exécutées dans des clusters Service Fabric. Le monitoring et les diagnostics sont essentiels au développement, au test et au déploiement de charges de travail dans tout environnement cloud. Le monitoring vous permet de faire le suivi de la façon dont vos applications sont utilisées, de votre utilisation des ressources et de l’intégrité globale de votre cluster. Vous pouvez utiliser ces informations pour diagnostiquer et corriger les problèmes dans le cluster et éviter qu’ils ne se reproduisent à l’avenir. 

Les principaux objectifs de la surveillance et des diagnostics sont de :
* Détecter et diagnostiquer les problèmes d’infrastructure
* Détecter les problèmes liés à votre application
* Comprendre la consommation des ressources
* Faire le suivi du niveau de performance des applications, des services et de l’infrastructure

Dans un cluster Service Fabric, les données de monitoring et de diagnostic proviennent de trois niveaux : application, plateforme (cluster) et infrastructure. 
* Le [niveau application](service-fabric-diagnostics-event-generation-app.md) inclut des données sur les performances de vos applications et toute journalisation personnalisée que vous avez ajoutée. Les données de monitoring des applications doivent être envoyées à AI (Application Insights) par les applications elles-mêmes. Les données peuvent être transmises par le SDK AI, EventFlow ou un autre pipeline de votre choix.
* Le [niveau plateforme](service-fabric-diagnostics-event-generation-infra.md) inclut des événements provenant d’actions effectuées sur votre cluster et liées à la gestion du cluster et aux applications exécutées sur celui-ci. Les données de monitoring de la plateforme doivent être envoyées à OMS Log Analytics avec la solution Service Fabric Analytics pour faciliter la visualisation des événements entrants. Ces données sont généralement envoyées par le biais de l’extension Azure Diagnostics pour Windows ou Linux à un compte de stockage accessible à OMS Log Analytics. 
* Le niveau infrastructure porte sur le [monitoring des performances](service-fabric-diagnostics-event-generation-perf.md). L’examen de métriques et de compteurs de performances clés permet de déterminer l’utilisation des ressources et de la charge. Il est possible d’utiliser un agent pour le monitoring des performances. Nous vous recommandons d’utiliser l’Agent OMS (Microsoft Monitoring) pour que vos données de performances se retrouvent au même endroit que vos événements de plateforme. Vous pourrez ainsi mettre en corrélation les changements sur tout le cluster et bénéficier d’une meilleure expérience en matière de diagnostics. 

![Graphique de vue d’ensemble des diagnostics](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="monitoring-scenarios"></a>Scénarios de monitoring

Cette section décrit les scénarios de monitoring clés d’un cluster Service Fabric : application, cluster, performances et intégrité. L’intention et l’approche globale de chaque scénario sont présentées. Pour plus d’informations et d’autres recommandations générales sur le monitoring des ressources Azure, consultez [Bonnes pratiques - Surveillance et diagnostics](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 

Ces scénarios sont mappés de manière approximative aux trois niveaux de données de monitoring et de diagnostic traités ci-dessus. Pour que chaque scénario soit correctement géré dans le cluster, faites en sorte que les données de monitoring et de diagnostic soient acheminées au niveau correspondant. Le scénario de monitoring de l’intégrité constitue une exception, car il englobe le cluster et tout ce qui s’exécute à l’intérieur.

## <a name="application-monitoring"></a>Monitoring des applications
Le monitoring des applications permet de faire le suivi de l’utilisation des fonctionnalités et des composants de votre application. L’objectif est d’intercepter les problèmes qui impactent les utilisateurs. Le monitoring des applications peut servir à :
* Déterminer la charge de l’application et le trafic des utilisateurs pour savoir s’il faut mettre à l’échelle les services pour répondre aux demandes des utilisateurs ou traiter un goulot d’étranglement potentiel
* Identifier les problèmes de communication avec le service, à distance ou non, sur l’ensemble de votre cluster
* Comprendre ce que font vos utilisateurs avec vos applications : l’instrumentation peut contribuer à orienter le développement de nouvelles fonctionnalités et à améliorer les diagnostics d’erreurs

Pour le monitoring au niveau application dans Service Fabric, nous vous recommandons d’utiliser AI (Application Insights). L’intégration d’AI à Service Fabric inclut des expériences basées sur des outils pour Visual Studio et le portail Azure, ainsi qu’une compréhension du contexte de service et de la communication à distance Service Fabric dans le tableau de bord AI et la cartographie d’application. Vous bénéficiez donc de fonctionnalités de journalisation complètes et prêtes à l’emploi. Bien que de nombreux journaux soient automatiquement créés et collectés pour vous quand vous utilisez AI, nous vous recommandons d’ajouter une journalisation personnalisée à vos applications et de faire apparaître le résultat aux côtés des éléments fournis pour créer une expérience de diagnostics plus riche pour la gestion des problèmes futurs. Pour plus d’informations sur l’utilisation d’AI avec Service Fabric, consultez [Analyse d’événements avec Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md). 

Pour commencer à instrumenter votre code afin de surveiller vos applications, consultez [Génération de journaux et d’événements au niveau application](service-fabric-diagnostics-event-generation-app.md). 

### <a name="monitoring-application-availability"></a>Monitoring de la disponibilité des applications
Même si tous les éléments de votre cluster semblent s’exécuter comme prévu et de manière saine, il peut arriver que vos applications soient inaccessibles. Bien que cela se produise rarement, il est important de savoir quand un tel problème se manifeste pour pouvoir l’atténuer le plus rapidement possible. D’une manière générale, le monitoring de disponibilité assure le suivi de la disponibilité des composants de votre système dans le but de comprendre sa « durée de fonctionnement » globale. Dans un cluster, nous étudions la disponibilité du point de vue de votre application, la plateforme veillant à ce que les scénarios de gestion du cycle de vie de l’application ne provoquent pas de temps d’arrêt. Toutefois, différents problèmes liés au cluster peuvent impacter la durée de fonctionnement de votre application. En tant que propriétaire de l’application, vous tenez à en être immédiatement informé. Nous vous recommandons de déployer, aux côtés de toutes les autres applications, un agent de surveillance dans votre cluster. Cet agent de surveillance a pour objectif de vérifier les points de terminaison appropriés de votre application à un intervalle de temps donné et d’indiquer s’ils sont accessibles. Vous pouvez également faire appel à un service externe qui effectue un test ping sur le point de terminaison et qui envoie un rapport à l’intervalle de temps donné.

## <a name="cluster-monitoring"></a>Monitoring du cluster
Le monitoring de votre cluster Service Fabric est essentiel pour garantir que la plateforme et toutes les charges de travail exécutées sur celle-ci fonctionnent comme prévu. L’un des objectifs de Service Fabric est d’assurer le bon fonctionnement des applications même en cas de défaillances matérielles. Sa réalisation repose sur la capacité des services système de la plateforme à détecter les problèmes d’infrastructure et à basculer rapidement les charges de travail sur d’autres nœuds du cluster. Mais dans ce cas précis, que se passe-t-il si les services système subissent eux aussi des problèmes ? Que se passe-t-il si, durant une tentative de déplacement d’une charge de travail, les règles de placement des services sont enfreintes ? Le monitoring du cluster vous permet d’être tenu informé de l’activité dans votre cluster pour diagnostiquer les problèmes et les résoudre efficacement. Voici quelques points clés à évaluer :
* Service Fabric se comporte-t-il de la manière attendue, aussi bien au niveau du placement de vos applications que de l’équilibrage de la charge de travail autour du cluster ? 
* Les mesures prises pour modifier la configuration de votre cluster sont-elles reconnues et mises à exécution comme prévu ? Ceci s’applique surtout lors de la mise à l’échelle d’un cluster.
* Service Fabric gère-t-il correctement vos données et vos communications de service à service à l’intérieur du cluster ?

Service Fabric propose un ensemble complet d’événements prêts à l’emploi qui sont accessibles par le biais du canal opérationnel et du canal de données et de messagerie. Dans Windows, ces événements se présentent sous la forme d’un fournisseur ETW unique comprenant un ensemble de `logLevelKeywordFilters` à l’aide desquels vous pouvez choisir entre les différents canaux. Sur Linux, tous les événements de la plateforme transitent par LTTng et sont placés dans une table où ils peuvent être filtrés selon les besoins. 

Ces canaux contiennent des événements organisés et structurés que vous pouvez utiliser pour mieux comprendre l’état de votre cluster. « Diagnostics » est activé par défaut au moment de la création du cluster. Vous disposez ainsi d’une table Stockage Azure où sont envoyés les événements de ces canaux que vous pourrez interroger ultérieurement. Pour plus d’informations sur le monitoring de votre cluster, consultez [Génération d’événements et de journaux au niveau plateforme](service-fabric-diagnostics-event-generation-infra.md). Nous vous recommandons d’utiliser OMS Log Analytics pour surveiller votre cluster. OMS Log Analytics offre une solution spécifique à Service Fabric : Service Fabric Analytics. Cette solution, qui fournit un tableau de bord personnalisé pour le monitoring des clusters Service Fabric, vous permet aussi d’interroger les événements de votre cluster et de configurer des alertes. Pour plus d’informations, consultez [Analyse des événements avec OMS](service-fabric-diagnostics-event-analysis-oms.md). 

### <a name="watchdogs"></a>Agents de surveillance
En général, un agent de surveillance est un service distinct capable de surveiller l’intégrité et la charge des services, d’effectuer des tests ping sur les points de terminaison et de créer des rapports d’intégrité pour n’importe quel élément du cluster. Cela permet d’éviter des erreurs qui ne seraient pas détectées à partir de l’affichage d’un service unique. Les agents de surveillance sont également un bon emplacement pour héberger du code qui exécute des actions correctives sans intervention de l’utilisateur (par exemple, le nettoyage de fichiers journaux dans le stockage à intervalles réguliers). Vous trouverez un exemple d’implémentation de service d’agent de surveillance [ici](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="performance-monitoring"></a>Analyse des performances
Le monitoring de l’infrastructure sous-jacente est essentiel pour comprendre l’état de votre cluster et l’utilisation de vos ressources. La mesure des performances système dépend de plusieurs facteurs, chacun d’eux étant généralement mesuré au moyen de KPI (indicateurs de performance clés). Les KPI pertinents pour Service Fabric peuvent être mappés à des métriques que vous pouvez collecter à partir des nœuds de votre cluster sous la forme de compteurs de performances.
Ces KPI peuvent vous aider à :
* Comprendre l’utilisation et la charge des ressources dans le but de mettre à l’échelle votre cluster ou d’optimiser vos processus de service.
* Prédire les problèmes d’infrastructure. De nombreux problèmes sont précédés d’un changement (déclin) soudain des performances. Vous pouvez donc utiliser des KPI comme E/S réseau et Utilisation de l’UC pour prédire et diagnostiquer les problèmes liés à l’infrastructure.

Vous trouverez la liste des compteurs de performances à collecter au niveau infrastructure dans [Métriques de performances](service-fabric-diagnostics-event-generation-perf.md). 

Pour le monitoring des performances au niveau application, Service Fabric propose un ensemble de compteurs de performances pour les modèles de programmation Reliable Services et Actors. Si vous utilisez l’un de ces modèles, ces compteurs de performances peuvent fournir des KPI vous permettant de vérifier que les procédures de « spin up » et de « spin down » de vos acteurs se déroulent correctement ou que vos demandes de services fiables sont gérées assez rapidement. Pour plus d’informations, consultez [Monitoring pour Reliable Service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) et [Monitoring des performances pour Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Application Insights comprend également un ensemble de métriques de performances qui sont collectées si vous les configurez avec votre application.

Utilisez l’agent OMS pour collecter les compteurs de performances appropriés et afficher ces KPI dans OMS Log Analytics. Vous pouvez également utiliser l’extension de l’agent Azure Diagnostics (ou tout autre agent similaire) pour collecter et stocker les métriques à des fins d’analyse. 

## <a name="health-monitoring"></a>Surveillance de l’intégrité
La plateforme Service Fabric inclut un modèle d’intégrité qui fournit des rapports d’intégrité extensibles sur l’état des entités dans un cluster. Chaque nœud, application, service, partition, réplica ou instance a un état d’intégrité qui est mis à jour en permanence. L’état d’intégrité peut avoir la valeur « OK », « Avertissement » ou « Erreur ». Il est changé par le biais des rapports d’intégrité qui sont émis pour chaque entité en fonction des problèmes affectant le cluster. Vous pouvez vérifier à tout moment l’état d’intégrité de vos entités dans Service Fabric Explorer (SFX), comme indiqué ci-dessous, ou l’interroger au moyen de l’API Health de la plateforme. Vous pouvez aussi personnaliser les rapports d’intégrité et modifier l’état d’intégrité d’une entité en ajoutant vos propres rapports d’intégrité ou en utilisant l’API Health. Pour plus d’informations sur le modèle d’intégrité, consultez [Présentation du contrôle d’intégrité de Service Fabric](service-fabric-health-introduction.md).

![Tableau de bord d’intégrité SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Outre l’affichage des derniers rapports d’intégrité dans SFX, chaque rapport est également disponible sous la forme d’un événement. Vous pouvez collecter les événements d’intégrité par le biais du canal opérationnel (consultez [Agrégation d’événements avec Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)) et les stocker dans OMS Log Analytics pour générer des alertes et lancer des requêtes par la suite. Ceci facilite la détection des problèmes qui peuvent impacter la disponibilité de votre application. Nous vous recommandons donc de définir des alertes pour les scénarios d’échec appropriés (alertes personnalisées par le biais d’OMS).

## <a name="monitoring-workflow"></a>Flux de travail du monitoring 

Le flux de travail global de la surveillance et des diagnostics se compose de trois étapes :

1. **Génération d’événements** : il s’agit des événements (journaux, traces, événements personnalisés) aux niveaux infrastructure, plateforme (cluster) et application.
2. **Agrégation d’événements** : cette étape constitue le pipeline qui achemine vos événements dans un outil dans lequel vous pouvez les analyser (qui comprend l’extension Azure Diagnostics ou EventFlow).
3. **Analyse** : vous devez pouvoir accéder aux événements dans un outil doté de fonctionnalités d’analyse, de visualisation, d’interrogation, de génération d’alertes, etc.

Plusieurs produits couvrant ces trois domaines sont disponibles, et vous êtes libre de choisir des technologies différentes pour chacun d’eux. Vous devez vous assurer que les différents composants s’associent parfaitement afin de fournir une solution de surveillance de bout en bout pour votre cluster.

## <a name="event-generation"></a>Génération d’événement

La première étape du flux de travail de monitoring et de diagnostics consiste à configurer la création et la génération des données d’événements et de journaux. Ces événements, journaux et compteurs de performances sont émis sur les trois niveaux : application (n’importe quelle instrumentation ajoutée aux applications et services déployés sur le cluster), plateforme (événements émis à partir du cluster selon le fonctionnement de Service Fabric) et infrastructure (métriques de performances de chaque nœud). Les données de diagnostic collectées sur chacun de ces niveaux sont personnalisables, bien que Service Fabric active certaines options d’instrumentation par défaut. 

Pour comprendre ce qui est fourni et comment ajouter une instrumentation, vous pouvez poursuivre votre lecture sur les [événements de niveau plateforme](service-fabric-diagnostics-event-generation-infra.md) et les [événements de niveau application](service-fabric-diagnostics-event-generation-app.md). Ce qui importe le plus ici, c’est de savoir comment vous souhaitez instrumenter votre application. Pour les applications .NET, nous vous recommandons d’utiliser ILogger, mais vous pouvez également explorer EventSource, Serilog et d’autres bibliothèques similaires. Pour Java, nous vous recommandons d’utiliser Log4j. D’autres options sont disponibles et peuvent être utilisées selon la nature de l’application. N’hésitez pas à explorer les différentes options pour trouver celle qui est la mieux adaptée à votre cas d’usage spécifique, ou choisissez celle que vous maîtrisez le mieux. 

Après avoir choisi le fournisseur de journalisation à utiliser, vous devez vérifier que vos journaux sont agrégés et stockés correctement.

## <a name="event-aggregation"></a>Agrégation des événements

Pour la collecte des journaux et des événements générés par vos applications et votre cluster, nous recommandons généralement d’utiliser [l’extension Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) (qui s’apparente davantage à la collecte de journaux en fonction de l’agent) ou [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (collecte de journaux in-process). Si vous utilisez Application Insights et que vous développez en .NET ou Java, nous vous recommandons d’utiliser le SDK Application Insights à la place d’EventFlow.

Bien qu’il soit possible d’obtenir des résultats similaires avec une seule de ces solutions, la combinaison de l’agent d’extension Azure Diagnostics et d’un pipeline de collection in-process (SDK AI ou EventFlow) engendre dans la plupart des cas un flux de monitoring plus fiable et plus complet. Vous pouvez choisir l’agent d’extension Azure Diagnostics pour les événements au niveau plateforme et le SDK AI ou EventFlow (collection in-process) pour les journaux au niveau application. 

Au cas où vous souhaiteriez n’utiliser qu’une seule de ces solutions, voici quelques points importants à retenir.
* La collecte des journaux d’application à l’aide de l’extension Azure Diagnostics s’avère efficace pour Service Fabric si l’ensemble de sources et de destinations des journaux ne change pas souvent et s’il existe un mappage simple entre les sources et les destinations. En effet, comme la configuration d’Azure Diagnostics se produit au niveau de la couche du Gestionnaire des ressources, l’apport de changements importants à la configuration nécessite la mise à jour de tout le cluster. Cela signifie qu’elle finit souvent par être moins efficace que le SDK AI ou EventFlow.
* Grâce à EventFlow, vous pouvez faire en sorte que les services envoient leurs journaux directement à une plateforme d’analyse et de visualisation et/ou au stockage. D’autres bibliothèques (ILogger, Serilog, etc.) peuvent également être utilisées dans le même but, mais EventFlow présente l’avantage d’avoir été conçu spécifiquement pour la collecte de journaux in-process et pour prendre en charge les services Service Fabric. Il en découle généralement plusieurs avantages : d’une part, la simplicité de la configuration et du déploiement et, d’autre part, davantage de souplesse du fait de la prise en charge de différents outils d’analyse et bibliothèques de journalisation. 

## <a name="event-analysis"></a>Analyse des événements

Plusieurs plateformes formidables sont disponibles sur le marché pour l’analyse et la visualisation des données de surveillance et de diagnostic. Les deux que nous recommandons sont [OMS](service-fabric-diagnostics-event-analysis-oms.md) et [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), en raison de leur intégration à Service Fabric. Considérez également [Elastic Stack](https://www.elastic.co/products) (surtout si vous envisagez d’exécuter un cluster dans un environnement hors connexion), [Splunk](https://www.splunk.com/) ou toute autre plateforme de votre choix. 

Quelle que soit la plateforme que vous choisissez, ses points clés doivent inclure la convivialité de l’interface utilisateur et des options de requêtes, la possibilité de visualiser les données et de créer des tableaux de bord faciles à lire, ainsi que les outils supplémentaires qu’elle met à votre disposition pour améliorer la surveillance, tels que l’alerte automatisée.

Outre la plateforme choisie, quand vous configurez un cluster Service Fabric comme ressource Azure, vous disposez des fonctionnalités de monitoring des performances prêtes à l’emploi d’Azure pour vos machines.

### <a name="azure-monitor"></a>Azure Monitor

Vous pouvez utiliser [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) pour surveiller un grand nombre des ressources Azure sur lesquelles repose un cluster Service Fabric. Un ensemble de mesures pour le [groupe de machines virtuelles identiques](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) et les [machines virtuelles](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) individuelles est automatiquement collecté et affiché dans le portail Azure. Pour consulter les informations collectées, dans le portail Azure, sélectionnez le groupe de ressources qui contient le cluster Service Fabric. Ensuite, sélectionnez le groupe de machines virtuelles identiques à afficher. Dans la section **Surveillance** (navigation de gauche), sélectionnez **Métriques** pour afficher un graphe des valeurs.

![Vue des informations de mesure collectées sur le portail Azure](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Pour personnaliser les graphiques, suivez les instructions présentées dans la [vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Vous pouvez également créer des alertes basées sur ces mesures, comme décrit dans l’article [Créer des alertes dans Azure Monitor pour les services Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). 

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le monitoring de la plateforme et des événements fournis par Service Fabric, consultez [Génération d’événements et de journaux au niveau plateforme](service-fabric-diagnostics-event-generation-infra.md)
* Pour commencer à instrumenter vos applications, consultez [Génération d’événements et de journaux au niveau application](service-fabric-diagnostics-event-generation-app.md).
* Suivez les étapes de configuration de l’intelligence artificielle pour votre application dans la section [Surveiller et diagnostiquer une application ASP.NET Core dans Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Pour découvrir comment configurer OMS Log Analytics pour la surveillance des conteneurs, consultez la page [Monitorage et diagnostics des conteneurs Windows dans Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).

