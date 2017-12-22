---
title: "Modifier les préfixes des adresses IP de la passerelle IP locale et l’adresse IP de la passerelle VPN | Azure | PowerShell | Microsoft Docs"
description: "Cet article vous guide tout au long du processus de modification des préfixes d’adresse IP de la passerelle de votre réseau local à l’aide de PowerShell"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: 2a095b96a8c352abeca72640d37c0d629b447763
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modification des paramètres de passerelle de réseau local à l’aide de PowerShell

Parfois, les paramètres de la passerelle de réseau local AddressPrefix ou GatewayIPAddress changent. Cet article vous montre comment modifier les paramètres de passerelle de votre réseau local. Vous pouvez également modifier ces paramètres à l’aide d’une méthode différente en sélectionnant une autre option dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interface de ligne de commande Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Avant de commencer

Installez la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) .

## <a name="ipaddprefix"></a>Modifier les préfixes d’adresse IP

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="gwip"></a>Modifier l’adresse IP de la passerelle

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez vérifier votre connexion à la passerelle. Consultez [Vérifier la connexion à une passerelle](vpn-gateway-verify-connection-resource-manager.md).