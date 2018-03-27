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
ms.openlocfilehash: c9457e51858d4a073d8baffdd435c8100d95d566
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
|**Référence (SKU)**   | **S2S/VNet-to-VNet<br>Tunnels** | **P2S<br>Connexions** | **Agrégat<br>Référence de débit** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Bande passante 30                         | Bande passante 128               | 650 Mbits/s                    |
|**VpnGw2**| Bande passante 30                         | Bande passante 128               | 1 Gbit/s                      |
|**VpnGw3**| Bande passante 30                         | Bande passante 128               | 1,25 Gbits/s                   |
|**De base** | Bande passante 10                         | Bande passante 128               | 100 Mbits/s                    | 
|          |                                 |                        |                             | 

- La référence de débit agrégée est basée sur les mesures de plusieurs tunnels agrégés via une passerelle unique. Le débit n’est pas garanti en raison de conditions de trafic Internet et des comportements de votre application.

- Pour des informations sur les prix, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/vpn-gateway) .

- Vous trouverez des informations relatives au contrat de niveau de service (SLA) sur la page [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

- VpnGw1, VpnGw2 et VpnGw3 sont compatibles avec les passerelles VPN qui utilisent le modèle de déploiement du Gestionnaire des ressources uniquement.