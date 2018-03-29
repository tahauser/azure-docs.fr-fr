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
ms.openlocfilehash: 0270fa207b782d2d89f2408a380b766f58fa05cb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
1. Dans le portail, à partir de **Toutes les ressources**, cliquez sur **+ Ajouter**. 
2. Dans la zone de recherche de la page **Tout**, tapez **passerelle de réseau local**, puis cliquez sur Rechercher. Cette commande retourne une liste. Cliquez sur **Passerelle de réseau local** pour ouvrir la page correspondante, puis cliquez sur **Créer** pour ouvrir la page **Créer une passerelle de réseau local**.

  ![créer une passerelle de réseau local](./media/vpn-gateway-add-lng-s2s-rm-portal-include/createlng.png)

3. Dans la page **Créer une passerelle de réseau local**, spécifiez les valeurs de votre passerelle de réseau local.

  - **Nom :** spécifiez un nom pour votre objet de passerelle de réseau local.
  - **Adresse IP :** il s’agit de l’adresse IP publique du périphérique VPN auquel vous souhaitez qu’Azure se connecte. Spécifiez une adresse IP publique valide. L’adresse IP ne peut pas se trouver derrière NAT et doit être accessible par Azure. Si vous ne disposez pas de l’adresse IP actuellement, vous pouvez utiliser les valeurs indiquées dans la capture d’écran. Toutefois, vous devrez revenir en arrière et remplacer votre adresse IP d’espace réservé par l’adresse IP publique de votre périphérique VPN. Dans le cas contraire, Azure ne sera pas en mesure de se connecter.
  - **Espace d’adressage** fait référence aux plages d’adresses du réseau qui représente ce réseau local. Vous pouvez ajouter plusieurs plages d’espaces d’adressage. Assurez-vous que les plages que vous spécifiez ici ne se chevauchent pas avec les plages d’autres réseaux auxquels vous souhaitez vous connecter. Azure achemine la plage d’adresses que vous spécifiez vers l’adresse IP du périphérique VPN local. *Utilisez vos propres valeurs ici, pas les valeurs indiquées dans la capture d’écran*.
  - **Configurer les paramètres de BGP** : sélectionnez cette option uniquement pour la configuration du protocole BGP.
  - **Abonnement** : vérifiez que l’abonnement approprié s’affiche.
  - **Groupe de ressources :** sélectionnez le groupe de ressources que vous souhaitez utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un déjà créé.
  - **Emplacement** : sélectionnez l’emplacement dans lequel cet objet sera créé. Vous pouvez sélectionner l’emplacement dans lequel se trouve votre réseau virtuel, mais vous n’êtes pas obligé de le faire.

4. Quand vous avez terminé de spécifier les valeurs, cliquez sur **Créer** en bas de la page pour créer la passerelle de réseau local.