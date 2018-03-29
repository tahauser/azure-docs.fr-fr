---
title: Créer, modifier ou supprimer une homologation de réseau virtuel Azure | Microsoft Docs
description: Découvrez comment créer, modifier ou supprimer une homologation de réseau virtuel.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial;anavin
ms.openlocfilehash: f42a23a8d4e6a90f104c97d4a1cbdef5343e585e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Créer, modifier ou supprimer une homologation de réseau virtuel

Découvrez comment créer, modifier ou supprimer une homologation de réseau virtuel. L’appairage de réseaux virtuels vous permet de connecter des réseaux virtuels via le réseau principal Azure. Une fois appairés, les réseaux virtuels continuent d’être gérés comme des ressources distinctes. Si vous n’êtes pas familiarisé avec l’homologation de réseau virtuel, avant d’accomplir les tâches décrites dans cet article, nous vous recommandons de lire [Homologation de réseaux virtuels](virtual-network-peering-overview.md), et de suivre le didacticiel [Créer une homologation de réseau virtuel](tutorial-connect-virtual-networks-portal.md).

L’appairage de réseaux virtuels au sein d’une même région est généralement possible. L’appairage de réseaux virtuels dans des régions différentes est actuellement en version préliminaire. Consultez les [mises à jour du réseau virtuel](https://azure.microsoft.com/updates/?product=virtual-network) pour les régions disponibles. Vous pouvez [inscrire votre abonnement pour accéder à la préversion](tutorial-connect-virtual-networks-powershell.md#register).

> [!WARNING]
> Les appairages de réseaux virtuels créés dans ce scénario peuvent ne pas avoir le même niveau de disponibilité et de fiabilité qu’avec les scénarios utilisant la version publique. Les appairages de réseaux virtuels peuvent avoir des fonctionnalités limitées et peuvent ne pas être disponibles dans toutes les régions Azure. Pour les notifications les plus récentes sur la disponibilité et l’état de cette fonctionnalité, consultez la page relative aux [mises à jour du réseau virtuel Azure](https://azure.microsoft.com/updates/?product=virtual-network).
>

## <a name="before-you-begin"></a>Avant de commencer

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez le portail, ouvrez https://portal.azure.com, puis connectez-vous avec votre compte Azure.
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, exécutez-les dans l’[Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Ce didacticiel requiert le module Azure PowerShell version 5.2.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Ce didacticiel requiert Azure CLI version 2.0.26 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI localement, vous devez également exécuter `az login` pour créer une connexion avec Azure.

## <a name="create-a-peering"></a>Créer une homologation

>[!NOTE]
>Avant de créer une homologation, familiarisez-vous avec les [exigences et contraintes](#requirements-and-constraints) et les [autorisations requises](#permissions).
>

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la. Ne sélectionnez pas **Réseaux virtuels (classiques)** si cette option apparaît dans la liste, car vous ne pouvez pas créer une homologation à partir d’un réseau virtuel déployé via le modèle de déploiement classique.
2. Sélectionnez dans la liste le réseau virtuel pour lequel vous souhaitez créer une homologation.
3. Dans la liste des réseaux virtuels, sélectionnez le réseau virtuel pour lequel vous souhaitez créer une homologation.
4. Sous **PARAMÈTRES**, sélectionnez **Homologations**.
5. Sélectionnez **Ajouter**. 
6. <a name="add-peering"></a> :
    - **Nom :** le nom doit être unique au sein du réseau virtuel.
    - **Modèle de déploiement de réseau virtuel :** sélectionnez le modèle de déploiement via lequel le réseau virtuel avec lequel vous souhaitez opérer l’homologation a été déployé.
    - **Je connais mon ID de ressource :** si vous avez accès en lecture au réseau virtuel avec lequel vous souhaitez opérer l’homologation, laissez cette case à cocher désactivée. Si vous n’avez pas accès en lecture au réseau virtuel ou à l’abonnement avec lequel vous souhaitez opérer l’homologation, activez cette case à cocher. Dans la zone **ID de ressource** qui s’affiche lorsque vous cochez la case, entrez l’ID de ressource complet du réseau virtuel avec lequel vous souhaitez opérer l’homologation. L’ID de ressource que vous entrez doit être celui d’un réseau virtuel existant dans la même [région](https://azure.microsoft.com/regions) Azure que ce réseau virtuel. Si vous souhaitez sélectionner un réseau virtuel situé dans une autre région, [inscrivez votre abonnement à la préversion](tutorial-connect-virtual-networks-portal.md). L’ID de ressource complet ressemble à /subscriptions/<Id>/resourceGroups/<nom-du-groupe-de-ressources>/providers/Microsoft.Network/virtualNetworks/<nom-du-réseau-virtuel>. Vous pouvez obtenir l’ID de ressource d’un réseau virtuel en affichant les propriétés de celui-ci. Pour savoir comment afficher les propriétés d’un réseau virtuel, consultez [Gérer des réseaux virtuels](manage-virtual-network.md#view-virtual-networks-and-settings).
    - **Abonnement :** sélectionnez l’[abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) du réseau virtuel avec lequel vous souhaitez opérer l’homologation. Un ou plusieurs abonnements sont répertoriés, selon le nombre d’abonnements auxquels votre compte a accès en lecture. Si vous avez activé la case à cocher **ID de ressource**, ce paramètre n’est pas disponible.
    - **Réseau virtuel :** sélectionnez le réseau virtuel avec lequel vous voulez opérer l’homologation. Vous pouvez sélectionner un réseau virtuel créé à l’aide d’un modèle de déploiement d’Azure. Si vous souhaitez sélectionner un réseau virtuel situé dans une autre région, [inscrivez votre abonnement à la préversion](tutorial-connect-virtual-networks-portal.md). Pour que le réseau virtuel apparaisse dans la liste, vous devez disposer de l’accès en lecture à celui-ci. Si un réseau virtuel est répertorié, mais grisé, ce peut être parce que l’espace d’adressage du réseau virtuel chevauche l’espace d’adressage de ce réseau. Si les espaces d’adressage de réseaux virtuels se chevauchent, il n’est pas possible d’homologuer ces réseaux. Si vous avez activé la case à cocher **ID de ressource**, ce paramètre n’est pas disponible.
    - **Autoriser l’accès au réseau virtuel :** sélectionnez **Activé** (par défaut) si vous souhaitez activer la communication entre les deux réseaux virtuels. L’activation de la communication entre les réseaux virtuels permet aux ressources connectées à ceux-ci de communiquer entre elles avec les mêmes bande passante et latence que si elles étaient connectées au même réseau virtuel. Toute communication entre les ressources des deux réseaux virtuels passe par le réseau privé Azure. La balise par défaut **VirtualNetwork** pour les groupes de sécurité réseau englobe le réseau virtuel et le réseau virtuel homologué. Pour en savoir plus sur les groupes de sécurité réseau, lisez l’article présentant les [Groupes de sécurité réseau](virtual-networks-nsg.md#default-tags).  Si vous ne souhaitez pas que le trafic soit dirigé vers le réseau virtuel homologué, sélectionnez **Désactivé**. Vous pouvez sélectionner **Désactivé** si vous avez homologué un réseau virtuel avec un autre réseau virtuel, mais souhaitez occasionnellement désactiver le flux de trafic entre les deux réseaux virtuels. Il se peut que vous trouviez plus pratique d’activer ou de désactiver les homologations que de les supprimer et recréer. Lorsque ce paramètre est désactivé, le trafic ne passe plus entre les réseaux virtuels homologues.
    - **Autoriser le trafic transféré :** Cochez cette case pour autoriser l’acheminement du trafic *transféré* par une appliance virtuelle réseau dans un réseau virtuel (ne provenant pas du réseau virtuel) vers ce réseau virtuel par le biais d’un appairage. Par exemple, considérez trois réseaux virtuels nommés Spoke1, Spoke2 et Hub. Une homologation existe entre chaque réseau virtuel spoke et le réseau virtuel Hub, mais les homologations n’existent pas entre les réseaux virtuels spoke. Une appliance virtuelle réseau est déployée dans le réseau virtuel Hub et des itinéraires définis par l’utilisateur sont appliqués à chaque réseau virtuel spoke acheminant le trafic entre les sous-réseaux via l’appliance virtuelle réseau. Si cette case à cocher n’est pas cochée pour l’homologation entre chaque réseau virtuel spoke et le réseau virtuel Hub, le trafic ne passe pas entre les réseaux virtuels spoke, car le Hub transfère le trafic entre les réseaux virtuels. Si l’activation de cette fonctionnalité autorise le transfert du trafic via l’homologation, elle n’a pas pour effet de créer des itinéraires définis par l’utilisateur ou des appliances virtuelles. Les itinéraires définis par l’utilisateur et les appliances virtuelles sont créés séparément. Pour en savoir plus, voir [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md#user-defined). Il est inutile de cocher ce paramètre si le trafic est transféré entre les réseaux virtuels par le biais d’une passerelle VPN Azure.
    -  **:** cochez cette case si vous avez une passerelle de réseau virtuel attachée à ce réseau virtuel, et souhaitez autoriser le trafic en provenance du réseau virtuel homologué via la passerelle. Par exemple, ce réseau virtuel peut être attaché à un réseau local via une passerelle de réseau virtuel. La passerelle peut être une passerelle ExpressRoute ou VPN. L’activation de cette case à cocher a pour effet d’autoriser que le trafic en provenance du réseau virtuel homologué passe par la passerelle attachée à ce réseau virtuel vers le réseau local. Si vous cochez cette case, le réseau virtuel homologué ne peut pas avoir de passerelle configurée. La case à cocher **Utiliser des passerelles distantes** doit être activée pour le réseau virtuel homologué lors de la configuration de l’homologation de l’autre réseau virtuel à ce réseau virtuel. Si vous laissez cette case à cocher désactivée (par défaut), le trafic en provenance du réseau virtuel homologué continue d’être acheminé vers ce réseau virtuel, mais ne peut pas passer par une passerelle de réseau virtuel attachée à ce réseau virtuel. 
    
    En plus de transférer le trafic vers un réseau local, une passerelle VPN peut transférer le trafic réseau entre les réseaux virtuels qui sont homologués avec le réseau virtuel dans lequel la passerelle se trouve, sans que les réseaux virtuels n’aient besoin d’être homologués entre eux. Cela est utile lorsque vous souhaitez utiliser une passerelle VPN dans un réseau virtuel Hub (consultez l’exemple de hub-and-spoke décrit pour **Autoriser le trafic transféré**) pour acheminer le trafic entre les réseaux virtuels spoke qui ne sont pas homologués entre eux. Pour en savoir plus, voir [Passerelles de réseau virtuel](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti). Ce scénario nécessite l’implémentation d’itinéraires définis par l’utilisateur qui spécifient la passerelle de réseau virtuel en tant que type de tronçon suivant. Pour en savoir plus, voir [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md#user-defined). Vous ne pouvez spécifier une passerelle VPN en tant que type de tronçon suivant uniquement dans un itinéraire défini par l’utilisateur, vous ne pouvez pas spécifier une passerelle ExpressRoute en tant que type de tronçon suivant dans un itinéraire défini par l’utilisateur.

        You cannot enable this option if you're peering a virtual network (Resource Manager) with a virtual network (classic). Though the traffic flows between the two virtual networks, the virtual network (classic) traffic cannot flow through a network gateway attached to the virtual network (Resource Manager). 

    -  **:** cochez cette case pour autoriser que le trafic en provenance de ce réseau virtuel passe par une passerelle de réseau virtuel attachée au réseau virtuel avec lequel vous opérez l’homologation. Par exemple, le réseau virtuel avec lequel vous opérez l’homologation a une passerelle VPN attachée qui permet la communication avec un réseau local.  L’activation de cette case à cocher a pour effet d’autoriser que le trafic en provenance de ce réseau virtuel passe par la passerelle VPN attachée au réseau virtuel homologué. Si vous cochez cette case, le réseau virtuel homologué doit avoir une passerelle de réseau virtuel attachée, et la case à cocher **Autoriser le transit par passerelle** doit être activée pour ce réseau. Si vous laissez cette case à cocher désactivée (par défaut), le trafic en provenance du réseau virtuel homologué peut toujours être acheminé vers ce réseau virtuel, mais ne peut pas passer par une passerelle de réseau virtuel attachée à ce réseau virtuel. 
Une seule homologation pour ce réseau virtuel peut avoir ce paramètre activé.
Vous ne pouvez pas utiliser ce paramètre si vous disposez déjà d’une passerelle configurée dans votre réseau virtuel.
        Vous ne pouvez pas activer cette option si vous homologuez un réseau virtuel (Gestionnaire des ressources) avec un réseau virtuel (classique). Bien que le trafic circule entre les deux réseaux virtuels, le trafic de réseau virtuel (Gestionnaire des ressources) ne peut pas passer par une passerelle réseau attachée au réseau virtuel (classique).

7. Cliquez sur le bouton **OK** pour ajouter le sous-réseau au réseau virtuel que vous avez sélectionné.

### <a name="commands"></a>Commandes

- Azure CLI : [az network vnet peering create](/cli/azure/network/vnet/peering#create)
- PowerShell : [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering)

### <a name="scenarios"></a>Scénarios

Une homologation est générée entre des réseaux virtuels créés via des modèles de déploiement identiques ou différents qui existent dans des abonnements identiques ou différents. Suivez un didacticiel détaillé pour l’un des scénarios suivants :
 
|Modèle de déploiement Azure  | Abonnement  |
|---------|---------|
|Les deux modèles Resource Manager |[Identique](tutorial-connect-virtual-networks-portal.md)|
| |[Différent](create-peering-different-subscriptions.md)|
|Un modèle Resource Manager, un modèle classique     |[Identique](create-peering-different-deployment-models.md)|
| |[Différent](create-peering-different-deployment-models-subscriptions.md)|

## <a name="view-or-change-peering-settings"></a>Afficher ou modifier les paramètres d’homologation

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la. Ne sélectionnez pas **Réseaux virtuels (classiques)** si cette option apparaît dans la liste, car vous ne pouvez pas créer une homologation à partir d’un réseau virtuel déployé via le modèle de déploiement classique.
2. Sélectionnez dans la liste le réseau virtuel dont vous souhaitez modifier les paramètres d’homologation.
3. Dans la liste des réseaux virtuels, sélectionnez le réseau dont vous souhaitez modifier les paramètres d’homologation.
4. Sous **PARAMÈTRES**, sélectionnez **Homologations**.
5. Cliquez sur l’homologation que vous souhaitez afficher ou dont vous voulez modifier les paramètres.
6. Modifiez le paramètre approprié. Lisez les options de chaque paramètres de l’[étape 6](#add-peering) de Créer une homologation. 

    >[!NOTE]
    >Avant de créer une homologation, familiarisez-vous avec les [exigences et contraintes](#requirements-and-constraints) et les [autorisations requises](#permissions).
    >

7. Cliquez sur **Enregistrer**.

**Commandes**

Azure CLI : [az network vnet peering list](/cli/azure/network/vnet/peering#az_network_vnet_peering_list) pour afficher la liste des homologations d’un réseau virtuel, [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) pour afficher les paramètres d’une homologation spécifique et [az network vnet peering update](/cli/azure/network/vnet/peering#az_network_vnet_peering_update) pour modifier les paramètres d’homologation.
- PowerShell : [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) pour afficher les paramètres d’homologation et [Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering) pour modifier les paramètres.

## <a name="delete-a-peering"></a>Supprimer une homologation

En cas de suppression d’une homologation, le trafic en provenance d’un réseau virtuel n’est plus acheminé vers le réseau virtuel homologué. Lorsque des réseaux virtuels déployés via le Gestionnaire de ressources sont homologués, chaque réseau virtuel a une homologation à l’autre réseau virtuel. Si la suppression de l’homologation d’un réseau virtuel désactive la communication entre les réseaux virtuels, elle ne supprime pas l’homologation de l’autre réseau virtuel. L’état d’homologation pour l’homologation existant dans l’autre réseau virtuel est **Déconnectée**. Vous ne pouvez pas recréer l’homologation tant que vous n’avez pas recréé l’homologation dans le premier réseau virtuel et que l’état d’homologation des deux réseaux virtuels n’est pas *Connectée*. 

Si vous souhaitez que les réseaux virtuels communiquent occasionnellement, au lieu de supprimer une homologation, vous pouvez définir le paramètre **Autoriser l’accès au réseau virtuel** sur **Désactivé**. Pour savoir comment procéder, voir l’étape 6 de la section [Créer une homologation](#create-peering) dans cet article. Il se peut que vous trouviez plus facile de désactiver et activer l’accès réseau que de supprimer et recréer des homologations.

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la. Ne sélectionnez pas **Réseaux virtuels (classiques)** si cette option apparaît dans la liste, car vous ne pouvez pas créer une homologation à partir d’un réseau virtuel déployé via le modèle de déploiement classique.
2. Sélectionnez dans la liste le réseau virtuel pour lequel vous souhaitez supprimer une homologation.
3. Dans la liste des réseaux virtuels, sélectionnez le réseau virtuel pour lequel vous souhaitez supprimer une homologation.
4. Sous **PARAMÈTRES**, sélectionnez **Homologations**.
5. Du côté droit de l’homologation que vous souhaitez supprimer, sélectionnez **...**, **Supprimer**, puis **Oui** pour supprimer l’homologation du premier réseau virtuel.
6. Suivez les étapes précédentes pour supprimer l’homologation de l’autre réseau virtuel dans l’homologation.

**Commandes**

- Azure CLI : [az network vnet peering delete](/cli/azure/network/vnet/peering#az_network_vnet_peering_delete)
- PowerShell : [Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Exigences et contraintes 

- Les réseaux virtuels que vous homologuez doivent avoir des espaces d’adressage IP qui ne se chevauchent pas.
- Il n’est pas possible d’ajouter ou de supprimer des plages d’adresses dans l’espace d’adressage d’un réseau virtuel après que celui-ci a été homologué avec un autre réseau virtuel. Pour ajouter ou supprimer des plages d’adresses, supprimez l’homologation, ajoutez ou supprimez les plages d’adresses, puis recréez l’homologation. Pour ajouter ou supprimer des plages d’adresses dans des réseaux virtuels, voir [Gérer les réseaux virtuels](manage-virtual-network.md).
- Vous pouvez homologuer deux réseaux virtuels déployés via le Gestionnaire de ressources, ou homologuer un réseau virtuel déployé via le Gestionnaire de ressources avec un réseau virtuel déployé via le modèle de déploiement classique. Vous ne pouvez pas homologuer deux réseaux virtuels créés via le modèle de déploiement classique. Si vous n’êtes pas familiarisé avec les modèles de déploiement Azure, lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) pour connecter deux réseaux virtuels créés via le modèle de déploiement classique.
- Lors de l’homologation de deux réseaux virtuels créés via le Gestionnaire de ressources, une homologation doit être configurée pour chaque réseau virtuel dans l’homologation. 
    - *Lancé* : lorsque vous créez l’appairage au deuxième réseau virtuel à partir du premier réseau virtuel, l’état d’appairage est *Lancé*. 
    - *Connecté* : lorsque vous créez l’appairage à partir du deuxième réseau virtuel au premier réseau virtuel, l’état d’appairage est *Connecté*. Si vous affichez l’état d’homologation pour le premier réseau virtuel, vous voyez que son état est passé de *Initiée* à *Connectée*. L’homologation n’est pas correctement établie tant que l’état d’homologation pour les deux homologations de réseau virtuel est *Connectée*.
- Lors de l’homologation d’un réseau virtuel créé via le Gestionnaire de ressources avec un réseau virtuel créé via le modèle de déploiement classique, vous configurez une homologation uniquement pour le réseau virtuel est déployé via le Gestionnaire de ressources. Vous ne pouvez pas configurer d’homologation pour un réseau virtuel (classique), ou entre deux réseaux virtuels déployés via le modèle de déploiement classique. Lorsque vous créez l’homologation à partir du réseau virtuel (Gestionnaire des ressources) au réseau virtuel (classique), l’état d’homologation est *Mise à jour*, puis passe rapidement à *Connectée*.
- Une homologation est établie entre deux réseaux virtuels. Les homologations ne sont pas transitives. Imaginons que vous créez des homologations entre :
    - VirtualNetwork1 et VirtualNetwork2
    - VirtualNetwork2 et VirtualNetwork3

  Il n’existe aucune homologation entre VirtualNetwork1 et VirtualNetwork3 via VirtualNetwork2. Si vous souhaitez créer une homologation de réseaux virtuels entre VirtualNetwork1 et VirtualNetwork3, vous devez créer une homologation entre VirtualNetwork1 et VirtualNetwork3.
- Vous ne pouvez pas résoudre des noms dans des réseaux virtuels homologués en utilisant une résolution de noms Azure par défaut. Pour résoudre des noms dans d’autres réseaux virtuels, vous devez utiliser un serveur DNS personnalisé. Pour savoir comment configurer votre propre serveur DNS, lisez l’article [Résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Les ressources des deux réseaux virtuels dans l’homologation peuvent communiquer entre elles avec les mêmes bande passante et latence que si elles étaient dans le même réseau virtuel. Toutefois, chaque taille de machine virtuelle a sa propre bande passante réseau maximale. Pour en savoir plus sur la bande passante réseau maximale pour différentes tailles de machine virtuelle, lisez les articles [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sur les tailles de machine virtuelle.
- Vous pouvez homologuer des réseaux virtuels déployés via le Gestionnaire de ressources, qu’ils figurent dans un même abonnement ou dans des abonnements différents.
- Vous pouvez homologuer des réseaux virtuels déployés via des modèles de déploiement différents, et figurant ou non dans un même abonnement. 
- Les abonnements dans lesquels figurent les deux réseaux virtuels doivent être associés au même client Azure Active Directory. Si vous n’avez pas encore de client Active Directory, vous pouvez rapidement en [créer un](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch). Vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) pour vous connecter à deux réseaux virtuels d’abonnements différents associés à différents clients Active Directory.
- Un réseau virtuel peut être homologué ainsi qu’être connecté à un autre réseau virtuel avec une passerelle de réseau virtuel Azure. Lorsque des réseaux virtuels sont connectés via une homologation et une passerelle, le trafic entre les réseaux virtuels passe par l’homologation plutôt que par la passerelle.
- Un coût nominal s’applique pour le trafic entrant et sortant qui utilise une homologation de réseau virtuel. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Autorisations

Les comptes que vous utilisez pour créer une homologation de réseaux virtuels doivent avoir les autorisations ou le rôle nécessaires. Par exemple, si vous homologuez deux réseaux virtuels nommés myVnetA et myVnetB, votre compte doit avoir le rôle ou les autorisations minimaux suivants pour chaque réseau virtuel :
    
|Réseau virtuel|Modèle de déploiement|Rôle|Autorisations|
|---|---|---|---|
|myVnetA|Gestionnaire de ressources|[Collaborateur de réseau](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classique|[Collaborateur de réseau classique](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|myVnetB|Gestionnaire de ressources|[Collaborateur de réseau](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classique|[Collaborateur de réseau classique](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Apprenez-en davantage sur les [rôles intégrés](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) et l’affectation d’autorisations spécifiques aux [rôles personnalisés](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Gestionnaire des ressources uniquement).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer une [topologie de réseau Hub and Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering).
