---
title: "Ajouter plusieurs connexions site à site de la passerelle VPN à un réseau virtuel : Portail Azure : Resource Manager | Microsoft Docs"
description: "Ajouter des connexions S2S multisites à une passerelle VPN qui dispose déjà d’une connexion"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 5830b3a4bdcd12c01626d9ff3f814d2e7612eaaa
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Ajouter une connexion de site à site à un réseau virtuel avec une connexion de passerelle VPN existante

> [!div class="op_single_selector"]
> * [Portail Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (classique)](vpn-gateway-multi-site.md)
>
> 

Cet article vous aide à utiliser le portail Azure pour ajouter des connexions de site à site (S2S) à une passerelle VPN qui dispose déjà d’une connexion. Pour ce type de connexion, on fait souvent référence à une configuration « multisite ». Vous pouvez ajouter une connexion S2S à un réseau virtuel qui possède déjà une connexion S2S, une connexion de point à site ou une connexion de réseau virtuel à réseau virtuel. L’ajout de connexions est soumis à certaines limitations. Consultez la section [Avant de commencer](#before) dans cet article pour effectuer les vérifications qu’il se doit avant de commencer votre configuration. 

Cet article s’applique aux réseaux virtuels de Resource Manager qui contiennent une passerelle VPN RouteBased. Ces étapes ne s’appliquent pas aux configurations de connexion coexistante ExpressRoute/site à site. Consultez l’article [ExpressRoute/S2S coexisting connections](../expressroute/expressroute-howto-coexist-resource-manager.md) (Connexions coexistantes ExpressRoute/S2S) pour en savoir plus sur les connexions coexistantes.

### <a name="deployment-models-and-methods"></a>Outils et modèles de déploiement
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Nous mettons à jour ce tableau à mesure que de nouveaux articles et des outils supplémentaires sont disponibles pour cette configuration. Quand un article est disponible, le lien vers cet article est ajouté à ce tableau.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Avant de commencer
Vérifiez les points suivants :

* Vous ne créez pas de connexion coexistante ExpressRoute/S2S.
* Vous disposez d’un réseau virtuel qui a été créé selon le modèle de déploiement Resource Manager avec une connexion existante.
* La passerelle de réseau virtuel de votre réseau virtuel est de type RouteBased. Si vous avez une passerelle VPN de type PolicyBased, vous devez supprimer la passerelle de réseau virtuel et créer une passerelle VPN de type RouteBased.
* Les plages d’adresses ne se chevauchent pour aucun des réseaux virtuels auxquels ce réseau virtuel se connecte.
* Vous disposez d’un périphérique VPN compatible et d’une personne capable de le configurer. Consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md). Si vous ne maîtrisez pas la configuration de votre appareil VPN ou les plages d’adresses IP mentionnées dans la configuration de votre réseau local, vous devez contacter une personne qui peut vous fournir ces informations.
* Vous disposez d’une adresse IP publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.

## <a name="part1"></a>Partie 1 - Configuration d’une connexion
1. Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Toutes les ressources**, repérez votre **passerelle de réseau virtuel** dans la liste des ressources, puis cliquez dessus.
3. Dans la page **Passerelle de réseau virtuel**, cliquez sur **Connexions**.
   
    ![Page Connexions](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Page Connexions")<br>
4. Dans la page **Connexions**, cliquez sur **+Ajouter**.
   
    ![Bouton Ajouter une connexion](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Bouton Ajouter une connexion")<br>
5. Dans la page **Ajouter une connexion**, renseignez les champs suivants :
   
   * **Nom** : nom que vous voulez donner au site pour lequel vous créez la connexion.
   * **Type de connexion** : sélectionnez **Site à site (IPSec)**.
     
     ![Page Ajouter une connexion](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Page Ajouter une connexion")<br>

## <a name="part2"></a>Partie 2 - Ajout d’une passerelle de réseau local
1. Cliquez sur **Passerelle de réseau local** ***Choisir une passerelle de réseau local***. La page **Choisir une passerelle de réseau local** s’ouvre.
   
    ![Choisir une passerelle de réseau local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Choisir une passerelle de réseau local")<br>
2. Cliquez sur **Créer un nouveau** pour ouvrir la page **Créer une passerelle de réseau local**.
   
    ![Page Créer une passerelle de réseau local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Créer une passerelle de réseau local")<br>
3. Dans la page **Créer une passerelle de réseau local**, renseignez les champs suivants :
   
   * **Nom** : nom que vous voulez attribuer à la ressource de passerelle de réseau local.
   * **Adresse IP** : adresse IP publique du périphérique VPN sur le site auquel vous voulez vous connecter.
   * **Espace d’adressage** : espace d’adressage qui doit être acheminé vers le nouveau site de réseau local.
4. Cliquez sur **OK** dans la page **Créer une passerelle de réseau local** pour enregistrer les modifications.

## <a name="part3"></a>Partie 3 : ajout de la clé partagée et création de la connexion
1. Dans la page **Ajouter une connexion**, ajoutez la clé partagée à utiliser pour créer la connexion. Vous pouvez soit obtenir la clé partagée à partir de votre périphérique VPN, soit en créer une ici et configurer votre périphérique VPN de sorte qu’il utilise la même clé partagée. Il importe que les clés soient exactement les mêmes.
   
    ![Clé partagée](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Clé partagée")<br>
2. Au bas de la page, cliquez sur **OK** pour créer la connexion.

## <a name="part4"></a>Partie 4 - Vérification de la connexion VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Voir le [parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) sur les machines virtuelles pour plus d’informations.
