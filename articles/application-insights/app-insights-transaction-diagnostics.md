---
title: Diagnostics de transaction Azure Application Insights | Microsoft Docs
description: Diagnostics de transaction de bout en bout Application Insights
services: application-insights
documentationcenter: .net
author: SoubhagyaDash
manager: victormu
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2018
ms.author: sdash
ms.openlocfilehash: 1c7eaafe99717324ad03287a1f1e0699d77cc74f
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnostics de transaction entre composants unifiés

*Cette expérience est actuellement en préversion et remplace les panneaux de diagnostics existants pour les requêtes côté serveur, les dépendances et les exceptions.*

La préversion introduit une nouvelle expérience de diagnostics unifiés qui met automatiquement en corrélation la télémétrie côté serveur de tous vos composants surveillés Application Insights dans une vue unique. Le fait d’avoir plusieurs ressources avec des clés d’instrumentation distinctes ne pose pas de problème. Application Insights détecte la relation sous-jacente et permet de diagnostiquer facilement le composant d’application, la dépendance ou l’exception à l’origine du ralentissement ou de l’échec de la transaction.

## <a name="what-is-a-component"></a>Qu’est un composant ?

Les composants sont des parties pouvant être déployées de manière indépendante de votre application distribuée/de microservices. Les développeurs et équipes d’opérations disposent d’une visibilité au niveau du code ou d’un accès à la télémétrie générée par ces composants d’application.

* Les composants sont différents des dépendances externes « observées » telles que SQL, EventHub, etc., auxquelles votre équipe/organisation peut ne pas avoir accès (code ou télémétrie).
* Les composants s’exécutent sur un nombre quelconque d’instances de serveur/rôle/conteneur.
* Les composants peuvent être des clés d’instrumentation Application Insights distinctes (même si les abonnements sont différents) ou des rôles différents rapportant à une clé d’instrumentation Application Insights unique. La nouvelle expérience affiche des détails sur tous les composants, quelle que soit leur configuration.

> [!NOTE]
> * **Il vous manque les liens des éléments connexes ?** Toutes les données de télémétrie liées à la requête côté serveur, à la dépendance et à l’exception se trouvent dans les sections en [haut](#cross-component-transaction-chart) et en [bas](#all-telemetry-related-to-the-selected-component-operation) à gauche. 
> * La section du [haut](#cross-component-transaction-chart) met en corrélation la transaction avec tous les composants. Pour de meilleurs résultats, vérifiez que tous les composants sont instrumentés avec les derniers Kits de développement logiciel (SDK) stables d’Application Insights. S’il existe différentes ressources Application Insights, assurez-vous de disposer des autorisations appropriées pour afficher leur télémétrie.
> * La section en [bas](#all-telemetry-related-to-the-selected-component-operation) à gauche présente **toutes** les données de télémétrie, y compris les traces et les événements liés à la requête provenant du composant sélectionné.

## <a name="enable-transaction-diagnostics-experience"></a>Activer l’expérience de diagnostic des transactions
Activez « Unified details: E2E Transaction Diagnostics » dans la [liste de préversions](app-insights-previews.md)

![Activer la version préliminaire](media/app-insights-e2eTxn-diagnostics/previews.png)

Cette préversion est actuellement disponible pour les requêtes côté serveur, les dépendances et les exceptions. Vous pouvez accéder à la nouvelle expérience à partir des expériences de triage **Résultats de la recherche**, **Performances** ou **Échec**. La préversion remplace les panneaux d’informations classiques correspondants.

![Exemples de performances](media/app-insights-e2eTxn-diagnostics/performanceSamplesClickThrough.png)

## <a name="transaction-diagnostics-experience"></a>Expériences de diagnostics de transaction
Cette vue compte trois parties principales : un graphique de transaction entre composants, une liste de séquence horaire de l’ensemble de la télémétrie d’une opération de composant spécifique, et le volet d’informations d’un élément de télémétrie sélectionné sur la gauche.

![Parties principales](media/app-insights-e2eTxn-diagnostics/3partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Graphique des transactions entre composants

Ce graphique fournit une chronologie avec des barres horizontales pendant la durée des requêtes et des dépendances entre les composants. Les exceptions collectées sont également marquées sur la chronologie.

* La ligne supérieure de ce graphique représente le point d’entrée, la requête entrante vers le premier composant appelé dans cette transaction. La durée correspond à la durée d’exécution totale de la transaction.
* Les appels vers des dépendances externes sont représentés par des lignes non réductibles simples, avec des icônes représentant le type de dépendance.
* Les appels vers d’autres composants sont représentés par des lignes réductibles. Chaque ligne correspond à une opération spécifique appelée au niveau du composant.
* Par défaut, la requête, la dépendance ou l’exception que vous avez initialement sélectionnée s’affiche sur le graphique.
* Sélectionnez une ligne pour afficher ses [détails à droite](#details-of-the-selected-telemetry). 

> [!NOTE]
Les appels vers d’autres composants comptent deux lignes : une ligne représente l’appel sortant (dépendance) à partir du composant appelant, et l’autre ligne correspond à la requête entrante au niveau du composant appelé. L’icône de début et le style distinct des barres de durée permettent de les différencier.

## <a name="all-telemetry-related-to-the-selected-component-operation"></a>Toutes les données de télémétrie liées à l’opération de composant sélectionnée

Une ligne sélectionnée dans le graphique de transaction entre composants est liée à une opération appelée sur un composant spécifique. Cette opération de composant sélectionnée est indiquée dans le titre de la section inférieure. Ouvrez cette section pour afficher une séquence horaire plate de l’ensemble de la télémétrie liée à cette opération spécifique. Vous pouvez sélectionner un élément de télémétrie de cette liste pour afficher les [détails correspondants à droite](#details-of-the-selected-telemetry).

![Séquence horaire de l’ensemble de la télémétrie](media/app-insights-e2eTxn-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Détails de la télémétrie sélectionnée

Ce volet affiche les détails des éléments sélectionnés dans l’une des deux sections sur la gauche. « Afficher tout » répertorie tous les attributs standard qui sont collectés. Les attributs personnalisés sont répertoriés séparément sous le jeu standard. Cliquez sur « Open profiler traces » (Ouvrir les traces du profileur) ou sur « Open debug snapshot » (Ouvrir l’instantané de débogage) pour les diagnostics au niveau du code dans les volets d’informations correspondants.

![Détail de l’exception](media/app-insights-e2eTxn-diagnostics/exceptiondetail.png)

## <a name="profiler-and-snapshot-debugger"></a>Profileur et débogueur de capture instantanée

Le [profileur Application Insights](app-insights-profiler.md) ou le [débogueur de la capture instantanée](app-insights-snapshot-debugger.md) apporte une aide avec des diagnostics au niveau du code des problèmes de performances et d’échec. Grâce à cette expérience, vous pouvez afficher les traces du profileur ou les instantanés d’un composant d’un simple clic.

Si Profiler ne fonctionne pas, contactez **serviceprofilerhelp@microsoft.com**.

Si le Débogueur de capture instantanée ne fonctionne pas, contactez **snapshothelp@microsoft.com**.

![Intégration du débogueur](media/app-insights-e2eTxn-diagnostics/debugSnapshot.png)

## <a name="faq"></a>Forum Aux Questions

*Je vois un composant unique sur le graphique, et les autres n’apparaissent que comme des dépendances externes sans détails sur ce qu’il s’est passé dans ces composants.*

Raisons possibles :

* Les autres composants sont-ils instrumentés avec Application Insights ?
* Utilisent-ils le dernier Kit de développement logiciel (SDK) d’Application Insights stable ?
* Si ces composants sont des ressources Application Insights distinctes, disposez-vous de l’accès requis à leur télémétrie ?

Si vous n’avez pas accès et si les composants sont instrumentés avec le dernier Kit de développement logiciel (SDK) Application Insights, prévenez-nous via le canal de commentaires en haut à droite.

*Je dispose uniquement de dépendances externes. Dois-je prendre en compte cette préversion ?*

Oui. La nouvelle expérience unifie l’ensemble de la télémétrie côté serveur associée dans une vue unique. Les panneaux d’informations antérieurs seront remplacés ultérieurement par cette expérience, essayez-la et faites-nous part de vos commentaires. Voici à quoi elle ressemble pour une transaction de composant unique :

![Expérience de composant unique](media/app-insights-e2eTxn-diagnostics/singleComponent.png)

*Je vois des lignes en double pour les dépendances. Est-ce normal ?*

À ce stade, nous présentons l’appel de dépendance sortant séparément de la requête entrante. En règle générale, les deux appels semblent identiques, seule la valeur de durée est différente en raison de l’aller-retour réseau. L’icône de début et le style distinct des barres de durée permettent de les différencier. Cette présentation des données porte-t-elle à confusion ? Faites-nous part de vos commentaires !

*Qu’en-est-il des variations d’horloges entre différentes instances de composant ?*

Les chronologies sont ajustées pour les variations d’horloges dans le graphique de transaction. Vous pouvez voir les timestamps exacts dans le volet d’informations ou à l’aide d’Analytics.

*Pourquoi la plupart des requêtes d’éléments associés est manquante dans la nouvelle expérience ?*

C’est normal. Tous les éléments associés, sur tous les composants, sont déjà disponibles sur le côté gauche (sections supérieure et inférieure). La nouvelle expérience comporte deux éléments associés non couverts par le côté gauche : l’ensemble de la télémétrie cinq minutes avant et après cet événement et la chronologie utilisateur.

*Pourquoi la nouvelle expérience ne comporte pas la fonctionnalité que j’aimais dans les panneaux antérieurs ?*

Faites-nous part de vos commentaires ! Nous voulons résoudre vos problèmes avant la mise à disposition générale de cette expérience, où les anciennes vues seront déconseillées. Pour le moment, vous pouvez désactiver la préversion pour revenir aux panneaux antérieurs.

## <a name="known-issues"></a>Problèmes connus

* Les exemples d’échecs d’Application Map renvoient vers les panneaux d’informations antérieurs.
* Les aperçus basés sur un autocluster dans les résultats de la recherche renvoient vers les panneaux d’informations antérieurs.
* L’intégration d’élément de travail n’est pas disponible dans la nouvelle expérience.
