---
title: Créer une passerelle VPN basée sur des itinéraires - Portail Azure | Microsoft Docs
description: Créez rapidement une passerelle VPN basée sur des itinéraires à l’aide du portail Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: cherylmc
ms.openlocfilehash: 2d6133e974e24c8c4f769995d8245b30a29a3983
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Créer une passerelle VPN basée sur des itinéraires à l’aide du portail Azure

Cet article vous aidera à créer rapidement une passerelle VPN basée sur des itinéraires à l’aide du portail Azure.  Une passerelle VPN est utilisée lors de la création d’une connexion VPN à votre réseau local. Vous pouvez également vous servir d’une passerelle VPN pour connecter des réseaux virtuels. 

Les étapes fournies dans cet article permettent de créer un réseau virtuel, un sous-réseau, un sous-réseau de passerelle et une passerelle VPN basée sur des itinéraires (passerelle de réseau virtuel). Une fois la passerelle créée, vous pourrez créer des connexions. Ces étapes nécessitent un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="vnet"></a>Créer un réseau virtuel

1. Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Cliquez sur **Créer une ressource**. Dans le champ **Rechercher dans le marketplace**, tapez « réseau virtuel ». Localisez **Réseau virtuel** dans la liste renvoyée et cliquez sur cet élément pour ouvrir la page **Réseau virtuel**.
3. Dans la liste déroulante **Sélectionner un modèle de déploiement** en bas de la page Réseau virtuel, vérifiez que **Resource Manager** est sélectionné, puis cliquez sur **Créer**. La page **Créer un réseau virtuel** s’ouvre.
4. Sur la page **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Lorsque vous renseignez les champs, le point d’exclamation rouge se transforme en coche verte si les caractères saisis dans le champ sont valides. Utilisez les valeurs suivantes :

  - **Nom** : TestVNet1
  - **Espace d’adressage** : 10.1.0.0/16
  - **Abonnement** : vérifiez que l’abonnement indiqué est celui que vous souhaitez utiliser. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.
  - **Groupe de ressources :** TestRG1
  - **Emplacement** : États-Unis de l’Est
  - **Sous-réseau** : Serveur frontal
  - **Plage d’adresses** : 10.1.0.0/24

  ![Page Créer un réseau virtuel](./media/create-routebased-vpn-gateway-portal/vnet1.png "Page Créer un réseau virtuel")
5. Après avoir entré les valeurs, sélectionnez **Épingler au tableau de bord** pour trouver facilement votre réseau virtuel sur le tableau de bord, puis cliquez sur **Créer**. Une fois que vous avez cliqué sur **Créer**, une vignette apparaît sur votre tableau de bord. Celle-ci indique la progression de la création de votre réseau virtuel. La vignette change lorsque le réseau virtuel est créé.

## <a name="gwsubnet"></a>Ajouter un sous-réseau de passerelle

Le sous-réseau de passerelle contient les adresses IP réservées utilisées par les services de passerelle de réseau virtuel. Créez un sous-réseau de passerelle.

1. Dans le portail, accédez au réseau virtuel pour lequel vous souhaitez créer une passerelle de réseau virtuel.
2. Dans la page de votre réseau virtuel, cliquez sur **Sous-réseaux** pour développer la page **VNet1 - Sous-réseaux**.
3. Cliquez sur **+ Sous-réseau de passerelle** en haut de la page pour ouvrir la page **Ajouter un sous-réseau**.

  ![Ajouter un sous-réseau de passerelle](./media/create-routebased-vpn-gateway-portal/gateway_subnet.png "Ajouter un sous-réseau de passerelle")
4. Le **nom** de votre sous-réseau est automatiquement renseigné avec la valeur requise « GatewaySubnet ». Ajustez les valeurs de **plage d’adresses** renseignées automatiquement pour qu’elles correspondent aux valeurs suivantes :

  **Plage d’adresses (bloc CIDR)** : 10.1.255.0/27

  ![Ajouter un sous-réseau de passerelle](./media/create-routebased-vpn-gateway-portal/add_gw_subnet.png "Ajouter un sous-réseau de passerelle")
5. Pour créer le sous-réseau de passerelle, cliquez sur **OK** en bas de la page.

## <a name="gwvalues"></a>Configurer les paramètres de la passerelle

1. Sur le côté gauche de la page du portail, cliquez sur **+ Créer une ressource**, puis entrez « passerelle de réseau virtuel » dans la zone de recherche. Dans **Résultats**, recherchez et cliquez sur **Passerelle de réseau virtuel**.
2. En bas de la page Passerelle de réseau virtuel, cliquez sur **Créer** pour ouvrir la page **Créer une passerelle de réseau virtuel**.
3. Dans la page **Créer une passerelle réseau virtuel**, renseignez les valeurs pour votre passerelle de réseau virtuel.

  - **Nom** : Vnet1GW
  - **Type de passerelle** : VPN 
  - **Type de VPN** : Basé sur itinéraires
  - **Référence (SKU)** : VpnGw1
  - **Emplacement** : États-Unis de l’Est
  - **Réseau virtuel** : cliquez sur **Réseau virtuel/Choisir un réseau virtuel** pour ouvrir la page **Choisir un réseau virtuel**. Sélectionnez **VNet1**.

  ![Configuration des paramètres de la passerelle](./media/create-routebased-vpn-gateway-portal/configure_gw.png "Configuration des paramètres de la passerelle")

## <a name="pip"></a>Créer une adresse IP publique

Une passerelle VPN doit présenter une adresse IP publique allouée dynamiquement. Quand vous créez une connexion à une passerelle VPN, il s’agit de l’adresse IP à laquelle votre appareil local se connecte.

1. Sélectionnez **Première configuration IP - Créer une configuration IP de passerelle** pour demander une adresse IP publique.

  ![Première configuration IP](./media/create-routebased-vpn-gateway-portal/ip.png "Première configuration IP")
2. Dans la page **Choisir une adresse IP publique**, cliquez sur **+ Créer** pour ouvrir la page **Créer une adresse IP publique**.
3. Configurez les paramètres en appliquant les valeurs suivantes :

  - **Nom** : **VNet1GWPIP**
  - **Référence (SKU)** : **De base**

  ![Créer l’adresse IP publique](./media/create-routebased-vpn-gateway-portal/gw_ip.png "Créer PIP")
4. Cliquez sur **OK** en bas de cette page pour enregistrer vos modifications.

## <a name="creategw"></a>Créer la passerelle VPN

1. Vérifiez les paramètres dans la page **Créer une passerelle de réseau virtuel**. Ajustez les valeurs si nécessaire.

  ![Création de la passerelle VPN](./media/create-routebased-vpn-gateway-portal/create_gw.png "Création de la passerelle VPN")
2. Cliquez sur **Créer** en bas de la page.

Une fois que vous avez cliqué sur **Créer**, les paramètres sont validés et la vignette **Déploiement d’une passerelle de réseau virtuel** s’affiche sur le tableau de bord. La création d’une passerelle VPN peut prendre jusqu’à 45 minutes. Vous devrez peut-être actualiser la page du portail pour que l’état terminé apparaisse.

## <a name="viewgw"></a>Afficher la passerelle VPN

1. Une fois la passerelle créée, accédez à VNet1 dans le portail. La passerelle VPN apparaît dans la page Vue d’ensemble en tant qu’appareil connecté.

  ![Appareils connectés](./media/create-routebased-vpn-gateway-portal/connected_devices.png "Appareils connectés")

2. Dans la liste des appareils, cliquez sur **VNet1GW** pour afficher des informations supplémentaires.

  ![Affichage de la passerelle VPN](./media/create-routebased-vpn-gateway-portal/view_gw2.png "Affichage de la passerelle VPN")

## <a name="next-steps"></a>Étapes suivantes

Une fois la création de la passerelle terminée, vous pouvez établir une connexion entre votre réseau virtuel et un autre réseau virtuel. Vous avez également la possibilité de créer une connexion entre votre réseau virtuel et un emplacement local.

> [!div class="nextstepaction"]
> [Créer une connexion de site à site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> [Créer une connexion point à site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> [Créer une connexion à un autre réseau virtuel](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)