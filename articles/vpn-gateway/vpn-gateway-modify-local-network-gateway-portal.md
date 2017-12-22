---
title: "Modifier les préfixes d’adresse IP d’une passerelle de réseau local et l’adresse IP d’une passerelle VPN | Azure | Portail | Microsoft Docs"
description: "Cet article vous guide tout au long du processus de modification des préfixes d’adresse IP de la passerelle de votre réseau local à l’aide du portail Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: bdd6f90fe97408bd45a72adf58bfdc190207de46
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modifier les paramètres de la passerelle du réseau local à l’aide du portail Azure

Parfois, les paramètres de la passerelle de réseau local AddressPrefix ou GatewayIPAddress changent. Cet article vous montre comment modifier les paramètres de passerelle de votre réseau local. Vous pouvez également modifier ces paramètres à l’aide d’une méthode différente en sélectionnant une autre option dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interface de ligne de commande Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>Modifier les préfixes d’adresse IP

Lorsque vous modifiez des préfixes d’adresse IP, la procédure à suivre varie selon que votre passerelle de réseau local dispose d’une connexion.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>Modifier l’adresse IP de la passerelle

Si le périphérique VPN auquel vous souhaitez vous connecter a changé son adresse IP publique, vous devez modifier la passerelle de réseau local pour refléter cette modification. Lorsque vous modifiez l’adresse IP publique, la procédure à suivre varie selon que votre passerelle de réseau local dispose d’une connexion.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez vérifier votre connexion à la passerelle. Consultez [Vérifier la connexion à une passerelle](vpn-gateway-verify-connection-resource-manager.md).