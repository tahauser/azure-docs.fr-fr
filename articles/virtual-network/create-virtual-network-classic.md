---
title: "Créer un réseau virtuel Azure (Classic) comprenant plusieurs sous-réseaux | Microsoft Docs"
description: "Découvrez comment créer un réseau virtuel (Classic) comprenant plusieurs sous-réseaux dans Azure."
services: virtual-network
documentationcenter: 
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
ms.date: 07/31/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f4a1562983ffa68dbd6586a2ae3a569f766387b5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Créer un réseau virtuel (Classic) comprenant plusieurs sous-réseaux

> [!IMPORTANT]
> Azure dispose de deux [modèles de déploiement différents](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour créer et utiliser des ressources : le déploiement Resource Manager et le déploiement Classic. Cet article traite du modèle de déploiement classique. Microsoft recommande de créer la plupart des réseaux virtuels via le modèle de déploiement [Resource Manager](virtual-networks-create-vnet-arm-pportal.md).

Ce didacticiel explique comment créer un réseau virtuel Azure (Classic) de base comprenant des sous-réseaux publics et privés distincts. Vous pouvez créer des ressources Azure, telles que des machines virtuelles et des services cloud, dans un sous-réseau. Les ressources créées dans des réseaux virtuels (Classic) peuvent communiquer entre elles, ainsi qu’avec des ressources d’autres réseaux connectés à un réseau virtuel.

Apprenez-en davantage sur tous les paramètres de [réseau virtuel](virtual-network-manage-network.md) et de [sous-réseau](virtual-network-manage-subnet.md).

> [!WARNING]
> Azure supprime immédiatement les réseaux virtuels (Classic) quand un [abonnement est désactivé](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Les réseaux virtuels (Classic) sont supprimés, qu’il existe ou non des ressources dans le réseau virtuel. Si vous réactivez l’abonnement par la suite, les ressources précédemment présentes dans le réseau virtuel doivent être recréées.

Vous pouvez créer un réseau virtuel (Classic) à l’aide du [portail Azure](#portal), de l’[interface de ligne de commande Azure (CLI) 1.0](#azure-cli) ou de [PowerShell](#powershell).

## <a name="portal"></a>Portail

1. Dans un navigateur Internet, accédez au [portail Azure](https://portal.azure.com). Connectez-vous à votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Cliquez sur **Créer une ressource** dans le portail.
3. Entrez *Réseau virtuel* dans la zone **Rechercher dans la place de marché** située dans le haut du volet **Nouveau** qui s’affiche. Quand la mention **Réseau virtuel** apparaît dans les résultats de la recherche, cliquez dessus.
4. Dans le volet **Réseau virtuel** qui s’affiche, repérez la zone **Sélectionnez un modèle de déploiement** et sélectionnez **Classique**, puis cliquez sur **Créer**. 
5. Dans le volet **Créer un réseau virtuel (classique)**, entrez les valeurs suivantes, puis cliquez sur **Créer** :

    |Paramètre|Valeur|
    |---|---|
    |NOM|myVnet|
    |Espace d’adressage|10.0.0.0/16|
    |Nom du sous-réseau|Public|
    |Plage d’adresses de sous-réseau|10.0.0.0/24|
    |Groupe de ressources|Laissez l’option **Créer** activée, puis entrez **myResourceGroup**.|
    |Abonnement et emplacement|Sélectionnez votre abonnement et son emplacement.

    Si vous débutez avec Azure, apprenez-en davantage sur les [groupes de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), les [abonnements](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) et les [emplacements](https://azure.microsoft.com/regions) (également appelés *régions*).
4. Dans le portail, lorsque vous créez un réseau virtuel, vous ne pouvez créer qu’un seul sous-réseau. Dans ce didacticiel, vous créez un deuxième sous-réseau après avoir créé le réseau virtuel. Vous pouvez créer ultérieurement des ressources accessibles sur Internet dans le sous-réseau **Public**. Vous pouvez également créer des ressources non accessibles à partir d’Internet dans le sous-réseau **Privé**. Pour créer le deuxième sous-réseau, dans la zone **Rechercher des ressources** en haut de la page, entrez **myVnet**. Quand la mention **myVnet** apparaît dans les résultats de recherche, cliquez dessus.
5. Dans le volet **Créer un réseau virtuel (classique)** qui s’affiche, repérez la section **PARAMÈTRES** et cliquez sur **Sous-réseaux**.
6. Dans le volet **myVnet - Sous-réseaux** qui s’affiche, cliquez sur **+Ajouter**.
7. Dans le volet **Ajouter un sous-réseau**, pour **Nom**, entrez **Privé**. Pour **Plage d’adresses**, entrez **10.0.1.0/24**.  Cliquez sur **OK**.
8. Dans le volet **myVnet - Sous-réseaux**, vous pouvez voir les sous-réseaux **Publics** et **Privés** que vous avez créés.
9. **Facultatif** : à l’issue de ce didacticiel, vous pouvez supprimer les ressources créées afin d’éviter la facturation liée à leur utilisation :
    - Dans le volet **myVnet**, cliquez sur **Vue d’ensemble**.
    - Dans le volet **myVnet**, cliquez sur l’icône **Supprimer**.
    - Pour confirmer la suppression, dans la zone **Supprimer le réseau virtuel**, cliquez sur **Oui**.

## <a name="azure-cli"></a>Azure CLI

1. Vous pouvez soit [installer et configurer Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), soit utiliser l’interface de ligne de commande au sein d’Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Pour obtenir de l’aide sur les commandes CLI, entrez `azure <command> --help`. 
2. Dans une session d’interface de ligne de commande, connectez-vous à Azure avec la commande suivante. Si vous cliquez sur **Essayez-la** dans la zone ci-dessous, Cloud Shell s’ouvre. Vous pouvez vous connecter à votre abonnement Azure sans entrer la commande suivante :

    ```azurecli-interactive
    azure login
    ```

3. Pour vous assurer de que l’interface de ligne de commande est en mode Gestion des services , entrez la commande suivante :

    ```azurecli-interactive
    azure config mode asm
    ```

4. Créez un réseau virtuel avec un sous-réseau privé :

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Créez un sous-réseau public à l’intérieur du réseau virtuel :

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Vérifiez le réseau virtuel et les sous-réseaux :

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Facultatif**: à l’issue de ce didacticiel, vous pouvez supprimer les ressources créées afin d’éviter la facturation liée à leur utilisation :

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Bien que vous ne puissiez pas spécifier un groupe de ressources pour créer un réseau virtuel (Classic) à l’aide de l’interface de ligne de commande, Azure crée le réseau virtuel dans un groupe de ressources nommé *Default-Networking*.

## <a name="powershell"></a>PowerShell

1. Installez la dernière version du module [Azure PowerShell](https://www.powershellgallery.com/packages/Azure). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Démarrez une session PowerShell.
3. Dans PowerShell, connectez-vous à Azure en entrant la commande `Add-AzureAccount`.
4. Le cas échéant, modifiez les chemin d’accès et nom de fichier suivants, puis exportez votre fichier de configuration réseau :

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Pour créer un réseau virtuel avec des sous-réseaux privés et publics, utilisez un éditeur de texte afin d’ajouter l’élément **VirtualNetworkSite** suivant au fichier de configuration réseau.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Vérifiez la totalité du [schéma du fichier de configuration réseau](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importez le fichier de configuration réseau :

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > L’importation d’un fichier de configuration réseau modifié peut entraîner des modifications des réseaux virtuels (Classic) figurant dans votre abonnement. Assurez-vous de n’ajouter que le réseau virtuel précédent et de ne modifier ou supprimer aucun réseau virtuel existant de votre abonnement. 

7. Vérifiez le réseau virtuel et les sous-réseaux :

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Facultatif**: à l’issue de ce didacticiel, vous pouvez supprimer les ressources créées afin d’éviter la facturation liée à leur utilisation. Pour supprimer le réseau virtuel, reprenez les étapes 4 à 6, cette fois en supprimant l’élément **VirtualNetworkSite** ajouté à l’étape 5.
 
> [!NOTE]
> Bien que vous ne puissiez pas spécifier un groupe de ressources pour créer un réseau virtuel (Classic) à l’aide de PowerShell, Azure crée le réseau virtuel dans un groupe de ressources nommé *Default-Networking*.

---

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir tous les paramètres de réseau virtuel et de sous-réseau, consultez [Gérer des réseaux virtuels](virtual-network-manage-network.md) et [Gérer les sous-réseaux virtuels](virtual-network-manage-subnet.md). Vous disposez de différentes options pour l’utilisation de réseaux virtuels et de sous-réseaux dans un environnement de production afin de répondre à différentes exigences.
- Pour filtrer le trafic de sous-réseau entrant et sortant, créez des [groupes de sécurité réseau](virtual-networks-nsg.md) et appliquez-les à des sous-réseaux.
- Créez une machine virtuelle [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou une machine virtuelle [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json), puis connectez-la à un réseau virtuel existant.
- Pour connecter deux réseaux virtuels situés dans un même emplacement Azure, créez une [homologation de réseaux virtuels](create-peering-different-deployment-models.md) entre les réseaux virtuels. Vous pouvez apparier un réseau virtuel (Resource Manager) à un réseau virtuel (Classic), mais vous ne pouvez pas créer une homologation entre deux réseaux virtuels (Classic).
- Connectez le réseau virtuel à un réseau local en utilisant un circuit [Passerelle VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
