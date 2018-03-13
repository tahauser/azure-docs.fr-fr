---
title: "Détection intelligente - anomalies de performances | Microsoft Docs"
description: "Application Insights réalise une analyse télémétrique intelligente de votre application et vous avertit des éventuels problèmes de performances. Cette fonctionnalité ne nécessite aucune configuration."
services: application-insights
documentationcenter: windows
author: antonfrMSFT
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: 3310239b5569ca5b63bd39acb4d192a4e54780e4
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="smart-detection---performance-anomalies"></a>Détection intelligente - anomalies de performances

[Application Insights](app-insights-overview.md) analyse automatiquement les performances de votre application web et peut vous avertir des éventuels problèmes. Vous pouvez lire ceci parce que vous avez reçu une de nos notifications de détection intelligente.

Cette fonctionnalité ne requiert aucune configuration spéciale, sauf la configuration de votre application pour Application Insights (sur [ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md) ou [Node.js](app-insights-nodejs.md) et dans le [code de page web](app-insights-javascript.md)). Elle est active lorsque votre application génère suffisamment de données de télémétrie.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Quand pouvez-vous recevoir une notification de détection intelligente ?

Application Insights a détecté que les performances de votre application se sont dégradées dans l’une des manières suivantes :

* **Dégradation du temps de réponse** : votre application a démarré en répondant plus lentement qu’auparavant aux demandes. Les changements peuvent avoir été rapides, par exemple en raison d’une régression dans votre dernier déploiement. Ou ils peuvent avoir été progressifs, probablement du fait d’une fuite de mémoire. 
* **Dégradation de la durée de dépendance** : votre application appelle une API REST, une base de données ou autre dépendance. La dépendance répond plus lentement qu’auparavant.
* **Modèle de performances lentes** : votre application semble rencontrer un problème de performances qui n’affecte que certaines demandes. Par exemple, les pages se chargent plus lentement dans un type de navigateur que dans d’autres, ou les demandes sont traitées plus lentement à partir d’un serveur particulier. À l’heure actuelle, nos algorithmes examinent le temps de chargement des pages, le temps de réponse aux demandes et le temps de réponse des dépendances.  

La détection intelligente requiert au moins 8 jours de télémétrie sur un volume utilisable afin d’établir une ligne de base de performances normales. Par conséquent, lorsque votre application a été exécutée pendant cette période, tout problème significatif entraîne une notification.


## <a name="does-my-app-definitely-have-a-problem"></a>Mon application rencontre-t-elle vraiment un problème ?

Non, une notification ne signifie pas que votre application rencontre réellement un problème. Il s’agit simplement d’une suggestion pour vous amener à examiner un élément de plus près.

## <a name="how-do-i-fix-it"></a>Comment la corriger ?

Les notifications incluent des informations de diagnostic. Voici un exemple :


![Voici un exemple de détection Dégradation du temps de réponse du serveur](./media/app-insights-proactive-diagnostics/server_response_time_degradation.png)

1. **Tri**. La notification vous indique le nombre d’utilisateurs ou le nombre d’opérations affectés. Ceci vous permet d’attribuer une priorité au problème.
2. **Portée**. Le problème affecte-t-il tout le trafic, ou certaines pages seulement ? Se limite-t-il à des navigateurs ou emplacements particuliers ? Ces informations peuvent être obtenues dans la notification.
3. **Diagnostic**. Souvent, les informations de diagnostic dans la notification suggèrent la nature du problème. Par exemple, si le temps de réponse ralentit lorsque le taux de demandes est élevé, cela suggère que votre serveur ou les dépendances sont surchargés. 

    Sinon, ouvrez le panneau Performances dans Application Insights. Vous y trouverez des données de [Profileur](app-insights-profiler.md). Si des exceptions sont levées, vous pouvez également essayer le [Débogueur de capture instantanée](app-insights-snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Configurer les notifications par courrier électronique

Les notifications de détection intelligente sont activées par défaut et envoyées aux personnes disposant d’un [accès propriétaire, collaborateur ou lecteur à la ressource Application Insights](app-insights-resources-roles-access-control.md). Pour le modifier, cliquez sur **Configurer** dans la notification par courrier électronique ou ouvrez Paramètres de détection intelligente dans Application Insights. 
  
  ![Paramètres de détection intelligente](./media/app-insights-proactive-diagnostics/smart_detection_configuration.png)
  
  * Vous pouvez utiliser le lien **se désabonner** dans l’e-mail de détection intelligente pour ne plus recevoir de notifications par courrier électronique.

Les courriers électroniques relatifs aux anomalies de performances de détections intelligentes se limitent à un courrier électronique par jour et par ressource Application Insights. Le courrier électronique n’est envoyé que si au moins un nouveau problème a été détecté ce jour-là. Vous n’obtiendrez pas plusieurs fois le même message. 

## <a name="faq"></a>Forum Aux Questions

* *Les équipes Microsoft consultent-elles mes données ?*
  * Non. Le service est entièrement automatique. Vous seul obtenez ces notifications. Vos données sont [privées](app-insights-data-retention-privacy.md).
* *Analysez-vous toutes les données collectées par Application Insights ?*
  * Pas à l'heure actuelle. Actuellement, nous analysons le temps de réponse des demandes, le temps de réponse des dépendances et le temps de chargement des pages. L’analyse d’autres métriques se trouve sur notre liste de travaux en souffrance.

* À quels types d’applications cela s’applique-t-il ?
  * Ces dégradations sont détectées dans n’importe quelle application qui génère les données de télémétrie appropriées. Si vous avez installé Application Insights dans votre application web, les demandes et les dépendances sont automatiquement suivies. Toutefois, dans les services principaux ou d’autres applications, si vous avez inséré des appels [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) ou [TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency), la détection intelligente fonctionnera de la même manière.

* *Puis-je créer mes propres règles de détection d’anomalies ou personnaliser des règles existantes ?*

  * Pas encore, mais vous pouvez :
    * [configurer des alertes](app-insights-alerts.md) qui vous indiquent qu'une métrique dépasse un seuil ;
    * [exporter la télémétrie](app-insights-export-telemetry.md) vers une [base de données](app-insights-code-sample-export-sql-stream-analytics.md) ou vers [Power BI](app-insights-export-power-bi.md), où vous pouvez les analyser vous-même.
* *À quelle fréquence sont réalisées les analyses ?*

  * Nous exécutons l’analyse tous les jours sur la télémétrie du jour précédent (jour complet en heure UTC).
* *Cela remplace-t-il les [alertes de métrique](app-insights-alerts.md)?*
  * Non.  Nous ne détectons pas tous les comportements que vous pouvez considérer comme étant anormaux.


* *Si je ne fais rien suite à une notification, un rappel me sera-t-il envoyé ?*
  * Non, vous ne recevez qu’un message pour chaque problème. Si le problème persiste, il sera mis à jour dans le panneau de flux Détection intelligente.
* *J’ai perdu le courrier électronique Où puis-je trouver les notifications dans le portail ?*
  * Dans la vue d'ensemble Application Insights de votre application, cliquez sur la mosaïque **Détection intelligente** . Vous serez en mesure de retrouver toutes les notifications jusqu’à 90 jours dans le passé.

## <a name="how-can-i-improve-performance"></a>Comment améliorer les performances ?
Les réponses lentes et les échecs font partie des expériences les plus frustrantes pour les utilisateurs de site web, comme vous l’avez sans doute appris à vos dépens. Il est donc important de résoudre les problèmes.

### <a name="triage"></a>Tri
Tout d’abord, est-il important ? Si une page est toujours lente à charger, mais que seul 1 % des utilisateurs de votre site doivent la consulter, vous avez sans doute des choses plus importantes à faire. Cependant, si seul 1 % des utilisateurs ouvre cette page, et si cette dernière génère systématiquement des exceptions, il peut s’avérer judicieux de s’intéresser à la chose.

Utilisez la déclaration d’impact (utilisateurs affectés ou % de trafic) comme guide général, mais dites-vous bien que tout ne se résume pas à ça. Recueillez d’autres informations pour confirmer.

Examinez les paramètres du problème. Si le problème est lié à la géographie, configurez des [tests de disponibilité](app-insights-monitor-web-app-availability.md) incluant la région. Il se peut qu’il y ait un problème de réseau dans la zone.

### <a name="diagnose-slow-page-loads"></a>Diagnostiquer des chargements de page lents
Où est le problème ? Le serveur est-il long à répondre, est-ce la page qui est très lente, ou le navigateur a-t-il du mal à l’afficher ?

Ouvrez le panneau métrique des navigateurs. L'affichage segmenté du temps de chargement de la page de navigateur montre le délai qui s'écoule. 

* Si le **Temps d'envoi de demande** est élevé, le serveur répond lentement ou la demande contient une importante quantité de données. Examinez les [mesures de performances](app-insights-web-monitor-performance.md#metrics) pour étudier les temps de réponse.
* Configurez le [suivi des dépendances](app-insights-asp-net-dependencies.md) pour voir si cette lenteur est due à des services externes ou à votre base de données.
* Si la **réception de réponse** est prédominante, votre page et ses composants dépendants (JavaScript, CSS, images, etc., mais pas de données chargées de manière asynchrone) sont longs. Configurez un [test de disponibilité](app-insights-monitor-web-app-availability.md)et veillez à définir l'option de chargement des éléments dépendants. Lorsque vous obtenez des résultats, ouvrez les détails d’un résultat et développez-les pour afficher les temps de chargement de différents fichiers.
* Des **durées de traitement du client** prolongées suggèrent que l'exécution des scripts est lente. Si la raison n’est pas évidente, pensez à ajouter un code de minutage et à envoyer les heures dans les appels trackMetric.

### <a name="improve-slow-pages"></a>Améliorer les pages lentes
Il existe un site web plein de conseils sur l’amélioration des temps de chargement des réponses serveur et des pages, et nous n’essaierons pas de tous les répéter ici. Voici quelques conseils que vous connaissez sans doute déjà, simplement pour susciter votre réflexion :

* Chargement lent du à de gros fichiers : charger des scripts et les autres éléments de façon asynchrone. Utiliser le regroupement de scripts. Divisez la page principale en widgets chargeant leurs données séparément. N’envoyez pas d’ancien langage HTML pour les longs tableaux : utilisez un script pour demander les données JSON ou un autre format compact, puis renseignez le tableau sur place. Il existe de grandes structures pour aider à cela. (Elles aussi comportent des scripts volumineux, bien sûr.)
* Ralentir les dépendances de serveur : prendre en compte des emplacements géographiques de vos composants. Par exemple, si vous utilisez Azure, assurez-vous que le serveur web et la base de données se trouvent dans la même région. Les requêtes récupèrent-elles plus d’informations que nécessaire ? La mise en mémoire cache ou en lot peut-elle aider ?
* Problèmes de capacité : examinez les métriques de serveur des temps de réponse et le nombre de demandes. Si les temps de réponse présentent des pics disproportionnés en termes de nombre de requêtes, vos serveurs sont étirés.


## <a name="server-response-time-degradation"></a>Dégradation du temps de réponse du serveur

La notification de dégradation du temps de réponse indique :

* Le temps de réponse par rapport au temps de réponse normal pour cette opération.
* Le nombre d’utilisateurs affectés.
* Temps de réponse moyen et temps de réponse 90e centile pour cette opération le jour de la détection et 7 jours auparavant. 
* Nombre de demandes de cette opération le jour de la détection et 7 jours auparavant.
* Corrélation entre la dégradation de cette opération et les dégradations de dépendances associées. 
* Liens pour vous aider à diagnostiquer le problème.
  * Suivis du Profileur pour vous aider à savoir ce à quoi la durée de l’opération est consacrée (le lien est disponible si des exemples de suivi du Profileur ont été collectés pour cette opération pendant la période de détection). 
  * Rapports de performance dans l’Explorateur de mesures, dans lequel vous pouvez segmenter et traiter une plage de temps/des filtres pour cette opération.
  * Recherches de ces appels pour afficher les propriétés d’appels spécifiques.
  * Rapports d’échecs : si le nombre > 1, cela signifie que des erreurs dans cette opération ont pu contribuer à la dégradation des performances.

## <a name="dependency-duration-degradation"></a>Dégradation de la durée de dépendance

Les applications modernes adoptent de plus en plus l’approche de conception de microservices qui, dans de nombreux cas, confère aux services externes une haute fiabilité. Par exemple, si votre application s’appuie sur une plateforme de données ou si vous créez votre propre service bot, vous vous appuierez probablement sur un fournisseur de services cognitifs pour que vos bots interagissent de manière plus humaine et sur des services de banque de données dans lequel le bot extraira les réponses.  

Exemple de notification de dégradation de dépendance :

![Voici un exemple de détection de la dégradation de la durée de dépendance](./media/app-insights-proactive-diagnostics/dependency_duration_degradation.png)

Notez qu’il vous indique :

* La durée par rapport au temps de réponse normal pour cette opération
* Le nombre d’utilisateurs affectés
* Durée moyenne et durée 90e centile de cette dépendance le jour de la détection et 7 jours auparavant
* Nombre d’appels de dépendance le jour de la détection et 7 jours auparavant
* Liens pour vous aider à diagnostiquer le problème
  * Rapports de performances dans l’Explorateur de mesures de cette dépendance
  * Recherches de ces appels de dépendance pour afficher les propriétés des appels
  * Rapports d’échecs : si le nombre > 1, cela signifie que des appels de dépendance ont échoué pendant la période de détection, qui ont pu contribuer à la dégradation de la durée. 
  * Ouverture de la fonctionnalité Analytics permettant de calculer cette durée de dépendance et un nombre  

## <a name="smart-detection-of-slow-performing-patterns"></a>Détection intelligente de modèles de performances lentes 

Application Insights recherche les problèmes de performances pouvant toucher uniquement une partie de vos utilisateurs ou bien l’ensemble des utilisateurs mais dans certains cas de figure seulement. Par exemple, une notification sur des pages qui se chargent plus lentement dans un type de navigateur que dans d’autres types de navigateurs ou si les demandes sont traitées plus lentement à partir d’un serveur particulier. Elle peut aussi détecter les problèmes liés à des combinaisons de propriétés, tels que des pages qui se chargent lentement dans une zone géographique pour des clients utilisant un système d’exploitation particulier.  

Les anomalies de ce type, qui sont très difficiles à détecter par la simple inspection des données, sont plus courantes que vous ne l’imaginez. Souvent, elles sont révélées par les plaintes des clients. Mais il est déjà trop tard : les utilisateurs affectés sont déjà passés à la concurrence !

À l’heure actuelle, nos algorithmes examinent le temps de chargement des pages, le temps de réponse aux demandes et le temps de réponse des dépendances.  

Vous n’avez pas à définir de seuils ni à configurer des règles. Des algorithmes d’apprentissage automatique (« Machine Learning ») et d’exploration de données sont utilisés pour détecter les schémas anormaux.

![Dans l’alerte reçue par courrier électronique, cliquez sur le lien pour ouvrir le rapport de diagnostic dans Azure](./media/app-insights-proactive-performance-diagnostics/03.png)

* **Quand** (When) montre l'heure à laquelle le problème a été détecté.
* **Quoi** (What) décrit :

  * le problème qui a été détecté ;
  * les caractéristiques du jeu d’événements qui a affiché le comportement du problème.
* Le tableau compare le jeu aux faibles performances avec le comportement moyen de tous les autres événements.

Cliquez sur les liens pour ouvrir Metric Explorer et effectuez une recherche dans les rapports appropriés qui sont filtrés sur l’heure et les propriétés du jeu dont l’exécution est lente.

Modifiez l’intervalle de temps et les filtres pour explorer la télémétrie.

## <a name="next-steps"></a>Étapes suivantes
Ces outils de diagnostic vous aident à inspecter les données de télémétrie à partir de votre application :

* [Profileur](app-insights-profiler.md) 
* [Débogueur de capture instantanée](app-insights-snapshot-debugger.md)
* [Analyse](app-insights-analytics-tour.md)
* [Diagnostics intelligents Analytics](app-insights-analytics-diagnostics.md)

Les détections intelligentes sont entièrement automatiques. Mais vous souhaitez peut-être configurer des alertes supplémentaires ?

* [Alertes de mesures configurées manuellement](app-insights-alerts.md)
* [Tests web de disponibilité](app-insights-monitor-web-app-availability.md)
