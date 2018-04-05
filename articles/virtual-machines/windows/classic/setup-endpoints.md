---
title: Configurer des points de terminaison sur une machine virtuelle Windows classique | Microsoft Docs
description: Apprenez à configurer des points de terminaison pour une machine virtuelle Windows classique dans le portail Azure pour permettre la communication avec une machine virtuelle Windows dans Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: d64feff341e389df4079c0603a414f0d40b754e7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Comment configurer des points de terminaison sur une machine virtuelle Windows classique dans Azure
Toutes les machines virtuelles Windows créées dans Azure à l’aide du modèle de déploiement classique peuvent automatiquement communiquer à travers un canal réseau privé avec d’autres machines virtuelles dans le même service cloud ou réseau virtuel. Toutefois, les ordinateurs sur Internet ou d'autres réseaux virtuels requièrent des points de terminaison pour diriger le trafic réseau entrant vers une machine virtuelle. Cet article est également disponible pour les [machines virtuelles Linux](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Dans le modèle de déploiement **Resource Manager**, les points de terminaison sont configurés à l’aide de **groupes de sécurité réseau (NSG)**. Pour plus d’informations, voir [Autoriser l’accès externe à votre machine virtuelle à l’aide du portail Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Quand vous créez une machine virtuelle Windows dans le portail Azure, les points de terminaison courants, notamment ceux du Bureau à distance et de l’Accès distant Windows PowerShell, sont généralement créés pour vous automatiquement. Vous pouvez configurer des points de terminaison supplémentaires lors de la création de la machine virtuelle, ou ultérieurement si nécessaire.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer un point de terminaison de machine virtuelle à l’aide d’une applet de commande Azure PowerShell, consultez [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Pour gérer une liste de contrôle d’accès sur un point de terminaison à l’aide d’une applet de commande Azure PowerShell, consultez [Gestion des listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Si vous avez créé une machine virtuelle dans le modèle de déploiement Resource Manager, vous pouvez utiliser Azure PowerShell pour [créer des groupes de sécurité réseau](../../../virtual-network/tutorial-filter-network-traffic.md) afin de contrôler le trafic vers la machine virtuelle.
