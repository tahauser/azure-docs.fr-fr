---
title: "Présentation d’Azure Network Watcher | Microsoft Docs"
description: "Cette page fournit une vue d’ensemble du service Network Watcher pour la surveillance et la visualisation des ressources connectées au réseau dans Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: b8a8e0653221af126ea137b1450ce27c29791ae3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-network-monitoring-overview"></a>Vue d’ensemble de la surveillance réseau Azure

Les clients créent un réseau de bout en bout dans Azure en orchestrant et composant diverses ressources réseau telles qu’un réseau virtuel, ExpressRoute, Application Gateway, des équilibrages de charge, etc. La surveillance est disponible sur chacune des ressources réseau. Cette surveillance est appelée surveillance au niveau des ressources.

Le réseau de bout en bout peut avoir des configurations et des interactions entre les ressources complexes, générant des scénarios compliqués pour lesquels la surveillance basée sur des scénarios via Network Watcher est nécessaire.

Cet article présente la surveillance au niveau des ressources et la surveillance basée sur des scénarios. La surveillance réseau dans Azure est complète et couvre deux grandes catégories :

* [**Network Watcher**](#network-watcher) - La surveillance basée sur des scénarios est fournie avec les fonctionnalités de Network Watcher. Ce service inclut la capture de paquets, le tronçon saut suivant, la vérification des flux IP, l’affichage de groupe de sécurité, les journaux de flux de groupe de sécurité réseau. La surveillance basée sur des scénarios fournit une vue de bout en bout des ressources réseau, à l’inverse de la surveillance des ressources réseau qui s’intéresse à chaque ressource spécifique.
* [**Surveillance des ressources**](#network-resource-level-monitoring) - La surveillance au niveau des ressources se compose de quatre fonctionnalités : journaux de diagnostic, mesures, résolution des problèmes et intégrité des ressources. Toutes ces fonctionnalités sont conçues au niveau des ressources réseau.

## <a name="network-watcher"></a>Network Watcher

Network Watcher est un service régional qui vous permet de surveiller et de diagnostiquer l’état au niveau d’un scénario réseau dans, vers et depuis Azure. Les outils de visualisation et de diagnostic réseau disponibles avec Network Watcher vous aident à comprendre, diagnostiquer et obtenir des informations sur votre réseau dans Azure.

Network Watcher propose actuellement les fonctionnalités suivantes :

* **[Topologie](network-watcher-topology-overview.md)** - Fournit une vue au niveau du réseau montrant les différentes interconnexions et associations entre les ressources réseau dans un groupe de ressources.
* **[Capture de paquets variables](network-watcher-packet-capture-overview.md)** - Capture des données de paquets dans et en dehors d’une machine virtuelle. Les options de filtrage avancées et les contrôles précis comme la possibilité de définir des limites de taille et de temps apportent de la polyvalence. Les données des paquets peuvent être stockées dans un magasin de blobs ou sur le disque local au format .cap.
* **[Vérification des flux IP](network-watcher-ip-flow-verify-overview.md)** - Vérifie si un paquet est autorisé ou refusé en fonction des paramètres de paquet des informations à 5 tuples de flux (adresse IP de destination, adresse IP source, port de destination, port source et protocole). Si le paquet est refusé par un groupe de sécurité, la règle et le groupe qui ont refusé le paquet sont renvoyés.
* **[Tronçon suivant](network-watcher-next-hop-overview.md)** - Détermine le tronçon suivant pour les paquets routés dans la structure de réseau Azure, vous permettant de diagnostiquer les itinéraires définis par l’utilisateur mal configurés.
* **[Affichage des groupes de sécurité](network-watcher-security-group-view-overview.md)** - Obtient les règles de sécurité appliquées et effectives d’une machine virtuelle.
* **[Journalisation des flux de groupe de sécurité réseau](network-watcher-nsg-flow-logging-overview.md)** - Les journaux de flux pour les groupes de sécurité réseau vous permettent de capturer les journaux relatifs au trafic autorisé ou refusé par les règles de sécurité dans le groupe. Le flux est défini par des informations à 5 tuples : adresse IP source, adresse IP de destination, port source, port de destination et protocole.
* **[Résolution des problèmes de connexion et de passerelle de réseau virtuel](network-watcher-troubleshoot-manage-rest.md)** - Offre la possibilité de résoudre les problèmes associés aux connexions et passerelles de réseau virtuel.
* **[Limites d’abonnement réseau](#network-subscription-limits)** - Vous permet d’afficher l’utilisation des ressources réseau par rapport aux limites.
* **[Configuration du journal de diagnostic](#diagnostic-logs)** - Fournit un seul volet pour activer ou désactiver les journaux de diagnostic pour les ressources réseau dans un groupe de ressources.
* **[Résolution des problèmes de connexion](network-watcher-connectivity-overview.md)** - Vérifie la possibilité d’établir une connexion TCP directe à partir d’une machine virtuelle à un point de terminaison donné enrichi avec le contexte Azure.

### <a name="role-based-access-control-rbac-in-network-watcher"></a>Contrôle d’accès basé sur les rôles dans Network Watcher

Network Watcher utilise le [modèle de contrôle d’accès basé sur les rôles](../active-directory/role-based-access-control-what-is.md). Les autorisations suivantes sont exigées par Network Watcher. Il est important de s’assurer que le rôle utilisé pour démarrer les API de Network Watcher ou utiliser Network Watcher depuis le portail dispose de l’accès requis.

|Ressource| Autorisation|
|---|---| 
|Microsoft.Storage/ |Lire|
|Microsoft.Authorization/| Lire| 
|Microsoft.Resources/subscriptions/resourceGroups/| Lire|
|Microsoft.Storage/storageAccounts/listServiceSas/ | Action|
|Microsoft.Storage/storageAccounts/listAccountSas/ |Action|
|Microsoft.Storage/storageAccounts/listKeys/ | Action|
|Microsoft.Compute/virtualMachines/ |Lire|
|Microsoft.Compute/virtualMachines/ |Écrire|
|Microsoft.Compute/virtualMachineScaleSets/ |Lire|
|Microsoft.Compute/virtualMachineScaleSets/ |Écrire|
|Microsoft.Network/networkWatchers/packetCaptures/ |Lire|
|Microsoft.Network/networkWatchers/packetCaptures/| Écrire|
|Microsoft.Network/networkWatchers/packetCaptures/| Supprimer|
|Microsoft.Network/networkWatchers/ |Écrire |
|Microsoft.Network/networkWatchers/| Lire |
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/ | *|

### <a name="network-subscription-limits"></a>Limites d’abonnement réseau

Les limites d’abonnement réseau fournissent des informations sur l’utilisation de chacune des ressources réseau d’un abonnement dans une région par rapport au nombre maximal de ressources disponibles.

![limite d’abonnement réseau][nsl]

## <a name="network-resource-level-monitoring"></a>Surveillance au niveau des ressources réseau

Les fonctionnalités suivantes sont disponibles pour la surveillance au niveau des ressources :

### <a name="audit-log"></a>Journal d’audit

Les opérations réalisées dans le cadre de la configuration des réseaux sont journalisées. Ces journaux peuvent être affichés dans le portail Azure ou récupérés à l’aide des outils Microsoft tels que Power BI ou des outils tiers. Les journaux d’audit sont disponibles via le portail, PowerShell, l’interface de ligne de commande et l’API REST. Pour plus d’informations sur les journaux d’audit, consultez [Afficher les journaux d’activité pour auditer les actions sur les ressources](../resource-group-audit.md).

Les journaux d’audit sont disponibles pour les opérations effectuées sur toutes les ressources réseau.

### <a name="metrics"></a>Mesures

Les mesures sont des indicateurs et des compteurs de performances collectés sur une période donnée. Les mesures sont disponibles pour Application Gateway. Elles peuvent être utilisées pour déclencher des alertes basées sur des seuils. Consultez [Intégrité du serveur principal, journalisation des diagnostics et métriques pour la passerelle Application Gateway](../application-gateway/application-gateway-diagnostics.md) pour afficher la façon dont les mesures peuvent être utilisées pour créer des alertes.

![affichage des mesures][metrics]

### <a name="diagnostic-logs"></a>Journaux de diagnostic

Les événements périodiques et spontanés sont créés par les ressources réseau et journalisés dans les comptes de stockage, puis envoyés à un hub d’événements ou à Log Analytics. Ces journaux fournissent des informations sur l’intégrité d’une ressource. Ces journaux peuvent être affichés dans des outils tels que Power BI et Log Analytics. Pour savoir comment afficher les journaux de diagnostic, consultez [Solutions d’analyse réseaux Azure dans Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).

Les journaux de diagnostic sont disponibles pour [l’équilibrage de charge](../load-balancer/load-balancer-monitor-log.md), [les groupes de sécurité réseau](../virtual-network/virtual-network-nsg-manage-log.md), les itinéraires, et [Application Gateway](../application-gateway/application-gateway-diagnostics.md).

Network Watcher permet d’afficher les journaux de diagnostic. Cet affichage contient toutes les ressources réseau qui prennent en charge la journalisation des diagnostics. À partir de celui-ci, vous pouvez activer et désactiver les ressources réseau facilement et rapidement.

![journaux][logs]

### <a name="troubleshooting"></a>Résolution de problèmes

Le panneau de résolution des problèmes, une expérience dans le portail, est proposé pour les ressources réseau dès aujourd’hui pour diagnostiquer les problèmes courants associés à une ressource individuelle. Cette expérience est disponible pour les ressources réseau suivantes : ExpressRoute, passerelle VPN, Application Gateway, journaux de sécurité réseau, itinéraires, DNS, équilibrage de charge et Traffic Manager. Pour plus d’informations sur la résolution des problèmes au niveau des ressources, consultez [Diagnostiquer et résoudre les problèmes avec la résolution des problèmes Azure](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/)

![informations sur la résolution des problèmes][TS]

### <a name="resource-health"></a>Intégrité des ressources

L’intégrité d’une ressource réseau est fournie régulièrement. Ces ressources incluent la passerelle VPN et le tunnel VPN. L’intégrité des ressources est accessible sur le portail Azure. Pour en savoir plus sur l’intégrité des ressources, consultez [Vue d’ensemble d’Azure Resource Health](../resource-health/resource-health-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert Network Watcher, vous pouvez apprendre à :

Effectuer une capture de paquets sur votre machine virtuelle en consultant [Gérer les captures de paquets avec Azure Network Watcher à l’aide du portail](network-watcher-packet-capture-manage-portal.md).

Mener une surveillance et faire des diagnostics de manière proactive à l’aide [des captures de paquets déclenchées par des alertes](network-watcher-alert-triggered-packet-capture.md).

Détecter les vulnérabilités de sécurité avec [l’analyse des captures des paquets au moyen de Wireshark](network-watcher-deep-packet-inspection.md), à l’aide d’outils open source.

Découvrez certaines des autres [fonctionnalités de réseau](../networking/networking-overview.md) clés d’Azure.

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png











