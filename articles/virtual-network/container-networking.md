---
title: "Réseau virtuel Azure pour conteneurs | Microsoft Docs"
description: "Apprenez-en davantage sur le plug-in CNI pour les clusters Kubernetes, qui permet aux conteneurs de communiquer entre eux et avec d’autres ressources, dans un réseau virtuel."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2017
---
# <a name="container-networking"></a>Mise en réseau de conteneurs

Azure fournit un [plug-in d’interface réseau de conteneur (CNI)](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) qui vous permet de déployer et de gérer votre propre cluster Kubernetes, avec la fonctionnalité de mise en réseau Azure native. Le plug-in est activé par défaut, lorsque du déploiement des clusters Kubernetes avec le [moteur Azure Container Service](https://github.com/Azure/acs-engine) (moteur ACS).

## <a name="networking-capabilities"></a>Fonctionnalités de mise en réseau

Les conteneurs peuvent utiliser l’ensemble complet de fonctionnalités qu’offre un réseau virtuel, tel que :
-   Vous pouvez créer un réseau virtuel distinct pour votre cluster, ou déployer votre cluster dans un réseau virtuel existant. 
-   Chaque pod dans le cluster reçoit une adresse IP du réseau virtuel et peut communiquer directement avec les autres pods dans le cluster ainsi qu’avec tout ordinateur virtuel dans le réseau virtuel. 
-   Un pod peut se connecter à d’autres pods et machines virtuelles dans des réseaux virtuels homologués et à des réseaux locaux, via les connexions VPN site à site et ExpressRoute. Les ressources locales peuvent communiquer avec les pods. 
-   Vous pouvez exposer un service Kubernetes à Internet via Azure Load Balancer.  
-   Les pods dans un sous-réseau disposant de points de terminaison de service activés peuvent se connecter en toute sécurité aux services Azure (Stockage et SQL Database, par exemple).
-   Vous pouvez utiliser les itinéraires définis par l’utilisateur pour acheminer le trafic des pods vers une appliance virtuelle réseau. 
-   Les pods peuvent accéder aux ressources publiques sur Internet.
-   Vous pouvez affecter une adresse IP publique à un pod, qui peut être associée à un nom DNS.
 
## <a name="limits"></a>limites
Vous pouvez déployer jusqu’à 4 000 nœuds dans un cluster Kubernetes et jusqu’à 250 pods par nœud, avec une limite globale de 16 000 pods par cluster, lorsque vous utilisez le plug-in.

## <a name="constraints"></a>Contraintes
- Le plug-in n’est pas activé lorsque vous déployez un cluster Kubernetes avec le cluster [Azure Container Service (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) avec Kubernetes.
- Il n’existe aucune prise en charge native pour les stratégies de réseau Kubernetes, notamment les stratégies d’accès ou DNS.
- Le plug-in ne prend pas en charge les stratégies de réseau par pod.

## <a name="pricing"></a>Tarifs
L’utilisation du plug-in CNI n’est pas facturée.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer un cluster Kubernetes](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md) dans votre [propre réseau virtuel](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni).
