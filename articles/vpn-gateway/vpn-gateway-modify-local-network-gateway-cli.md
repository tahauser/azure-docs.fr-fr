---
title: "Modifier les préfixes d’adresse IP d’une passerelle de réseau local et l’adresse IP d’une passerelle VPN | Azure | CLI | Microsoft Docs"
description: "Cet article vous guide tout au long du processus de modification des préfixes d’adresse IP de la passerelle de votre réseau local à l’aide d’Azure CLI."
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
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: d62fa14ea24a9b62e793ef7022a761897984df32
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modifier les paramètres de la passerelle du réseau local à l’aide d’Azure CLI

Parfois, les paramètres de la passerelle de réseau local AddressPrefix ou Gateway IP Address changent. Cet article vous montre comment modifier les paramètres de passerelle de votre réseau local. Vous pouvez également modifier ces paramètres à l’aide d’une méthode différente en sélectionnant une autre option dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interface de ligne de commande Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Avant de commencer

Installez la dernière version des commandes CLI (version 2.0 ou ultérieure). Pour plus d’informations sur l’installation des commandes CLI consultez l’article [Installer l’interface de ligne de commande Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>Modifier les préfixes d’adresse IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Modifier l’adresse IP de la passerelle

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez vérifier votre connexion à la passerelle. Consultez [Vérifier la connexion à une passerelle](vpn-gateway-verify-connection-resource-manager.md).

