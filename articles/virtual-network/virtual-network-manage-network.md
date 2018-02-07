---
title: "Créer, modifier ou supprimer un réseau virtuel Azure | Microsoft Docs"
description: "Découvrez comment créer, modifier ou supprimer un réseau virtuel dans Azure."
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
ms.openlocfilehash: 0d3f4a83b654315a5ff9344594323c5dcb801e77
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="create-change-or-delete-a-virtual-network"></a>Créer, modifier ou supprimer un réseau virtuel

Découvrez comment créer et supprimer un réseau virtuel, ainsi que modifier des paramètres tels que les serveurs DNS et les espaces d’adressage IP, pour un réseau virtuel existant.

Un réseau virtuel est une représentation de votre propre réseau dans le cloud. Un réseau virtuel est une isolation logique du cloud Azure, dédiée à votre abonnement Azure. Pour chaque réseau virtuel que vous créez, vous pouvez :
- Choisir un espace d’adressage à assigner. Un espace d’adressage se compose d’une ou plusieurs plages d’adresses qui sont définies à l’aide d’une notation de routage CIDR (Classless InterDomain Routing), telle que 10.0.0.0/16.
- Choisir d’utiliser le serveur DNS fourni par Azure ou votre propre serveur DNS. Toutes les ressources connectées au réseau virtuel sont assignées à ce serveur DNS pour résoudre les noms au sein du réseau virtuel.
- Segmenter le réseau virtuel en sous-réseaux, chacun avec sa propre plage d’adresses, au sein de l’espace d’adressage du réseau virtuel. Pour savoir comment créer, modifier et supprimer des sous-réseaux, voir [Ajouter, modifier ou supprimer des sous-réseaux](virtual-network-manage-subnet.md).

Cet article explique comment créer, modifier et supprimer des réseaux virtuels à l’aide du modèle de déploiement Azure Resource Manager.

## <a name="before"></a>Avant de commencer

Avant d’aborder les tâches décrites dans cet article, préparez-vous comme suit :

- Si vous ignorez tout de l’utilisation de réseaux virtuels, nous vous recommandons de consulter l’exercice proposé dans [Créer votre premier réseau virtuel Azure](quick-create-portal.md). Cet exercice peut vous aider à vous familiariser avec les réseaux virtuels.
- Pour en savoir plus sur les limites des réseaux virtuels, consultez [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Connectez-vous au portail Azure, à l’outil en ligne de commande Azure (Azure CLI) ou à Azure PowerShell en utilisant votre compte Azure. Si vous n’avez pas encore de compte Azure, inscrivez-vous pour bénéficier d’un [compte d’essai gratuit](https://azure.microsoft.com/free).
- Si vous prévoyez d’utiliser des commandes PowerShell pour accomplir les tâches décrites dans cet article, vous devez d’abord [installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que les applets de commande Azure PowerShell de la version la plus récente sont installées. Pour obtenir de l’aide concernant les commandes PowerShell utilisées dans les exemples, entrez `get-help <command> -full`.
- Si vous prévoyez d’utiliser des commandes Azure CLI pour accomplir les tâches décrites dans cet article, vous devez d’abord [installer et configurer Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Vérifiez que la version la plus récente d’Azure CLI est installée. Pour obtenir de l’aide concernant les commandes d’Azure CLI, entrez `az <command> --help`.


## <a name="create-vnet"></a>Créer un réseau virtuel

Pour créer un réseau virtuel :

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) en utilisant un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Pour en savoir plus sur l’affectation de rôles et d’autorisations à des comptes, voir [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Cliquez sur **Nouveau** > **Mise en réseau** > **Réseau virtuel**.
3. Dans le panneau **Réseau virtuel**, dans la boîte de dialogue **Sélectionnez un modèle de déploiement**, laissez l’option **Gestionnaire des ressources** activée, puis cliquez sur **Créer**.
4. Dans le panneau **Créer un réseau virtuel**, entrez ou sélectionnez les valeurs des paramètres suivants, puis cliquez sur **Créer** :
    - **Nom** : le nom doit être unique dans le [groupe de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) dans lequel vous choisissez de créer le réseau virtuel. Vous ne pouvez pas modifier le nom une fois le réseau virtuel créé. Vous pouvez créer plusieurs réseaux virtuels au fil du temps. Pour des suggestions de dénomination, voir [Conventions d’affectation de noms](/azure/architecture/best-practices/naming-conventions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Le respect d’une convention d’affectation de noms peut simplifier la gestion de plusieurs réseaux virtuels.
    - **Espace d’adressage** : spécifiez l’espace d’adressage dans la notation CIDR. L’espace d’adressage que vous définissez peut être public ou privé (RFC 1918). Que vous définissiez l’espace d’adressage comme public ou privé, celui-ci est accessible uniquement à partir du réseau virtuel, à partir de réseaux virtuels interconnectés, et à partir de tout réseau local que vous avez connecté au réseau virtuel. Vous ne pouvez pas ajouter les espaces d’adressage suivants :
        - 224.0.0.0/4 (multidiffusion)
        - 255.255.255.255/32 (diffusion)
        - 127.0.0.0/8 (bouclage)
        - 169.254.0.0/16 (lien-local)
        - 168.63.129.16/32 (DNS interne)

      Si vous ne pouvez définir qu’un seul espace d’adressage lors de la création du réseau virtuel, vous pouvez en ajouter d’autres une fois que le réseau virtuel est créé. Pour savoir comment ajouter un espace d’adressage à un réseau virtuel existant, voir [Ajouter ou supprimer un espace d’adressage](#add-address-spaces) dans cet article.

      >[!WARNING]
      >Si un réseau virtuel comporte des espaces d’adressage qui se chevauchent avec ceux d’un autre réseau virtuel ou local, il n’est pas possible de connecter les deux réseaux. Avant de définir un espace d’adressage, demandez-vous si vous pourriez vouloir connecter le réseau virtuel à d’autres réseaux virtuels ou locaux dans le futur.
      >
      >

    - **Nom de sous-réseau** : le nom du sous-réseau doit être unique au sein du réseau virtuel. Vous ne pouvez pas modifier le nom du sous-réseau une fois celui-ci créé. Lorsque vous créez un réseau virtuel, le portail exige que vous définissiez un sous-réseau, même si un réseau virtuel ne doit pas obligatoirement comprendre des sous-réseaux. Dans le portail, vous ne pouvez définir qu’un seul sous-réseau lorsque vous créez un réseau virtuel. Vous pouvez ajouter des sous-réseaux au réseau virtuel une fois celui-ci créé. Pour savoir comment ajouter un sous-réseau à un réseau virtuel, voir [Créer un sous-réseau](virtual-network-manage-subnet.md#create-subnet) dans [Créer, modifier ou supprimer des sous-réseaux](virtual-network-manage-subnet.md). Vous pouvez créer un réseau virtuel comprenant plusieurs sous-réseaux en utilisant PowerShell ou Azure CLI.

      >[!TIP]
      >Parfois, des administrateurs créent des sous-réseaux distincts afin de filtrer ou contrôler le routage du trafic entre ceux-ci. Avant de définir des sous-réseaux, réfléchissez à la manière dont vous pourriez vouloir filtrer et router le trafic entre ceux-ci. Pour en savoir plus sur le filtrage du trafic entre des sous-réseaux, voir [Filtrer le trafic réseau avec les groupes de sécurité réseau](virtual-networks-nsg.md). Azure route automatiquement le trafic entre sous-réseaux, mais vous pouvez remplacer les itinéraires par défaut d’Azure. Pour savoir comment modifier le routage du trafic de sous-réseau par défaut d’Azure, voir [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md).
      >

    - **Plage d’adresses de sous-réseau** : la plage d’adresses doit s’inscrire dans l’espace d’adressage vous avez entré pour le réseau virtuel. La plus petite plage que vous puissiez spécifier est /29. Celle-ci fournit huit adresses IP pour le sous-réseau. Azure réserve la première et la dernière adresses dans chaque sous-réseau pour la conformité du protocole. Trois adresses supplémentaires sont réservées à l’usage du service Azure. Par conséquent, un réseau virtuel dont la plage d’adresses de sous-réseau est /29 ne comprend que trois adresses IP utilisables. Si vous envisagez de connecter un réseau virtuel à une passerelle VPN, vous devez créer un sous-réseau de passerelle. Pour en savoir plus, voir [Sous-réseau de passerelle](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Vous pouvez modifier la plage d’adresses après la création du sous-réseau sous certaines conditions. Pour savoir comment modifier une plage d’adresses de sous-réseau, voir [Modifier les paramètres de sous-réseau](#change-subnet) dans [Créer, modifier ou supprimer des réseaux virtuels](virtual-network-manage-subnet.md).
    - **Abonnement :** sélectionnez un [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Vous ne pouvez pas utiliser un même réseau virtuel dans plus d’un abonnement Azure. En revanche, vous pouvez connecter un réseau virtuel figurant dans un abonnement à des réseaux virtuels figurant dans d’autres abonnements. Pour connecter des réseaux virtuels figurant dans des abonnements différents, utilisez une passerelle VPN Azure ou une homologation de réseau virtuel. Toute ressource Azure que vous connectez au réseau virtuel doit figurer dans le même abonnement que le réseau virtuel.
    - **Groupe de ressources** : sélectionnez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) existant ou créez-en un. Une ressource Azure que vous connectez au réseau virtuel peut figurer dans le même groupe de ressources que le réseau virtuel ou dans un autre groupe de ressources.
    - **Emplacement** : sélectionnez un [emplacement](https://azure.microsoft.com/regions/) Azure (également appelé région). Un réseau virtuel peut figurer que dans un seul emplacement d’Azure. En revanche, vous pouvez connecter un réseau virtuel figurant dans un emplacement à un réseau virtuel figurant dans un autre emplacement en utilisant une passerelle VPN. Toute ressource Azure que vous connectez au réseau virtuel doit figurer dans le même emplacement que le réseau virtuel.

**Commandes**

|Outil|Commande|
|---|---|
|Azure CLI|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>Afficher des réseaux virtuels et des paramètres

Pour afficher les réseaux virtuels et les paramètres :

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) en utilisant un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Pour en savoir plus sur l’attribution de rôles et d’autorisations à des comptes, consultez [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Dans la zone de recherche du portail, entrez **réseaux virtuels**. Dans les résultats de recherche, cliquez sur **Réseaux virtuels**.
3. Dans le panneau **Réseaux virtuels**, cliquez sur le réseau virtuel dont vous souhaitez afficher les paramètres.
4. Les paramètres répertoriés dans le panneau pour le réseau virtuel que vous avez sélectionné sont les suivants :
    - **Vue d’ensemble** : fournit des informations sur le réseau virtuel, dont l’espace d’adressage et les serveurs DNS. La capture d’écran suivante présente les paramètres de vue d’ensemble pour un réseau virtuel nommé **MyVNet** :

        ![Vue d’ensemble de l’interface réseau](./media/virtual-network-manage-network/vnet-overview.png)

      Dans le panneau **Vue d’ensemble**, vous pouvez déplacer un réseau virtuel vers un autre abonnement ou groupe de ressources. Pour savoir comment déplacer un réseau virtuel, voir [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Cet article répertorie les conditions préalables et explique comment déplacer des ressources à l’aide du portail Azure, de PowerShell ou d’Azure CLI. Toutes les ressources connectées au réseau virtuel doivent être déplacées avec celui-ci.
    - **Espace d’adressage** : les espaces d’adressage assignés au réseau virtuel sont répertoriés. Pour savoir comment ajouter et supprimer un espace d’adressage, procédez de la manière décrite dans la section [Ajouter ou supprimer un espace d’adressage](#address-spaces) de cet article.
    - **Appareils connectés** : toutes les ressources connectées au réseau virtuel sont répertoriées. Dans la capture d’écran précédente, trois interfaces réseau et un équilibreur de charge sont connectés au réseau virtuel. Toutes les ressources que vous créez et connectez au réseau virtuel sont répertoriées. Si vous supprimez une ressource connectée au réseau virtuel, elle n’apparaît plus dans la liste.
    - **Sous-réseaux** : la liste des sous-réseaux présents dans le réseau virtuel est affichée. Pour savoir comment ajouter et supprimer un sous-réseau, voir [Créer un sous-réseau](virtual-network-manage-subnet.md#create-subnet) et [Supprimer un sous-réseau](virtual-network-manage-subnet.md#delete-subnet) dans [Créer, modifier ou supprimer des réseaux virtuels](virtual-network-manage-subnet.md).
    - **Serveurs DNS** : vous pouvez spécifier le serveur chargé d’assurer la résolution de noms pour les appareils connectés au réseau virtuel : le serveur DNS interne d’Azure ou le serveur DNS personnalisé. Lorsque vous créez un réseau virtuel via le portail Azure, par défaut, les serveurs DNS d’Azure sont utilisés pour la résolution de noms au sein du réseau virtuel. Pour modifier les serveurs DNS, procédez de la manière décrite dans la section [Ajouter, modifier ou supprimer un serveur DNS](#dns-servers) de cet article.
    - **Homologations** : s’il existe des homologations dans l’abonnement, celles-ci sont répertoriées ici. Vous pouvez afficher les paramètres des homologations existantes, ou créer, modifier ou supprimer des homologations. Pour en savoir plus sur les homologations, voir [Homologation de réseaux virtuels](virtual-network-peering-overview.md).
    - **Propriétés** : affiche les paramètres du réseau virtuel, dont l’ID de ressource et l’abonnement Azure dans lequel il figure.
    - **Diagramme** : le diagramme fournit une représentation visuelle de tous les appareils connectés au réseau virtuel. Il comporte des informations clés sur les appareils. Pour gérer un appareil affiché dans le diagramme, cliquez dessus.
    - **Paramètres Azure courants** : pour en savoir plus sur les paramètres Azure courants, voir les informations suivantes :
        *   [Journal d’activité](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Contrôle d’accès (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Balises](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [Verrous](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Script Automation](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Commandes**

|Outil|Commande|
|---|---|
|Azure CLI|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="add-address-spaces"></a>Ajouter ou supprimer un espace d’adressage

Vous pouvez ajouter et supprimer des espaces d’adressage pour un réseau virtuel. Un espace d’adressage doit être spécifié dans une notation CIDR. Il ne peut pas chevaucher d’autres espaces d’adressage au sein du même réseau virtuel. Les espaces d’adressage que vous définissez peuvent être publics ou privés (RFC 1918). Que vous définissiez l’espace d’adressage comme public ou privé, celui-ci est accessible uniquement à partir du réseau virtuel, à partir de réseaux virtuels interconnectés, et à partir de tout réseau local que vous avez connecté au réseau virtuel. Vous ne pouvez pas ajouter les espaces d’adressage suivants :

- 224.0.0.0/4 (multidiffusion)
- 255.255.255.255/32 (diffusion)
- 127.0.0.0/8 (bouclage)
- 169.254.0.0/16 (lien-local)
- 168.63.129.16/32 (DNS interne)

Pour ajouter ou supprimer un espace d’adressage :

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) en utilisant un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Pour en savoir plus sur l’attribution de rôles et d’autorisations à des comptes, consultez [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Dans la zone de recherche du portail, entrez **réseaux virtuels**. Dans les résultats de recherche, sélectionnez **Réseaux virtuels**.
3. Dans le panneau **Réseaux virtuels**, cliquez sur le réseau virtuel pour lequel vous souhaitez ajouter ou supprimer un espace d’adressage.
4. Dans le panneau du réseau virtuel, sous **Paramètres**, cliquez sur **Espace d’adressage**.
5. Dans le panneau de l’espace d’adressage, complétez l’une des options suivantes :
    - **Ajouter un espace d’adressage** : entrez le nouvel espace d’adressage. L’espace d’adressage ne peut pas chevaucher un espace d’adressage existant défini pour le réseau virtuel.
    - **Supprimer un espace d’adressage** : cliquez avec le bouton droit sur un espace d’adressage, puis cliquez sur **Supprimer**. S’il existe un sous-réseau dans l’espace d’adressage, vous ne pouvez pas supprimer l’espace d’adressage. Avant de supprimer un espace d’adressage, vous devez supprimer tous les sous-réseaux (ainsi que les ressources qui y sont connectées) existant dans celui-ci.
6. Cliquez sur **Enregistrer**.

**Commandes**

|Outil|Commande|
|---|---|
|Azure CLI|Resource Manager uniquement|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>Ajouter, modifier ou supprimer un serveur DNS

Toutes les machines virtuelles connectées au réseau virtuel s’inscrivent auprès des serveurs DNS que vous spécifiez pour le réseau virtuel. Elles utilisent également le serveur DNS spécifié pour la résolution de noms. Chaque carte réseau (NIC) sur un ordinateur virtuel peut avoir ses propres paramètres de serveur DNS. Si une carte réseau a ses propres paramètres de serveur DNS, ceux-ci remplacent les paramètres du serveur DNS pour le réseau virtuel. Pour en savoir plus sur les paramètres DNS de carte réseau, voir [Interfaces réseau (cartes d’interface réseau)](virtual-network-network-interface.md#change-dns-servers). Pour en savoir plus sur la résolution de noms pour des machines virtuelles et des instances de rôle dans Azure Cloud Services, voir [Résolution de noms pour les machines virtuelles et les instances de rôle](virtual-networks-name-resolution-for-vms-and-role-instances.md). Pour ajouter, modifier ou supprimer un serveur DNS :

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) en utilisant un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Pour en savoir plus sur l’attribution de rôles et d’autorisations à des comptes, consultez [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Dans la zone de recherche du portail, tapez **réseaux virtuels**. Dans les résultats de recherche, sélectionnez **Réseaux virtuels**.
3. Dans le panneau **Réseaux virtuels**, cliquez sur le réseau virtuel dont vous souhaitez modifier les paramètres DNS.
4. Dans le panneau du serveur virtuel, sous **Paramètres**, cliquez sur **Serveurs DNS**.
5. Sélectionnez l’une des options suivantes dans le panneau qui affiche la liste des serveurs DNS :
    - **Par défaut (fournie par Azure)** : les noms de ressources et adresses IP privées sont inscrits automatiquement sur les serveurs DNS d’Azure. Vous pouvez résoudre les noms parmi toutes les ressources connectées à un même réseau virtuel. Vous ne pouvez pas utiliser cette option pour résoudre les noms dans plusieurs réseaux virtuels. Pour résoudre les noms dans plusieurs réseaux virtuels, vous devez utiliser un serveur DNS personnalisé.
    - **Personnalisé** : vous pouvez ajouter un ou plusieurs serveurs, jusqu’à la limite qu’Azure autorise pour un réseau virtuel. Pour en savoir plus sur les limites du serveur DNS, voir [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Vous disposez des options suivantes :
        - **Ajouter une adresse** : ajoute le serveur à la liste des serveurs DNS de votre réseau virtuel. Cette option inscrit également le serveur DNS auprès d’Azure. Si vous avez déjà inscrit un serveur DNS auprès d’Azure, vous pouvez sélectionner ce serveur dans la liste.
        - **Supprimer une adresse** : cliquez sur le **X** en regard du serveur que vous souhaitez supprimer. La suppression du serveur ne supprime que le serveur de cette liste de réseaux virtuels. Le serveur DNS reste enregistré dans Azure pour être utilisé par vos autres réseaux virtuels.
        - **Réorganiser les adresses des serveurs DNS** : il est important que vous vous assuriez de répertorier vos serveurs DNS dans l’ordre approprié pour votre environnement. Ils sont utilisés dans l’ordre où ils sont spécifiés. Ils ne fonctionnent pas comme un tourniquet (round robin). Si le premier serveur DNS de la liste est accessible, le client utilise celui-ci, qu’il fonctionne correctement ou non. Supprimez tous les serveurs DNS répertoriés, puis rajoutez-les dans l’ordre souhaité.
        - **Modifier une adresse** : mettez en surbrillance le serveur DNS dans la liste, puis entrez le nouveau nom.
6. Cliquez sur **Enregistrer**.
7. Redémarrez les machines virtuelles connectées au réseau virtuel, afin que les nouveaux paramètres de serveur DNS leur soient assignés. Les machines virtuelles continuent d’utiliser leurs paramètres DNS actifs jusqu’à ce qu’elles soient redémarrés.

**Commandes**

|Outil|Commande|
|---|---|
|Azure CLI|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>Supprimer un réseau virtuel

Vous pouvez supprimer un réseau virtuel uniquement si aucune ressource n’est connectée à celui-ci. Si des ressources sont connectées à un sous-réseau du réseau virtuel, vous devez commencer par supprimer les ressources connectées à tous les sous-réseaux du réseau virtuel. La procédure à suivre pour supprimer une ressource varient en fonction de celle-ci. Pour savoir comment supprimer des ressources connectées à des sous-réseaux, lisez la documentation relative à chaque type de ressource que vous souhaitez supprimer. Pour supprimer un réseau virtuel :

1. Connectez-vous au [portail](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Pour en savoir plus sur l’attribution de rôles et d’autorisations à des comptes, consultez [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Dans la zone de recherche du portail, entrez **réseaux virtuels**. Dans les résultats de recherche, cliquez sur **Réseaux virtuels**.
3. Dans le panneau **Réseaux virtuels**, sélectionnez le réseau virtuel à supprimer.
4. Dans le panneau du réseau virtuel, pour vérifier qu’aucun appareil n’est connecté au réseau virtuel, sous **Paramètres**, cliquez sur **Appareils connectés**. Si des appareils sont connectés, vous devez les supprimer avant de supprimer le réseau virtuel. Si aucun appareil n’est connecté, cliquez sur **Vue d’ensemble**.
5. Dans le haut du panneau, cliquez sur l’icône **Supprimer**.
6. Pour confirmer la suppression du réseau virtuel, cliquez sur **Oui**.


**Commandes**

|Outil|Commande|
|---|---|
|Azure CLI|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment créer une machine virtuelle et la connecter à un réseau virtuel, voir [Créer votre premier réseau virtuel](quick-create-portal.md#create-virtual-machines).
- Pour savoir comment filtrer le trafic réseau entre les sous-réseaux d’un réseau virtuel, voir [Créer des groupes de sécurité réseau à l’aide du portail Azure](virtual-networks-create-nsg-arm-pportal.md).
- Pour homologuer un réseau virtuel à un autre réseau virtuel, voir [Créer une homologation de réseau virtuel](virtual-network-create-peering.md#portal).
- Pour découvrir les options de connexion d’un réseau virtuel à un réseau local, voir [À propose de la passerelle du VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).
