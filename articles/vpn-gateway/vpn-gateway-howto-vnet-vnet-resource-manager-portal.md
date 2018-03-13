---
title: "Connecter un réseau virtuel Azure à un autre réseau virtuel : Portail | Microsoft Docs"
description: "Créer une connexion de passerelle VPN entre des réseaux virtuels à l’aide de Resource Manager et du portail Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: a24bd41adbe26b1905f7817f5e5888e492d234f4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>Configurer une connexion de passerelle VPN de réseau virtuel à réseau virtuel à l’aide du portail Azure

Cet article vous explique comment connecter des réseaux virtuels avec une connexion de réseau virtuel à réseau virtuel. Les réseaux virtuels peuvent être situés dans des régions identiques ou différentes et appartenir à des abonnements identiques ou différents. Lors de la connexion de réseaux virtuels provenant de différents abonnements, les abonnements ne sont pas tenus d’être associés au même locataire Active Directory. 

Les étapes mentionnées dans cet article s’appliquent au modèle de déploiement Resource Manager et font appel au portail Azure. Vous pouvez également créer cette configuration à l’aide d’un autre outil ou modèle de déploiement en sélectionnant une option différente dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [interface de ligne de commande Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portail Azure (classique)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Connexions entre différents modèles de déploiement - Portail Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Connexions entre différents modèles de déploiement - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagramme v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

## <a name="about"></a>À propos de la connexion de réseaux virtuels

Il existe plusieurs manières de se connecter à des réseaux virtuels. Les sections ci-dessous décrivent les différentes manières de se connecter à des réseaux virtuels.

### <a name="vnet-to-vnet"></a>Connexion entre deux réseaux virtuels

La configuration d’une connexion de réseau virtuel à réseau virtuel est un bon moyen de se connecter facilement à des réseaux virtuels. La connexion entre deux réseaux virtuels est semblable à la création d’une connexion IPsec de site à site dans un emplacement local. Ces deux types de connectivité font appel à une passerelle VPN pour offrir un tunnel sécurisé utilisant Ipsec/IKE et communiquent de la même façon. La différence entre ces types de connexion se trouve dans la configuration de la passerelle réseau locale. Lorsque vous créez une connexion de réseau virtuel à réseau virtuel, vous ne voyez pas l’espace d’adressage de la passerelle réseau locale. L’espace est créé et rempli automatiquement. Si vous mettez à jour l’espace d’adressage pour un réseau virtuel, l’autre réseau virtuel achemine automatiquement le trafic vers le nouvel espace d’adressage. La création d’une connexion de réseau virtuel à réseau virtuel est généralement plus rapide et plus simple que la création d’une connexion de site à site entre des réseaux virtuels.

### <a name="site-to-site-ipsec"></a>Site à site (IPsec)

Si vous travaillez avec une configuration réseau complexe, vous pouvez connecter vos réseaux virtuels à l’aide des étapes [site à site](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Lorsque vous utilisez les étapes IPsec de site à site, vous créez et configurez manuellement les passerelles réseau locales. La passerelle de réseau local pour chaque réseau virtuel traite l’autre réseau virtuel comme un site local. Ainsi, vous pourrez spécifier un espace d’adressage supplémentaire pour la passerelle réseau locale afin d’acheminer le trafic. Si l’espace d’adressage pour un réseau virtuel est modifié, vous devez mettre à jour la passerelle réseau local correspondante pour le refléter. Elle n’est pas automatiquement mise à jour.

### <a name="vnet-peering"></a>Homologation de réseaux virtuels

Vous envisagerez probablement de connecter vos réseaux virtuels à l’aide de VNet Peering. VNet Peering n’utilise pas une passerelle VPN et possède d’autres contraintes. En outre, la [tarification de VNet Peering](https://azure.microsoft.com/pricing/details/virtual-network) est différente de la [tarification de la passerelle VPN de réseau virtuel à réseau virtuel](https://azure.microsoft.com/pricing/details/vpn-gateway). Pour plus d’informations, consultez l’article [Homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>Pourquoi créer une connexion de réseau virtuel à réseau virtuel ?

Vous pouvez décider de connecter des réseaux virtuels à l’aide d’une connexion de réseau virtuel à réseau virtuel pour les raisons suivantes :

* **Géo-redondance et présence géographique dans plusieurs régions**

  * Vous pouvez configurer la géo-réplication ou la synchronisation avec une connectivité sécurisée sans passer par les points de terminaison accessibles sur Internet.
  * Avec Traffic Manager et l’équilibreur de charge Azure, vous pouvez configurer une charge de travail hautement disponible avec la géo-redondance dans plusieurs régions Azure. Vous pouvez par exemple configurer SQL Always On avec des groupes de disponibilité répartis dans différentes régions Azure.
* **Applications multiniveaux régionales avec une limite d’isolement ou administrative**

  * Dans la même région, vous pouvez configurer des applications multiniveaux avec plusieurs réseaux virtuels interconnectés pour des besoins d’isolement ou d’administration.

Vous pouvez combiner une communication de réseau virtuel à réseau virtuel avec des configurations multisites. Vous établissez ainsi des topologies réseau qui combinent une connectivité entre différents locaux et une connectivité entre différents réseaux virtuels, comme indiqué dans le schéma suivant :

![À propos des connexions](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "À propos des connexions")

Cet article vous aide à connecter des réseaux virtuels à l’aide d’une connexion de réseau virtuel à réseau virtuel. Lorsque vous suivez ces étapes dans le cadre d’un exercice, vous pouvez vous servir de ces exemples de paramètres. Dans l’exemple, les réseaux virtuels se trouvent dans le même abonnement, mais dans des groupes de ressources différents. Si vos réseaux virtuels existent dans des abonnements différents, vous ne pourrez pas créer la connexion dans le portail. Vous pouvez utiliser [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) ou [l’interface de ligne de commande (CLI)](vpn-gateway-howto-vnet-vnet-cli.md). Pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel, consultez le [Forum Aux Questions sur l’interconnexion de réseaux virtuels](#faq) à la fin de cet article.

### <a name="values"></a>Exemples de paramètres

**Valeurs pour TestVNet1 :**

* Nom du réseau virtuel : TestVNet1
* Espace d’adressage : 10.11.0.0/16
* Abonnement : sélectionnez l’abonnement à utiliser
* Groupe de ressources : TestRG1
* Emplacement : Est des États-Unis
* Nom du sous-réseau : FrontEnd
* Plage d’adresses de sous-réseau : 10.11.0.0/24
* Nom du sous-réseau de passerelle : GatewaySubnet (renseigné automatiquement dans le portail)
* Plage d’adresses de sous-réseau de passerelle : 10.11.255.0/27
* Serveur DNS : utilisez l’adresse IP de votre serveur DNS
* Nom de passerelle de réseau virtuel : TestVNet1GW
* Type de passerelle : VPN
* Type de VPN : Route-based
* Référence (SKU): la référence de la passerelle que vous souhaitez utiliser
* Nom de l’adresse IP publique : TestVNet1GWIP
* Nom de la connexion : TestVNet1toTestVNet4
* Clé partagée : vous pouvez créer la clé partagée vous-même. Pour cet exemple, nous allons utiliser abc123. L’important est que lorsque vous créez la connexion entre les réseaux virtuels, la valeur doit correspondre.

**Valeurs pour TestVNet4 :**

* Nom du réseau virtuel : TestVNet4
* Espace d’adressage : 10.41.0.0/16
* Abonnement : sélectionnez l’abonnement à utiliser
* Groupe de ressources : TestRG4
* Emplacement : États-Unis de l’Ouest
* Nom du sous-réseau : FrontEnd
* Plage d’adresses de sous-réseau : 10.41.0.0/24
* Nom du sous-réseau de passerelle : GatewaySubnet (renseigné automatiquement dans le portail)
* Plage d’adresses de GatewaySubnet : 10.41.255.0/27
* Serveur DNS : utilisez l’adresse IP de votre serveur DNS
* Nom de passerelle de réseau virtuel : TestVNet4GW
* Type de passerelle : VPN
* Type de VPN : Route-based
* Référence (SKU): la référence de la passerelle que vous souhaitez utiliser
* Nom de l’adresse IP publique : TestVNet4GWIP
* Nom de la connexion : TestVNet4toTestVNet1
* Clé partagée : vous pouvez créer la clé partagée vous-même. Pour cet exemple, nous allons utiliser abc123. L’important est que lorsque vous créez la connexion entre les réseaux virtuels, la valeur doit correspondre.

## <a name="CreatVNet"></a>1. Créer et configurer TestVNet1
Si vous disposez déjà d’un réseau virtuel, vérifiez que les paramètres sont compatibles avec la conception de votre passerelle VPN, avec une attention particulière pour tous les sous-réseaux qui pourraient chevaucher d’autres réseaux. Si vos sous-réseaux se chevauchent, votre connexion ne fonctionnera pas correctement. Si votre réseau virtuel est correctement configuré, vous pouvez commencer à suivre les étapes de la section [Spécifier un serveur DNS](#dns).

### <a name="to-create-a-virtual-network"></a>Pour créer un réseau virtuel
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. Ajouter des espaces d’adressage supplémentaires et créer des sous-réseaux
Vous pouvez ajouter des espaces d’adressage supplémentaires et créer des sous-réseaux pour votre réseau virtuel une fois qu’il a été créé.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. Créer un sous-réseau de passerelle
Avant de connecter votre réseau virtuel à une passerelle, vous devez créer le sous-réseau de passerelle pour le réseau virtuel auquel vous souhaitez vous connecter. Si possible, il est préférable de créer un sous-réseau de passerelle à l’aide d’un bloc CIDR de /28 ou /27 afin de fournir suffisamment d’adresses IP pour satisfaire les exigences de configuration future supplémentaires.

Si vous créez cette configuration dans le cadre d’un exercice, reportez-vous à ces [Exemples de paramètres](#values) lorsque vous créez votre sous-réseau de passerelle.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Pour créer un sous-réseau de passerelle
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4. Spécifier un serveur DNS (facultatif)
Aucun DNS n’est nécessaire pour les connexions entre des réseaux virtuels. Toutefois, si vous souhaitez résoudre les noms des ressources qui sont déployées sur votre réseau virtuel, vous devez spécifier un serveur DNS. Ce paramètre vous permet de spécifier le serveur DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel. Il n'entraîne pas la création d'un serveur DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Créer une passerelle de réseau virtuel
Dans cette étape, vous créez la passerelle de réseau virtuel de votre réseau virtuel. La création d’une passerelle nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle sélectionnée. Si vous créez cette configuration dans le cadre d’un exercice, vous pouvez vous reporter aux [Exemples de paramètres](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Pour créer une passerelle de réseau virtuel
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. Créer et configurer TestVNet4
Une fois que vous avez configuré TestVNet1, créez TestVNet4 en répétant les étapes précédentes, en remplaçant les valeurs par celles de TestVNet4. Vous n’avez pas besoin d’attendre que la création de la passerelle de réseau virtuel pour TestVNet1 soit terminée pour configurer TestVNet4. Si vous utilisez vos propres valeurs, assurez-vous que les espaces d’adressage ne chevauchent pas les réseaux virtuels auxquels vous souhaitez vous connecter.

## <a name="TestVNet1Connection"></a>7. Configurer la connexion de passerelle TestVNet1
Lorsque les passerelles de réseau virtuel pour TestVNet1 et TestVNet4 sont terminées, vous pouvez créer vos connexions de passerelle de réseau virtuel. Dans cette section, vous allez créer une connexion de VNet1 à VNet4. Ces étapes s’appliquent uniquement aux réseaux virtuels situés dans le même abonnement. Si vos réseaux virtuels figurent dans des abonnements différents, vous devrez utiliser PowerShell pour établir la connexion. Consultez l’article concernant [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). Toutefois, si vos réseaux virtuels se trouvent dans des groupes de ressources différents au sein du même abonnement, vous pouvez les connecter à l’aide du portail.

1. Dans **Toutes les ressources**, accédez à la passerelle de réseau virtuel pour votre réseau virtuel. Par exemple, **TestVNet1GW**. Cliquez sur **TestVNet1GW** pour ouvrir la page de la passerelle de réseau virtuel.

  ![Page Connexions](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Page Connexions")
2. Cliquez sur **+Ajouter** pour ouvrir la page **Ajouter une connexion**.

  ![Ajouter une connexion](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Ajouter une connexion")
3. Dans la page **Ajouter une connexion**, dans le champ Nom, tapez un nom pour votre connexion. Par exemple, **TestVNet1toTestVNet4**.
4. Pour **Type de connexion**, sélectionnez **Réseau virtuel à réseau virtuel** dans la liste déroulante.
5. La valeur du champ **Première passerelle de réseau virtuel** est automatiquement renseignée parce que vous créez cette connexion à partir de la passerelle de réseau virtuel spécifiée.
6. Le champ **Deuxième passerelle de réseau virtuel** correspond à la passerelle de réseau virtuel sur laquelle vous souhaitez créer une connexion. Cliquez sur **Choisir une autre passerelle de réseau virtuel** pour ouvrir la page **Choisir la passerelle de réseau virtuel**.
7. Cette page répertorie les différentes passerelles de réseau virtuel disponibles. Notez que seules les passerelles de réseau virtuel incluses dans votre abonnement sont répertoriées. Si vous souhaitez vous connecter à une passerelle de réseau virtuel qui ne se trouve pas dans votre abonnement, veuillez utiliser l’[article PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).
8. Cliquez sur la passerelle de réseau virtuel à laquelle vous souhaitez vous connecter.
9. Dans le champ **Clé partagée**, tapez une clé partagée pour votre connexion. Vous pouvez générer ou créer cette clé vous-même. Dans une connexion de site à site, la clé que vous utilisez serait exactement la même pour votre appareil local et votre connexion de passerelle de réseau virtuel. Le concept est similaire, sauf qu’au lieu de se connecter à un périphérique VPN, vous vous connectez à une autre passerelle de réseau virtuel.
10. Cliquez sur **OK** en bas de la page pour enregistrer vos modifications.

## <a name="TestVNet4Connection"></a>8. Configurer la connexion de passerelle TestVNet4
Créez ensuite une connexion de TestVNet4 à TestVNet1. Dans le portail, recherchez la passerelle de réseau virtuel associée à TestVNet4. Suivez les étapes de la section précédente, en remplaçant les valeurs pour créer une connexion de TestVNet4 à TestVNet1. Vérifiez que vous utilisez la même clé partagée.

## <a name="VerifyConnection"></a>9. Vérifiez vos connexions

Recherchez la passerelle de réseau virtuel dans le portail. Dans la page de la passerelle de réseau virtuel, cliquez sur **Connexions** pour afficher la page des connexions de la passerelle de réseau virtuel. Une fois la connexion créée, l’état passe à **Opération réussie**, puis à **Connecté**. Vous pouvez double-cliquer sur une connexion pour ouvrir la page **Éléments principaux**, qui contient des informations supplémentaires.

![Réussite](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Réussite")

Quand les données commencent à circuler, des valeurs apparaissent pour Données entrantes et Données sortantes.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="to-add-additional-connections"></a>Pour ajouter des connexions supplémentaires

Si vous souhaitez ajouter des connexions supplémentaires, accédez à la passerelle de réseau virtuel à partir de laquelle créer la connexion, puis cliquez sur **Connexions**. Vous pouvez créer une autre connexion de réseau virtuel à réseau virtuel, ou créer une connexion de site à site IPsec vers un emplacement local. Veillez à ajuster le **type de connexion** en fonction du type de connexion que vous souhaitez créer. Avant de créer des connexions supplémentaires, vérifiez que l’espace d’adressage de votre réseau virtuel ne chevauche aucun des espaces d’adressage auxquels vous souhaitez vous connecter. Pour connaître la procédure de création d’une connexion site à site, consultez [Créer une connexion de site à site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq"></a>Forum Aux Questions sur l’interconnexion de réseaux virtuels
Consultez les détails du Forum Aux Questions pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment limiter le trafic réseau aux ressources d’un réseau virtuel, consultez [Sécurité du réseau](../virtual-network/security-overview.md).

Pour plus d’informations sur la façon dont Azure achemine le trafic entre les ressources Azure, locales et Internet, consultez [Routage du trafic de réseau virtuel](../virtual-network/virtual-networks-udr-overview.md).