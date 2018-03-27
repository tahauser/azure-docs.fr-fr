---
title: Anciennes références SKU de passerelle VPN de réseau virtuel Azure | Microsoft Docs
description: Comment utiliser les anciennes références SKU de passerelle de réseau virtuel ; de base, standard et HighPerformance.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: cherylmc
ms.openlocfilehash: 4feecb9c1e91e1bc6c66a610c092e7bf894886e5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Utilisation des références SKU de passerelle de réseau virtuel (anciennes références SKU)

Cet article contient des informations sur les anciennes références SKU de passerelle de réseau virtuel. Les anciennes références SKU continuent de fonctionner dans les deux modèles de déploiement pour les passerelles VPN qui ont déjà été créés. Les passerelles VPN classiques continuent à utiliser d’anciennes références SKU, aussi bien pour les passerelles existantes que pour les nouvelles passerelles. Lorsque vous créez de nouvelles passerelles VPN de gestionnaire de ressources, utilisez les références SKU des nouvelles passerelles. Pour plus d’informations sur les nouvelles références SKU, reportez-vous à la rubrique [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKU de passerelle

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Débit agrégé estimé par SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Configurations prises en charge par référence SKU et type de VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Redimensionner une passerelle

Vous pouvez redimensionner votre passerelle selon une référence SKU de passerelle au sein de la même famille de références SKU. Par exemple, si vous avez une référence SKU standard, vous pouvez redimensionner selon une référence SKU HighPerformance. Cependant, vous ne pouvez pas redimensionner votre passerelle VPN entre d’anciennes références SKU est de nouvelles familles de références SKU. Par exemple, vous ne pouvez pas aller d’une référence SKU standard à une référence SKU VpnGw2, ou d’une référence SKU de base à VpnGw1.

Pour redimensionner une passerelle pour le modèle de déploiement classique, utilisez la commande suivante :

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Pour redimensionner une passerelle pour le modèle de déploiement du Gestionnaire des ressources à l’aide de PowerShell, utilisez la commande suivante :

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
Vous pouvez également redimensionner une passerelle dans le portail Azure.

## <a name="change"></a>Basculer vers les nouvelles références SKU de passerelle

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les nouvelles références SKU de passerelle, voir [Références (SKU) de passerelle](vpn-gateway-about-vpngateways.md#gwsku).

Pour plus d’informations sur les paramètres de configuration, consultez [À propos des paramètres de configuration de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md).