---
title: "Ajouter, modifier ou supprimer un sous-réseau de réseau virtuel Azure | Microsoft Docs"
description: "Découvrez comment ajouter, modifier ou supprimer un sous-réseau de réseau virtuel dans Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.openlocfilehash: 413ec2ef4fcc7752b95984a209818eeba535746e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Ajouter, modifier ou supprimer un sous-réseau de réseau virtuel

Découvrez comment ajouter, modifier ou supprimer un sous-réseau de réseau virtuel. 

Si vous n’êtes pas familiarisé avec les réseaux virtuels, nous vous recommandons de lire [Vue d’ensemble des réseaux virtuels Azure](virtual-networks-overview.md) et [Créer, modifier ou supprimer un réseau virtuel](virtual-network-manage-network.md) avant d’ajouter, modifier ou supprimer un sous-réseau. Toutes les ressources Azure déployées dans un réseau virtuel sont déployées dans un sous-réseau au sein d’un réseau virtuel. Plusieurs sous-réseaux sont généralement créés au sein d’un réseau virtuel pour :
- **Filtrer le trafic entre sous-réseaux**. Vous pouvez appliquer des groupes de sécurité réseau à des sous-réseaux pour filtrer le trafic réseau entrant et sortant de toutes les ressources (telles que des machines virtuelles) dans le réseau virtuel. Pour en savoir plus sur la création d’un groupe de sécurité réseau, consultez [Créer des groupes de sécurité réseau](virtual-networks-create-nsg-arm-pportal.md).
- **Contrôler le routage entre les sous-réseaux**. Azure crée des itinéraires par défaut de façon à ce que le trafic soit automatiquement routé entre les sous-réseaux. Vous pouvez substituer les itinéraires Azure par défaut en créant des itinéraires définis par l’utilisateur. Pour en savoir plus sur les itinéraires définis par l’utilisateur, consultez [Créer des itinéraires définis par l’utilisateur](virtual-network-create-udr-arm-ps.md). 

Cet article explique comment ajouter, modifier et supprimer un sous-réseau de réseau virtuel créé à l’aide du modèle de déploiement Azure Resource Manager.
 
## <a name="before"></a>Avant de commencer

Avant d’aborder les tâches décrites dans cet article, préparez-vous comme suit :

- Si vous ignorez tout de l’utilisation de réseaux virtuels, nous vous recommandons de consulter l’exercice proposé dans [Créer votre premier réseau virtuel Azure](quick-create-portal.md). Cet exercice peut vous aider à vous familiariser avec les réseaux virtuels.
- Pour en savoir plus sur les limites des réseaux virtuels, consultez [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Connectez-vous au portail Azure, à l’outil en ligne de commande Azure (Azure CLI) ou à Azure PowerShell en utilisant votre compte Azure. Si vous n’avez pas encore de compte Azure, inscrivez-vous pour bénéficier d’un [compte d’essai gratuit](https://azure.microsoft.com/free).
- Si vous prévoyez d’utiliser des commandes PowerShell pour accomplir les tâches décrites dans cet article, vous devez d’abord [installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que les applets de commande Azure PowerShell de la version la plus récente sont installées. Pour obtenir de l’aide concernant les commandes PowerShell utilisées dans les exemples, entrez `get-help <command> -full`.
- Si vous prévoyez d’utiliser des commandes Azure CLI pour accomplir les tâches décrites dans cet article, vous devez soit :
    - [Installer et configurer Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Vérifiez que la version la plus récente d’Azure CLI est installée.
    - Utiliser Azure Cloud Shell. Au lieu d’installer l’interface CLI et ses dépendances, vous pouvez utiliser Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Pour utiliser Cloud Shell, cliquez sur l’icône Cloud Shell (**> _**) en haut du portail Azure. 

  Pour obtenir de l’aide concernant les commandes d’Azure CLI, entrez `az <command> --help`.

## <a name="create-subnet"></a>Ajouter un sous-réseau

Pour ajouter un sous-réseau :

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) en utilisant un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Pour en savoir plus sur l’attribution de rôles et d’autorisations à des comptes, consultez [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Dans la zone de recherche du portail, entrez **réseaux virtuels**. Dans les résultats de recherche, cliquez sur **Réseaux virtuels**.
3. Dans le panneau **Réseaux virtuels**, cliquez sur le réseau virtuel auquel vous souhaitez ajouter un sous-réseau.
4. Dans le panneau Réseau virtuel, cliquez sur **Sous-réseaux**.
5. Cliquez sur **+Sous-réseau**.
6. Dans le panneau **Ajouter un sous-réseau**, entrez des valeurs pour les paramètres suivants :
    - **Nom** : le nom doit être unique au sein du réseau virtuel.
    - **Plage d’adresses** : la plage doit être unique dans l’espace d’adressage du réseau virtuel. La plage ne peut pas chevaucher d’autres plages d’adresses de sous-réseau au sein du réseau virtuel. L’espace d’adressage doit être spécifié en utilisant la notation de routage CIDR (Classless InterDomain Routing). Par exemple, dans un réseau virtuel avec l’espace d’adressage 10.0.0.0/16, vous pouvez définir l’espace d’adressage de sous-réseau 10.0.0.0/24. La plus petite plage que vous puissiez spécifier est /29. Celle-ci fournit huit adresses IP pour le sous-réseau. Azure réserve la première et la dernière adresses dans chaque sous-réseau pour la conformité du protocole. Trois adresses supplémentaires sont réservées à l’usage du service Azure. Par conséquent, définir un sous-réseau avec une plage d’adresses /29 produit trois adresses IP utilisables dans le sous-réseau. Si vous envisagez de connecter un réseau virtuel à une passerelle VPN, vous devez créer un sous-réseau de passerelle. Pour en savoir plus, voir [Sous-réseau de passerelle](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Vous pouvez modifier la plage d’adresses après l’ajout du sous-réseau sous certaines conditions. Pour savoir comment changer une plage d’adresses de sous-réseau, consultez [Changer les paramètres de sous-réseau](#change-subnet) dans cet article.
    - **Groupe de sécurité réseau** : vous pouvez éventuellement associer un groupe de sécurité réseau existant au sous-réseau pour contrôler le filtrage du trafic réseau entrant et sortant du sous-réseau. Le groupe de sécurité réseau doit exister dans le même abonnement et au même emplacement que le réseau virtuel. Il doit également être créé à l’aide du modèle de déploiement Resource Manager. Pour en savoir plus sur la création d’un groupe de sécurité réseau, consultez [Groupes de sécurité réseau](virtual-networks-create-nsg-arm-pportal.md).
    - **Table de routage** : vous pouvez éventuellement associer une table de routage existante au sous-réseau pour contrôler le routage du trafic réseau vers d’autres réseaux. La table de routage doit exister dans le même abonnement et au même emplacement que le réseau virtuel. Elle doit également être créée à l’aide du modèle de déploiement Resource Manager. Pour en savoir plus sur la création de tables de routage, consultez [Itinéraires définis par l’utilisateur](virtual-network-create-udr-arm-ps.md).
    - **Utilisateurs** : vous pouvez contrôler l’accès au sous-réseau en utilisant des rôles intégrés ou vos propres rôles personnalisés. Pour en savoir plus sur l’attribution de rôles et d’utilisateurs pour l’accès au sous-réseau, consultez [Utiliser l’attribution de rôles pour gérer l’accès à vos ressources Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
7. Pour ajouter le sous-réseau au réseau virtuel que vous avez sélectionné, cliquez sur **OK**.

**Commandes**

|Outil|Commande|
|---|---|
|Azure CLI|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json), [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>Changer les paramètres de sous-réseau

Vous pouvez changer des groupes de sécurité réseau, des tables de routage et l’accès utilisateur à un sous-réseau en gérant les ressources dans un sous-réseau. Pour en savoir plus sur ces paramètres, dans [Ajouter un sous-réseau](#create-subnet), consultez l’étape 6. Si vous voulez changer l’espace d’adressage d’un sous-réseau, vous devez d’abord supprimer toutes les ressources dans le sous-réseau. La procédure à suivre pour supprimer une ressource varient en fonction de celle-ci. Pour savoir comment supprimer des ressources dans des sous-réseaux, lisez la documentation relative à chaque type de ressource que vous souhaitez supprimer. Pour changer la plage d’adresses d’un sous-réseau :

1. Connectez-vous au [portail](https://portal.azure.com) en utilisant un compte disposant (au minimum) des autorisations affectées au rôle Contributeur de réseaux dans votre abonnement. Pour en savoir plus sur l’attribution de rôles et d’autorisations à des comptes, consultez [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Dans la zone de recherche du portail, entrez **réseaux virtuels**. Dans les résultats de recherche, cliquez sur **Réseaux virtuels**.
3. Dans le panneau **Réseaux virtuels**, cliquez sur le réseau virtuel pour lequel vous souhaitez changer une plage d’adresses de sous-réseau.
4. Cliquez sur le sous-réseau pour lequel vous voulez changer la plage d’adresses.
5. Dans le panneau du sous-réseau, dans la zone **Plage d’adresses**, entrez la nouvelle plage d’adresses. La plage doit être unique dans l’espace d’adressage du réseau virtuel. La plage ne peut pas chevaucher d’autres plages d’adresses de sous-réseau au sein du réseau virtuel. L’espace d’adressage doit être spécifié à l’aide de la notation CIDR. Par exemple, dans un réseau virtuel avec l’espace d’adressage 10.0.0.0/16, vous pouvez définir l’espace d’adressage de sous-réseau 10.0.0.0/24. La plus petite plage que vous puissiez spécifier est /29. Celle-ci fournit huit adresses IP pour le sous-réseau. Azure réserve la première et la dernière adresses dans chaque sous-réseau pour la conformité du protocole. Trois adresses supplémentaires sont réservées à l’usage du service Azure. Par conséquent, un sous-réseau avec une plage d’adresses /29 compte trois adresses IP utilisables. Si vous envisagez de connecter un réseau virtuel à une passerelle VPN, vous devez créer un sous-réseau de passerelle. Pour en savoir plus, voir [Sous-réseau de passerelle](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Vous pouvez modifier la plage d’adresses après la création du sous-réseau sous certaines conditions. Pour savoir comment changer une plage d’adresses de sous-réseau, consultez [Changer les paramètres de sous-réseau](#change-subnet) dans cet article.
6. Cliquez sur **Enregistrer**.

**Commandes**

|Outil|Commande|
|---|---|
|Azure CLI|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>Supprimer un sous-réseau

Vous pouvez supprimer un sous-réseau uniquement si aucune ressource ne s’y trouve. S’il existe des ressources dans le sous-réseau, vous devez les supprimer avant de pouvoir supprimer le sous-réseau. La procédure à suivre pour supprimer une ressource varient en fonction de celle-ci. Pour savoir comment supprimer des ressources dans des sous-réseaux, lisez la documentation relative à chaque type de ressource que vous souhaitez supprimer. Pour supprimer un sous-réseau :

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) en utilisant un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Pour en savoir plus sur l’attribution de rôles et d’autorisations à des comptes, consultez [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Dans la zone de recherche du portail, entrez **réseaux virtuels**. Dans les résultats de la recherche, cliquez sur **Réseaux virtuels**.
3. Dans le panneau **Réseaux virtuels**, cliquez sur le réseau virtuel duquel vous voulez supprimer un sous-réseau.
4. Dans le panneau du réseau virtuel, sous **PARAMÈTRES**, cliquez sur **Sous-réseaux**.
5. Dans la liste des sous-réseaux qui s’affiche dans le panneau des sous-réseaux, cliquez avec le bouton droit sur le sous-réseau que vous souhaitez supprimer. Cliquez ensuite sur **Supprimer**, puis sur **Oui** pour supprimer le sous-réseau.

**Commandes**

|Outil|Commande|
|---|---|
|Azure CLI|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Étapes suivantes

Pour créer une machine virtuelle dans un sous-réseau, consultez [Créer un réseau virtuel et de déployer des machines virtuelles dans le sous-réseau](quick-create-portal.md#create-virtual-machines).
