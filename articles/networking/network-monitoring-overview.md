---
title: "À propos de l’analyse du réseau dans Log Analytics | Microsoft Docs"
description: "Vue d’ensemble des solutions d’analyse du réseau, dont NPM, pour gérer les réseaux dans des environnements cloud, locaux et hybrides."
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 6d93821b59e1f69a48c3d5eeda96dad2edddb188
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="network-monitoring-solutions"></a>Solutions d’analyse du réseau 

Azure propose de nombreuses solutions pour analyser les ressources de votre réseau. Azure propose des solutions et des utilitaires pour analyser la connectivité du réseau et l’intégrité des circuits ExpressRoute ainsi que pour analyser le trafic réseau dans le cloud.

## <a name="network-performance-monitor-npm"></a>Analyseur de performances réseau (NPM)

Network Performance Monitor (NPM) est une suite de fonctionnalités conçues pour surveiller l’intégrité de votre réseau ainsi que la connectivité réseau à vos applications et fournir des informations sur les performances de votre réseau. La suite NPM est basée sur le cloud et fournit une solution de surveillance de réseaux hybrides qui analyse la connectivité entre :
 
* Les déploiements dans le cloud et les emplacements locaux
* Plusieurs centres de données et des filiales
* Les applications multicouches critiques et les microservices
* Les emplacements des utilisateurs et les applications web (HTTP/HTTPS) 

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

## <a name="next-steps"></a>Étapes suivantes

* [Configurer Network Performance Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [Configurer Network Performance Monitor pour ExpressRoute](../expressroute/how-to-npm.md)
