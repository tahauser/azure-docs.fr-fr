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
ms.openlocfilehash: ea616786d69d41435be2a46e90d4973b21270935
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
1. Accédez à la page de votre passerelle de réseau virtuel et ouvrez-la. Vous pouvez y accéder de plusieurs manières. Dans notre exemple, nous avons accédé à la passerelle VNet1GW en accédant à **TestVNet1 -> Vue d’ensemble -> Appareils connectés -> VNet1GW**.
2. Sur la page de VNet1GW, cliquez sur **Connexions**. En haut du panneau Connexions, cliquez sur **+ Ajouter** pour ouvrir la page **Ajouter une connexion**.

    ![Créer une connexion de site à site](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. Sur la page **Ajouter une connexion**, renseignez les valeurs pour créer votre connexion.

  - **Nom** : nommez votre connexion. Nous utilisons **VNet1toSite2** dans notre exemple.
  - **Type de connexion** : sélectionnez **Site à site (IPSec)**.
  - **Passerelle de réseau virtuel** : la valeur est fixe, car vous vous connectez à partir de cette passerelle.
  - **Passerelle de réseau local** : cliquez sur **Choisir une passerelle de réseau local** et sélectionnez la passerelle de réseau local que vous souhaitez utiliser. Dans notre exemple, nous utilisons **Site2**.
  - **Clé partagée** : la valeur doit correspondre à celle que vous utilisez pour votre périphérique VPN local. Dans l’exemple, nous avons utilisé « abc123 », mais vous pouvez (et devriez) utiliser une valeur plus complexe. L’important, c’est que la valeur que vous spécifiez ici doit être identique à celle spécifiée lors de la configuration de votre périphérique VPN.
  - Les autres valeurs pour **abonnement**, **groupe de ressources**, et **emplacement** sont fixes.

4. Cliquez sur **OK** pour créer votre connexion. Le message *Création de la connexion* clignote à l'écran.
5. Vous pouvez afficher la connexion dans la page **Connexions** de la passerelle de réseau virtuel. L’état passe de *Inconnu* à *Connexion*, puis à *Réussi*.