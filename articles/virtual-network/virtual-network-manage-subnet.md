---
title: Ajouter, modifier ou supprimer un sous-réseau de réseau virtuel Azure | Microsoft Docs
description: Découvrez comment ajouter, modifier ou supprimer un sous-réseau de réseau virtuel dans Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 16ce5aac26abcf2ef2cf7664fb0b9aae600708d4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Ajouter, modifier ou supprimer un sous-réseau de réseau virtuel

Découvrez comment ajouter, modifier ou supprimer un sous-réseau de réseau virtuel. Si vous n’êtes pas familiarisé avec les réseaux virtuels, nous vous recommandons de lire [Vue d’ensemble des réseaux virtuels Azure](virtual-networks-overview.md) et [Créer, modifier ou supprimer un réseau virtuel](manage-virtual-network.md) avant d’ajouter, modifier ou supprimer un sous-réseau. Toutes les ressources Azure déployées dans un réseau virtuel sont déployées dans un sous-réseau au sein d’un réseau virtuel.
 
## <a name="before-you-begin"></a>Avant de commencer

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez le portail, ouvrez https://portal.azure.com, puis connectez-vous avec votre compte Azure.
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, exécutez-les dans l’[Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Ce didacticiel requiert le module Azure PowerShell version 5.2.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Ce didacticiel requiert Azure CLI version 2.0.26 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI localement, vous devez également exécuter `az login` pour créer une connexion avec Azure.

## <a name="add-a-subnet"></a>Ajouter un sous-réseau

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste des réseaux virtuels, sélectionnez le réseau auquel vous souhaitez ajouter un sous-réseau.
3. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
4. Sélectionnez **+Sous-réseau**.
5. Entrez des valeurs pour les paramètres suivants :
    - **Nom** : le nom doit être unique au sein du réseau virtuel.
    - **Plage d’adresses** : la plage doit être unique dans l’espace d’adressage du réseau virtuel. La plage ne peut pas chevaucher d’autres plages d’adresses de sous-réseau au sein du réseau virtuel. L’espace d’adressage doit être spécifié en utilisant la notation de routage CIDR (Classless InterDomain Routing). Par exemple, dans un réseau virtuel avec l’espace d’adressage 10.0.0.0/16, vous pouvez définir l’espace d’adressage de sous-réseau 10.0.0.0/24. La plus petite plage que vous puissiez spécifier est /29. Celle-ci fournit huit adresses IP pour le sous-réseau. Azure réserve la première et la dernière adresses dans chaque sous-réseau pour la conformité du protocole. Trois adresses supplémentaires sont réservées à l’usage du service Azure. Par conséquent, définir un sous-réseau avec une plage d’adresses /29 produit trois adresses IP utilisables dans le sous-réseau. Si vous envisagez de connecter un réseau virtuel à une passerelle VPN, vous devez créer un sous-réseau de passerelle. Pour en savoir plus, voir [Sous-réseau de passerelle](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Vous pouvez modifier la plage d’adresses après l’ajout du sous-réseau sous certaines conditions. Pour savoir comment modifier une plage d’adresses de sous-réseau, consultez [Modifier les paramètres de sous-réseau](#change-subnet-settings).
    - **Groupe de sécurité réseau** : vous pouvez associer un groupe de sécurité réseau existant au sous-réseau pour filtrer le trafic réseau entrant et sortant du sous-réseau, ou n’associer aucun groupe. Le groupe de sécurité réseau doit exister dans le même abonnement et au même emplacement que le réseau virtuel. Découvrez-en plus sur les [groupes de sécurité réseau](security-overview.md) et la [création d’un groupe de sécurité réseau](virtual-networks-create-nsg-arm-pportal.md).
    - **Table de routage** : vous pouvez associer une table de routage existante à un sous-réseau pour contrôler le routage du trafic réseau vers d’autres réseaux, ou n’associer aucune table. La table de routage doit exister dans le même abonnement et au même emplacement que le réseau virtuel. Découvrez-en plus sur le [routage Azure](virtual-networks-udr-overview.md) et la [ création d’une table de routage](tutorial-create-route-table-portal.md).
    - **Points de terminaison de service :** un sous-réseau peut avoir plusieurs ou aucun point de terminaison de service activé pour celui-ci. Pour activer un point de terminaison de service pour un service spécifique, sélectionnez le ou les services pour lesquels vous souhaitez activer des points de terminaison de service dans la liste **Services**. Pour supprimer un point de terminaison de service, désélectionnez le service dont vous souhaitez supprimer le point de terminaison de service. Pour en savoir plus sur les points de terminaison de service, consultez [Vue d’ensemble des points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md). Après avoir activé un point de terminaison de service pour un service spécifique, vous devez également activer l’accès réseau du sous-réseau pour une ressource créée avec le service. Par exemple, si vous activez le point de terminaison de service *Microsoft.Storage*, vous devez également activer l’accès réseau à tous les comptes de stockage Azure auxquels vous souhaitez accorder l’accès réseau. Pour plus d’informations sur l’activation de l’accès réseau à des sous-réseaux pour lesquels un point de terminaison de service est activé, consultez la documentation relative au service particulier pour lequel vous avez activé le point de terminaison de service.
6. Pour ajouter le sous-réseau au réseau virtuel que vous avez sélectionné, cliquez sur **OK**.

**Commandes**

- Azure CLI : [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- PowerShell : [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Modifier les paramètres de sous-réseau

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste des réseaux virtuels, sélectionnez le réseau qui contient le sous-réseau dont vous souhaitez modifier les paramètres.
3. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
4. Dans la liste des sous-réseaux, sélectionnez le sous-réseau dont vous souhaitez modifier les paramètres. Vous pouvez modifier les paramètres suivants :

    - **Plage d’adresses :** si aucune ressource n’a été déployée dans le sous-réseau, vous pouvez modifier la plage d’adresses. Si des ressources existent déjà dans le sous-réseau, vous devez soit déplacer les ressources vers un autre sous-réseau, soit les supprimer d’abord du sous-réseau. La procédure à suivre pour supprimer ou déplacer une ressource varie en fonction de celle-ci. Pour savoir comment supprimer ou déplacer des ressources dans des sous-réseaux, lisez la documentation relative à chaque type de ressource que vous souhaitez supprimer ou déplacer. Consultez les contraintes pour la **plage d’adresses** à l’étape 5 de la section [Ajouter un sous-réseau](#add-a-subnet).
    - **Utilisateurs** : vous pouvez contrôler l’accès au sous-réseau en utilisant des rôles intégrés ou vos propres rôles personnalisés. Pour en savoir plus sur l’attribution de rôles et d’utilisateurs pour l’accès au sous-réseau, consultez [Utiliser l’attribution de rôles pour gérer l’accès à vos ressources Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
    - Pour plus d’informations sur la modification d’un **groupe de sécurité réseau**, d’une **table de routage**, d’**utilisateurs** et de **points de terminaison de service**, reportez-vous à l’étape 5 de la section [Ajouter un sous-réseau](#add-a-subnet).
5. Sélectionnez **Enregistrer**.

**Commandes**

- Azure CLI : [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell : [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Supprimer un sous-réseau

Vous pouvez supprimer un sous-réseau uniquement si aucune ressource ne s’y trouve. S’il existe des ressources dans le sous-réseau, vous devez les supprimer avant de pouvoir supprimer le sous-réseau. La procédure à suivre pour supprimer une ressource varient en fonction de celle-ci. Pour savoir comment supprimer des ressources dans des sous-réseaux, lisez la documentation relative à chaque type de ressource que vous souhaitez supprimer.

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste des réseaux virtuels, sélectionnez le réseau qui contient le sous-réseau à supprimer.
3. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
4. Dans la liste des sous-réseaux, sélectionnez **...** à droite du sous-réseau à supprimer.
5. Sélectionnez **Supprimer**, puis cliquez sur **Oui**.

**Commandes**

- Azure CLI : [az network vnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell : [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Autorisations

Pour effectuer des tâches sur des sous-réseaux, votre compte doit posséder le rôle de [contributeur de réseaux](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un rôle [personnalisé](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées qui sont répertoriées dans le tableau suivant :

|Opération                                                                |   Nom d’opération                               |
|-----------------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Obtenir un sous-réseau de réseau virtuel                   |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Créer ou mettre à jour un sous-réseau de réseau virtuel      |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Supprimer un sous-réseau de réseau virtuel                |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Joindre un réseau virtuel                         |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Joindre un service à un sous-réseau                     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Obtenir des machines virtuelles de sous-réseau de réseau virtuel  |
