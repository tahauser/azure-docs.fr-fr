---
title: À propos des passerelles de réseau virtuel pour ExpressRoute | Microsoft Docs
description: En savoir plus sur les passerelles de réseau virtuel pour ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: cherylmc
ms.openlocfilehash: 0517caed3a7d6632c1a5650147f4db240dbe0a17
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>À propos des passerelles de réseau virtuel pour ExpressRoute
Une passerelle de réseau virtuel est conçue pour faire circuler le trafic réseau entre les réseaux virtuels Azure et les emplacements locaux. Lorsque vous configurez une connexion ExpressRoute, vous devez créer et configurer une passerelle de réseau virtuel et une connexion à la passerelle de réseau virtuel.

Lorsque vous créez une passerelle de réseau virtuel, vous spécifiez plusieurs paramètres. L’un des paramètres requis spécifie si la passerelle sera utilisée pour le trafic ExpressRoute ou le trafic VPN de site à site. Dans le modèle de déploiement de Resource Manager, le paramètre est « -GatewayType ».

Lorsque le trafic réseau est envoyé sur une connexion privée, vous utilisez le type de passerelle « ExpressRoute ». C’est ce que l’on appelle une passerelle ExpressRoute. Lorsque le trafic réseau est transmis chiffré sur l’Internet public, vous utilisez le type de passerelle « Vpn ». Il s’agit alors d’une passerelle VPN. Les connexions site à site, point à site et réseau virtuel à réseau virtuel utilisent toutes une passerelle VPN.

Chaque réseau virtuel ne peut posséder qu’une seule passerelle de réseau virtuel par type de passerelle. Par exemple, une passerelle de réseau virtuel peut utiliser le type de passerelle VPN et une autre le type de passerelle ExpressRoute. Cet article décrit la passerelle de réseau virtuel ExpressRoute.

## <a name="gwsku"></a>SKU de passerelle
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Si vous souhaitez mettre à niveau votre passerelle vers une référence (SKU) de passerelle plus puissante, dans la plupart des cas, vous pouvez utiliser l’applet de commande PowerShell « Resize-AzureRmVirtualNetworkGateway ». Cela fonctionne pour les mises à niveau vers les références (SKU) Standard HighPerformance. Toutefois, pour mettre à niveau vers la référence (SKU) UltraPerformance, vous devez recréer la passerelle.

### <a name="aggthroughput"></a>Débit agrégé estimé par SKU de passerelle
Le tableau ci-dessous présente les types de passerelle et le débit total estimé. Cette table s’applique aux modèles de déploiement classique et Resource Manager.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Le débit de l’application dépend de plusieurs facteurs, tels que la latence de bout en bout et le nombre de flux de trafic que l’application ouvre. Les numéros indiqués dans le tableau représentent la limite supérieure que l’application peut théoriquement atteindre dans un environnement idéal. 
> 
>

## <a name="resources"></a>API REST et applets de commande PowerShell
Pour accéder à des ressources techniques supplémentaires et connaître les exigences spécifiques en matière de syntaxe lors de l’utilisation d’API REST et d’applets de commande PowerShell pour les configurations de passerelles de réseau virtuel, consultez les pages suivantes :

| **Classique** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les configurations de connexion disponibles, consultez la rubrique [Vue d’ensemble d’ExpressRoute](expressroute-introduction.md) .

Consultez [Créer une passerelle de réseau virtuel pour ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) pour plus d’informations sur la création des passerelles ExpressRoute.
