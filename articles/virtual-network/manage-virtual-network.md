---
title: Créer, modifier ou supprimer un réseau virtuel Azure | Microsoft Docs
description: Découvrez comment créer, modifier ou supprimer un réseau virtuel dans Azure.
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
ms.openlocfilehash: ac0b15f120071093fd81de1d83cf2067ecbac269
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="create-change-or-delete-a-virtual-network"></a>Créer, modifier ou supprimer un réseau virtuel

Découvrez comment créer et supprimer un réseau virtuel, ainsi que modifier des paramètres tels que les serveurs DNS et les espaces d’adressage IP, pour un réseau virtuel existant.

Un réseau virtuel est une représentation de votre propre réseau dans le cloud. Un réseau virtuel est une isolation logique du cloud Azure, dédiée à votre abonnement Azure. Pour chaque réseau virtuel que vous créez, vous pouvez :
- Choisir un espace d’adressage à assigner. Un espace d’adressage se compose d’une ou plusieurs plages d’adresses qui sont définies à l’aide d’une notation de routage CIDR (Classless InterDomain Routing), telle que 10.0.0.0/16.
- Choisir d’utiliser le serveur DNS fourni par Azure ou votre propre serveur DNS. Toutes les ressources connectées au réseau virtuel sont assignées à ce serveur DNS pour résoudre les noms au sein du réseau virtuel.
- Segmenter le réseau virtuel en sous-réseaux, chacun avec sa propre plage d’adresses, au sein de l’espace d’adressage du réseau virtuel. Pour savoir comment créer, modifier et supprimer des sous-réseaux, voir [Ajouter, modifier ou supprimer des sous-réseaux](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Avant de commencer

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez le portail, ouvrez https://portal.azure.com, puis connectez-vous avec votre compte Azure.
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, exécutez-les dans l’[Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Ce didacticiel requiert le module Azure PowerShell version 5.2.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Ce didacticiel requiert Azure CLI version 2.0.26 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI localement, vous devez également exécuter `az login` pour créer une connexion avec Azure.

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. Sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
2. Entrez ou sélectionnez des valeurs pour les paramètres suivants, puis cliquez sur **Créer** :
    - **Nom** : le nom doit être unique dans le [groupe de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) dans lequel vous choisissez de créer le réseau virtuel. Vous ne pouvez pas modifier le nom une fois le réseau virtuel créé. Vous pouvez créer plusieurs réseaux virtuels au fil du temps. Pour des suggestions de dénomination, voir [Conventions d’affectation de noms](https://docs.microsoft.com/en-us/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). Le respect d’une convention d’affectation de noms peut simplifier la gestion de plusieurs réseaux virtuels.
    - **Espace d’adressage** : l’espace d’adressage d’un réseau virtuel est composé d’une ou de plusieurs plages d’adresses ne se chevauchant pas et spécifiées dans la notation CIDR. La plage d’adresses que vous définissez peut être publique ou privée (RFC 1918). Que vous définissiez la plage d’adresses comme publique ou privée, celle-ci est uniquement accessible à partir du réseau virtuel, à partir de réseaux virtuels interconnectés et à partir de tout réseau local que vous avez connecté au réseau virtuel. Vous ne pouvez pas ajouter les plages d’adresses suivantes :
        - 224.0.0.0/4 (multidiffusion)
        - 255.255.255.255/32 (diffusion)
        - 127.0.0.0/8 (bouclage)
        - 169.254.0.0/16 (lien-local)
        - 168.63.129.16/32 (DNS interne)

      Si vous ne pouvez définir qu’une seule plage d’adresses durant la création du réseau virtuel, vous pouvez en ajouter d’autres à l’espace d’adressage une fois le réseau virtuel créé. Pour savoir comment ajouter une plage d’adresses à un réseau virtuel existant, consultez [Ajouter ou supprimer une plage d’adresses](#add-or-remove-an-address-range).

      >[!WARNING]
      >Si un réseau virtuel comporte des plages d’adresses qui chevauchent celles d’un autre réseau virtuel ou local, il est impossible de connecter les deux réseaux. Avant de définir une plage d’adresses, demandez-vous si vous souhaiteriez peut-être connecter le réseau virtuel à d’autres réseaux virtuels ou locaux par la suite.
      >
      >

    - **Nom de sous-réseau** : le nom du sous-réseau doit être unique au sein du réseau virtuel. Vous ne pouvez pas modifier le nom du sous-réseau une fois celui-ci créé. Lorsque vous créez un réseau virtuel, le portail exige que vous définissiez un sous-réseau, même si un réseau virtuel ne doit pas obligatoirement comprendre des sous-réseaux. Dans le portail, vous ne pouvez définir qu’un seul sous-réseau lorsque vous créez un réseau virtuel. Vous pouvez ajouter des sous-réseaux au réseau virtuel une fois celui-ci créé. Pour ajouter un sous-réseau à un réseau virtuel, consultez [Gérer des sous-réseaux](virtual-network-manage-subnet.md). Vous pouvez créer un réseau virtuel comprenant plusieurs sous-réseaux en utilisant PowerShell ou Azure CLI.

      >[!TIP]
      >Les administrateurs créent parfois des sous-réseaux distincts pour filtrer ou contrôler le routage du trafic entre ceux-ci. Avant de définir des sous-réseaux, réfléchissez à la manière dont vous pourriez vouloir filtrer et router le trafic entre ceux-ci. Pour en savoir plus sur le filtrage du trafic entre des sous-réseaux, voir [Filtrer le trafic réseau avec les groupes de sécurité réseau](security-overview.md). Azure route automatiquement le trafic entre sous-réseaux, mais vous pouvez remplacer les itinéraires par défaut d’Azure. Pour en savoir plus sur le routage du trafic de sous-réseau par défaut Azure, consultez [Vue d’ensemble du routage](virtual-networks-udr-overview.md).
      >

    - **Plage d’adresses de sous-réseau** : la plage d’adresses doit s’inscrire dans l’espace d’adressage vous avez entré pour le réseau virtuel. La plus petite plage que vous puissiez spécifier est /29. Celle-ci fournit huit adresses IP pour le sous-réseau. Azure réserve la première et la dernière adresses dans chaque sous-réseau pour la conformité du protocole. Trois adresses supplémentaires sont réservées à l’usage du service Azure. Par conséquent, un réseau virtuel dont la plage d’adresses de sous-réseau est /29 ne comprend que trois adresses IP utilisables. Si vous envisagez de connecter un réseau virtuel à une passerelle VPN, vous devez créer un sous-réseau de passerelle. Pour en savoir plus, voir [Sous-réseau de passerelle](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Vous pouvez modifier la plage d’adresses après la création du sous-réseau sous certaines conditions. Pour savoir comment modifier une plage d’adresses de sous-réseau, consultez [Gérer des sous-réseaux](virtual-network-manage-subnet.md).
    - **Abonnement :** sélectionnez un [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Vous ne pouvez pas utiliser un même réseau virtuel dans plus d’un abonnement Azure. En revanche, vous pouvez connecter un réseau virtuel figurant dans un abonnement à des réseaux virtuels figurant dans d’autres abonnements via une [homologation de réseau virtuel](virtual-network-peering-overview.md). Toute ressource Azure que vous connectez au réseau virtuel doit figurer dans le même abonnement que le réseau virtuel.
    - **Groupe de ressources** : sélectionnez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) existant ou créez-en un. Une ressource Azure que vous connectez au réseau virtuel peut figurer dans le même groupe de ressources que le réseau virtuel ou dans un autre groupe de ressources.
    - **Emplacement** : sélectionnez un [emplacement](https://azure.microsoft.com/regions/) Azure (également appelé région). Un réseau virtuel peut figurer que dans un seul emplacement d’Azure. En revanche, vous pouvez connecter un réseau virtuel figurant dans un emplacement à un réseau virtuel figurant dans un autre emplacement en utilisant une passerelle VPN. Toute ressource Azure que vous connectez au réseau virtuel doit figurer dans le même emplacement que le réseau virtuel.

**Commandes**

- Azure CLI : [az network vnet create](/cli/azure/network/vnet)
- PowerShell : [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Afficher des réseaux virtuels et des paramètres

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste des réseaux virtuels, sélectionnez le réseau dont vous souhaitez afficher les paramètres.
3. Les paramètres répertoriés pour le réseau virtuel que vous avez sélectionné sont les suivants :
    - **Vue d’ensemble** : fournit des informations sur le réseau virtuel, dont l’espace d’adressage et les serveurs DNS. La capture d’écran suivante présente les paramètres de vue d’ensemble pour un réseau virtuel nommé **MyVNet** :

        ![Vue d’ensemble de l’interface réseau](./media/manage-virtual-network/vnet-overview.png)

      Vous pouvez déplacer un réseau virtuel vers un autre abonnement ou un autre groupe de ressources en sélectionnant l’option **Modifier** en regard de **Groupe de ressources** ou **Nom de l’abonnement**. Pour savoir comment déplacer un réseau virtuel, voir [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Cet article répertorie les conditions préalables et explique comment déplacer des ressources à l’aide du portail Azure, de PowerShell ou d’Azure CLI. Toutes les ressources connectées au réseau virtuel doivent être déplacées avec celui-ci.
    - **Espace d’adressage** : les espaces d’adressage assignés au réseau virtuel sont répertoriés. Pour savoir comment ajouter et supprimer une plage d’adresses, procédez comme indiqué dans la section [Ajouter ou supprimer une plage d’adresses](#add-or-remove-an-address-range).
    - **Appareils connectés** : toutes les ressources connectées au réseau virtuel sont répertoriées. Dans la capture d’écran précédente, trois interfaces réseau et un équilibreur de charge sont connectés au réseau virtuel. Toutes les ressources que vous créez et connectez au réseau virtuel sont répertoriées. Si vous supprimez une ressource connectée au réseau virtuel, elle n’apparaît plus dans la liste.
    - **Sous-réseaux** : la liste des sous-réseaux présents dans le réseau virtuel est affichée. Pour savoir comment ajouter et supprimer un sous-réseau, consultez [Gérer des sous-réseaux](virtual-network-manage-subnet.md).
    - **Serveurs DNS** : vous pouvez spécifier le serveur chargé d’assurer la résolution de noms pour les appareils connectés au réseau virtuel : le serveur DNS interne d’Azure ou le serveur DNS personnalisé. Lorsque vous créez un réseau virtuel via le portail Azure, par défaut, les serveurs DNS d’Azure sont utilisés pour la résolution de noms au sein du réseau virtuel. Pour modifier les serveurs DNS, procédez comme indiqué dans la section [Modifier les serveurs DNS](#change-dns-servers) de cet article.
    - **Homologations** : s’il existe des homologations dans l’abonnement, celles-ci sont répertoriées ici. Vous pouvez afficher les paramètres des homologations existantes, ou créer, modifier ou supprimer des homologations. Pour en savoir plus sur les homologations, voir [Homologation de réseaux virtuels](virtual-network-peering-overview.md).
    - **Propriétés** : affiche les paramètres du réseau virtuel, dont l’ID de ressource et l’abonnement Azure dans lequel il figure.
    - **Diagramme** : le diagramme fournit une représentation visuelle de tous les appareils connectés au réseau virtuel. Il comporte des informations clés sur les appareils. Pour gérer un appareil affiché dans le diagramme, sélectionnez-le.
    - **Paramètres Azure courants** : pour en savoir plus sur les paramètres Azure courants, voir les informations suivantes :
        *   [Journal d’activité](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Contrôle d’accès (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Balises](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [Verrous](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Script Automation](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Commandes**

- Azure CLI : [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show)
- PowerShell : [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Ajouter ou supprimer une plage d’adresses

Vous pouvez ajouter et supprimer des plages d’adresses pour un réseau virtuel. Une plage d’adresses doit être spécifiée dans une notation CIDR. Elle ne peut pas chevaucher d’autres plages d’adresses au sein du même réseau virtuel. Les plages d’adresses que vous définissez peuvent être publiques ou privées (RFC 1918). Que vous définissiez la plage d’adresses comme publique ou privée, celle-ci est uniquement accessible à partir du réseau virtuel, à partir de réseaux virtuels interconnectés et à partir de tout réseau local que vous avez connecté au réseau virtuel. Vous ne pouvez pas ajouter les plages d’adresses suivantes :

- 224.0.0.0/4 (multidiffusion)
- 255.255.255.255/32 (diffusion)
- 127.0.0.0/8 (bouclage)
- 169.254.0.0/16 (lien-local)
- 168.63.129.16/32 (DNS interne)

Pour ajouter ou supprimer une plage d’adresses :

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste des réseaux virtuels, sélectionnez le réseau pour lequel vous souhaitez ajouter ou supprimer une plage d’adresses.
3. Sous **PARAMÈTRES**, sélectionnez **Espace d'adressage**.
4. Choisissez l'une des options suivantes :
    - **Ajouter une plage d’adresses** : entrez la nouvelle plage d’adresses. La plage d’adresses ne peut pas chevaucher une plage d’adresses existante définie pour le réseau virtuel.
    - **Supprimer une plage d’adresses** : à droite de la plage d’adresses à supprimer, sélectionnez **...** , puis cliquez sur **Supprimer**. S’il existe déjà un sous-réseau dans la plage d’adresses, vous ne pouvez pas supprimer la plage. Avant de supprimer une plage d’adresses, vous devez supprimer tous les sous-réseaux (et toutes les ressources des sous-réseaux) existant dans celle-ci.
5. Sélectionnez **Enregistrer**.

**Commandes**

- Azure CLI : [az network vnet update](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell : [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="change-dns-servers"></a>Modifier les serveurs DNS

Toutes les machines virtuelles connectées au réseau virtuel s’inscrivent auprès des serveurs DNS que vous spécifiez pour le réseau virtuel. Elles utilisent également le serveur DNS spécifié pour la résolution de noms. Chaque carte réseau (NIC) sur un ordinateur virtuel peut avoir ses propres paramètres de serveur DNS. Si une carte réseau a ses propres paramètres de serveur DNS, ceux-ci remplacent les paramètres du serveur DNS pour le réseau virtuel. Pour en savoir plus sur les paramètres DNS de carte réseau, voir [Interfaces réseau (cartes d’interface réseau)](virtual-network-network-interface.md#change-dns-servers). Pour en savoir plus sur la résolution de noms pour des machines virtuelles et des instances de rôle dans Azure Cloud Services, voir [Résolution de noms pour les machines virtuelles et les instances de rôle](virtual-networks-name-resolution-for-vms-and-role-instances.md). Pour ajouter, modifier ou supprimer un serveur DNS :

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste des réseaux virtuels, sélectionnez le réseau dont vous souhaitez modifier les serveurs DNS.
3.  Sous**PARAMÈTRES**, sélectionnez **Serveurs DNS**.
4. Sélectionnez l’une des options suivantes :
    - **Par défaut (fournie par Azure)** : les noms de ressources et adresses IP privées sont inscrits automatiquement sur les serveurs DNS d’Azure. Vous pouvez résoudre les noms parmi toutes les ressources connectées à un même réseau virtuel. Vous ne pouvez pas utiliser cette option pour résoudre les noms dans plusieurs réseaux virtuels. Pour résoudre les noms dans plusieurs réseaux virtuels, vous devez utiliser un serveur DNS personnalisé.
    - **Personnalisé** : vous pouvez ajouter un ou plusieurs serveurs, jusqu’à la limite qu’Azure autorise pour un réseau virtuel. Pour en savoir plus sur les limites du serveur DNS, voir [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Vous disposez des options suivantes :
        - **Ajouter une adresse** : ajoute le serveur à la liste des serveurs DNS de votre réseau virtuel. Cette option inscrit également le serveur DNS auprès d’Azure. Si vous avez déjà inscrit un serveur DNS auprès d’Azure, vous pouvez sélectionner ce serveur dans la liste.
        - **Supprimer une adresse** : sélectionnez **...** en regard du serveur à supprimer, puis cliquez sur **Supprimer**. La suppression du serveur ne supprime que le serveur de cette liste de réseaux virtuels. Le serveur DNS reste enregistré dans Azure pour être utilisé par vos autres réseaux virtuels.
        - **Réorganiser les adresses des serveurs DNS** : il est important que vous vous assuriez de répertorier vos serveurs DNS dans l’ordre approprié pour votre environnement. Ils sont utilisés dans l’ordre où ils sont spécifiés. Ils ne fonctionnent pas comme un tourniquet (round robin). Si le premier serveur DNS de la liste est accessible, le client utilise celui-ci, qu’il fonctionne correctement ou non. Supprimez tous les serveurs DNS répertoriés, puis rajoutez-les dans l’ordre souhaité.
        - **Modifier une adresse** : mettez en surbrillance le serveur DNS dans la liste, puis entrez la nouvelle adresse.
5. Sélectionnez **Enregistrer**.
6. Redémarrez les machines virtuelles connectées au réseau virtuel, afin que les nouveaux paramètres de serveur DNS leur soient assignés. Les machines virtuelles continuent d’utiliser leurs paramètres DNS actifs jusqu’à ce qu’elles soient redémarrés.

**Commandes**

- Azure CLI : [az network vnet update](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell : [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Supprimer un réseau virtuel

Vous pouvez supprimer un réseau virtuel uniquement si aucune ressource n’est connectée à celui-ci. Si des ressources sont connectées à un sous-réseau du réseau virtuel, vous devez commencer par supprimer les ressources connectées à tous les sous-réseaux du réseau virtuel. La procédure à suivre pour supprimer une ressource varient en fonction de celle-ci. Pour savoir comment supprimer des ressources connectées à des sous-réseaux, lisez la documentation relative à chaque type de ressource que vous souhaitez supprimer. Pour supprimer un réseau virtuel :

1. Dans la zone de recherche située en haut du portail, entrez *réseaux virtuels*. Quand la mention **Réseaux virtuels** apparaît dans les résultats de recherche, sélectionnez-la.
2. Dans la liste des réseaux virtuels, sélectionnez le réseau à supprimer.
3. Vérifiez qu’aucun appareil n’est connecté au réseau virtuel en sélectionnant **Appareils connectés** sous **PARAMÈTRES**. Si des appareils sont connectés, vous devez les supprimer avant de supprimer le réseau virtuel. Si aucun appareil n’est connecté, sélectionnez **Vue d’ensemble**.
4. Sélectionnez **Supprimer**.
5. Pour confirmer la suppression du réseau virtuel, cliquez sur **Oui**.

**Commandes**

- Azure CLI : [azure network vnet delete](/cli/azure/network/vnet#az_network_vnet_delete)
- PowerShell : [Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork)

## <a name="permissions"></a>Autorisations

Pour effectuer des tâches sur des réseaux virtuels, votre compte doit posséder le rôle de [contributeur de réseaux](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un rôle [personnalisé](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées qui sont répertoriées dans le tableau suivant :

|Opération                                    |   Nom d’opération                    |
|-------------------------------------------  |   --------------------------------  |
|Microsoft.Network/virtualNetworks/read       |   Obtenir un réseau virtuel               |
|Microsoft.Network/virtualNetworks/write      |   Créer ou mettre à jour un réseau virtuel  |
|Microsoft.Network/virtualNetworks/delete     |   Supprimer un réseau virtuel            |

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment créer une machine virtuelle et la connecter à un réseau virtuel, voir [Créer votre premier réseau virtuel](quick-create-portal.md#create-virtual-machines).
- Pour savoir comment filtrer le trafic réseau entre les sous-réseaux d’un réseau virtuel, voir [Créer des groupes de sécurité réseau à l’aide du portail Azure](virtual-networks-create-nsg-arm-pportal.md).
- Pour homologuer un réseau virtuel à un autre réseau virtuel, voir [Créer une homologation de réseau virtuel](tutorial-connect-virtual-networks-portal.md).
- Pour découvrir les options de connexion d’un réseau virtuel à un réseau local, voir [À propose de la passerelle du VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).
