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
ms.openlocfilehash: 05dc8ae48a9164e4f7118d378ab0eb7c30a4249e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Lorsque vous créez une passerelle de réseau virtuel, vous devez spécifier la référence SKU de passerelle que vous voulez utiliser. Sélectionnez la référence SKU qui répond à vos besoins en fonction des types de charges de travail, des débits, des fonctionnalités et des contrats de niveau de service.

###  <a name="benchmark"></a>Références SKU de passerelle par tunnel, connexion et débit

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>Références SKU de passerelle par ensemble de fonctionnalités

Les nouvelles références SKU de passerelle VPN rationalisent les ensembles de fonctionnalités proposés sur les passerelles :

| **Référence (SKU)**| **Caractéristiques**|
| ---    | ---         |
|**De base** (**)   | **VPN basés sur un itinéraire** : 10 tunnels avec P2S ; aucune authentification RADIUS pour P2S ; aucune IKEV2 pour P2S<br>**VPN basés sur les stratégies** : (IKEv1) : 1 itinéraire; aucune P2S|
| **VpnGw1, VpnGw2 et VpnGw3** | **VPN basés sur le routage** : jusqu’à 30 itinéraires (*), P2S, BGP, actif/passif, stratégie IPsec/IKE personnalisée, coexistence ExpressRoute/VPN |
|        |             |

( * ) Vous pouvez configurer « PolicyBasedTrafficSelectors » pour connecter une passerelle VPN basée sur l’itinéraire (VpnGw1, VpnGw2, VpnGw3) à plusieurs pare-feu locaux basés sur des stratégies. Pour en savoir plus, consultez la section [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Connecter des passerelles VPN à plusieurs appareils VPN basés sur des stratégies via PowerShell).

(**) La référence SKU De base est considérée comme une référence SKU héritée. La référence SKU De base a certaines limitations en termes de fonctionnalités. Vous ne pouvez pas redimensionner une passerelle qui utilise une référence SKU De base vers l’une des nouvelles références SKU de passerelle. À la place, vous devez passer à une nouvelle référence SKU, ce qui implique la suppression et la recréation de votre passerelle VPN.

###  <a name="workloads"></a>Références SKU de passerelle - Charges de travail de production ou charges de travail de développement et de test

En raison de différences des contrats de niveau de service et des ensembles de fonctionnalités, nous vous recommandons les références SKU suivantes pour la production par rapport au développement et au test :

| **Charge de travail**                       | **Références SKU**               |
| ---                                | ---                    |
| **Production, charges de travail critiques** | VpnGw1, VpnGw2, VpnGw3 |
| **Développement-Test ou preuve de concept**   | De base (**)                 |
|                                    |                        |

(**) La référence SKU De base est considéré comme une référence SKU héritée et présente des limitations en termes de fonctionnalités. Vérifiez que la fonctionnalité dont vous avez besoin est prise en charge avant d’utiliser la référence SKU De base.

Si vous utilisez les anciennes références SKU (héritées), les recommandations en termes de références SKU de production sont Standard et HighPerformance. Pour obtenir plus d’informations et des instructions sur les anciennes références SKU, consultez [Références SKU de passerelle (héritées)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).