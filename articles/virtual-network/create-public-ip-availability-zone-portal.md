---
title: "Créer une adresse IP publique zonale à l’aide du portail Azure | Microsoft Docs"
description: "Créez une adresse IP publique dans une zone de disponibilité à l’aide du portail Azure."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 0e796c8be0a5db8cb67e4a091fa51ed89920852c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-portal"></a>Créer une adresse IP publique dans une zone de disponibilité à l’aide du portail Azure

Vous pouvez déployer une adresse IP publique dans une zone de disponibilité Azure (préversion). Une zone de disponibilité est une zone physiquement séparée dans une région Azure. Découvrez comment :

> * Créer une adresse IP publique dans une zone de disponibilité
> * Identifier les ressources associées créées dans la zone de disponibilité

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
> Les zones de disponibilité sont en préversion et sont préparées pour vos scénarios de développement et de test. La prise en charge est fournie pour certaines ressources, régions et familles de tailles de machine virtuelle Azure. Pour bien démarrer avec les zones de disponibilité, et pour plus d’informations sur les ressources, les régions et les familles de tailles de machine virtuelle Azure pour lesquelles vous pouvez tester les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Pour obtenir de l’aide, vous pouvez nous contacter sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [ouvrir un ticket de support Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse https://portal.azure.com. 

## <a name="create-a-zonal-public-ip-address"></a>Créer une adresse IP publique zonale

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Adresse IP publique**.
3. Entrez ou sélectionnez des valeurs pour les paramètres suivants, sélectionnez votre abonnement, acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **Créer** :

    |Paramètre|Valeur|
    |---|---|
    |SKU| **De base** : assignée à l’aide de la méthode d’allocation statique ou dynamique. Peut être assignée aux ressources Azure acceptant une adresse IP publique, comme les interfaces réseau, les passerelles VPN, les passerelles d’application (Application Gateway) et les équilibreurs de charge accessibles sur Internet. Vous pouvez assigner l’adresse IP publique à une zone spécifique avec le paramètre **Zone de disponibilité**. Ne sont pas redondantes dans une zone. Pour en savoir plus sur les zones de disponibilité, consultez [Vue d’ensemble de zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). **Standard** : assignée à l’aide de la méthode d’allocation statique uniquement. Peut être assignée à des interfaces réseau ou à des équilibreurs de charge standard accessibles sur Internet. Si vous assignez l’adresse IP publique à un équilibreur de charge standard accessible sur Internet, vous devez sélectionner Standard. Pour plus d’informations sur les références SKU de l’équilibreur de charge Azure, consultez [Référence SKU standard de l’équilibreur de charge Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview). Sont redondantes dans une zone, par défaut. Peuvent être créées pour une zone et garanties dans une zone de disponibilité spécifique. La référence SKU standard est disponible en préversion. Si vous souhaitez créer une adresse IP publique de référence SKU standard, vous devez d’abord vous inscrire pour la préversion. Pour vous inscrire pour la préversion, consultez [S’inscrire pour la préversion de la référence SKU standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#preview-sign-up). Vous pouvez créer la référence SKU standard uniquement dans un emplacement pris en charge.  Pour obtenir la liste des emplacements (régions) pris en charge, consultez [Disponibilité des régions](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#region-availability), ainsi que la page [Mises à jour du Réseau virtuel Microsoft Azure](https://azure.microsoft.com/updates/?product=virtual-network) pour voir si d’autres régions sont prises en charge.|   
    |NOM|Le nom doit être unique au sein du groupe de ressources que vous avez sélectionné.|
    |Groupe de ressources|Cliquez sur Créer et entrez myResourceGroup|
    |Lieu|Europe de l'Ouest|
    |Zone de disponibilité|Si vous avez sélectionné la référence SKU **Standard**, vous pouvez sélectionner *Redondant dans une zone* pour rendre l’adresse IP résiliente entre les zones. Si vous avez sélectionné la référence SKU **De base**, l’adresse IP n’est pas résiliente entre les zones. Quelle que soit la référence SKU que vous choisissez, vous pouvez assigner l’adresse à une zone spécifique, si vous le souhaitez. |

    Les paramètres s’affichent dans le portail, comme dans l’image suivante :

    ![Créer une adresse IP publique zonale](./media/create-public-ip-availability-zone-portal/public-ip-address.png) 

> [!NOTE]
> Quand vous assignez une adresse IP publique de référence SKU standard à l’interface réseau d’une machine virtuelle, vous devez explicitement autoriser le trafic prévu avec un [groupe de sécurité réseau](security-overview.md#network-security-groups). La communication avec la ressource est possible uniquement si vous créez et associez un groupe de sécurité réseau et que vous autorisez explicitement le trafic prévu.

## <a name="next-steps"></a>étapes suivantes

- En savoir plus sur les [zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview)
- En savoir plus sur les [adresses IP publiques](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)