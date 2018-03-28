---
title: À propos de l’analyse du réseau dans Log Analytics | Microsoft Docs
description: Vue d’ensemble des solutions d’analyse du réseau, dont NPM, pour gérer les réseaux dans des environnements cloud, locaux et hybrides.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 7b9f42607f313f5570f414e810eafc6775ea18b9
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="network-monitoring-solutions"></a>Solutions d’analyse du réseau 

Azure propose de nombreuses solutions pour analyser les ressources de votre réseau. Azure propose des solutions et des utilitaires pour analyser la connectivité du réseau et l’intégrité des circuits ExpressRoute ainsi que pour analyser le trafic réseau dans le cloud.

## <a name="network-performance-monitor-npm"></a>Analyseur de performances réseau (NPM)

Network Performance Monitor (NPM) est une suite de fonctionnalités conçues pour surveiller l’intégrité de votre réseau ainsi que la connectivité réseau à vos applications et fournir des informations sur les performances de votre réseau. La suite NPM est basée sur le cloud et fournit une solution de surveillance de réseaux hybrides qui analyse la connectivité entre :
 
* Les déploiements dans le cloud et les emplacements locaux
* Plusieurs centres de données et des filiales
* Les applications multicouches critiques et les microservices
* Les emplacements des utilisateurs et les applications web (HTTP/HTTPS) 

L’Analyseur de performances, le moniteur ExpressRoute et le moniteur de points de terminaison de service sont des fonctionnalités de surveillance au sein de NPM et sont décrites ci-dessous.

## <a name="performance-monitor"></a>Analyseur de performances

L’analyseur de performances fait partie de NPM et se charge de la surveillance du réseau dans les environnements cloud, hybrides et sur site. Vous pouvez surveiller la connectivité réseau entre les filiales distantes et les bureaux, les magasins physiques, les centres de données et les clouds. Vous pouvez détecter les problèmes réseau avant que vos utilisateurs ne se plaignent. Voici les avantages clés :

* Surveiller les pertes et la latence entre différents sous-réseaux et définir des alertes
* Surveiller tous les chemins d’accès (y compris les chemins d’accès redondants) sur le réseau
* Résoudre les problèmes réseau temporaires et ponctuels qui sont difficiles à répliquer
* Déterminer le segment spécifique sur le réseau qui est responsable de la dégradation des performances
* Surveiller l’intégrité du réseau, sans SNMP

![Mappage de la topologie NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Pour plus d’informations, consultez les articles suivants :

* [Solution Network Performance Monitor dans Log Analytics](../log-analytics/log-analytics-network-performance-monitor.md) 
* [Cas d’utilisation](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Mises à jour de produit : [février 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [août 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>Moniteur ExpressRoute

NPM pour ExpressRoute offre une surveillance complète ExpressRoute pour les connexions d’appairage privées. Vous pouvez surveiller la connectivité et les performances E2E entre vos filiales et Azure via ExpressRoute. Ses fonctionnalités principales sont :

* Détection automatique des circuits ER associés à votre abonnement
* Détection de la topologie du réseau, des sites locaux aux applications cloud
* Planification de la capacité, analyse de l’utilisation
* Surveillance et alerte sur les chemins d’accès primaires et secondaires
* Détection des dégradations de la connectivité des réseaux virtuels

Pour plus d’informations, consultez les articles suivants :

* [Configurer Network Performance Monitor pour ExpressRoute](../expressroute/how-to-npm.md)
* [Billet de blog](https://aka.ms/NPMExRmonitorGA)

## <a name="service-endpoint-monitor"></a>Moniteur de points de terminaison de service

Avec le moniteur de point de terminaison de service, vous pouvez désormais tester l’accessibilité des applications et détecter les goulots d’étranglement au niveau des performances en local, sur les réseaux des opérateurs et sur les centres de données de cloud/privé.

* Surveiller la connectivité réseau de bout en bout avec les applications
* Mettre en corrélation la distribution des applications avec les performances réseau, détecter l’emplacement précis des dégradations sur le chemin entre l’utilisateur et l’application
* Tester l’accessibilité des applications à partir de plusieurs emplacements d’utilisateur dans le monde entier
* Déterminer la latence et les pertes de paquets pour vos applications métier et SaaS
* Déterminer les zones réactives sur le réseau susceptibles de provoquer une baisse des performances applicatives
* Surveiller l’accessibilité aux applications Office 365, à l’aide de tests intégrés pour Microsoft Office 365, Dynamics 365, Skype Entreprise et autres services Microsoft

Pour plus d’informations, consultez les articles suivants :

* [Configure Network Performance Monitor for monitoring Service Endpoints](https://aka.ms/applicationconnectivitymonitorguide) (Configurer Network Performance Monitor pour l’analyse des points de terminaison de service)
* [Billet de blog](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Traffic Analytics
Traffic Analytics est une solution basée sur le cloud qui offre une visibilité de l’activité des utilisateurs et des applications sur vos réseaux cloud. Les journaux de flux NSG sont analysés pour fournir des insights sur :

* Les flux de trafic sur vos réseaux entre Azure et Internet, les régions de cloud public, les réseaux virtuels et les sous-réseaux
* Les applications et les protocoles sur votre réseau, sans renifleur ni appliance de collecteur de flux dédiée
* Les principaux consommateurs, les applications bavardes, les conversations de machine virtuelle dans le cloud, les zones réactives du trafic
* Les sources et destinations du trafic entre les réseaux virtuels, les relations entre les services métier critiques et les applications
* La sécurité, comme le trafic malveillant, les ports ouverts sur Internet, les applications ou machines virtuelles tentant d’accéder à Internet
* L’utilisation de capacité, qui vous aide à éliminer les problèmes de provisionnement excessif ou de sous-exploitation en suivant les tendances d’utilisation de passerelles VPN et d’autres services

Traffic Analytics vous dote d’informations actionnables qui vous permettent d’auditer l’activité réseau de votre organisation, de sécuriser les applications et les données, d’optimiser les performances de la charge de travail et de rester en conformité.

![Carte géographique montrant le trafic entre les régions](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Liens connexes :
* [Billet de blog](https://aka.ms/trafficanalytics), [Documentation](https://aka.ms/trafficanalyticsdocs), [FAQ](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS Analytics
Conçue pour les administrateurs DNS, cette solution collecte, analyse et met en corrélation des journaux DNS pour assurer la sécurité, les opérations et les insights liés aux performances.  Les fonctionnalités sont notamment les suivantes :

* Identification des clients qui tentent de résoudre des domaines malveillants
* Identification des enregistrements de ressources obsolètes
* Visibilité des noms de domaine fréquemment interrogés et des clients DNS communiquant
* Visibilité de la charge des demandes sur les serveurs DNS
* Surveillance des échecs d’inscription DNS dynamique

![Tableau de bord DNS Analytics](./media/network-monitoring-overview/dns-analytics-overview.png) 

Liens connexes :
* [Billet de blog](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [Documentation](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="next-steps"></a>Étapes suivantes

* [Configurer Network Performance Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [Configurer Network Performance Monitor pour ExpressRoute](../expressroute/how-to-npm.md)
