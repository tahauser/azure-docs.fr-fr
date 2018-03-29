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
ms.openlocfilehash: aa2e11246502175364121ca701113c36906a5e91
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Pour créer un réseau virtuel dans le modèle de déploiement Resource Manager à l’aide du portail Azure, suivez les étapes ci-dessous. Les captures d’écran sont fournies à titre d’exemple. Assurez-vous de remplacer ces valeurs par les vôtres. Pour plus d’informations sur l’utilisation des réseaux virtuels, voir [Présentation du réseau virtuel](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Afin que ce réseau virtuel puisse se connecter à un emplacement local, vous devez prendre contact avec votre administrateur de réseau local pour définir une plage d’adresses IP à utiliser spécifiquement pour ce réseau virtuel. Si une plage d’adresses en double existe des deux côtés de la connexion VPN, le trafic ne sera pas correctement acheminé. En outre, si vous souhaitez connecter ce réseau virtuel à un autre réseau virtuel, l’espace d’adressage ne peut pas se chevaucher avec un autre réseau virtuel. Veillez à planifier votre configuration réseau en conséquence.
>
>

1. Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **+** Dans le champ **Rechercher dans le marketplace**, tapez « réseau virtuel ». Localisez **Réseau virtuel** dans la liste renvoyée et cliquez pour ouvrir la page **Réseau virtuel**.

  ![Page Localiser les ressources du réseau virtuel](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Page Localiser les ressources du réseau virtuel")
3. En bas de la page Réseau virtuel, à partir de la liste **Sélectionner un modèle de déploiement**, choisissez **Gestionnaire des ressources** puis cliquez sur **Créer**.

  ![Sélectionnez le Gestionnaire de ressources](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "Sélectionner le Gestionnaire de ressources")
4. Sur la page **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Lorsque vous renseignez les champs, le point d’exclamation rouge se transforme en coche verte si les caractères saisis dans le champ sont valides. Il se peut que certaines valeurs soient renseignées automatiquement. Dans ce cas, remplacez ces valeurs par les vôtres. La page **Créer un réseau virtuel** ressemble à l’exemple suivant :

  ![Page Créer un réseau virtuel](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Page Créer un réseau virtuel")
5. **Nom** : entrez le nom du réseau virtuel.
6. **Espace d’adressage** : entrez l’espace d’adressage. Si vous avez plusieurs espaces d’adressage à ajouter, ajoutez le premier espace d’adressage. Vous pourrez ajouter des espaces d’adressage supplémentaires plus tard, après avoir créé le réseau virtuel.
7. **Abonnement** : vérifiez que l’abonnement répertorié est approprié. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.
8. **Groupe de ressources** : sélectionnez un groupe de ressources existant ou créez-en un nouveau en tapant un nom pour ce dernier. Si vous créez un groupe, nommez-le en fonction de vos valeurs de configuration planifiée. Pour plus d’informations sur les groupes de ressources, consultez [Présentation d’Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
9. **Emplacement** : sélectionnez l’emplacement du réseau virtuel. L’emplacement détermine où se trouveront les ressources que vous déployez sur ce réseau virtuel.
10. **Sous-réseau** : ajoutez le nom et de la plage d’adresses du sous-réseau. Vous pourrez ajouter des sous-réseaux supplémentaires plus tard, après avoir créé le réseau virtuel.
11. Sélectionnez **Épingler au tableau de bord** si vous souhaitez être en mesure de trouver votre réseau virtuel facilement sur le tableau de bord, puis cliquez sur **Créer**.

  ![Épingler au tableau de bord](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "Épingler au tableau de bord")