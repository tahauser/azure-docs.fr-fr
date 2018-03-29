---
title: Présentation d’Azure Internal Load Balancer | Microsoft Docs
description: Fonctionnement d’un équilibreur de charge interne dans Azure et scénarios de configuration des points de terminaison internes.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0511165225f5a336291e86e0c504e60989933f3c
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="overview-of-azure-internal-load-balancer"></a>Présentation d’Azure Internal Load Balancer



Azure Internal Load Balancer (ILB) dirige uniquement le trafic vers les ressources qui sont à l’intérieur d’un service cloud ou qui utilisent un réseau VPN pour accéder à une infrastructure Azure. À cet égard, l’ILB diffère d’un équilibreur de charge accessible sur Internet. L’infrastructure Azure limite l’accès aux adresses IP virtuelles (VIP) d’un service cloud soumises à l’équilibrage de charge ou à un réseau virtuel. Les adresses IP virtuelles et les réseaux virtuels ne sont jamais directement exposés à un point de terminaison Internet. Les applications métier internes s’exécutent dans Azure et sont accessibles à partir d’Azure ou à partir des ressources locales.

## <a name="why-you-might-need-an-internal-load-balancer"></a>Pourquoi utiliser un équilibreur de charge interne

L’équilibreur de charge interne permet l’équilibrage de charge entre les machines virtuelles qui résident dans un service cloud ou un réseau virtuel avec une portée régionale. Pour plus d’informations sur les réseaux virtuels avec une portée régionale, consultez [Réseaux virtuels régionaux](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) sur le blog Azure. Les réseaux virtuels existants qui sont configurés pour un groupe d’affinités ne peuvent pas utiliser l’ILB.

L’ILB permet d’effectuer les types d'équilibrage de charge suivants :

* Dans un service cloud : équilibrage de charge des machines virtuelles à un ensemble de machines virtuelles qui résident dans le même service cloud. Voir cet <a href="#figure1">exemple</a>.
* Dans un réseau virtuel : équilibrage de charge des machines virtuelles dans le réseau virtuel à un ensemble de machines virtuelles qui résident dans le même service cloud du réseau virtuel. Voir cet <a href="#figure2">exemple</a>.
* Pour un réseau virtuel entre différents locaux : équilibrage de charge des ordinateurs locaux à un ensemble de machines virtuelles qui résident dans le même service cloud du réseau virtuel. Voir cet <a href="#figure3">exemple</a>.
* Pour les applications à plusieurs niveaux : équilibrage de charge pour les applications multiniveau accessibles sur Internet dans lesquelles les niveaux principaux ne sont pas accessibles sur Internet. Les niveaux principaux nécessitent un équilibrage de la charge du trafic du niveau accessible sur Internet.
* Pour les applications métier : équilibrage de charge pour des applications métier hébergées dans Azure, sans matériel ou logiciel d’équilibrage de charge supplémentaire. Ce scénario inclut des serveurs locaux dans l’ensemble d’ordinateurs dont la charge du trafic est équilibrée.

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>Équilibrage de charge pour les applications multiniveau accessibles sur Internet

La couche web a des points de terminaison sur Internet pour les clients Internet et fait partie d’un jeu d’équilibrage de charge. L’ILB répartit le trafic entrant à partir des clients web pour le port TCP 443 (HTTPS) pour les serveurs web.

Les serveurs de base de données sont situés derrière un point de terminaison de l’ILB utilisé par les serveurs web pour le stockage. Le point de terminaison de l’ILB est un point de terminaison de service de base de données soumis à l’équilibrage de charge. Le trafic est équilibré entre les serveurs de base de données dans l’ensemble de l’ILB.

L’image suivante présente l’équilibrage de charge interne de l’application multiniveau sur Internet dans le même service cloud.

<a name="figure1"></a>
![Une application multiniveau sur Internet](./media/load-balancer-internal-overview/IC736321.png)

Un autre scénario est disponible pour les applications multiniveau. L’équilibreur de charge est déployé sur un service cloud différent de celui qui utilise le service pour l’ILB.

Les services cloud qui utilisent le même réseau virtuel peuvent accéder au point de terminaison de l’ILB. L’image suivante présente des serveurs web frontaux qui se trouvent dans un autre service cloud que la base de données principale. Les serveurs frontaux utilisent le point de terminaison de l’ILB dans le même réseau virtuel que le serveur principal.

<a name="figure2"></a>
![Serveurs frontaux dans un service cloud différent](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>Équilibrage de charge pour les applications métier sur Intranet

Le trafic des clients sur le réseau local obtient un équilibrage de charge sur l’ensemble des serveurs métier qui utilisent une connexion VPN au réseau Azure.

L’ordinateur client peut accéder à une adresse IP du service VPN Azure à l’aide d’un VPN point à site. L’application métier peut être hébergée derrière le point de terminaison de l’ILB.

<a name="figure3"></a>
![Application métier hébergée derrière le point de terminaison de l’ILB](./media/load-balancer-internal-overview/IC744148.png)

Un autre scénario pour les applications métier est d’avoir un VPN de site à site sur le réseau virtuel dans lequel le point de terminaison de l’ILB est configuré. Le trafic du réseau local est acheminé vers le point de terminaison de l’ILB.

<a name="figure4"></a>
![Trafic du réseau local acheminé vers le point de terminaison de l’ILB](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>Limites

Les configurations de l’équilibreur de charge interne ne prennent pas en charge SNAT. Dans cet article, SNAT fait référence aux scénarios qui impliquent la traduction d’adresses réseau sources en masquant des ports. Une machine virtuelle située dans un pool d’équilibrage de charge doit atteindre l’adresse IP frontale de l’équilibreur de charge interne correspondant. Des problèmes de connexion surviennent lorsque le flux est à charge équilibrée pour la machine virtuelle à l’origine du flux. Ces scénarios ne sont pas pris en charge pour l’ILB. Un équilibreur de charge de type proxy doit être utilisé à la place.

## <a name="next-steps"></a>Étapes suivantes

* [Prise en charge d'Azure Resource Manager pour l'équilibrage de charge Azure](load-balancer-arm.md)
* [Prise en main de la configuration d’un équilibreur de charge sur Internet](load-balancer-get-started-internet-arm-ps.md)
* [Prise en main de la configuration d’un équilibreur de charge interne](load-balancer-get-started-ilb-arm-ps.md)
* [Configuration d’un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)
* [Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
