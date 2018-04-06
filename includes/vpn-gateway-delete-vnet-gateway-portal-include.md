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
ms.openlocfilehash: 5b2aa7fedbc203c50796a0e0c8d9cdb3895ae6c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Étape 1 : Accès à la passerelle de réseau virtuel

1. Dans le [Portail Azure](https://portal.azure.com), accédez à **Toutes les ressources**. 
2. Pour ouvrir le page Passerelle de réseau virtuel, accédez à la passerelle de réseau virtuel que vous souhaitez supprimer et cliquez dessus.

### <a name="step-2-delete-connections"></a>Étape 2 : Suppression des connexions

1. Dans la page de votre passerelle de réseau virtuel, cliquez sur **Connexions** pour afficher toutes les connexions à la passerelle.
2. Cliquez sur **'...'** sur la ligne du nom de la connexion, puis sélectionnez **Supprimer** dans la liste déroulante.
3. Cliquez sur **Oui** pour confirmer que vous souhaitez supprimer la connexion. Si vous avez plusieurs connexions, supprimez chaque connexion.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Étape 3 : Supprimez la passerelle de réseau virtuel

À savoir : Si vous avez une configuration P2S sur ce réseau virtuel en plus de votre configuration S2S, la suppression de la passerelle de réseau virtuel déconnecte automatiquement tous les clients P2S sans avertissement.

1. Dans la page Passerelle de réseau virtuel, cliquez sur **Vue d’ensemble**.
2. Dans la page **Vue d’ensemble**, cliquez sur **Supprimer** pour supprimer la passerelle.
