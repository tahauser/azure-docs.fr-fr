---
title: Lier un réseau virtuel à un circuit ExpressRoute avec l’interface Azure CLI | Microsoft Docs
description: Ce document explique comment lier des réseaux virtuels à des circuits ExpressRoute à l’aide du modèle de déploiement Resource Manager et de l’interface CLI.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlit
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: anzaman,cherylmc
ms.openlocfilehash: 5e8d1739aa3d7f5be6c6450edcad43bc83db71fb
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Connecter un réseau virtuel à un circuit ExpressRoute à l’aide de l’interface CLI

Cet article est conçu pour vous aider à lier des réseaux virtuels à des circuits ExpressRoute de Azure à l’aide de l’interface CLI. Pour pouvoir être liés avec l’interface Azure CLI, les réseaux virtuels doivent être créés à l’aide du modèle de déploiement Resource Manager. Ils peuvent appartenir au même abonnement ou faire partie d’un autre abonnement. Si vous souhaitez utiliser une autre méthode pour connecter votre réseau virtuel à un circuit ExpressRoute, vous pouvez sélectionner un article dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [interface de ligne de commande Azure](howto-linkvnet-cli.md)
> * [Vidéo - portail Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (classique)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Prérequis de configuration

* Vous avez besoin de la dernière version de l’interface de ligne de commande (CLI). Pour plus d’informations, consultez [Installer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Avant de commencer la configuration, vous devez examiner les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md).

* Vous devez disposer d’un circuit ExpressRoute actif. 
  * Suivez les instructions permettant de [créer un circuit ExpressRoute](howto-circuit-cli.md) et faites-le activer par votre fournisseur de service de connectivité. 
  * Vérifiez que l’homologation privée Azure est configurée pour votre circuit. Pour obtenir des instructions de routage, consultez l'article sur la [configuration du routage](howto-routing-cli.md) . 
  * Assurez-vous que l’homologation privée Azure est configurée. L’homologation BGP entre votre réseau et Microsoft doit être opérationnelle pour que vous puissiez activer la connectivité de bout en bout.
  * Vérifiez qu’un réseau virtuel et une passerelle de réseau virtuel ont été créés et entièrement approvisionnés. Suivez les instructions fournies dans l’article [Configurer une passerelle de réseau virtuel pour ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Veillez à utiliser `--gateway-type ExpressRoute`.

* Vous pouvez lier jusqu’à 10 réseaux virtuels à un circuit ExpressRoute standard. Tous les réseaux virtuels doivent figurer dans la même région géopolitique lors de l’utilisation d’un circuit ExpressRoute standard. 

* Un réseau virtuel unique peut être lié à quatre circuits ExpressRoute maximum. Utilisez la procédure ci-dessous afin de créer un objet de connexion pour chaque circuit ExpressRoute auquel vous vous connectez. Les circuits ExpressRoute peuvent être dans le même abonnement, dans des abonnements différents ou dans une combinaison des deux.

* Si vous avez activé le module complémentaire ExpressRoute Premium, vous pouvez lier un réseau virtuel à l’extérieur de la zone géopolitique du circuit ExpressRoute ou connecter un plus grand nombre de réseaux virtuels à votre circuit ExpressRoute. Pour plus d’informations sur le module complémentaire Premium, consultez le [FAQ ExpressRoute](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Connecter un réseau virtuel du même abonnement à un circuit

Vous pouvez connecter une passerelle de réseau virtuel à un circuit ExpressRoute à l’aide de l’exemple suivant. Assurez-vous que la passerelle de réseau virtuel est créée et prête pour la liaison avant d’exécuter la commande.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Connecter un réseau virtuel d’un autre abonnement à un circuit

Vous pouvez partager un circuit ExpressRoute entre plusieurs abonnements. La figure suivante montre un schéma simple sur le fonctionnement du partage de circuits ExpressRoute entre plusieurs abonnements.

Chacun des petits clouds dans le cloud principal est utilisé pour représenter les abonnements appartenant à différents services au sein d’une organisation. Chacun des services au sein de l’organisation peut utiliser son propre abonnement pour déployer ses services, mais ils peuvent partager un même circuit ExpressRoute pour se connecter à votre réseau local. Un seul service (dans cet exemple, le service informatique) peut posséder le circuit ExpressRoute. D’autres abonnements au sein de l’organisation peuvent utiliser le circuit ExpressRoute.

> [!NOTE]
> Les frais de connectivité et de bande passante pour le circuit dédié s’appliquent au propriétaire du circuit ExpressRoute. Tous les réseaux virtuels partagent la même bande passante.
> 
> 

![Connectivité entre abonnements](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administration : propriétaires de circuit et utilisateurs du circuit

Le « propriétaire du circuit » est un utilisateur avancé autorisé de la ressource de circuit ExpressRoute. Le propriétaire du circuit peut créer des autorisations utilisables par les « utilisateurs du circuit ». Les utilisateurs du circuit sont les propriétaires des passerelles de réseau virtuel qui ne figurent pas dans le même abonnement que le circuit ExpressRoute. Les utilisateurs du circuit peuvent utiliser des autorisations (une seule autorisation par réseau virtuel).

Le propriétaire du circuit a le pouvoir de modifier et de révoquer les autorisations à tout moment. Lorsqu’une autorisation est révoquée, toutes les connexions sont supprimées de l’abonnement dont l’accès a été révoqué.

### <a name="circuit-owner-operations"></a>Opérations du propriétaire du circuit

**Création d’une autorisation**

Le propriétaire du circuit crée une autorisation, ce qui entraîne la création d’une clé d’autorisation qui peut être utilisée par un utilisateur du circuit pour connecter ses passerelles de réseau virtuel à un circuit ExpressRoute. Une autorisation n’est valide que pour une seule connexion.

L’exemple suivant montre comment créer une autorisation :

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

La réponse contient la clé et l’état d’autorisation :

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Vérification des autorisations**

Le propriétaire du circuit peut vérifier toutes les autorisations émises pour un circuit donné en exécutant l’exemple suivant :

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Ajout d’autorisations**

Le propriétaire du circuit peut ajouter des autorisations à l’aide de l’exemple suivant :

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Suppression d’autorisations**

Le propriétaire du circuit peut révoquer/supprimer les autorisations accordées à l’utilisateur en exécutant l’exemple suivant :

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Opérations de l’utilisateur du circuit

L’utilisateur du circuit a besoin de l’ID de pair et d’une clé d’autorisation de la part du propriétaire du circuit. La clé d'autorisation est un GUID.

```azurecli
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Réclamation d’une autorisation de connexion**

L’utilisateur du circuit peut exécuter l’applet de commande suivante pour utiliser une autorisation de liaison :

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Libération d’une autorisation de connexion**

Vous pouvez libérer une autorisation en supprimant la connexion qui lie le circuit ExpressRoute et le réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).