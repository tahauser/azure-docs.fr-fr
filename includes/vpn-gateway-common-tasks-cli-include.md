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
ms.openlocfilehash: e021a1b109f735dee16d75c05c26ab35e599a3d9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
### <a name="to-view-local-network-gateways"></a>Pour afficher les passerelles de réseau local

Pour afficher une liste des passerelles de réseau local, utilisez la commande [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_list).

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>Pour vérifier les valeurs de clé partagée

Vérifiez que la valeur de clé partagée est identique à celle utilisée pour la configuration de votre périphérique VPN. Si ce n’est pas le cas, exécutez à nouveau la connexion en utilisant la valeur du périphérique ou mettez à jour le périphérique avec la valeur obtenue. Les valeurs doivent correspondre. Pour afficher la clé partagée, utilisez [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection#az_network_vpn_connection_list).

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>Pour afficher l’adresse IP publique de la passerelle VPN

Pour trouver l’adresse IP publique de votre passerelle de réseau virtuel, utilisez la commande [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_list). Pour faciliter la lecture, la sortie de cet exemple est mise en forme pour afficher la liste des adresses IP publiques sous forme de tableau.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
