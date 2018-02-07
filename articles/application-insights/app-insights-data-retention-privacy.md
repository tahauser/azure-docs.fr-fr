---
title: "Conservation et stockage des données dans Azure Application Insights | Microsoft Docs"
description: "Déclaration de confidentialité et politique de rétention"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: mbullwin
ms.openlocfilehash: 1818e564acb0e9b5fa620d6f38db141811ca9777
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Collecte, rétention et stockage des données dans Application Insights


Quand vous installez le Kit de développement logiciel (SDK) [Azure Application Insights][start] dans votre application, il envoie des données de télémétrie sur votre application au cloud. Bien-sûr, les développeurs responsables veulent savoir exactement quelles données sont envoyées, ce qu’elles deviennent et comment ils peuvent conserver le contrôle. Ils souhaitent savoir, plus particulièrement, si les données sensibles peuvent être envoyées, où elles sont stockées et à quel point elles sont sécurisées ? 

Tout d’abord, la réponse courte :

* Les modules de télémétrie standard qui s’exécutent « dès le départ » sont peu susceptibles d’envoyer des données sensibles au service. La télémétrie s’attache aux métriques de charge, de performance et d’utilisation, aux rapports d’exception et autres données de diagnostic. Les données d’utilisateur principal visibles dans les rapports de diagnostics sont des URL ; mais votre application ne doit en aucun cas mettre des données sensibles en texte brut dans une URL.
* Vous pouvez écrire du code qui envoie les données de télémétrie personnalisées supplémentaires, afin de vous aider avec les diagnostics et à surveiller l’utilisation. (Cette extensibilité est une fonctionnalité intéressante de Application Insights.) Il serait possible, par erreur, d’écrire ce code de manière à ce qu’il inclue les données personnelles et d’autres données sensibles. Si votre application fonctionne avec ces données, vous devez vérifier de manière approfondie l’ensemble du code que vous écrivez.
* Pendant le développement et le test de votre application, il est facile d’examiner ce qui est envoyé par le Kit de développement logiciel (SDK). Les données apparaissent dans les fenêtres de sortie de débogage de l’IDE et du navigateur. 
* Les données sont stockées sur des serveurs [Microsoft Azure](http://azure.com) aux États-Unis. (Mais votre application peut s’exécuter n’importe où.) Azure dispose de [processus de sécurité renforcés, il est conforme à une large gamme de normes de conformité](https://azure.microsoft.com/support/trust-center/). Seuls vous et votre équipe avez accès à vos données. Le personnel Microsoft peut avoir un accès restreint uniquement dans certaines circonstances définies, avec votre consentement. Il est chiffré en transit, mais pas dans les serveurs.

Le reste de cet article aborde plus en détail ces réponses. Il est conçu pour être autonome, afin que vous puissiez le montrer à des collègues qui ne font pas partie de votre équipe.

## <a name="what-is-application-insights"></a>Présentation d’Application Insights
[Azure Application Insights][start] est un service de Microsoft qui vous aide à améliorer les performances et la convivialité de votre application dynamique. Il analyse votre application tout au long de son exécution, pendant les tests et une fois que vous avez l’avez publiée ou déployée. Application Insights crée des graphiques et tableaux présentant, par exemple, les heures de la journée à laquelle vous avez le plus d’utilisateurs, la réactivité de l’application et comment elle est prise en charge par les services externes dont elle dépend. En cas d’incidents, d’échecs ou de problèmes de performances, vous pouvez effectuer une recherche dans les données de télémétrie pour diagnostiquer la cause en détail. Et le service vous enverra des messages électroniques si des modifications sont apportées à la disponibilité et à la performance de votre application.

Pour obtenir cette fonctionnalité, vous devez installer un Kit SDK Application Insights dans votre application, il devient partie intégrante de son code. Lorsque votre application s’exécute, le Kit SDK surveille ses opérations et envoie les données de télémétrie au service Application Insights. Il s’agit d’un service cloud hébergé par [Microsoft Azure](http://azure.com). (Mais Application Insights fonctionne pour toutes les applications, pas uniquement celles qui sont hébergées dans Azure.)

![Le Kit SDK dans votre application envoie la télémétrie au service Application Insights.](./media/app-insights-data-retention-privacy/01-scheme.png)

Le service Application Insights stocke et analyse les données de télémétrie. Pour afficher l’analyse ou effectuer une recherche dans la télémétrie stockée, connectez-vous à votre compte Azure et ouvrez la ressource Application Insights pour votre application. Vous pouvez également partager l’accès aux données avec d’autres membres de votre équipe, ou avec des abonnés Azure spécifiés.

Vous pouvez exporter des données à partir du service Application Insights vers une base de données ou des outils externes, par exemple. Vous devez attribuer une clé spéciale, que vous obtenez de la part du service, à chaque outil. La clé peut être révoquée si nécessaire. 

Les Kits SDK d’Application Insights sont disponibles pour une variété d’applications : les services web hébergés dans vos propres serveurs J2EE ou ASP.NET ou dans Azure ; les clients web, autrement dit, le code s’exécutant dans une page web ; les applications et les services de bureau ; les applications pour appareils comme Windows Phone, iOS et Android. Toutes envoient les données de télémétrie vers le même service.

## <a name="what-data-does-it-collect"></a>Quelles données collecte-t-il ?
### <a name="how-is-the-data-is-collected"></a>Comment les données sont-elles collectées ?
Il existe trois sources de données :

* Le SDK, que vous intégrez à votre application soit [pendant le développement](app-insights-asp-net.md), soit [au moment de l’exécution](app-insights-monitor-performance-live-website-now.md). Il existe différents Kits SDK pour différents types d’applications. Il existe également un [Kit SDK pour les pages web](app-insights-javascript.md), qui se charge dans le navigateur de l’utilisateur final et sur la page.
  
  * Chaque Kit SDK comporte un certain nombre de [modules](app-insights-configuration-with-applicationinsights-config.md), utilisant diverses techniques pour collecter différents types de données de télémétrie.
  * Si vous installez le Kit SDK pendant le développement, vous pouvez utiliser ses API pour envoyer votre propre télémétrie, ainsi que les modules standards. Ces données de télémétrie personnalisées peuvent inclure toutes les données que vous souhaitez envoyer.
* Sur certains serveurs web, il existe également des agents qui s’exécutent en même temps que l’application et qui envoient des données de télémétrie concernant l’UC, la mémoire et l’occupation du réseau. Par exemple, les machines virtuelles Azure, les hôtes Docker, et [les serveurs J2EE](app-insights-java-agent.md) peuvent disposer de ces agents.
* [Les tests de disponibilité](app-insights-monitor-web-app-availability.md) sont des processus exécutés par Microsoft qui envoient des requêtes à votre application web à intervalles réguliers. Les résultats sont envoyés au service Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Quel genre de données est collecté ?
Les principales catégories sont :

* [La télémétrie du serveur web](app-insights-asp-net.md) : requêtes HTTP.  L’URI, le temps nécessaire pour traiter la demande, le code de réponse, l’adresse IP du client. L’Id de session.
* [Les pages web](app-insights-javascript.md) : page, utilisateur et décomptes de sessions. Le temps de chargement de page. Les exceptions. Appels Ajax.
* Les performances des compteurs : mémoire, UC, E/S, occupation du réseau.
* Le contexte du client et du serveur : système d’exploitation, paramètres régionaux, type d’appareil, navigateur, résolution d’écran.
* [Les exceptions](app-insights-asp-net-exceptions.md) et les incidents : **vidages de pile**, génération d’id, type d’UC. 
* [Les dépendances](app-insights-asp-net-dependencies.md) : les appels aux services externes tels que REST, SQL, AJAX. L’URI ou la chaîne de connexion, la durée, la réussite, la commande.
* [Les tests de disponibilité](app-insights-monitor-web-app-availability.md) : durée et étapes du test, réponses.
* [Les journaux de suivi](app-insights-asp-net-trace-logs.md) et [la télémétrie personnalisée](app-insights-api-custom-events-metrics.md) - **tout ce que vous codez dans vos journaux ou télémétrie**.

[Plus de détails](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Comment puis-je vérifier ce qui a été collecté ?
Si vous développez l’application à l’aide de Visual Studio, exécutez l’application en mode débogage (F5). Les données de télémétrie s’affichent dans la fenêtre Sortie. À partir de là, vous pouvez les copier et les mettre au format JSON pour une inspection facile. 

![](./media/app-insights-data-retention-privacy/06-vs.png)

Il existe également une vue plus lisible dans la fenêtre Diagnostics.

Pour les pages web, ouvrez la fenêtre de débogage de votre navigateur.

![Appuyez sur F12 et ouvrez l’onglet Réseau.](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Puis-je écrire le code pour filtrer les données de télémétrie avant qu’elles soient envoyées ?
Cela est possible en écrivant un [plug-in de processeur de télémétrie](app-insights-api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Combien de temps sont conservées les données ?
Les points de données brutes (autrement dit, les éléments que vous pouvez interroger dans Analytics et inspecter dans Recherche) sont conservés pendant 90 jours maximum. Si vous voulez conserver les données plus longtemps, vous pouvez utiliser l’ [exportation continue](app-insights-export-telemetry.md) pour les copier dans un compte de stockage.

Les données agrégées (autrement dit, les nombres, moyennes et autres données statistiques que vous voyez dans Metrics Explorer) sont conservées avec une granularité de 1 minute pendant 90 jours.

## <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?
Les données sont visibles par vous et, si vous disposez un compte d’organisation, par les membres de votre équipe. 

Vous ou les membres de votre équipe pouvez les exporter et les copier à d’autres endroits, ou les transférer à d’autres personnes.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Que fait Microsoft des informations que mon application envoie à Application Insights ?
Microsoft n’utilise les données que pour vous fournir le service.

## <a name="where-is-the-data-held"></a>Où sont conservées les données ?
* Aux États-Unis, en Europe ou en Asie du Sud-Est. Vous pouvez sélectionner l’emplacement quand vous créez une ressource Application Insights. 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Cela signifie-t-il que mon application doit être hébergée aux États-Unis, en Europe ou en Asie du Sud-Est ?
* Non. Votre application peut s’exécuter n’importe où, sur vos propres hôtes locaux ou dans le cloud.

## <a name="how-secure-is-my-data"></a>Mes données sont-elles sécurisées ?
Application Insights est un service Azure. Les stratégies de sécurité sont décrites dans le [livre blanc sur la sécurité, la confidentialité et la conformité Azure](http://go.microsoft.com/fwlink/?linkid=392408).

Les données sont stockées sur des serveurs Microsoft Azure. Pour les comptes du portail Azure, les restrictions sont décrites dans le [document relatif à la sécurité, la confidentialité et la conformité Azure](http://go.microsoft.com/fwlink/?linkid=392408).

L'accès à vos données par le personnel Microsoft est limité. Nous n'accédons à vos données qu'avec votre autorisation, et seulement pour vous aider à utiliser Application Insights. 

Les données agrégées sur toutes les applications de nos clients (comme le débit des données et la taille moyenne des suivis) sont utilisées pour améliorer Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Les données de télémétrie d’une autre personne peuvent-elles interférer avec mes données d’Application Insights ?
Elles peuvent envoyer des données de télémétrie supplémentaires à votre compte à l’aide de la clé d’instrumentation, qui se trouve dans le code de vos pages web. Avec suffisamment de données supplémentaires, vos mesures ne représentent pas correctement les performances et l’utilisation de votre application.

Si vous partagez le code avec d’autres projets, pensez à supprimer votre clé d’instrumentation.

## <a name="is-the-data-encrypted"></a>Les données sont-elles chiffrées ?
Pas sur les serveurs à l’heure actuelle.

Toutes les données sont chiffrées lors de leurs déplacements entre les centres de données.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Les données sont-elles chiffrées lors de leur passage depuis mon application vers les serveurs Application Insights ?
Oui, nous utilisons le protocole HTTPS pour envoyer les données au portail à partir de presque tous les Kits de développement logiciel (SDK), y compris les serveurs web, les appareils et les pages web HTTPS. La seule exception concerne les données envoyées à partir des pages web HTTP. 

## <a name="personally-identifiable-information"></a>Informations d’identification personnelle
#### <a name="could-personally-identifiable-information-pii-be-sent-to-application-insights"></a>Est-ce que des informations d'identification personnelle peuvent être envoyées à Application Insights ?
Oui, c’est possible. 

En règle générale :

* La plupart des données de télémétrie standard (à savoir toute télémétrie envoyée sans que n’ayez écrit de code) ne comprennent pas d’informations explicites. Toutefois, il est parfois possible d’identifier les personnes par déduction à partir d’un ensemble d’événements.
* Les messages d’exception et les messages de suivi peuvent contenir des informations personnelles
* Les données de télémétrie personnalisée, autrement dit les appels comme TrackEvent que vous écrivez dans le code à l’aide des API ou du suivi du journal, peuvent contenir toutes les données que vous voulez.

Le tableau à la fin de ce document contient une description plus détaillée des données collectées.

#### <a name="am-i-responsible-for-complying-with-laws-and-regulations-in-regard-to-pii"></a>Suis-je tenu de me conformer aux lois et règlements en ce qui concerne les informations d’identification personnelle ?
Oui. Il vous incombe de garantir que la collecte et l’utilisation des données sont conformes aux lois et réglementations et aux termes du contrat Microsoft Online Services.

Vous devez correctement informer vos clients sur les données collectées par votre application et la façon dont elles sont utilisées.

#### <a name="can-my-users-turn-off-application-insights"></a>Mes utilisateurs peuvent-ils désactiver Application Insights ?
Pas directement. Il n’existe pas de commutateur que vos utilisateurs peuvent utiliser pour désactiver Application Insights.

Toutefois, vous pouvez implémenter cette fonctionnalité dans votre application. Tous les Kits de développement logiciel (SDK) comprennent un paramètre API qui désactive la collecte de télémétrie. 

#### <a name="my-application-is-unintentionally-collecting-sensitive-information-can-application-insights-scrub-this-data-so-it-isnt-retained"></a>Mon application collecte involontairement des informations sensibles. Est-ce qu’Application Insights peut nettoyer ces données afin qu’elles ne soient pas conservées ?
Application Insights ne filtre pas les données et ne les supprime pas non plus. Vous devez gérer les données de façon appropriée et éviter d’envoyer ce type de données à Application Insights.

## <a name="data-sent-by-application-insights"></a>Données envoyées par Application Insights
Les Kits SDK varient en fonction des plateformes et vous pouvez installer plusieurs composants. (Consultez [Application Insights - Vue d’ensemble][start].) Chaque composant envoie des données différentes.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Classes de données envoyées dans différents scénarios
| Votre action | Classes de données collectées (voir tableau suivant) |
| --- | --- |
| [Ajouter le kit de développement logiciel (SDK) Application Insights à un projet web .NET][greenbrown] |ServerContext<br/>Inferred<br/>Perf counters<br/>Requests<br/>**Exceptions**<br/>session<br/>users |
| [Installer Status Monitor sur IIS][redfield] |Dépendances<br/>ServerContext<br/>Inferred<br/>Perf counters |
| [Ajouter le kit de développement logiciel (SDK) Application Insights à une application web Java][java] |ServerContext<br/>Inferred<br/>Requête<br/>session<br/>users |
| [Ajouter le kit de développement logiciel (SDK) JavaScript à une page web][client] |ClientContext  <br/>Inferred<br/>Page<br/>ClientPerf<br/>Ajax |
| [Définir les propriétés par défaut][apiproperties] |**Propriétés** sur tous les événements standard et personnalisés |
| [Appeler TrackMetric][api] |Valeurs numériques<br/>**Propriétés** |
| [Appeler Track*][api] |Nom de l'événement<br/>**Propriétés** |
| [Appeler TrackException][api] |**Exceptions**<br/>Vidage de pile<br/>**Propriétés** |
| Le Kit SDK ne peut pas collecter les données. Par exemple :  <br/> - impossible d’accéder aux compteurs de performances<br/> - exception dans l’initialiseur de télémétrie |SDK diagnostics |

Pour les [Kits de développement logiciel (SDK) des autres plateformes][platforms], consultez les documents correspondants.

#### <a name="the-classes-of-collected-data"></a>Les classes de données collectées
| Classe des données collectées | Comprend (liste non exhaustive) |
| --- | --- |
| **Propriétés** |**Toutes les données - en fonction de votre code** |
| DeviceContext |ID, adresse IP, paramètres régionaux, modèle d’appareil, réseau, type de réseau, nom OEM, résolution d’écran, instance de rôle, nom du rôle, type d’appareil |
| ClientContext  |Système d’exploitation, paramètres régionaux, langue, réseau, résolution de la fenêtre |
| session |ID de session |
| ServerContext |Nom de l’ordinateur, paramètres régionaux, système d’exploitation, appareil, session utilisateur, contexte utilisateur, opération |
| Inferred |Emplacement géographique à partir de l’adresse IP, horodatage, système d’exploitation, navigateur |
| Mesures |Valeur et nom de la mesure |
| Événements |Valeur et nom de l’événement |
| PageViews |URL et nom de la page ou de l’écran |
| Client perf |URL/nom de la page, temps de chargement du navigateur |
| Ajax |Appels HTTP de la page web au serveur |
| Requests |URL, durée, code de réponse |
| Les dépendances |Type (SQL, HTTP, ...), chaîne de connexion ou URI, synchronisation/désynchronisation, durée, réussite, instruction SQL (avec Status Monitor) |
| **Exceptions** |Type, **message**, piles d’appels, fichier source et numéro ligne, ID du thread |
| Crashes |ID de processus, ID de processus parent, ID de thread d’incident ; correctif de l’application, ID, version ; type d’exception, adresse, motif ; symboles et enregistrements masqués, adresses binaires de début et de fin, nom et chemin du fichier binaire, type de processeur |
| Trace |**Message** et niveau de gravité |
| Perf counters |Temps processeur, mémoire disponible, taux de demande, taux d’exception, octets privés du processus, taux d’E/S, durée de la demande, longueur de file d’attente de la demande |
| Disponibilité |Code de réponse de test web, durée de chaque étape de test, nom de test, horodatage, réussite, temps de réponse, emplacement de test |
| SDK diagnostics |Message de suivi ou exception |

Vous pouvez [désactiver certaines données en modifiant ApplicationInsights.config][config].

## <a name="credits"></a>Crédits
Ce produit contient des données GeoLite2 créées par MaxMind, disponible sur [http://www.maxmind.com](http://www.maxmind.com).



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

