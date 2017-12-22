---
title: "Ajouter une passerelle de réseau virtuel à un réseau virtuel pour ExpressRoute : portail Azure | Microsoft Docs"
description: "Cet article vous explique comment ajouter une passerelle de réseau virtuel à un réseau virtuel Resource Manager déjà créé pour ExpressRoute."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: cherylmc
ms.openlocfilehash: 2bd0cf8be87937044ad515a2c6f253b1711bb2bf
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Configurer une passerelle de réseau virtuel pour ExpressRoute à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vidéo - portail Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Cet article détaille la procédure requise pour ajouter une passerelle de réseau virtuel pour un réseau virtuel existant. Cet article vous montre les étapes nécessaires pour ajouter, redimensionner et supprimer une passerelle de réseau virtuel pour un réseau virtuel existant. Les étapes de cette configuration sont spécifiquement adaptées aux réseaux virtuels qui ont été créés à l’aide du modèle de déploiement Resource Manager qui sera utilisé dans une configuration ExpressRoute. Pour plus d’informations sur les passerelles de réseau virtuel et les paramètres de configuration de passerelle pour ExpressRoute, consultez [À propos des passerelles de réseau virtuel pour ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Avant tout chose

Les étapes de cette tâche utilisent un réseau virtuel basé sur les valeurs figurant dans la liste de référence de configuration suivante. Nous utilisons cette liste dans notre exemple de procédure. Vous pouvez copier la liste et l’utiliser en tant que référence, en remplaçant les valeurs par les vôtres.

**Liste de référence de configuration**

* Nom du réseau virtuel : « TestVNet »
* Espace d’adressage du réseau virtuel : 192.168.0.0/16
* Nom du sous-réseau : « FrontEnd » 
    * Espace d’adressage du sous-réseau : « 192.168.1.0/24 »
* Groupe de ressources : « TestRG »
* Emplacement = « Est des États-Unis »
* Nom de sous-réseau de passerelle : « GatewaySubnet » Vous devez toujours nommer un sous-réseau de passerelle *GatewaySubnet*.
    * Espace d'adressage du sous-réseau de passerelle : « 192.168.200.0/26 »
* Nom de la passerelle : « ERGW »
* Nom d’adresse IP de la passerelle : « MyERGWVIP »
* Type de passerelle : « ExpressRoute ». Ce type est requis pour une configuration ExpressRoute.
* Nom d’adresse IP publique de passerelle = « MyERGWVIP »

Vous pouvez afficher une [vidéo](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) de ces étapes avant de commencer votre configuration.

## <a name="create-the-gateway-subnet"></a>Création d’un sous-réseau de passerelle

1. Dans le [portail](http://portal.azure.com), accédez au réseau virtuel Resource Manager pour lequel vous souhaitez créer une passerelle de réseau virtuel.
2. Dans la section**Paramètres** du panneau de votre réseau virtuel, cliquez sur **Sous-réseaux** pour développer le panneau Sous-réseaux.
3. Dans le panneau **Sous-réseaux**, cliquez sur **+Sous-réseau de passerelle** pour ouvrir le panneau **Ajouter un sous-réseau**. 
   
    ![Ajouter un sous-réseau de passerelle](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Ajouter un sous-réseau de passerelle")


4. Le **Nom** de votre sous-réseau est automatiquement rempli avec la valeur « GatewaySubnet ». Cette valeur est nécessaire pour qu’Azure puisse reconnaître le sous-réseau en tant que sous-réseau de passerelle. Ajustez les valeurs de **plage d’adresses** renseignées automatiquement pour qu’elles correspondent à la configuration requise. Nous vous recommandons de créer un sous-réseau de passerelle avec /27 ou plus (/26, /25, etc.). Puis, cliquez sur **OK** pour enregistrer les valeurs et créer le sous-réseau de passerelle.

    ![Ajout du sous-réseau](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Ajout du sous-réseau")

## <a name="create-the-virtual-network-gateway"></a>Créer la passerelle de réseau virtuel

1. Dans le portail, sur le côté gauche, cliquez sur **+**, puis tapez « Passerelle de réseau virtuel » dans la recherche. Recherchez **passerelle de réseau virtuel** dans la zone de recherche et cliquez sur l’entrée. Dans le panneau **Passerelle de réseau virtuel**, cliquez sur **Créer** en bas du panneau. Cette opération ouvre le panneau **Créer une passerelle de réseau virtuel**.
2. Dans le panneau **Créer une passerelle réseau virtuel**, renseignez les valeurs pour votre passerelle de réseau virtuel.

    ![Champs du panneau Créer une passerelle de réseau virtuel](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Champs du panneau Créer une passerelle de réseau virtuel")
3. **Nom** : nommez votre passerelle. Cela ne revient pas au même que de nommer un sous-réseau de passerelle. Il s’agit du nom de l’objet de passerelle que vous créez.
4. **Type de passerelle** : sélectionnez **ExpressRoute**.
5. **Référence** : sélectionnez la référence de passerelle dans la liste déroulante.
6. **Emplacement** : définissez le champ **Emplacement** pour pointer sur l’emplacement dans lequel le réseau virtuel est situé. Si l’emplacement ne pointe pas vers la région où se trouve votre réseau virtuel, le réseau virtuel n’apparaît pas dans la liste déroulante « Choisir un réseau virtuel ».
7. Choisissez le réseau virtuel auquel vous souhaitez ajouter cette passerelle. Cliquez sur **Réseau virtuel** pour ouvrir le panneau **Choisir un réseau virtuel**. Sélectionnez le réseau virtuel. Si vous ne voyez pas votre réseau virtuel, assurez-vous que le champ **Emplacement** pointe sur la région dans laquelle se trouve votre réseau virtuel.
9. Définissez une adresse IP publique. Cliquez sur **l’adresse IP publique** pour ouvrir le panneau **Choisir une adresse IP publique**. Cliquez sur **Créer** pour ouvrir le panneau **Créer une adresse IP publique**. Donnez à un nom à votre adresse IP publique. Ce panneau crée un objet d’adresse IP publique à laquelle une adresse IP publique sera être affectée dynamiquement. Cliquez sur **OK** pour enregistrer vos modifications à ce panneau.
10. **Abonnement** : vérifiez que l’abonnement approprié est sélectionné.
11. **Groupe de ressources** : ce paramètre est déterminé par le réseau virtuel que vous sélectionnez.
12. Ne changez pas **l’emplacement** après avoir spécifié les paramètres ci-dessus.
13. Vérifiez les paramètres. Si vous souhaitez que votre passerelle apparaisse sur le tableau de bord, vous pouvez sélectionner **Épingler au tableau de bord** en bas du panneau.
14. Cliquez sur **Créer** pour créer la passerelle. Les paramètres sont validés et la passerelle se déploie. La création d’une passerelle de réseau virtuel peut prendre jusqu’à 45 minutes.

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez créé la passerelle de réseau virtuel, vous pouvez lier votre réseau virtuel à un circuit ExpressRoute. Consultez [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).