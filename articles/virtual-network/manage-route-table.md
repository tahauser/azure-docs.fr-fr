---
title: Créer, modifier ou supprimer une table de routage Azure | Microsoft Docs
description: Découvrez comment créer, modifier ou supprimer une table de routage.
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
ms.author: jdial
ms.openlocfilehash: 7630fd82cf62f1fcb0df80cec5b5e0030da81a85
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-change-or-delete-a-route-table"></a>Créer, modifier ou supprimer une table de routage

Azure achemine automatiquement le trafic entre les sous-réseaux, les réseaux virtuels et les réseaux locaux Azure. Si vous souhaitez modifier un routage par défaut d’Azure, vous pouvez le faire en créant une table de routage. Si vous n’êtes pas familiarisé avec le routage Azure, nous vous recommandons de consulter la [vue d’ensemble du routage](virtual-networks-udr-overview.md) et de suivre le didacticiel [Acheminer le trafic réseau à l’aide d’une table de routage](tutorial-create-route-table-portal.md) avant d’exécuter les tâches décrites dans cet article.

## <a name="before-you-begin"></a>Avant de commencer

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez le portail, ouvrez https://portal.azure.com, puis connectez-vous avec votre compte Azure.
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, exécutez-les dans l’[Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Ce didacticiel requiert le module Azure PowerShell version 5.2.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Ce didacticiel requiert Azure CLI version 2.0.26 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI localement, vous devez également exécuter `az login` pour créer une connexion avec Azure.

## <a name="create-a-route-table"></a>Créer une table de routage

Le nombre de tables de routage que vous pouvez créer par abonnement et par emplacement Azure est limité. Pour plus d’informations, consultez [limites Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Dans l’angle supérieur gauche du portail, sélectionnez **+ Créer une ressource**.
2. Sélectionnez **Mise en réseau**, puis **Table de routage**.
3. Attribuez un **nom** à la table de routage, sélectionnez votre **abonnement**, créez un **groupe de ressources** ou sélectionnez un groupe de ressources existant, sélectionnez un **emplacement** et cliquez sur **Créer**. L’option **Désactiver la propagation des itinéraires BGP** empêche les itinéraires locaux de se propager sur un réseau virtuel Azure via le protocole BGP. Si votre réseau virtuel n’est pas connecté à une passerelle réseau Azure (VPN ou ExpressRoute), laissez cette option *désactivée*. 

**Commandes**

- Azure CLI : [az network route-table create](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell : [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>Afficher les tables de routage

Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de recherche, sélectionnez-la. Les tables de routage présentes dans votre abonnement sont répertoriées.

**Commandes**

- Azure CLI : [az network route-table list](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell : [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>Afficher les détails d’une table de routage

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste, sélectionnez la table de routage dont vous souhaitez afficher les détails. Sous **PARAMÈTRES**, vous pouvez afficher les **itinéraires** de la table de routage et les **sous-réseaux** auxquels est associée la table de routage.
3. Pour en savoir plus sur les paramètres Azure communs, consultez les informations suivantes :
    *   [Journal d’activité](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Contrôle d’accès (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Balises](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
    *   [Verrous](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Script Automation](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Commandes**

- Azure CLI : [az network route-table show](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell : [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>Modifier une table de routage

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sélectionnez la table de routage à modifier. Les modifications les plus courantes sont l’[ajout](#create-a-route) ou la [suppression](#delete-a-route) d’itinéraires, et l’[association](#associate-a-route-table-to-a-subnet) de tables de routage à des sous-réseaux ou leur [dissociation](#dissociate-a-route-table-from-a-subnet) de ces derniers.

**Commandes**

- Azure CLI : [az network route-table update](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell : [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Associer une table de routage à un sous-réseau

Un sous-réseau peut avoir zéro ou une table de routage associée. Une table de routage peut être associée à plusieurs ou aucun sous-réseau. Étant donné que les tables de routage ne sont pas associées à des réseaux virtuels, vous devez associer une table de routage à chaque sous-réseau auquel vous souhaitez associer la table. Tout le trafic sortant du sous-réseau est basé sur les itinéraires que vous avez créés dans les tables de routage, les [itinéraires par défaut](virtual-networks-udr-overview.md#default) et les itinéraires propagés à partir d’un réseau local, si le réseau virtuel est connecté à une passerelle de réseau virtuel Azure (ExpressRoute, ou VPN si vous utilisez le protocole BGP avec une passerelle VPN). Vous pouvez uniquement associer une table de routage à des sous-réseaux de réseaux virtuels qui se trouvent au même emplacement et dans le même abonnement Azure que la table de routage.

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste, sélectionnez le réseau virtuel qui contient le sous-réseau auquel associer une table de routage.
3. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
4. Sélectionnez le sous-réseau auquel associer la table de routage.
5. Cliquez sur **Table de routage**, sélectionnez la table de routage à associer au sous-réseau, puis cliquez sur **Enregistrer**.

**Commandes**

- Azure CLI : [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell : [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Dissocier une table de routage d’un sous-réseau

Quand vous dissociez une table de routage d’un sous-réseau, Azure achemine le trafic en fonction de ses [itinéraires par défaut](virtual-networks-udr-overview.md#default).

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sélectionnez le réseau virtuel qui contient le sous-réseau duquel dissocier une table de routage.
3. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
4. Sélectionnez le sous-réseau duquel dissocier la table de routage.
5. Cliquez sur **Table de routage**, sélectionnez **Aucune**, puis cliquez sur **Enregistrer**.

**Commandes**

- Azure CLI : [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell : [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>Supprimer une table de routage

Une table de routage associée à un sous-réseau ne peut pas être supprimée. [Dissociez](#dissociate-a-route-table-from-a-subnet) la table de routage de tous les sous-réseaux avant de tenter de la supprimer.

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sélectionnez **...** à droite de la table de routage à supprimer.
3. Sélectionnez **Supprimer**, puis cliquez sur **Oui**.

**Commandes**

- Azure CLI : [az network route-table delete](/cli/azure/network/route-table/route#az_network_route_table_delete)
- PowerShell : [Delete-AzureRmRouteTable](/powershell/module/azurerm.network/delete-azurermroutetable) 

## <a name="create-a-route"></a>Créer un itinéraire

Le nombre d’itinéraires par table de routage que vous pouvez créer par abonnement et par emplacement Azure est limité. Pour plus d’informations, consultez [limites Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste, sélectionnez la table de routage à laquelle vous souhaitez ajouter un itinéraire.
3. Sous **PARAMÈTRES**, sélectionnez **Itinéraires**.
4. Sélectionnez **Ajouter**.
5. Attribuez un **nom** unique à l’itinéraire dans la table de routage.
6. Entrez le **préfixe d’adresse** (dans la notation CIDR) vers lequel vous souhaitez acheminer le trafic. Le préfixe ne peut pas être dupliqué dans plusieurs itinéraires de la table de routage, bien qu’il puisse se trouver dans un autre préfixe. Par exemple, si vous avez défini 10.0.0.0/16 comme préfixe pour un itinéraire, vous pouvez toujours définir un autre itinéraire avec le préfixe d’adresse 10.0.0.0/24. Azure sélectionne un itinéraire pour le trafic en fonction de la correspondance de préfixe la plus longue. Pour en savoir plus sur la façon dont Azure sélectionne les itinéraires, consultez [vue d’ensemble du routage](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Sélectionnez un **type de tronçon suivant**. Pour obtenir une description détaillée de tous les types de tronçon suivant, consultez [vue d’ensemble du routage](virtual-networks-udr-overview.md).
8. Entrez une adresse IP pour l’**adresse de tronçon suivant**. Vous pouvez uniquement entrer une adresse si vous avez sélectionné *Appliance virtuelle* sous **Type de tronçon suivant**.
9. Sélectionnez **OK**. 

**Commandes**

- Azure CLI : [az network route-table route create](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell : [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>Afficher des itinéraires

Une table de routage peut contenir plusieurs ou aucun itinéraire. Pour en savoir plus sur les informations affichées avec les itinéraires, consultez [Vue d’ensemble du routage](virtual-networks-udr-overview.md).

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste, sélectionnez la table de routage dont vous souhaitez afficher les itinéraires.
3. Sous **PARAMÈTRES**, sélectionnez **Itinéraires**.

**Commandes**

- Azure CLI : [az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_list)
- PowerShell : [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>Afficher les détails d’un itinéraire

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sélectionnez la table de routage contenant l’itinéraire dont vous souhaitez afficher les détails.
3. Sélectionnez **Itinéraires**.
4. Sélectionnez l’itinéraire dont vous souhaitez afficher les détails.

**Commandes**

- Azure CLI : [az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell : [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>Modifier un itinéraire

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sélectionnez la table de routage dont vous souhaitez modifier un itinéraire.
3. Sélectionnez **Itinéraires**.
4. Sélectionnez l’itinéraire à modifier.
5. Modifiez les paramètres souhaités, puis cliquez sur **Enregistrer**.

**Commandes**

- Azure CLI : [az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_update)
- PowerShell : [Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>Supprimer un itinéraire

1. Dans la zone de recherche située en haut du portail, entrez *tables de routage*. Quand la mention **Tables de routage** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sélectionnez la table de routage de laquelle vous souhaitez supprimer un itinéraire.
3. Sélectionnez **Itinéraires**.
4. Dans la liste des itinéraires, sélectionnez **...** à droite de l’itinéraire à supprimer.
5. Sélectionnez **Supprimer**, puis cliquez sur **Oui**.

**Commandes**

- Azure CLI : [az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell : [Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>Afficher les itinéraires effectifs

Les itinéraires effectifs pour chaque interface réseau attachée à une machine virtuelle sont une combinaison des tables de routage que vous avez créées, des itinéraires par défaut d’Azure et de tout itinéraire propagé à partir de réseaux locaux via le protocole BGP à travers une passerelle de réseau virtuel Azure. Il est important de comprendre ce que représentent les itinéraires effectifs pour une interface réseau pour pouvoir résoudre les éventuels problèmes de routage. Vous pouvez afficher les itinéraires effectifs pour toute interface réseau attachée à une machine virtuelle en cours d’exécution.

1. Dans la zone de recherche située en haut du portail, entrez le nom d’une machine virtuelle dont vous souhaitez afficher les itinéraires effectifs. Si vous ne connaissez pas le nom de la machine virtuelle, entrez *machines virtuelles* dans la zone de recherche. Quand la mention **Machines virtuelles** apparaît dans les résultats de recherche, sélectionnez-la et choisissez une machine virtuelle dans la liste.
2. Sous **PARAMÈTRES**, sélectionnez **Mise en réseau**.
3. Sélectionnez le nom d’une interface réseau.
4. Sous **SUPPORT + DÉPANNAGE**, cliquez sur **Routages effectifs**.
5. Passez en revue la liste des itinéraires effectifs pour déterminer si l’itinéraire correct existe pour l’emplacement vers lequel vous souhaitez acheminer le trafic. Pour en savoir plus sur les types de tronçon suivant qui apparaissent dans cette liste, consultez [Vue d’ensemble du routage](virtual-networks-udr-overview.md).

**Commandes**

- Azure CLI : [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell : [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/remove-azurermrouteconfig) 

## <a name="validate-routing-between-two-endpoints"></a>Valider le routage entre deux points de terminaison

Vous pouvez déterminer le type de tronçon suivant entre une machine virtuelle et l’adresse IP d’une autre ressource Azure, une ressource locale ou une ressource sur Internet. La détermination du routage d’Azure est utile pour pouvoir résoudre les éventuels problèmes de routage. Pour exécuter cette tâche, vous devez disposer d’un Network Watcher existant. Si ce n’est pas le cas, créez-en un en procédant comme indiqué sous [Créer une instance de Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Dans la zone de recherche située en haut du portail, entrez *network watcher*. Quand la mention **Network Watcher** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sous **OUTILS DE DIAGNOSTIC RÉSEAU**, sélectionnez **Tronçon suivant**.
3. Sélectionnez votre **abonnement** et le **groupe de ressources** de la machine virtuelle source à partir de laquelle vous souhaitez valider le routage.
4. Sélectionnez la **machine virtuelle**, l’**interface réseau** attachée à la machine virtuelle, et l’**adresse IP source** affectée à l’interface réseau à partir de laquelle vous souhaitez valider le routage.
5. Entrez l’**adresse IP de destination** vers laquelle vous souhaitez valider le routage.
6. Sélectionnez **Tronçon suivant**.
7. Après un court délai d’attente, les informations retournées vous indiquent le type de tronçon suivant et l’ID de l’itinéraire qui a acheminé le trafic. Pour en savoir plus sur les types de tronçon suivant qui apparaissent dans les informations retournées, consultez [Vue d’ensemble du routage](virtual-networks-udr-overview.md).

**Commandes**

- Azure CLI : [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell : [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 
 
## <a name="permissions"></a>Autorisations

Pour exécuter des tâches sur des tables de routage et des itinéraires, votre compte doit posséder le rôle de [contributeur de réseaux](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un rôle [personnalisé](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées qui sont répertoriées dans le tableau suivant :

|Opération                                                       |   Nom d’opération                               |
|--------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/routeTables/read                              |   Obtenir une table de routage                              |
|Microsoft.Network/routeTables/write                             |   Créer ou mettre à jour une table de routage                 |
|Microsoft.Network/routeTables/delete                            |   Supprimer une table de routage                           |
|Microsoft.Network/routeTables/join/action                       |   Joindre une table de routage                             |
|Microsoft.Network/routeTables/routes/read                       |   Obtenir un itinéraire                                    |
|Microsoft.Network/routeTables/routes/write                      |   Créer ou mettre à jour un itinéraire                       |
|Microsoft.Network/routeTables/routes/delete                     |   Supprimer un itinéraire                                 |
|Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Obtenir une table de routage effective d’interface réseau  | 
|Microsoft.Network/networkWatchers/nextHop/action                |   Obtenir le tronçon suivant à partir d’une machine virtuelle                  |

L’opération *Joindre une table de routage* est nécessaire pour associer une table de routage à un sous-réseau.
