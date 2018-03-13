---
title: "Solution Analyseur de performances réseau dans Azure Log Analytics | Microsoft Docs"
description: "La fonctionnalité ExpressRoute Manager de Network Performance Monitor vous permet de surveiller les performances et la connectivité de bout en bout entre vos succursales et Azure, sur Azure ExpressRoute."
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 405bcd7d69e93f838d35b61be489464fcf55ee88
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="expressroute-manager"></a>ExpressRoute Manager

La fonctionnalité ExpressRoute Manager de [Network Performance Monitor](log-analytics-network-performance-monitor.md) vous permet de surveiller les performances et la connectivité de bout en bout entre vos succursales et Azure, sur Azure ExpressRoute. Les avantages clés sont les suivants : 

- Détection automatique des circuits ExpressRoute associés à votre abonnement 
- Suivi de l’utilisation de la bande passante, perte et latence au niveau du circuit, appairage et niveau du réseau virtuel de votre ExpressRoute 
- Découverte de la topologie réseau de vos circuits ExpressRoute 

![Moniteur ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>Configuration 
Pour ouvrir la configuration de Network Performance Monitor, ouvrez la [solution Network Performance Monitor](log-analytics-network-performance-monitor.md) et cliquez sur le bouton **Configurer**.

### <a name="configure-nsg-rules"></a>Configurer les règles du groupe de sécurité réseau 
Pour les serveurs se trouvant dans Azure utilisés pour la surveillance via NPM, vous devez configurer les règles du groupe de sécurité réseau (NSG) pour autoriser le trafic TCP sur un port utilisé par NPM pour les transactions synthétiques. Par défaut, il s’agit du port 8084. Cela permet à un agent OMS installé sur une machine virtuelle Azure de communiquer avec un agent de surveillance local. 

Pour plus d’informations concernant le groupe de sécurité réseau, consultez  [Créer des groupes de sécurité réseau à l’aide du portail Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

>[!NOTE]
> Vérifiez que vous avez installé les agents (l’agent de serveur local et l’agent de serveur Azure) et que vous avez exécuté le script PowerShell EnableRules.ps1 avant de continuer avec cette étape. 

 
### <a name="discover-expressroute-peering-connections"></a>Détecter les connexions d’appairage ExpressRoute 
 
1. Cliquez sur la vue **Appairages ExpressRoute**.  
2. Cliquez sur le bouton **Découvrir maintenant** pour découvrir tous les appairages privés ExpressRoute connectés aux réseaux virtuels dans l’abonnement Azure lié à cet espace de travail Log Analytics.  

>[!NOTE]  
> La solution ne découvre pour le moment que les appairages privés ExpressRoute. 

>[!NOTE]  
> Ces appairages privés, connectés aux réseaux virtuels associés à l’abonnement lié à cet espace de travail Log Analytics, sont les seuls à être découverts. Si votre ExpressRoute est connecté aux réseaux virtuels en dehors de l’abonnement lié à cet espace de travail, vous devez créer un espace de travail Log Analytics dans ces abonnements et utiliser NPM pour surveiller ces appairages. 

 ![Configurer le moniteur ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 Une fois la détection terminée, les connexions d’appairages privés sont répertoriées dans une table. La surveillance de ces appairages est initialement désactivée. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Activer la surveillance des connexions d’appairage ExpressRoute 

1. Cliquez sur la connexion d’appairage privé que vous souhaitez surveiller.  
2. Dans le volet RHS, cliquez sur la case à cocher **Surveiller cet appairage**. 
3. Si vous envisagez de créer des événements d’intégrité pour cette connexion, cochez **Activer le monitoring d’intégrité pour cet appairage**. 
4. Choisissez les conditions d’analyse. Vous pouvez définir des seuils personnalisés pour la génération d’événements d’intégrité en tapant des valeurs de seuil. Chaque fois que la valeur d’une condition dépasse son seuil sélectionné pour la connexion d’appairage, un événement d’intégrité est généré. 
5. Cliquez sur **Ajouter des agents** pour choisir les agents de surveillance que vous souhaitez utiliser pour surveiller cette connexion d’appairage. Assurez-vous que vous ajoutez des agents aux deux extrémités de la connexion : au moins un agent dans le réseau virtuel Azure connecté à cet appairage, ainsi qu’au moins un agent local connecté à cet appairage. 
6. Pour enregistrer la configuration, cliquez sur **Enregistrer**. 

![Configurer le moniteur ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


Après l’activation des règles et la sélection des valeurs et des agents que vous souhaitez surveiller, vous devez attendre entre 30 minutes et 1 heure pour que les valeurs commencent à s’ajouter et que les vignettes **Surveillance ExpressRoute** deviennent disponibles. Une fois que vous voyez les vignettes de surveillance, vos circuits ExpressRoute et ressources de connexion sont surveillés par NPM. 

>[!NOTE]
> Cette fonctionnalité s’exécute de façon fiable sur les espaces de travail mis à niveau vers le nouveau langage de requête.  

## <a name="walkthrough"></a>Procédure pas à pas 

Le tableau de bord Network Performance Monitoring présente une vue d’ensemble de l’intégrité des circuits ExpressRoute et des connexions d’appairage. 

![Tableau de bord NPM ExpressRoute](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Liste des circuits 

Pour afficher une liste de tous les circuits ExpressRoute surveillés, cliquez sur la vignette Circuits ExpressRoute. Vous pouvez sélectionner un circuit et afficher son état d’intégrité, des graphiques de tendances pour la perte de paquets, l’utilisation de la bande passante et la latence. Les graphiques sont interactifs. Vous pouvez sélectionner une fenêtre de temps personnalisée pour tracer les graphiques. Vous pouvez faire glisser la souris sur une zone sur le graphique pour zoomer et voir des points de données précis. 

![Liste des circuits ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trend-of-loss-latency-and-throughput"></a>Tendances concernant les pertes, la latence et le débit 

Les graphiques représentant la bande passante, la latence et la perte sont interactifs. Vous pouvez zoomer sur n’importe quelle section de ces graphiques, à l’aide de contrôles de la souris. Vous pouvez également voir des données de bande passante, de latence et de perte pour d’autres intervalles en cliquant sur l’option Date/Heure, située sous le bouton Actions, à gauche. 

![Latence d’ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>Liste des appairages 

En cliquant sur la vignette **Appairages privés** du tableau de bord, une liste de toutes les connexions à des réseaux virtuels sur l’homologation privée s’affiche. Ici, vous pouvez sélectionner une connexion de réseau virtuel et afficher son état d’intégrité, des graphiques de tendances pour la perte de paquets, l’utilisation de la bande passante et la latence. 

![Appairages ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologie de circuit 

Pour afficher la topologie de circuit, cliquez sur la vignette **Topologie**. Vous accédez ainsi à l’affichage de la topologie du circuit ou de l’homologation. Le diagramme de topologie fournit la latence pour chaque segment sur le réseau, et chaque tronçon de couche 3 est représenté par un nœud du diagramme. En cliquant sur un tronçon, vous pouvez obtenir plus de détails sur le tronçon. Vous pouvez augmenter le niveau de visibilité pour inclure des sauts locaux en déplaçant le curseur sous **Filtres**. Déplacez le curseur vers la droite ou gauche pour augmenter/diminuer le nombre de sauts dans le graphique de la topologie. La latence pour chaque segment est visible, ce qui permet une isolation plus rapide des segments à latence élevée sur votre réseau. 

![Topologie d’ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-topology.png)  

### <a name="detailed-topology-view-of-a-circuit"></a>Vue détaillée de la topologie d’un circuit 

Cette vue affiche les connexions de réseau virtuel. 

![Réseau virtuel ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>Diagnostics 

Network Performance Monitor vous permet de diagnostiquer plusieurs problèmes de connectivité de circuit. Certains d’entre eux sont répertoriés ci-dessous 

**Le circuit est arrêté.** NPM vous informe dès que la connectivité entre vos ressources locales et les réseaux virtuels Azure est perdue. Cela vous permet d’agir de façon proactive avant de recevoir les remontées des utilisateurs et de réduire le temps d’arrêt. 

![Circuit ExpressRoute arrêté](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**Le trafic n’est pas transmis via le circuit prévu.** NPM peut vous avertir chaque fois que le trafic n’est pas transmis via le circuit ExpressRoute prévu. Cela peut se produire si le circuit est arrêté et que le trafic est transmis via le routage de secours, ou s’il existe un problème de routage. Ces informations vous permettent de gérer proactivement les problèmes de configuration de vos stratégies de routage et de vous assurer que vous utilisez l’itinéraire le plus optimal et le plus sécurisé. 

 

**Le trafic n’est pas transmis via le circuit principal.** La fonctionnalité vous avertit lorsque le trafic est transmis via le circuit ExpressRoute secondaire. Même si vous ne rencontrez aucun problème de connectivité dans ce cas, la résolution proactive des problèmes avec le circuit principal vous permet d’être mieux préparé. 

 
![Flux de trafic ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**Dégradation due à un pic d’utilisation.** Vous pouvez mettre en corrélation la tendance d’utilisation de la bande passante avec la tendance de latence pour déterminer si la dégradation de la charge de travail Azure est due à un pic d’utilisation de la bande passante ou non, et agir en conséquence.  

![Moniteur ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Étapes suivantes
* [Rechercher dans les journaux](log-analytics-log-searches.md) pour afficher des enregistrements de données détaillées sur les performances réseau.
