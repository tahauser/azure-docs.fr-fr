---
title: "Ajouter ou supprimer des cartes réseau de machines virtuelles Azure | Documents Microsoft"
description: "Découvrez comment ajouter ou supprimer des interfaces réseau de machines virtuelles."
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
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: abe6abb942d206330e809f3aef388b846d7d7c7f
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Ajouter ou supprimer des interfaces réseau pour des machines virtuelles

Découvrez comment ajouter une interface réseau existante lors de la création d’une machine virtuelle, ou ajouter ou supprimer des interfaces réseau d’une machine virtuelle existante à l’état arrêté (désalloué). Une interface réseau permet à une machine virtuelle Azure de communiquer avec des ressources sur Internet, sur Azure et locales. Une machine virtuelle peut avoir une ou plusieurs interfaces réseau. 

Si vous avez besoin d’ajouter, de modifier ou de supprimer des adresses IP pour une interface réseau, consultez l’article [Ajouter, modifier ou supprimer des adresses IP pour des cartes réseau Azure](virtual-network-network-interface-addresses.md). Si vous avez besoin de créer, modifier ou supprimer des interfaces réseau, consultez l’article [Gérer les interfaces réseau](virtual-network-network-interface.md).

## <a name="before"></a>Avant de commencer

Avant d’effectuer une étape de cet article, quelle que soit sa section, effectuez les tâches suivantes :

- Connectez-vous au [portail](https://portal.azure.com) Azure, à Azure CLI ou à Azure PowerShell avec un compte Azure. Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, vous devez [Installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que les applets de commande Azure PowerShell installées sont celles de la version la plus récente. Pour obtenir de l’aide sur les commandes PowerShell ainsi que des exemples, entrez `get-help <command> -full`. Au lieu d’installer Azure PowerShell, vous pouvez utiliser Azure Cloud Shell. Azure Cloud Shell est un PowerShell gratuit que vous pouvez exécuter directement dans le portail Azure. Azure PowerShell y est préinstallé et configuré pour être utilisé avec votre compte. Pour utiliser Cloud Shell, cliquez sur le bouton Cloud Shell **>_** en haut du [portail](https://portal.azure.com) et sélectionnez PowerShell dans le coin supérieur gauche quand la fenêtre de l’interpréteur de commandes s’affiche.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, [installez et configurez Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que la version la plus récente d’Azure CLI est installée. Pour obtenir de l’aide sur les commandes CLI, entrez `az <command> --help`. Au lieu d’installer CLI et ses prérequis, vous pouvez utiliser Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Pour utiliser Cloud Shell, cliquez sur le bouton Cloud Shell **>_** en haut du [portail](https://portal.azure.com) et sélectionnez Bash dans le coin supérieur gauche quand la fenêtre de l’interpréteur de commandes s’affiche.

## <a name="vm-create"></a>Ajouter des interfaces réseau existantes à une nouvelle machine virtuelle

Lorsque vous créez une machine virtuelle via le portail, celui-ci crée une interface réseau avec des paramètres par défaut, et l’attache à la machine virtuelle pour vous. Vous ne pouvez pas ajouter d’interfaces réseau existantes à une nouvelle machine virtuelle, ou créer une machine virtuelle avec plusieurs interfaces réseau via le portail Azure. Vous pouvez en revanche effectuer les deux opérations à l’aide de l’interface de ligne de commande ou de PowerShell. Avant d’utiliser PowerShell ou l’interface CLI pour créer une machine virtuelle avec une interface réseau existante, familiarisez-vous cependant avec les [contraintes](#constraints). Si vous créez une machine virtuelle avec plusieurs interfaces réseau, vous devez également configurer le système d’exploitation pour les utiliser correctement une fois la machine virtuelle créée. Pour plus d’informations, consultez Configurer [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) pour plusieurs interfaces réseau.

**Commandes** : avant de créer la machine virtuelle, créez une interface réseau en utilisant la procédure décrite dans [Créer une interface réseau](virtual-network-network-interface.md#create-a-network-interface).

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Ajouter une interface réseau à une machine virtuelle existante

1. Connectez-vous au portail Azure.
2. Dans la zone de recherche en haut du portail, recherchez le nom de la machine virtuelle à laquelle vous voulez ajouter l’interface réseau, ou accédez à la machine virtuelle en cliquant sur **Tous les services**, puis **Machines virtuelles**. Une fois que vous avez trouvé la machine virtuelle, cliquez sur celle-ci. La machine virtuelle à laquelle vous voulez ajouter une interface réseau doit prendre en charge le nombre d’interfaces réseau que vous voulez y ajouter. Pour découvrir le nombre d’interfaces réseau prises en charge par chaque taille de machine virtuelle, lisez les articles sur les tailles des machines virtuelles [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Cliquez sur **Vue d’ensemble**, sous **PARAMÈTRES**. Cliquez sur **Arrêter** et attendez que la zone **État** de la machine virtuelle passe à *Arrêté (désalloué)*. 
4. Cliquez sur **Mise en réseau** sous **PARAMÈTRES**.
5. Cliquez sur **Attacher l’interface réseau**. Dans la liste des interfaces réseau existantes qui ne sont actuellement pas attachées à une autre machine virtuelle, cliquez sur l’interface réseau que vous voulez attacher. L’interface réseau que vous sélectionnez doit remplir certaines conditions : la mise en réseau accélérée ne doit pas être activée pour celle-ci, aucune adresse IPv6 ne doit lui être affectée et elle doit se trouver dans le même réseau virtuel que l’interface réseau actuellement attaché à la machine virtuelle. Si vous ne disposez pas d’une interface réseau existante, vous devez d’abord en créer une. Pour créer une interface réseau, cliquez sur **Créer une interface réseau**. Pour plus d’informations sur la création d’une interface réseau, consultez [Créer une interface réseau](virtual-network-network-interface.md#create-a-network-interface). Pour plus d’informations sur les contraintes supplémentaires qui s’appliquent à l’ajout d’interfaces réseau à des machines virtuelles, consultez [Contraintes](#constraints).
6. Cliquez sur **OK**.
7. Cliquez sur **Vue d’ensemble**, sous **PARAMÈTRES**. Cliquez sur **Démarrer** pour démarrer la machine virtuelle.
8. Configurez le système d’exploitation de la machine virtuelle de façon à utiliser correctement plusieurs interfaces réseau. Pour plus d’informations, consultez Configurer [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) pour plusieurs interfaces réseau.

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add) (référence) ou [procédure détaillée](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (référence) ou [procédure détaillée](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a> Afficher les interfaces réseau d’une machine virtuelle

Vous pouvez afficher les interfaces réseau actuellement attachées à une machine virtuelle pour découvrir la configuration de chaque interface réseau et l’adresse IP qui lui est attribuée. 

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant des autorisations associées au rôle Propriétaire, Collaborateur ou Collaborateur de réseau pour votre abonnement. Pour en savoir plus sur l’assignation de rôles à des comptes, consultez [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *machines virtuelles*. Lorsque la mention **machines virtuelles** apparaît dans les résultats de recherche, cliquez dessus.
3. Cliquez sur le nom de la machine virtuelle pour laquelle vous voulez afficher les interfaces réseau.
4. Dans la section **PARAMÈTRES** pour la machine virtuelle que vous avez sélectionnée, cliquez sur **Mise en réseau**. Pour plus d’informations sur les paramètres d’interface réseau et leur modification, consultez [Gérer les interfaces réseau](virtual-network-network-interface.md). Pour en savoir plus sur l’ajout, la modification ou la suppression d’adresses IP d’une interface réseau, consultez [Ajouter, modifier ou supprimer des adresses IP pour des cartes réseau Azure](virtual-network-network-interface-addresses.md).

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> Supprimer une interface réseau d’une machine virtuelle

1. Connectez-vous au portail Azure.
2. Dans la zone de recherche en haut du portail, recherchez le nom de la machine virtuelle de laquelle vous voulez supprimer (détacher) l’interface réseau, ou accédez à la machine virtuelle en cliquant sur **Tous les services**, puis **Machines virtuelles**. Une fois que vous avez trouvé la machine virtuelle, cliquez sur celle-ci.
3. Cliquez sur **Vue d’ensemble**, sous **PARAMÈTRES**. Cliquez sur **Arrêter** et attendez que la zone **État** de la machine virtuelle passe à *Arrêté (désalloué)*. 
4. Cliquez sur **Mise en réseau** sous **PARAMÈTRES**.
5. Cliquez sur **Détacher l’interface réseau**. Dans la liste des interfaces réseau actuellement attachées à la machine virtuelle, cliquez sur l’interface réseau que vous voulez détacher. S’il n’y a qu’une seule interface réseau, vous ne pouvez pas la détacher, car une machine virtuelle doit toujours avoir au moins une interface réseau attachée.
6. Cliquez sur **OK**.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove) (référence) ou [procédure détaillée](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (référence) ou [procédure détaillée](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="next-steps"></a>Étapes suivantes
Pour créer une machine virtuelle avec plusieurs interfaces réseau ou adresses IP, lisez les articles suivants :

**Commandes**

|Tâche|Outil|
|---|---|
|Créer une machine virtuelle avec plusieurs cartes d’interface réseau|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Créer une machine virtuelle à carte réseau unique avec plusieurs adresses IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Créer une machine virtuelle à carte réseau unique avec une adresse IPv6 privée (derrière Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modèle Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="constraints"></a>Contraintes

- Une machine virtuelle doit avoir au moins une interface réseau attachée.
- Le nombre d’interfaces réseau d’une machine virtuelle est limité par ce que la taille de machine virtuelle prend en charge. Pour plus d’informations sur le nombre d’interfaces réseau prises en charge par chaque taille de machine virtuelle, reportez-vous aux tailles des machines virtuelles [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Toutes les tailles prennent en charge au moins deux interfaces réseau.
- Actuellement, les interfaces réseau que vous ajoutez à une machine virtuelle ne peuvent pas être attachées à une autre machine virtuelle. Pour plus d’informations sur la création d’interfaces réseau, consultez [Créer une interface réseau](virtual-network-network-interface.md#create-a-network-interface).
- Auparavant, les interfaces réseau ne pouvaient être ajoutées qu’aux machines virtuelles prenant en charge plusieurs interfaces réseau et créées avec au moins deux interfaces réseau. Il n’était pas possible d’ajouter une interface réseau à une machine virtuelle créée avec une seule interface réseau, même si la taille de machine virtuelle prenait en charge plusieurs interfaces réseau. À l’inverse, vous pouviez uniquement supprimer des interfaces réseau à partir d’une machine virtuelle comportant au moins trois interfaces réseau, car les machines virtuelles créées avec au moins deux interfaces réseau devaient toujours avoir au moins deux interfaces réseau. Ces contraintes sont aujourd’hui obsolètes. Vous pouvez désormais créer des machines virtuelles avec un nombre quelconque d’interfaces réseau (dans la limite du nombre pris en charge par la taille de machine virtuelle).
- Par défaut, la première interface réseau attachée à une machine virtuelle est définie comme interface réseau *principale*. Toutes les autres interfaces réseau de la machine virtuelle sont des interfaces réseau *secondaires*.
- Vous pouvez contrôler à quelle interface réseau vous envoyez le trafic sortant, mais par défaut, tout le trafic sortant de la machine virtuelle est envoyé à l’adresse IP affectée à la configuration IP principale de l’interface réseau principale.
- Auparavant, toutes les machines virtuelles du même groupe à haute disponibilité étaient requises pour une seule ou plusieurs interfaces réseau. Des machines virtuelles comportant un nombre quelconque d’interfaces réseau peuvent désormais exister dans le même groupe à haute disponibilité, pour autant que ce nombre soit pris en charge par la taille de la machine virtuelle. Cependant, vous ne pouvez ajouter une machine virtuelle à un groupe à haute disponibilité qu’au moment de la création de celui-ci. Pour en savoir plus sur les groupes à haute disponibilité, voir [Gérer la disponibilité des machines virtuelles dans Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Si des interfaces réseau attachées à la même machine virtuelle peuvent être connectées à différents sous-réseaux d’un réseau virtuel, les interfaces réseau doivent toutes être connectées au même réseau virtuel.
- Vous pouvez ajouter n’importe quelle adresse IP pour n’importe quelle configuration IP d’une interface réseau principale ou secondaire à un pool principal Azure Load Balancer. Auparavant, seule l’adresse IP principale de l’interface réseau principale pouvait être ajoutée à un pool principal. Pour en savoir plus sur les configurations et les adresses IP, voir [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md).
- La suppression d’une machine virtuelle n’a pas pour effet de supprimer les interfaces réseau qui y sont attachées. Lorsqu’une machine virtuelle est supprimée, les interfaces réseau sont détachées de la machine virtuelle. Vous pouvez attacher les interfaces réseau à différentes machines virtuelles, ou les supprimer de celles-ci.
- Si une adresse IPv6 privée est affectée à une interface réseau, vous devez ajouter (attacher) cette interface à une machine virtuelle lors de la création de la machine virtuelle. Vous ne pouvez pas ajouter une interface réseau à laquelle une adresse IPv6 est affectée à une machine virtuelle après la création de celle-ci. Si vous ajoutez une interface réseau à laquelle une adresse IPv6 privée est affectée lors de la création d’une machine virtuelle, vous pouvez seulement ajouter cette interface réseau à la machine virtuelle, quel que soit le nombre d’interfaces réseau pris en charge par la taille de machine virtuelle. Consultez [Ajouter, modifier ou supprimer des adresses IP pour des cartes réseau Azure](virtual-network-network-interface-addresses.md) pour en savoir plus sur l’assignation d’adresses IP à des interfaces réseau.
- Comme pour IPv6, vous ne pouvez pas attacher une interface réseau pour laquelle la mise en réseau accélérée est activée à une machine virtuelle après la création de celle-ci. En outre, pour tirer parti de la mise en réseau accélérée, vous devez également effectuer certaines actions dans le système d’exploitation de la machine virtuelle. Pour plus d’informations sur la mise en réseau accélérée et d’autres contraintes liées à son utilisation, consultez « Mise en réseau accélérée pour les machines virtuelles [Windows](create-vm-accelerated-networking-powershell.md) ou [Linux](create-vm-accelerated-networking-cli.md) ».
