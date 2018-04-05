---
title: Déplacer un appairage public sur Azure ExpressRoute vers l’appairage Microsoft | Microsoft Docs
description: Cet article explique comment déplacer votre appairage public vers l’appairage Microsoft sur ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: cherylmc
ms.openlocfilehash: f34fabc95d5b56edc6e37c323bebf60bd98c8b90
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Déplacer un appairage public vers l’appairage Microsoft

ExpressRoute prend en charge l’utilisation de l’appairage Microsoft avec les filtres de routes dans les services PaaS Azure, tels que le stockage Azure et Azure SQL Database. Vous avez maintenant besoin d’un seul domaine de routage pour accéder aux services SaaS et PaaS Microsoft. Vous pouvez utiliser les filtres de routes afin de publier sélectivement les préfixes de service PaaS pour les régions Azure que vous souhaitez utiliser.

Cet article vous permet de déplacer la configuration d’un appairage public vers l’appairage Microsoft sans temps d’arrêt. Pour plus d’informations sur les domaines de routage et les appairages, consultez [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).


## <a name="before"></a>Avant de commencer

* Pour vous connecter à l’appairage Microsoft, vous devez configurer et gérer un processus NAT. Votre fournisseur de connectivité peut-configurer et gérer le processus NAT en tant que service géré. Si vous envisagez d’accéder aux services SaaS et PaaS Azure avec l’appairage Microsoft, vous devez dimensionner le pool d’adresses IP NAT correctement. Pour plus d’informations sur le processus NAT pour ExpressRoute, consultez [Configuration NAT requise pour l’homologation Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Si vous utilisez l’appairage public et disposez actuellement de règles de réseau IP pour les adresses IP publiques qui sont utilisées pour accéder au [Stockage Azure](../storage/common/storage-network-security.md) ou à [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), vérifiez que le pool d’adresses IP NAT configuré avec l’appairage Microsoft se trouve bien dans la liste des adresses IP publiques du compte de stockage Azure ou du compte SQL Azure.

* Pour passer à l’appairage Microsoft sans temps d’arrêt, suivez les étapes fournies dans cet article, dans l’ordre indiqué.

## <a name="create"></a>1. Créer un appairage Microsoft

Si l’appairage Microsoft n’a pas été créé, utilisez un des articles suivants pour le créer. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’appairage Microsoft pour votre circuit.

  * [Créer l’appairage Microsoft à l’aide du portail Azure](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Créer l’appairage Microsoft à l’aide d’Azure PowerShell](expressroute-howto-routing-arm.md#msft)
  * [Créer l’appairage Microsoft à l’aide de l’interface de ligne de commande Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Vérifier que l’appairage Microsoft est activé

Vérifiez que l’appairage Microsoft est activé et que les préfixes publics publiés sont dans l’état configuré.

  * [Portail Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [interface de ligne de commande Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Configurer un filtre de routes et le joindre au circuit

Par défaut, les nouveaux appairages Microsoft ne publient pas de préfixes tant qu’un filtre de routes n’est pas joint au circuit. Quand vous créez une règle de filtre de routes, vous pouvez spécifier la liste des communautés de service pour les régions Azure que vous souhaitez utiliser pour les services PaaS Azure, comme l’indique la capture d’écran suivante :

![Fusionner l’appairage public](.\media\how-to-move-peering\public.png)

Configurez les filtres de routes à l’aide des articles suivants :

  * [Configurer des filtres de routes pour l’appairage Microsoft à l’aide du portail Azure](how-to-routefilter-portal.md)
  * [Configurer des filtres de routes pour l’appairage Microsoft à l’aide d’Azure PowerShell](how-to-routefilter-powershell.md)
  * [Configurer des filtres de routes pour l’appairage Microsoft à l’aide de l’interface de ligne de commande Azure](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Supprimer l’appairage public

Après avoir vérifié que l’appairage Microsoft est configuré et que les préfixes que vous souhaitez utiliser sont correctement publiés sur l’appairage Microsoft, vous pouvez supprimer l’appairage public. Pour supprimer l’appairage public, utilisez l’un des articles suivants :

  * [Supprimer un appairage public Azure à l’aide du portail Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Supprimer un appairage public Azure à l’aide d’Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Supprimer un appairage public Azure à l’aide de l’interface de ligne de commande](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Afficher les homologations
  
Vous pouvez afficher une liste de tous les circuits et homologations ExpressRoute dans le portail Azure. Pour plus d’informations, consultez [Afficher les détails d’homologation Microsoft](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).
