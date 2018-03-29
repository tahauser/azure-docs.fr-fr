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
ms.openlocfilehash: 79747acf92a43316e34c68d012c4643ba83d304c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Pour créer un réseau virtuel dans le modèle de déploiement Resource Manager à l’aide du portail Azure, suivez les étapes ci-dessous. Utilisez les [valeurs d’exemple](#values) si vous utilisez ces étapes sous forme de didacticiel. Si vous n’effectuez pas ces étapes sous forme d’un didacticiel, veillez à remplacer les valeurs par les vôtres. Pour plus d’informations sur l’utilisation des réseaux virtuels, voir [Présentation du réseau virtuel](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Afin que ce réseau virtuel puisse se connecter à un emplacement local, vous devez prendre contact avec votre administrateur de réseau local pour définir une plage d’adresses IP à utiliser spécifiquement pour ce réseau virtuel. Si une plage d’adresses en double existe des deux côtés de la connexion VPN, le trafic ne sera pas correctement acheminé. En outre, si vous souhaitez connecter ce réseau virtuel à un autre réseau virtuel, l’espace d’adressage ne peut pas se chevaucher avec un autre réseau virtuel. Veillez à planifier votre configuration réseau en conséquence.
>
>

1. Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Cliquez sur **Créer une ressource**. Dans le champ **Rechercher dans le marketplace**, tapez « réseau virtuel ». Localisez **Réseau virtuel** dans la liste renvoyée et cliquez sur cet élément pour ouvrir la page **Réseau virtuel**.
3. En bas de la page Réseau virtuel, à partir de la liste **Sélectionner un modèle de déploiement**, choisissez **Gestionnaire des ressources** puis cliquez sur **Créer**. La page Créer un réseau virtuel s’ouvre.

    ![Page Créer un réseau virtuel](./media/vpn-gateway-basic-vnet-s2s-rm-portal-include/vnet.png "Page Créer un réseau virtuel")
4. Sur la page **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Lorsque vous renseignez les champs, le point d’exclamation rouge se transforme en coche verte si les caractères saisis dans le champ sont valides.

  - **Nom** : entrez le nom du réseau virtuel. Dans cet exemple, nous utilisons TestVNet1.
  - **Espace d’adressage** : entrez l’espace d’adressage. Si vous avez plusieurs espaces d’adressage à ajouter, ajoutez le premier espace d’adressage. Vous pourrez ajouter des espaces d’adressage supplémentaires plus tard, après avoir créé le réseau virtuel. Assurez-vous que l’espace d’adressage que vous spécifiez ne chevauche pas l’espace d’adressage de votre emplacement local.
  - **Abonnement** : vérifiez que l’abonnement répertorié est approprié. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.
  - **Groupe de ressources** : sélectionnez un groupe de ressources existant ou créez-en un nouveau en tapant un nom pour ce dernier. Si vous créez un groupe, nommez-le en fonction de vos valeurs de configuration planifiée. Pour plus d’informations sur les groupes de ressources, consultez [Présentation d’Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
  - **Emplacement** : sélectionnez l’emplacement du réseau virtuel. L’emplacement détermine où se trouveront les ressources que vous déployez sur ce réseau virtuel.
  - **Sous-réseau** : ajoutez le nom et la plage d’adresses du premier sous-réseau. Vous pouvez ajouter des sous-réseaux supplémentaires et le sous-réseau de passerelle ultérieurement, après avoir créé ce réseau virtuel. 

5. Sélectionnez **Épingler au tableau de bord** si vous souhaitez être en mesure de trouver votre réseau virtuel facilement sur le tableau de bord, puis cliquez sur **Créer**. Après avoir cliqué sur **Créer**, vous verrez une vignette apparaître sur votre tableau de bord. Celle-ci indique la progression de votre réseau virtuel. La vignette change lorsque le réseau virtuel est créé.