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
ms.openlocfilehash: 205c67377e4bff66c02e3ee508c0f6f4e2823608
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Vous payez deux choses : les coûts horaires de calcul de la passerelle de réseau virtuel, et les coûts de transfert des données sortantes à partir de la passerelle de réseau virtuel. Pour des informations sur les prix, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/vpn-gateway) .

**Coûts de calcul de la passerelle de réseau virtuel**<br>Chaque passerelle de réseau virtuel a un coût horaire de calcul. Le prix est basé sur la référence SKU de passerelle que vous spécifiez lorsque vous créez une passerelle de réseau virtuel. Le coût est celui de la passerelle elle-même. Il s’ajoute au coût du transfert des données qui transitent par la passerelle.

**Coût de transfert des données**<br>Les coûts de transfert de données sont calculés en fonction du trafic sortant à partir de la passerelle de réseau virtuel source.

* Si vous envoyez du trafic vers votre périphérique VPN local, il est facturé avec le taux de transfert des données sortantes sur Internet.
* Si vous envoyez le trafic entre les réseaux virtuels dans différentes régions, la tarification est basée sur la région.
* Si vous envoyez le trafic seulement entre les réseaux virtuels qui sont dans la même région, il n’y a aucun coût de données. Le trafic entre les réseaux virtuels dans la même région est gratuit.