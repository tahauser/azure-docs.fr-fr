---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c06e69dd9d1997500589659e936dc25ee01ed145
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Pour les références SKU en cours (VpnGw1, VpnGw2 et VPNGW3) pour lesquelles vous souhaitez redimensionner la référence SKU de votre passerelle en vue d’une mise à niveau vers une référence SKU plus puissante, vous pouvez utiliser la cmdlet PowerShell `Resize-AzureRmVirtualNetworkGateway`. Vous pouvez également réduire la taille de référence SKU à l’aide de cet applet de commande. Si vous utilisez la référence SKU de passerelle De base [utilisez ces instructions à la place](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) pour redimensionner votre passerelle.

L’exemple PowerShell suivant montre une référence SKU de passerelle redimensionnée sur VpnGw2.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Vous pouvez également redimensionner une passerelle dans le portail Azure en accédant à la page **Configuration** de la passerelle de votre réseau virtuel, puis en sélectionnant une autre référence SKU dans la liste déroulante.