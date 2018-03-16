---
title: "Créer, modifier ou supprimer une interface réseau Azure | Microsoft Docs"
description: "Découvrez les interfaces réseau et apprenez à les créer, en modifier les paramètres et les supprimer."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
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
ms.openlocfilehash: 6c3fb08a43bce6193aa0094992616a290129bac8
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-change-or-delete-a-network-interface"></a>Créer, modifier ou supprimer une interface réseau

Découvrez comment créer, paramétrer et supprimer une interface réseau. Une interface réseau permet à une machine virtuelle Azure de communiquer avec des ressources Internet, Azure et locales. Lorsque vous créez une machine virtuelle par le biais du portail Azure, ce dernier crée pour vous une interface réseau avec des paramètres par défaut. Mais vous pouvez créer des interfaces réseau avec des paramètres personnalisés et ajouter une ou plusieurs interfaces réseau sur une machine virtuelle lors de sa création. Vous pouvez également modifier les paramètres d’interface réseau par défaut d’une interface réseau existante. Cet article explique comment créer une interface réseau avec des paramètres personnalisés, modifier les paramètres d’une interface réseau (par exemple, l’attribution d’un filtre réseau pour les groupes de sécurité réseau, l’attribution d’un sous-réseau, les paramètres de serveur DNS et le transfert IP) ainsi que supprimer une interface réseau.

Si vous devez ajouter, modifier ou supprimer des adresses IP pour une interface réseau, consultez l’article [Ajouter, modifier ou supprimer des adresses IP pour des cartes réseau Azure](virtual-network-network-interface-addresses.md). Si vous devez ajouter ou supprimer des interfaces réseau sur des machines virtuelles, lisez l’article [Ajouter ou supprimer des interfaces réseau](virtual-network-network-interface-vm.md).


## <a name="before-you-begin"></a>Avant de commencer

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez le portail, ouvrez https://portal.azure.com, puis connectez-vous avec votre compte Azure.
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, exécutez-les dans l’[Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Ce didacticiel requiert le module Azure PowerShell version 5.2.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Ce didacticiel requiert Azure CLI version 2.0.26 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI localement, vous devez également exécuter `az login` pour créer une connexion avec Azure.

## <a name="create-a-network-interface"></a>Créer une interface réseau

Lorsque vous créez une machine virtuelle par le biais du portail Azure, ce dernier crée pour vous une interface réseau avec des paramètres par défaut. Si vous préférez spécifier vous-même tous les paramètres de votre interface réseau, vous pouvez créer une interface réseau avec des paramètres personnalisés, puis l’attacher à une machine virtuelle lors de la création de cette dernière (à l’aide de PowerShell ou d’Azure CLI). Vous pouvez également créer une interface réseau et l’ajouter à une machine virtuelle existante (à l’aide de PowerShell ou d’Azure CLI). Pour savoir comment créer une machine virtuelle avec une interface réseau existante, ou comment ajouter ou supprimer des interfaces réseau sur des machines virtuelles existantes, consultez l’article [Ajouter ou supprimer des interfaces réseau](virtual-network-network-interface-vm.md). Avant de créer une interface réseau, vous devez disposer d’un [réseau virtuel](manage-virtual-network.md#create-a-virtual-network) dans les mêmes emplacement et abonnement que ceux dans lesquels vous créez l’interface.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur **+ Ajouter**.
4. Dans le panneau **Créer l’interface réseau** qui s’affiche, entrez ou sélectionnez des valeurs pour les paramètres suivants, puis cliquez sur **Créer**:

    |Paramètre|Requis ?|Détails|
    |---|---|---|
    |NOM|OUI|Le nom doit être unique au sein du groupe de ressources que vous avez sélectionné. Au fil du temps, vous accumulerez probablement plusieurs interfaces réseau dans votre abonnement Azure. Pour obtenir des suggestions lors de la création d’une convention d’affectation de noms et ainsi faciliter la gestion de plusieurs interfaces réseau, consultez [Conventions d’affectation de noms](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Il est impossible de modifier le nom une fois que l’interface réseau a été créée.|
    |Réseau virtuel|OUI|Sélectionnez le réseau virtuel pour l’interface réseau. Vous pouvez uniquement attribuer une interface réseau à un réseau virtuel qui existe dans le même abonnement et le même emplacement que l’interface réseau. Après la création d’une interface réseau, vous ne pouvez pas modifier le réseau virtuel auquel elle est assignée. La machine virtuelle à laquelle vous ajoutez l’interface réseau doit également exister dans le même emplacement et le même abonnement que l’interface réseau.|
    |Sous-réseau|OUI|Sélectionnez un sous-réseau dans le réseau virtuel que vous avez sélectionné. Vous pouvez modifier le sous-réseau auquel l’interface réseau est assignée après sa création.|
    |Affectation d’adresses IP privées|OUI| Dans ce paramètre, vous choisissez la méthode d’affectation de l’adresse IPv4. Choisissez parmi les méthodes d’affectation suivantes : **Dynamique :** lorsque vous sélectionnez cette option, Azure affecte automatiquement une adresse disponible de l’espace d’adressage du sous-réseau sélectionné. **Statique :** lorsque vous sélectionnez cette option, vous devez affecter manuellement une adresse IP disponible de l’espace d’adressage du sous-réseau sélectionné. Les adresses statiques et dynamiques ne changent que si vous les modifiez ou si l’interface réseau est supprimée. Vous pouvez modifier la méthode d’affectation après la création de l’interface réseau. Le serveur DHCP Azure assigne cette adresse à l’interface réseau au sein du système d’exploitation de la machine virtuelle.|
    |Groupe de sécurité réseau|Non | Conservez la valeur **Aucune**, puis sélectionnez un [groupe de sécurité réseau](virtual-networks-nsg.md) ou [créez un groupe de sécurité réseau](virtual-networks-create-nsg-arm-pportal.md). Les groupes de sécurité réseau vous permettent de filtrer le trafic réseau entrant et sortant d’une interface réseau. Vous pouvez appliquer zéro ou un groupe de sécurité réseau à une interface réseau. Zéro ou un groupe de sécurité réseau peut également être appliqué au sous-réseau auquel l’interface réseau est assignée. Lorsqu’un groupe de sécurité réseau est appliqué à une interface réseau et au sous-réseau auquel l’interface réseau est assignée, des résultats inattendus peuvent se produire. Pour résoudre les problèmes relatifs aux groupes de sécurité réseau appliqués aux interfaces réseau et aux sous-réseaux, consultez l’article [Résoudre les problèmes relatifs aux groupes de sécurité réseau](virtual-network-nsg-troubleshoot-portal.md#nsg).|
    |Abonnement|OUI|Sélectionnez l’un de vos [abonnements](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) Azure. La machine virtuelle à laquelle vous attachez une interface réseau et le réseau virtuel auquel vous la connectez doivent être dans le même abonnement.|
    |Adresse IP privée (IPv6)|Non | Si vous cochez cette case, une adresse IPv6 est assignée à l’interface réseau, en plus de l’adresse IPv4 assignée à l’interface réseau. Consultez la section [IPv6](#IPv6) de cet article pour obtenir des informations importantes sur l’utilisation du protocole IPv6 avec des interfaces réseau. Vous ne pouvez pas sélectionner de méthode d’attribution pour l’adresse IPv6. Si vous choisissez d’assigner une adresse IPv6, c’est la méthode dynamique qui est sélectionnée.
    |Nom IPv6 (apparaît uniquement lorsque la case **Adresse IP privée (IPv6)** est cochée) |Oui, si la case **Adresse IP privée (IPv6)** est cochée.| Ce nom est assigné à une configuration IP secondaire pour l’interface réseau. Pour en savoir plus sur les configurations IP, consultez la section [Afficher les paramètres d’interface réseau](#view-network-interface-settings) de cet article.|
    |Groupe de ressources|OUI|Sélectionnez un [groupe de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) ou créez-en un. Une interface réseau peut se trouver ou non dans le même groupe de ressources que celui de la machine virtuelle à laquelle vous l’attachez ou du réseau virtuel auquel vous la connectez.|
    |Lieu|OUI|La machine virtuelle à laquelle vous attachez une interface réseau et le réseau virtuel auquel vous la connectez doivent être dans le même [emplacement](https://azure.microsoft.com/regions), également appelé région.|

Le portail ne permet pas d’assigner une adresse IP publique à l’interface réseau lorsque vous la créez, bien qu’il crée une adresse IP publique et l’assigne à une interface réseau lorsque vous créez une machine virtuelle à l’aide du portail. Pour savoir comment ajouter une adresse IP publique à l’interface réseau après sa création, consultez l’article [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md). Si vous souhaitez créer une interface réseau avec une adresse IP publique, vous devez utiliser l’interface de ligne de commande ou PowerShell pour la créer.

>[!Note]
> Azure attribue une adresse MAC à l’interface réseau une fois que l’interface réseau est attachée à une machine virtuelle et que la machine virtuelle est démarrée pour la première fois. Vous ne pouvez pas spécifier l’adresse MAC qu’Azure assigne à l’interface réseau. L’adresse MAC reste assignée à l’interface réseau jusqu’à ce que l’interface réseau soit supprimée ou que l’adresse IP privée assignée à la configuration IP principale de l’interface réseau principale soit modifiée. Pour en savoir plus sur les adresses IP et les configurations IP, consultez [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md).

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-network-interface-settings"></a>Afficher les paramètres d’interface réseau

Vous pouvez afficher et modifier la plupart des paramètres d’une interface réseau après sa création.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur l’interface réseau que vous souhaitez afficher ou dont vous souhaitez modifier les paramètres.
4. Les paramètres suivants sont répertoriés dans le panneau qui s’affiche pour l’interface réseau que vous avez sélectionnée :
    - **Vue d’ensemble :** fournit des informations sur l’interface réseau, comme les adresses IP assignées, le réseau virtuel/sous-réseau auquel l’interface réseau est assignée, et la machine virtuelle à laquelle l’interface réseau est attachée (le cas échéant). L’illustration suivante montre les paramètres de vue d’ensemble d’une interface réseau nommée **mywebserver256** : ![Vue d’ensemble de l’interface réseau](./media/virtual-network-network-interface/nic-overview.png) Vous pouvez déplacer une interface réseau vers un autre groupe de ressources ou abonnement en cliquant sur (**Modifier**) en regard du **Groupe de ressources** ou du **Nom d’abonnement**. Si vous déplacez l’interface réseau, vous devez également déplacer toutes les ressources liées à cette interface réseau. Par exemple, si l’interface réseau est attachée à une machine virtuelle, vous devez également déplacer la machine virtuelle et les autres ressources relatives à la machine virtuelle. Pour déplacer une interface réseau, consultez l’article [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). Cet article répertorie les conditions préalables et explique comment déplacer des ressources à l’aide du portail Azure, de PowerShell ou d’Azure CLI.
    - **Configurations IP :** les adresses IPv4 et IPv6 publiques et privées, assignées à des configurations IP, sont répertoriées ici. Si une adresse IPv6 est assignée à une configuration IP, l’adresse n’est pas affichée. Pour en savoir plus sur les configurations IP et pour apprendre à ajouter et supprimer des adresses IP, consultez l’article [Configurer des adresses IP pour une interface réseau Azure](virtual-network-network-interface-addresses.md). Le transfert IP et l’affectation de sous-réseau sont également configurés dans cette section. Pour en savoir plus sur ces paramètres, lisez les sections [Activer et désactiver le transfert IP](#enable-or-disable-ip-forwarding) et [Modifier l’affectation de sous-réseau](#change-subnet-assignment) de cet article.
    - **Serveurs DNS :** vous pouvez spécifier à quel serveur DNS une interface réseau est assignée par le biais des serveurs DHCP Azure. L’interface réseau peut hériter du paramètre provenant du réseau virtuel auquel l’interface réseau est assignée, ou présenter un paramètre personnalisé pour remplacer le paramètre du réseau virtuel auquel elle est assignée. Pour modifier ce qui est affiché, procédez de la manière décrite dans la section [Modifier les serveurs DNS](#change-dns-servers) de cet article.
    - **Groupe de sécurité réseau :** indique le groupe de sécurité réseau (éventuel) associé à l’interface réseau. Un groupe de sécurité réseau contient des règles entrantes et sortantes pour filtrer le trafic réseau transitant par l’interface réseau. Si un groupe de sécurité réseau est associé à l’interface réseau, le nom du groupe de sécurité réseau associé s’affiche. Pour modifier ce qui est affiché, suivez les étapes de l’article [Manage network security group associations](virtual-network-manage-nsg-arm-portal.md#manage-associations) (Gérer les associations de groupes de sécurité réseau).
    - **Propriétés :** affiche des paramètres clés concernant l’interface réseau, dont son adresse MAC (vide si l’interface réseau n’est pas attachée à une machine virtuelle), ainsi que l’abonnement dans lequel elle existe.
    - **Obtenir les règles de sécurité effectives :** les règles de sécurité sont répertoriées si l’interface réseau est attachée à une machine virtuelle en cours d’exécution, tandis qu’un groupe de sécurité réseau est associé à l’interface réseau et/ou au sous-réseau auquel il est assigné. Pour en savoir plus sur ce qui est affiché, listez l’article [Résoudre les problèmes relatifs aux groupes de sécurité réseau](virtual-network-nsg-troubleshoot-portal.md#nsg). Pour en savoir plus sur les groupes de sécurité réseau, consultez l’article [Groupes de sécurité réseau](virtual-networks-nsg.md).
    - **Obtenir les routages effectifs :** les itinéraires sont répertoriés si l’interface réseau est attachée à une machine virtuelle en cours d’exécution. Les itinéraires sont une combinaison d’itinéraires par défaut d’Azure, d’itinéraires définis par l’utilisateur et de tous les itinéraires BGP existants pour le sous-réseau auquel l’interface réseau est assignée. Pour en savoir plus sur ce qui est affiché, consultez l’article [Résoudre les problèmes relatifs aux itinéraires](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface). Pour plus d’informations sur les itinéraires par défaut Azure et les itinéraires définis par l’utilisateur, voir [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md).
    - **Paramètres courants d’Azure Resource Manager :** pour en savoir plus sur les paramètres courants d’Azure Resource Manager, consultez les articles [Journal d’activité](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Contrôle d’accès (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Balises](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Verrous](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Script Automation](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

**Commandes**

Si une adresse IPv6 est assignée à une interface réseau, la sortie PowerShell retourne le fait que l’adresse est assignée, mais elle ne retourne pas l’adresse assignée. De même, l’interface CLI retourne le fait que l’adresse est assignée, mais elle retourne *Null* pour la sortie de l’adresse.

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_list) pour afficher les interfaces réseau dans l’abonnement ; [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_show) pour afficher les paramètres d’une interface réseau|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) pour afficher les interfaces réseau dans l’abonnement ou afficher les paramètres d’une interface réseau|

## <a name="change-dns-servers"></a>Modifier les serveurs DNS

Le serveur DHCP Azure assigne le serveur DNS à l’interface réseau au sein du système d’exploitation de la machine virtuelle. Le serveur DNS assigné correspond au paramètre de serveur DNS d’une interface réseau. Pour en savoir plus sur les paramètres de résolution de noms d’une interface réseau, consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](virtual-networks-name-resolution-for-vms-and-role-instances.md). L’interface réseau peut hériter les paramètres du réseau virtuel, ou utiliser ses propres paramètres uniques qui remplacent le paramètre pour le réseau virtuel.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur l’interface réseau que vous souhaitez afficher ou dont vous souhaitez modifier les paramètres.
4. Dans le panneau de l’interface réseau que vous avez sélectionnée, cliquez sur **Serveurs DNS** sous **Paramètres**.
5. Cliquez sur l’une des deux options suivantes :
    - **Hériter de VNet (par défaut)** : choisissez cette option afin d’hériter le paramètre de serveur DNS défini pour le réseau virtuel auquel l’interface réseau est assignée. Au niveau du réseau virtuel, soit un serveur DNS personnalisé, soit le serveur DNS fourni par Azure est défini. Le serveur DNS fourni par Azure peut résoudre les noms d’hôtes pour les ressources assignées au même réseau virtuel. Le nom de domaine complet doit être utilisé pour résoudre les ressources assignées à différents réseaux virtuels.
    - **Personnalisé** : vous pouvez configurer votre propre serveur DNS pour résoudre les noms sur plusieurs réseaux virtuels. Entrez l’adresse IP du serveur que vous souhaitez utiliser comme serveur DNS. L’adresse de serveur DNS que vous spécifiez est assignée uniquement à cette interface réseau et remplace tout paramètre DNS du réseau virtuel auquel l’interface réseau est assignée.
6. Cliquez sur **Enregistrer**.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="enable-or-disable-ip-forwarding"></a>Activer et désactiver le transfert IP

Le transfert IP permet à la machine virtuelle à laquelle une interface réseau est attachée de :
- Recevoir le trafic réseau qui n’est pas destiné à l’une des adresses IP assignées à l’une des configurations IP assignées à l’interface réseau.
- Envoyer le trafic réseau avec une adresse IP source différente de celle assignée à l’une des configurations IP d’une interface réseau.

Le paramètre doit être activé pour chaque interface réseau attachée à la machine virtuelle qui reçoit le trafic que la machine virtuelle doit transférer. Une machine virtuelle peut transférer le trafic, qu’une ou plusieurs interfaces réseau y soient attachées. Bien que le transfert IP soit un paramètre Azure, la machine virtuelle doit également exécuter une application capable de transférer le trafic, comme une application de pare-feu, d’optimisation WAN et d’équilibrage de charge. Lorsqu’une machine virtuelle exécute des applications réseau, la machine virtuelle est communément appelée appliance virtuelle réseau. Vous pouvez afficher une liste d’appliances virtuelles réseau prêtes au déploiement sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Le transfert IP est généralement utilisé avec les routages définis par l’utilisateur. Pour en savoir plus sur les itinéraires définis par l’utilisateur, consultez l’article [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md).

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur l’interface réseau pour laquelle vous voulez activer ou désactiver le transfert IP.
4. Dans le panneau relatif à l’interface réseau que vous avez sélectionnée, cliquez sur **Configurations IP** dans la section **Paramètres**.
5. Cliquez sur **Activé** ou **Désactivé** (paramètre par défaut) pour modifier le paramètre.
6. Cliquez sur **Enregistrer**.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet-assignment"></a>Modifier l’affectation de sous-réseau

Vous pouvez modifier le sous-réseau, mais pas le réseau virtuel auquel une interface réseau est assignée.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur l’interface réseau que vous souhaitez afficher ou dont vous souhaitez modifier les paramètres.
4. Dans le panneau de l’interface réseau que vous avez sélectionnée, cliquez sur **Configurations IP** sous **Paramètres**. Si des adresses IP privées affectées à des configurations IP répertoriées sont assorties de la mention **(Statique)**, vous devez modifier la méthode d’affectation d’adresse IP en dynamique en procédant comme suit. Toutes les adresses IP privées doivent être assignées à l’aide de la méthode d’affectation dynamique afin de modifier l’affectation de sous-réseau pour l’interface réseau. Si les adresses sont affectées à l’aide de la méthode dynamique, passez à l’étape cinq. Si des adresses IPv4 sont assignées à l’aide de la méthode d’affectation statique, procédez comme suit pour basculer vers la méthode d’affectation dynamique :
    - Cliquez sur la configuration IP pour laquelle vous souhaitez modifier la méthode d’affectation d’adresse IPv4 dans la liste des configurations IP.
    - Dans le panneau qui s’affiche pour la configuration IP, cliquez sur **Dynamique** pour la méthode **Affectation**. Vous ne pouvez pas assigner d’adresse IPv6 avec la méthode d’affectation statique.
    - Cliquez sur **Enregistrer**.
5. Sélectionnez le sous-réseau auquel vous souhaitez connecter l’interface réseau à partir de la liste déroulante **Sous-réseau**.
6. Cliquez sur **Enregistrer**. De nouvelles adresses dynamiques sont affectées à partir de la plage d’adresses de sous-réseau pour le nouveau sous-réseau. Après avoir assigné l’interface réseau à un nouveau sous-réseau, vous pouvez assigner une adresse IPv4 statique à partir de la nouvelle plage d’adresses de sous-réseau si vous le souhaitez. Pour en savoir plus sur l’ajout, la modification et la suppression d’adresses IP concernant une interface réseau, consultez [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md).

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-a-network-interface"></a>Supprimer une interface réseau

Vous pouvez supprimer une interface réseau tant qu’elle n’est pas attachée à une machine virtuelle. Si elle est attachée à une machine virtuelle, vous devez d’abord placer la machine virtuelle en état Arrêté (libéré), puis détacher l’interface réseau de la machine virtuelle, avant de pouvoir supprimer l’interface réseau. Pour détacher une interface réseau d’une machine virtuelle, procédez de la manière décrite dans la section [Supprimer une interface réseau d’une machine virtuelle](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm) de l’article [Ajouter ou supprimer des interfaces réseau](virtual-network-network-interface-vm.md). La suppression d’une machine virtuelle détache toutes les interfaces réseau attachées, mais elle ne supprime pas les interfaces réseau.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Cliquez avec le bouton droit sur l’interface réseau à supprimer, puis cliquez sur **Supprimer**.
4. Cliquez sur **Oui** pour confirmer la suppression de l’interface réseau.

Lorsque vous supprimez une interface réseau, toutes les adresses MAC ou IP qui lui sont assignées sont libérées.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Étapes suivantes
Pour créer une machine virtuelle avec plusieurs interfaces réseau ou adresses IP, lisez les articles suivants :

**Commandes**

|Tâche|Outil|
|---|---|
|Créer une machine virtuelle avec plusieurs cartes d’interface réseau|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Créer une machine virtuelle à carte réseau unique avec plusieurs adresses IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Créer une machine virtuelle à carte réseau unique avec une adresse IPv6 privée (derrière Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modèle Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
