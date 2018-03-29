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
ms.openlocfilehash: 932aab3f16a571d4c83c77c1cc2274ae60ea3d35
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
1. Dans le portail, à partir de **Toutes les ressources**, cliquez sur **+ Ajouter**.
2. Dans la zone de recherche de la page **Tout**, tapez **passerelle de réseau local**, puis cliquez pour obtenir une liste de ressources. Cliquez sur **Passerelle de réseau local** pour ouvrir la page correspondante, puis cliquez sur **Créer** pour ouvrir la page **Créer une passerelle de réseau local**.

  ![créer une passerelle de réseau local](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. Dans la page **Créer une passerelle de réseau local**, spécifiez les valeurs de votre passerelle de réseau local.

  - **Nom :** spécifiez un nom pour votre objet de passerelle de réseau local. Dans la mesure du possible, utilisez un nom intuitif, tel que **RéseauVirtuelClassicLocal** ou **RéseauVirtuel1TestLocal**. Cette approche vous aidera à identifier plus facilement la passerelle de réseau local dans le portail.
  - **Adresse IP :** spécifiez une **adresse IP** publique valide pour la passerelle de réseau virtuel ou l’appareil VPN auquel vous souhaitez vous connecter.

    * **Si ce réseau local représente un emplacement local:** spécifiez l’adresse IP publique du périphérique VPN auquel vous souhaitez vous connecter. Il ne peut pas se trouver derrière NAT et doit être accessible par Azure.
    * **Si ce réseau local représente un autre réseau virtuel :** spécifiez l’adresse IP publique qui a été affectée à la passerelle de réseau virtuel pour ce réseau virtuel.
    * **Si vous ne disposez pas encore de l’adresse IP :** vous pouvez créer une adresse IP avec espace réservé valide, puis réaccéder à ce panneau et modifier ce paramètre avant de vous connecter.
  - **Espace d’adressage** fait référence aux plages d’adresses du réseau qui représente ce réseau local. Vous pouvez ajouter plusieurs plages d’espaces d’adressage. Assurez-vous que les plages que vous spécifiez ici ne chevauchent pas les plages d’autres réseaux auxquels vous vous connectez.
  - **Configurer les paramètres de BGP** : sélectionnez cette option uniquement pour la configuration du protocole BGP.
  - **Abonnement** : vérifiez que l’abonnement approprié s’affiche.
  - **Groupe de ressources :** sélectionnez le groupe de ressources que vous souhaitez utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un déjà créé.
  - **Emplacement** : sélectionnez l’emplacement dans lequel cet objet sera créé. Vous pouvez sélectionner l’emplacement dans lequel se trouve votre réseau virtuel, mais vous n’êtes pas obligé de le faire.
4. Cliquez sur **Créer** pour créer la passerelle de réseau local.