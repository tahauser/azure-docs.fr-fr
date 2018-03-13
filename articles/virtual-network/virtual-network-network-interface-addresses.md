---
title: "Configurer des adresses IP pour une interface réseau Azure | Microsoft Docs"
description: "Découvrez comment ajouter, modifier et supprimer des adresses IP publiques et privées pour une interface réseau."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 47f72fcfe2a4c9ab6e89314a64dae0027ef76924
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Ajouter, modifier ou supprimer des adresses IP pour une interface réseau Azure

Découvrez comment ajouter, modifier et supprimer des adresses IP publiques et privées pour une interface réseau. Les adresses IP privées assignées à une interface réseau permettent à une machine virtuelle de communiquer avec d’autres ressources dans un réseau virtuel Azure et des réseaux connectés. Une adresse IP privée permet également une communication sortante vers Internet à l’aide d’une adresse IP non prédictible. Une [adresse IP publique](virtual-network-public-ip-address.md) assignée à une interface réseau permet d’établir une communication entrante vers une machine virtuelle à partir d’Internet. L’adresse permet également d’établir une communication sortante de la machine virtuelle vers Internet à l’aide d’une adresse IP prédictible. Pour en savoir plus, consultez [Comprendre les connexions sortantes dans Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Si vous avez besoin de créer, modifier ou supprimer une interface réseau, consultez l’article [Ajouter, modifier ou supprimer des adresses IP pour des cartes réseau Azure](virtual-network-network-interface.md). Si vous devez ajouter ou supprimer des interfaces réseau sur une machine virtuelle, lisez l’article [Ajouter ou supprimer des interfaces réseau](virtual-network-network-interface-vm.md). 


## <a name="before-you-begin"></a>Avant de commencer

Avant d’effectuer une étape de cet article, quelle que soit sa section, effectuez les tâches suivantes :

- Pour découvrir les limites des adresses IP publiques et privées, voir [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Connectez-vous au [portail](https://portal.azure.com) Azure, à Azure CLI ou à Azure PowerShell avec un compte Azure. Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, vous devez [Installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que les applets de commande Azure PowerShell installées sont celles de la version la plus récente. Pour obtenir de l’aide sur les commandes PowerShell ainsi que des exemples, entrez `get-help <command> -full`.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, [installez et configurez Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que la version la plus récente d’Azure CLI est installée. Pour obtenir de l’aide sur les commandes CLI, entrez `az <command> --help`. Au lieu d’installer CLI et ses prérequis, vous pouvez utiliser Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Pour utiliser Cloud Shell, cliquez sur le bouton Cloud Shell **> _** en haut du [portail](https://portal.azure.com).

## <a name="add-ip-addresses"></a>Ajouter des adresses IP

Vous pouvez ajouter autant d’adresses [privées](#private) et [publiques](#public) [IPv4](#ipv4) que nécessaire à une interface réseau, dans le respect des [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Vous ne pouvez pas utiliser le portail pour ajouter une adresse IPv6 à une interface réseau existante (même si vous pouvez utiliser le portail pour ajouter une adresse IPv6 privée à une interface réseau lorsque vous créez l’interface réseau). Vous pouvez utiliser PowerShell ou l’interface CLI pour ajouter une adresse IPv6 privée à une [configuration IP secondaire](#secondary) (à condition qu’il n’existe encore aucune configuration IP secondaire) pour une interface réseau existante qui n’est pas attachée à une machine virtuelle. Vous ne pouvez pas utiliser n’importe quel outil pour ajouter une adresse IPv6 publique à une interface réseau. Consultez [IPv6](#ipv6) pour plus d’informations sur l’utilisation des adresses IPv6. 

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur l’interface réseau pour laquelle vous souhaitez ajouter une adresse IPv4.
4. Cliquez sur **Configurations IP** dans la section **Paramètres** du panneau relatif à l’interface réseau que vous avez sélectionnée.
5. Cliquez sur **+ Ajouter** dans le panneau qui s’ouvre pour les configurations IP.
6. Indiquez les éléments suivants, puis cliquez sur **OK** pour fermer le panneau **Ajouter une configuration IP** :

    |Paramètre|Requis ?|Détails|
    |---|---|---|
    |NOM|OUI|Doit être unique pour l’interface réseau|
    |type|OUI|Étant donné que vous ajoutez une configuration IP à une interface réseau existante et que chaque interface réseau doit disposer d’une configuration IP [principale](#primary), la seule option possible est **Secondaire**.|
    |Méthode d’affectation d’adresses IP privées|OUI|[**Dynamique**](#dynamic) : Azure attribue l’adresse disponible suivante pour la plage d’adresses de sous-réseau dans laquelle l’interface réseau est déployée. [**Statique**](#static) : Azure attribue une adresse inutilisée pour la plage d’adresses de sous-réseau dans laquelle l’interface réseau est déployée.|
    |Adresse IP publique|Non |**Désactivée :** aucune ressource d’adresse IP publique n’est actuellement associée à la configuration IP. **Activée :** sélectionnez une adresse IPv4 publique existante ou créez-en une. Pour savoir comment créer une adresse IP publique, consultez l’article [Adresses IP publiques](virtual-network-public-ip-address.md#create-a-public-ip-address).|
7. Ajoutez manuellement des adresses IP privées secondaires au système d’exploitation de la machine virtuelle en suivant les instructions de l’article [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](virtual-network-multiple-ip-addresses-portal.md#os-config). Consultez les adresses IP [privées](#private) pour connaître les considérations spécifiques avant d’ajouter manuellement des adresses IP à un système d’exploitation de machine virtuelle. N’ajoutez pas d’adresse IP publique au système d’exploitation de la machine virtuelle.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-address-settings"></a>Modifier les paramètres d’adresse IP

Vous pouvez modifier la méthode d’affectation d’une adresse IPv4, modifier l’adresse IPv4 statique ou modifier l’adresse IP publique assignées à une interface réseau. Si vous modifiez l’adresse IPv4 privée d’une configuration IP secondaire associée à une interface réseau secondaire dans une machine virtuelle (en savoir plus sur les [interfaces réseau principales et secondaires](virtual-network-network-interface-vm.md)), mettez la machine virtuelle dans l’état Arrêté (libéré) avant d’effectuer les opérations suivantes : 

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur l’interface réseau que vous souhaitez afficher ou dont vous souhaitez modifier les paramètres d’adresse IP.
4. Cliquez sur **Configurations IP** dans la section **Paramètres** du panneau relatif à l’interface réseau que vous avez sélectionnée.
5. Cliquez sur la configuration IP que vous souhaitez modifier dans la liste du panneau qui s’affiche pour les configurations IP.
6. Modifiez les paramètres comme vous le souhaitez en utilisant les informations relatives aux paramètres fournies à l’étape 6 de la section [Ajouter une configuration IP](#create-ip-config) de cet article. Pour fermer le panneau de la configuration IP que vous avez modifiée, cliquez sur **Enregistrer**.

>[!NOTE]
>Si l’interface réseau principale dispose de plusieurs configurations IP et si vous modifiez l’adresse IP privée de la configuration IP principale, vous devez réassigner manuellement les adresses IP principales et secondaires à l’interface réseau dans Windows (non requis pour Linux). Pour affecter manuellement des adresses IP à une interface réseau au sein d’un système d’exploitation, consultez l’article [Affecter plusieurs adresses IP aux machines virtuelles](virtual-network-multiple-ip-addresses-portal.md#os-config). Consultez les adresses IP [privées](#private) pour connaître les considérations spécifiques avant d’ajouter manuellement des adresses IP à un système d’exploitation de machine virtuelle. N’ajoutez pas d’adresse IP publique au système d’exploitation de la machine virtuelle.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-ip-addresses"></a>Supprimer des adresses IP

Vous pouvez supprimer des adresses IP [privées](#private) et [publiques](#public) à partir d’une interface réseau, mais une interface réseau doit toujours avoir au moins une adresse IPv4 privée.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur l’interface réseau dont vous souhaitez supprimer des adresses IP.
4. Cliquez sur **Configurations IP** dans la section **Paramètres** du panneau relatif à l’interface réseau que vous avez sélectionnée.
5. Cliquez avec le bouton droit sur une configuration IP [secondaire](#secondary) (vous ne pouvez pas supprimer la configuration [principale](#primary)) que vous souhaitez supprimer. Cliquez ensuite sur **Supprimer**, puis sur **Oui** pour confirmer la suppression. Si une ressource d’adresse IP publique est associée à la configuration, la ressource est dissociée de la configuration IP, mais la ressource n’est pas supprimée.
6. Fermez le panneau **Configurations IP**.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configurations"></a>Configurations IP

Les adresses IP [privées](#private) et (éventuellement) [publiques](#public) sont assignées à une ou plusieurs configurations IP assignées à une interface réseau. Il existe deux types de configuration IP :

### <a name="primary"></a>Primaire

Une configuration IP principale est assignée à chaque interface réseau. Une configuration IP principale :

- Dispose d’une adresse [privée](#private) [IPv4](#ipv4) qui lui est assignée. Vous ne pouvez pas assigner d’adresse [IPv6](#ipv6) privée à une configuration IP principale.
- Une adresse IPv4 [publique](#public) peut également lui être assignée. Vous ne pouvez pas assigner d’adresse IPv6 publique à une configuration IP principale ou secondaire. Toutefois, vous pouvez assigner une adresse IPv6 publique à un équilibreur de charge Azure, qui peut équilibrer la charge du trafic vers l’adresse IPv6 privée d’une machine virtuelle. Pour en savoir plus, consultez les [informations et limitations relatives à IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Secondaire

En plus d’une configuration IP principale, une interface réseau peut avoir zéro, une ou plusieurs configurations IP secondaires qui lui sont assignées. Une configuration IP secondaire :

- Doit avoir une adresse IPv4 ou IPv6 privée qui lui est assignée. Si l’adresse est de type IPv6, l’interface réseau peut uniquement avoir une seule configuration IP secondaire. Si l’adresse est de type IPv4, plusieurs configurations IP secondaires peuvent être assignées à l’interface réseau. Pour plus d’informations sur le nombre d’adresses IPv4 privées et publiques qui peuvent être assignées à une interface réseau, consultez l’article [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).  
- Une adresse IPv4 publique peut également lui être assignée si l’adresse IP privée est de type IPv4. Si l’adresse IP privée est de type IPv6, vous ne pouvez pas assigner d’adresse IPv4 ou IPv6 publique à la configuration IP. L’affectation de plusieurs adresses IP à une interface réseau est notamment utile dans les scénarios suivants :
    - D’héberger plusieurs sites web ou services avec des adresses IP différentes et des certificats SSL sur un serveur unique.
    - Une machine virtuelle joue le rôle d’appliance virtuelle réseau, comme un pare-feu ou un équilibreur de charge.
    - Possibilité d’ajouter l’une des adresses IPv4 privées pour toutes les interfaces réseau à un pool principal Azure Load Balancer. Auparavant, seule l’adresse IPv4 principale de l’interface réseau principale pouvait être ajoutée à un pool principal. Pour plus d’informations sur la façon d’équilibrer la charge entre plusieurs configurations IPv4, consultez l’article [Équilibrage de la charge entre plusieurs configurations IP](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    - Possibilité d’équilibrer la charge d’une adresse IPv6 assignée à une interface réseau. Pour en savoir plus sur l’équilibrage de charge vers une adresse IPv6 privée, consultez l’article [Vue d’ensemble du protocole IPv6 pour Azure Load Balancer](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).


## <a name="address-types"></a>Types d’adresse

Vous pouvez assigner les types d’adresse IP suivants à une [IP de configuration](#ip-configurations) :

### <a name="private"></a>Privé

Les adresses [IPv4](#ipv4) privées permettent à une machine virtuelle de communiquer avec d’autres ressources dans un réseau virtuel Azure ou d’autres réseaux connectés. Une machine virtuelle ne peut pas être en communication entrante, et elle ne peut pas non plus être en communication sortante avec une adresse [IPv6](#ipv6) privée, à une exception près. Une machine virtuelle peut communiquer avec l’équilibreur de charge Azure au travers d’une adresse IPv6. Pour en savoir plus, consultez les [informations et limitations relatives à IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations). 

Par défaut, les serveurs DHCP Azure assignent l’adresse IPv4 privée pour la [configuration IP principale](#primary) de l’interface réseau Azure à l’interface réseau située au sein du système d’exploitation de la machine virtuelle. Sauf obligation, vous ne devez jamais définir manuellement l’adresse IP d’une interface réseau au sein du système d’exploitation de la machine virtuelle. 

> [!WARNING]
> Si l’adresse IPv4 définie en tant qu’adresse IP principale d’une interface réseau au sein du système d’exploitation d’une machine virtuelle diffère de l’adresse IPv4 privée assignée à la configuration IP principale de l’interface réseau principale attachée à une machine virtuelle dans Azure, la connectivité à la machine virtuelle est interrompue.

Il est parfois nécessaire de définir manuellement l’adresse IP d’une interface réseau au sein du système d’exploitation de la machine virtuelle. Par exemple, vous devez définir manuellement les adresses IP principales et secondaires d’un système d’exploitation Windows lors de l’ajout de plusieurs adresses IP à une machine virtuelle Azure. Pour une machine virtuelle Linux, vous devrez peut-être uniquement définir manuellement les adresses IP secondaires. Consultez [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](virtual-network-multiple-ip-addresses-portal.md#os-config) pour en savoir plus. Si vous devez modifier l’adresse assignée à une configuration IP, il est recommandé de :

1. Assurez-vous que la machine virtuelle reçoit une adresse à partir des serveurs DHCP Azure. Ensuite, redéfinissez l’attribution de l’adresse IP sur la valeur DHCP au sein du système d’exploitation, puis redémarrez la machine virtuelle.
2. Arrêter (libérer) la machine virtuelle.
3. Modifier l’adresse IP pour la configuration IP dans Azure.
4. Démarrez la machine virtuelle.
5. [Configurer manuellement](virtual-network-multiple-ip-addresses-portal.md#os-config) les adresses IP secondaires dans le système d’exploitation (ainsi que l’adresse IP principale dans Windows) pour obtenir ce que vous avez défini dans Azure.
 
Si vous suivez les étapes précédentes, l’adresse IP privée assignée à l’interface réseau dans Azure reste identique à celle assignée dans le système d’exploitation de la machine virtuelle. Pour garder une trace des machines virtuelles de votre abonnement au sein desquelles vous avez défini manuellement les adresses IP dans un système d’exploitation, pensez à ajouter une [balise](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags) Azure aux machines virtuelles. Vous pouvez utiliser « attribution d’adresses IP : statique », par exemple. De cette manière, vous pouvez facilement trouver les machines virtuelles de votre abonnement pour lesquelles vous avez défini manuellement l’adresse IP dans le système d’exploitation.

En plus de permettre à une machine virtuelle de communiquer avec d’autres ressources dans les mêmes réseaux virtuels ou des réseaux virtuels connectés, une adresse IP privée permet à une machine virtuelle de communiquer en sortie vers Internet. Les connexions sortantes correspondent à une adresse de réseau source convertie par Azure en adresse IP publique non prédictible. Pour en savoir plus sur la connectivité Internet sortante d’Azure, voir [À propos de la connectivité Internet sortante d’Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Vous ne pouvez communiquer en entrée vers l’adresse IP privée d’une machine virtuelle à partir d’Internet. Si vos connexions sortantes requièrent une adresse IP publique prédictible, associez une ressource d’adresse IP publique à une interface réseau.

### <a name="public"></a>Public

Les adresses IP publiques assignées via une ressource d’adresse IP publique permettent une connectivité entrante vers une machine virtuelle à partir d’Internet. Les connexions sortantes vers Internet utilisent une adresse IP prédictible. Pour en savoir plus, consultez [Comprendre les connexions sortantes dans Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Vous pouvez affecter une adresse IP publique à une configuration IP, mais ce n’est pas obligatoire. Si vous n’assignez pas d’adresse IP publique à une machine virtuelle par l’association d’une ressource d’adresse IP publique, cette dernière peut encore communiquer en sortie vers Internet. Dans ce cas l’adresse IP privée correspond à une adresse de réseau source convertie par Azure en adresse IP publique non prédictible. Pour en savoir plus sur les ressources d’adresses IP publiques, voir [Ressource d’adresse IP publique](virtual-network-public-ip-address.md).

Le nombre d’adresses IP publiques et privées que vous pouvez assigner à une interface réseau est limité. Pour en savoir plus, consultez l’article [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

> [!NOTE]
> Azure convertit l’adresse IP privée d’une machine virtuelle en adresse IP publique. Par conséquent, le système d’exploitation de la machine virtuelle n’a pas connaissance d’adresses IP publiques qui lui sont assignées, et il est inutile d’assigner manuellement une adresse IP publique au sein du système d’exploitation.

## <a name="assignment-methods"></a>Méthodes d’affectation

Les adresses IP privées et publiques sont assignées à l’aide d’une des méthodes d’affectation suivantes :

### <a name="dynamic"></a>Dynamique

Les adresses IPv4 et (éventuellement) IPv6 privées dynamiques sont assignées par défaut. 

- **Publique uniquement** : Azure attribue l’adresse à partir d’une plage unique à chaque région Azure. Pour savoir quelles plages sont affectées à chaque région, consultez [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Plages d’adresses IP du centre de données Azure Microsoft). L’adresse peut changer lorsqu’une machine virtuelle est arrêtée (désallouée), puis redémarrée. Vous ne pouvez pas assigner d’adresse IPv6 publique à une configuration IP, quelle que soit la méthode employée.
- **Dynamique** : Azure réserve les quatre premières adresses dans chaque plage d’adresses de sous-réseau sans les assigner. Azure assigne la prochaine adresse disponible à une ressource de la plage d’adresses du sous-réseau. Par exemple, si la plage d’adresses du sous-réseau est 10.0.0.0/16, et que les adresses 10.0.0.0.4 à 10.0.0.14 (celles de .0 à .3 sont réservées), Azure assigne alors l’adresse 10.0.0.15 à la ressource. La méthode d’allocation par défaut est dynamique. Une fois assignées, les adresses IP dynamiques ne sont libérées que si l’interface réseau est supprimée, assignée à un sous-réseau différent au sein du même réseau virtuel ou bien si la méthode d’allocation devient statique et qu’une adresse IP différente est spécifiée. Par défaut, Azure assigne l’adresse dynamique assignée précédente à l’adresse statique lorsque vous modifiez la méthode d’allocation. Vous pouvez uniquement assigner une adresse IPv6 privée à l’aide de la méthode d’affectation dynamique.

### <a name="static"></a>statique

Vous pouvez (éventuellement) assigner une adresse IPv4 statique publique ou privée à une configuration IP. Vous ne pouvez pas assigner d’adresse IPv6 statique publique ou privée à une configuration IP. Pour en savoir plus sur la façon dont Azure assigne les adresses IPv4 publiques statiques, consultez l’article [Adresse IP publique](virtual-network-public-ip-address.md).

- **Publique uniquement** : Azure attribue l’adresse à partir d’une plage unique à chaque région Azure. Pour savoir quelles plages sont affectées à chaque région, consultez [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Plages d’adresses IP du centre de données Azure Microsoft). L’adresse ne change pas tant que la ressource d’adresse IP publique à laquelle elle est assignée n’est pas supprimée, ou que la méthode d’affectation dynamique n’est pas modifiée. Si la ressource d’adresse IP publique est associée à une configuration IP, elle doit être dissociée de la configuration IP avant de modifier sa méthode d’affectation.
- **Privée uniquement** : vous sélectionnez et assignez une adresse de la plage d’adresses du sous-réseau. L’adresse que vous assignez peut correspondre à n’importe quelle adresse qui se trouve au sein de la plage d’adresses de sous-réseau n’étant pas une de ses quatre premières adresses, et qui n’est pas déjà assignée à une autre ressource du sous-réseau. Les adresses statiques ne sont libérées que si l’interface réseau est supprimée. Si vous changez la méthode d’allocation en statique, Azure assigne l’adresse IP statique précédemment assignée en tant qu’adresse dynamique, même si ce n’est pas la prochaine adresse disponible dans la plage d’adresses du sous-réseau. L’adresse change aussi si l’interface réseau est assignée à un autre sous-réseau dans le même réseau virtuel. Pour l’assigner à un autre sous-réseau, vous devez d’abord modifier la méthode d’allocation statique en dynamique. Une fois que l’interface réseau est assignée à un autre sous-réseau, vous pouvez redéfinir la méthode d’allocation en statique, et assigner une adresse IP de la nouvelle plage d’adresses du sous-réseau.

## <a name="ip-address-versions"></a>Versions d’adresse IP

Vous pouvez spécifier les versions suivantes lors de l’attribution d’adresses :

### <a name="ipv4"></a>IPv4

Chaque interface réseau doit avoir une configuration IP [principale](#primary) avec une adresse [privée](#private) [IPv4](#ipv4) qui lui est assignée. Vous pouvez ajouter une ou plusieurs configurations IP [secondaires](#secondary) disposant chacune d’une adresse IPv4 privée et (éventuellement ) d’une adresse IPv4 [publique](#public).

### <a name="ipv6"></a>IPv6

Vous pouvez assigner zéro ou une adresse [IPv6](#ipv6) privée à une configuration IP secondaire d’une interface réseau. L’interface réseau ne peut pas déjà avoir de configuration IP secondaire. Vous ne pouvez pas ajouter de configuration IP avec une adresse IPv6 à l’aide du portail. Pour ajouter une configuration IP avec une adresse IPv6 privée à une interface réseau existante, utilisez PowerShell ou l’interface CLI. L’interface réseau ne peut pas être attachée à une machine virtuelle existante.

> [!NOTE]
> Bien que vous puissiez créer une interface réseau avec une adresse IPv6 à l’aide du portail, il vous est impossible de l’utiliser pour ajouter une interface réseau existante à un ordinateur virtuel nouveau ou existant. Utilisez PowerShell ou Azure CLI 2.0 pour créer une interface réseau avec une adresse IPv6 privée, puis attachez l’interface réseau lors de la création d’une machine virtuelle. Vous ne pouvez pas attacher d’interface réseau, avec une adresse IPv6 privée qui lui est assignée, à une machine virtuelle existante. Vous ne pouvez pas ajouter d’adresse IPv6 privée à la configuration IP d’une interface réseau attachée à une machine virtuelle, quel que soit l’outil utilisé (portail, CLI ou PowerShell).

Vous ne pouvez pas assigner d’adresse IPv6 publique à une configuration IP principale ou secondaire.

## <a name="skus"></a>Références (SKU)

Une adresse IP publique est créée avec la référence SKU de base ou standard.  Pour plus d’informations sur les différences entre les références SKU, consultez [Gérer les adresses IP publiques](virtual-network-public-ip-address.md).

> [!NOTE]
> Quand vous assignez une adresse IP publique de référence SKU Standard à l’interface réseau d’une machine virtuelle, vous devez explicitement autoriser le trafic prévu avec un [groupe de sécurité réseau](security-overview.md#network-security-groups). La communication avec la ressource est possible uniquement si vous créez et associez un groupe de sécurité réseau et que vous autorisez explicitement le trafic prévu.

## <a name="next-steps"></a>Étapes suivantes
Pour créer une machine virtuelle avec différentes configurations IP, consultez les articles suivants :

|Tâche|Outil|
|---|---|
|Créer une machine virtuelle avec plusieurs cartes d’interface réseau|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Créer une machine virtuelle à carte réseau unique avec plusieurs adresses IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Créer une machine virtuelle à carte réseau unique avec une adresse IPv6 privée (derrière Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modèle Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
