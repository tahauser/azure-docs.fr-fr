---
title: "Créer une homologation de réseaux virtuels Azure - Modèles de déploiement différents - Même abonnement | Microsoft Docs"
description: "Découvrez comment créer une homologation de réseaux virtuels entre des réseaux virtuels créés via des modèles de déploiement Azure différents qui existent dans le même abonnement."
services: virtual-network
documentationcenter: 
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
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 033d696f0d219fef66b4acd523f28a35afcf9929
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Créer une homologation de réseaux virtuels Azure - Modèles de déploiement différents, même abonnement 

Dans ce didacticiel, vous allez découvrir comment créer une homologation de réseaux virtuels entre des réseaux virtuels créés par le biais de modèles de déploiement différents. Les deux réseaux virtuels doivent se trouver dans le même abonnement. Homologuer deux réseaux virtuels permet aux ressources de différents réseaux virtuels de communiquer entre elles avec la même bande passante et latence, comme si les ressources se trouvaient sur le même réseau virtuel. En savoir plus sur l’[homologation de réseaux virtuels](virtual-network-peering-overview.md). 

Les étapes de création d’une homologation de réseaux virtuels sont différentes, selon que les réseaux virtuels sont dans le même abonnement ou dans des abonnements différents, et selon le [modèle de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) utilisé pour les créer. Découvrez comment créer une homologation de réseaux virtuels dans d’autres scénarios en cliquant sur le scénario souhaité dans le tableau suivant :

|Modèle de déploiement Azure  | Abonnement Azure  |
|--------- |---------|
|[Tous deux Resource Manager](virtual-network-create-peering.md) |Identique|
|[Tous deux Resource Manager](create-peering-different-subscriptions.md) |Différent|
|[Un modèle Resource Manager, un modèle classique](create-peering-different-deployment-models-subscriptions.md) |Différent|

Vous ne pouvez pas créer d’homologation de réseaux virtuels entre deux réseaux virtuels déployés via le modèle de déploiement classique. Si vous avez besoin de connecter des réseaux virtuels tous deux créés par le biais du modèle de déploiement classique, vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure. 

Ce didacticiel permet d’homologuer des réseaux virtuels situés dans la même région. La capacité à homologuer des réseaux virtuels dans des régions différentes est une fonctionnalité en préversion pour le moment. Effectuez les étapes indiquées dans [S’inscrire à l’homologation de réseaux virtuels mondiaux](#register) avant d’essayer d’homologuer des réseaux virtuels dans des régions différentes, sans quoi l’homologation échoue. La capacité à connecter des réseaux virtuels situés dans des régions différentes avec une [passerelle VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure est généralement disponible et ne nécessite pas d’inscription.

Vous pouvez utiliser le [portail Azure](#portal), [l’interface de ligne de commande](#cli) (CLI) Azure, Azure [PowerShell](#powershell) ou un [modèle Azure Resource Manager](#template) pour créer une homologation de réseaux virtuels. Cliquez sur les liens des outils précédents pour accéder directement à la procédure permettant de créer une homologation de réseaux virtuels à l’aide de l’outil de votre choix.

## <a name="create-peering---azure-portal"></a>Créer une homologation - portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com). Le compte auquel vous vous connectez doit avoir les autorisations nécessaires pour créer une homologation de réseaux virtuels. Consultez la section [Autorisations](#permissions) de cet article pour plus d’informations.
2. Cliquez sur **+ Nouveau**, puis sur **Mise en réseau** et **Réseau virtuel**.
3. Dans le panneau **Créer un réseau virtuel**, entrez ou sélectionnez les valeurs des paramètres suivants, puis cliquez sur **Créer** :
    - **Nom** : *myVnet1*
    - **Espace d’adressage** : *10.0.0.0/16*
    - **Nom du sous-réseau** : *par défaut*
    - **Espace d’adressage de sous-réseau** : *10.0.0.0/24*
    - **Abonnement** : sélectionnez votre abonnement
    - **Groupe de ressources** : sélectionnez **Créer** et entrez *myResourceGroup*
    - **Emplacement** : *États-Unis de l’Est*
4. Cliquez sur **+ Nouveau**. Dans le champ **Rechercher dans le marketplace**, tapez *Réseau virtuel*. Quand la mention **Réseau virtuel** apparaît dans les résultats de la recherche, cliquez dessus. 
5. Dans le panneau **Réseau virtuel**, sélectionnez **Classique** dans la zone **Sélectionnez un modèle de déploiement**, puis cliquez sur **Créer**.
6. Dans le panneau **Créer un réseau virtuel**, entrez ou sélectionnez les valeurs des paramètres suivants, puis cliquez sur **Créer** :
    - **Nom** : *myVnet2*
    - **Espace d’adressage** : *10.1.0.0/16*
    - **Nom du sous-réseau** : *par défaut*
    - **Espace d’adressage de sous-réseau** : *10.1.0.0/24*
    - **Abonnement** : sélectionnez votre abonnement
    - **Groupe de ressources** : sélectionnez **Use existing** (Utiliser existant), puis *myResourceGroup*
    - **Emplacement** : *États-Unis de l’Est*
7. Dans le champ **Rechercher des ressources** située en haut du portail, tapez *myResourceGroup*. Cliquez sur **myResourceGroup** quand il apparaît dans les résultats de la recherche. Un panneau s’affiche pour le groupe de ressources **myresourcegroup**. Le groupe de ressources contient les deux réseaux virtuels créés dans les étapes précédentes.
8. Cliquez sur **myVNet1**.
9. Dans le panneau **myVnet1** qui s’affiche, cliquez sur **Homologations** dans la liste verticale des options sur le côté gauche du panneau.
10. Dans le panneau **myVnet1 - Homologations** qui s’est affiché, cliquez sur **+ Ajouter**.
11. Dans le panneau **Ajouter l’homologation** qui s’affiche, entrez ou sélectionnez les options suivantes et cliquez sur **OK** :
     - **Nom** : *myVnet1ToMyVnet2*
     - **Modèle de déploiement de réseau virtuel** : sélectionnez **Classique**. 
     - **Abonnement** : sélectionnez votre abonnement
     - **Réseau virtuel** : cliquez sur **Choisir un réseau virtuel**, puis sur **myVnet2**.
     - **Autoriser l’accès au réseau virtuel :** vérifiez que l’option **Activé** est sélectionnée.
    Il n’y aucun autre paramètre utilisé dans ce didacticiel. Pour en savoir plus sur tous les paramètres d’homologation, consultez [Create, change, or delete a virtual network peering](virtual-network-manage-peering.md#create-a-peering) (Créer, modifier ou supprimer une homologation de réseaux virtuels).
12. Après avoir cliqué sur **OK** à l’étape précédente, le panneau **Ajouter l’homologation** se ferme et vous pouvez à nouveau voir le panneau **myVnet1 - Homologations**. Après quelques secondes, l’homologation que vous avez créée apparaît dans le panneau. **Connecté** est indiqué dans la colonne **ÉTAT D’APPAIRAGE** pour l’homologation **myVnet1ToMyVnet2** que vous avez créée.

    L’homologation est maintenant établie. Les ressources Azure que vous créez dans un réseau virtuel sont désormais en mesure de communiquer entre elles via leurs adresses IP. Si vous utilisez la résolution de noms Azure par défaut pour les réseaux virtuels, les ressources dans les réseaux virtuels ne sont pas en mesure de résoudre les noms dans les réseaux virtuels. Si vous souhaitez résoudre les noms dans les réseaux virtuels d’une homologation, vous devez créer votre propre serveur DNS. Apprenez à configurer la [résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
13. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
14. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-portal) de cet article.

## <a name="cli"></a>Créer une homologation - interface de ligne de commande Azure

1. [Installez](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure CLI 1.0 pour créer le réseau virtuel (classique).
2. Ouvrez une session de commande et connectez-vous à Azure à l’aide de la commande `azure login`.
3. Exécutez l’interface CLI en mode Gestion des services en entrant la commande `azure config mode asm`.
4. Entrez la commande suivante pour créer le réseau virtuel (classique) :
 
    ```azurecli
    azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

5. Créez un groupe de ressources et un réseau virtuel (Resource Manager). Vous pouvez utiliser l’interface CLI 1.0 ou 2.0 ([installer](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)). Dans ce didacticiel, l’interface CLI 2.0 est utilisée pour créer le réseau virtuel (Resource Manager), étant donné que la version 2.0 doit être utilisée pour créer l’homologation. Exécutez le script CLI Batch suivant à partir de votre ordinateur local avec CLI 2.0.4 ou version ultérieure installé. Pour en savoir plus les options d’exécution de scripts CLI Bash sur un client Windows, consultez [Utilisation de l’interface de ligne de commande Azure sur Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Vous pouvez également exécuter le script à l’aide d’Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Cliquez sur le bouton **Try it** dans le script qui suit, ce qui appelle un Cloud Shell vous permettant de vous connecter à votre compte Azure. Pour exécuter le script, cliquez sur le bouton **Copier**, collez le contenu dans votre Cloud Shell, puis appuyez sur `Enter`.

    ```azurecli-interactive
    #!/bin/bash

    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus

    # Create the virtual network (Resource Manager).
    az network vnet create \
      --name myVnet1 \
      --resource-group myResourceGroup \
      --location eastus \
      --address-prefix 10.0.0.0/16
    ```

6. Créez une homologation de réseaux virtuels entre les deux réseaux virtuels créés par le biais des modèles de déploiement différents. Copiez le script suivant dans un éditeur de texte sur votre PC. Remplacez `<subscription id>` par votre ID d’abonnement. Si vous ne connaissez pas votre ID d’abonnement, entrez la commande `az account show`. La valeur de **id** dans la sortie est votre ID d’abonnement. Collez le script modifié dans votre session CLI, puis appuyez sur `Enter`.

    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group myResourceGroup \
      --name myVnet1 \
      --query id --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
      --allow-vnet-access
    ```
7. Après l’exécution du script, passez en revue l’homologation pour le réseau virtuel (Resource Manager). Copiez la commande suivante, collez-la dans votre session CLI, puis appuyez sur `Enter` :

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    La sortie indique **Connecté** dans la colonne **État d’appairage**. 

    Les ressources Azure que vous créez dans un réseau virtuel sont désormais en mesure de communiquer entre elles via leurs adresses IP. Si vous utilisez la résolution de noms Azure par défaut pour les réseaux virtuels, les ressources dans les réseaux virtuels ne sont pas en mesure de résoudre les noms dans les réseaux virtuels. Si vous souhaitez résoudre les noms dans les réseaux virtuels d’une homologation, vous devez créer votre propre serveur DNS. Apprenez à configurer la [résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
8. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
9. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-cli) de cet article.

## <a name="powershell"></a>Créer une homologation - PowerShell

1. Installez la dernière version des modules PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) et [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Démarrez une session PowerShell.
3. Dans PowerShell, connectez-vous à Azure en entrant la commande `Add-AzureAccount`.
4. Pour créer un réseau virtuel (classique) avec PowerShell, vous devez créer un fichier de configuration réseau ou en modifier un existant. Pour découvrir comment exporter, mettre à jour et importer des fichiers de configuration réseau, consultez [cet article](virtual-networks-using-network-configuration-file.md). Le fichier doit inclure l’élément **VirtualNetworkSite** suivant pour le réseau virtuel utilisé dans ce didacticiel :

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > L’importation d’un fichier de configuration réseau modifié peut entraîner des modifications des réseaux virtuels (Classic) figurant dans votre abonnement. Assurez-vous de n’ajouter que le réseau virtuel précédent et de ne modifier ou supprimer aucun réseau virtuel existant de votre abonnement. 
5. Connectez-vous à Azure pour créer le réseau virtuel (Resource Manager) en entrant la commande `login-azurermaccount`. Le compte auquel vous vous connectez doit avoir les autorisations nécessaires pour créer une homologation de réseaux virtuels. Consultez la section [Autorisations](#permissions) de cet article pour plus d’informations.
6. Créez un groupe de ressources et un réseau virtuel (Resource Manager). Copiez le script, collez-le dans PowerShell, puis appuyez sur `Enter`.

    ```powershell
    # Create a resource group.
      New-AzureRmResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Créez une homologation de réseaux virtuels entre les deux réseaux virtuels créés par le biais des modèles de déploiement différents. Copiez le script suivant dans un éditeur de texte sur votre PC. Remplacez `<subscription id>` par votre ID d’abonnement. Si vous ne connaissez pas votre ID d’abonnement, entrez la commande `Get-AzureRmSubscription` pour l’afficher. La valeur de **id** dans la sortie retournée est votre ID d’abonnement. Pour exécuter le script, copiez le script modifié à partir de votre éditeur de texte, cliquez avec le bouton droit dans votre session PowerShell, puis appuyez sur `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Après l’exécution du script, passez en revue l’homologation pour le réseau virtuel (Resource Manager). Copiez la commande suivante, collez-la dans votre session PowerShell, puis appuyez sur `Enter` :

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    La sortie indique **Connecté** dans la colonne **État d’appairage**.

    Les ressources Azure que vous créez dans un réseau virtuel sont désormais en mesure de communiquer entre elles via leurs adresses IP. Si vous utilisez la résolution de noms Azure par défaut pour les réseaux virtuels, les ressources dans les réseaux virtuels ne sont pas en mesure de résoudre les noms dans les réseaux virtuels. Si vous souhaitez résoudre les noms dans les réseaux virtuels d’une homologation, vous devez créer votre propre serveur DNS. Apprenez à configurer la [résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

9. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
10. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-powershell) de cet article.
 
## <a name="permissions"></a>Autorisations

Les comptes que vous utilisez pour créer une homologation de réseaux virtuels doivent avoir les autorisations ou le rôle nécessaires. Par exemple, si vous homologuez deux réseaux virtuels nommés myVnet1 et myVnet2, votre compte doit avoir le rôle ou les autorisations minimaux suivants pour chaque réseau virtuel :
    
|Réseau virtuel|Modèle de déploiement|Rôle|Autorisations|
|---|---|---|---|
|myVnet1|Gestionnaire de ressources|[Collaborateur de réseau](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classique|[Collaborateur de réseau classique](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|myVnet2|Gestionnaire de ressources|[Collaborateur de réseau](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classique|[Collaborateur de réseau classique](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Apprenez-en davantage sur les [rôles intégrés](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) et l’assignation des autorisations spécifiques aux [rôles personnalisés](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Resource Manager uniquement).

## <a name="delete"></a>Supprimer des ressources
Lorsque vous aurez terminé ce didacticiel, vous souhaiterez peut-être supprimer les ressources que vous avez créées, afin que leur utilisation ne soit pas facturée. La suppression d’un groupe de ressources supprime également toutes les ressources qu’il contient.

### <a name="delete-portal"></a>Portail Azure

1. Dans le champ de recherche du portail, entrez **myResourceGroup**. Dans les résultats de la recherche, cliquez sur **myResourceGroup**.
2. Dans le panneau **myResourceGroup**, cliquez sur l’icône **Supprimer**.
3. Pour confirmer la suppression, dans le champ **TAPEZ LE NOM DU GROUPE DE RESSOURCES :**, entrez **myResourceGroup**, puis cliquez sur **Supprimer**.

### <a name="delete-cli"></a>Interface CLI Azure

1. Utilisez Azure CLI 2.0 pour supprimer le réseau virtuel (Resource Manager) avec la commande suivante :

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Utilisez Azure CLI 1.0 pour supprimer le réseau virtuel (classique) avec les commandes suivantes :

    ```azurecli
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Entrez la commande suivante pour supprimer le réseau virtuel (Resource Manager) :

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. Pour supprimer le réseau virtuel (classique) avec PowerShell, vous devez modifier un fichier de configuration réseau existant. Pour découvrir comment exporter, mettre à jour et importer des fichiers de configuration réseau, consultez [cet article](virtual-networks-using-network-configuration-file.md). Supprimez l’élément VirtualNetworkSite suivant pour le réseau virtuel utilisé dans ce didacticiel :

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > L’importation d’un fichier de configuration réseau modifié peut entraîner des modifications des réseaux virtuels (classiques) existants dans votre abonnement. Assurez-vous de ne supprimer que le réseau virtuel précédent et de ne modifier ou supprimer aucun réseau virtuel existant de votre abonnement. 

## <a name="register"></a>Préversion de l’inscription à l’homologation de réseaux virtuels mondiaux

L’appairage de réseaux virtuels au sein d’une même région est généralement possible. L’homologation des réseaux virtuels dans des régions différentes est une fonctionnalité en préversion pour le moment. Consultez [Virtual network updates](https://azure.microsoft.com/en-us/updates/?product=virtual-network) (Mises à jour du réseau virtuel) pour les régions disponibles. Pour homologuer des réseaux virtuels dans différentes régions, vous devez d’abord vous inscrire à la préversion, en effectuant les étapes suivantes (dans l’abonnement dans lequel se trouve chaque réseau virtuel à homologuer) à l’aide d’Azure PowerShell ou d’Azure CLI :

### <a name="powershell"></a>PowerShell

1. Installez la dernière version du module PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Démarrez une session PowerShell et connectez-vous à Azure à l’aide de la commande `Login-AzureRmAccount`.
3. Pour inscrire l’abonnement dans lequel se trouve chaque réseau virtuel à homologuer à la préversion, entrez les commandes suivantes :

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. Vérifiez que vous êtes inscrit à la préversion en saisissant la commande suivante :

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    N’effectuez pas les étapes décrites dans les sections Portail, Interface de ligne de commande Azure, PowerShell ou Modèle Resource Manager de cet article tant que la sortie **RegistrationState** que vous avez reçue après avoir entré la commande précédente n’est pas **Registered** pour les deux abonnements.

### <a name="azure-cli"></a>Azure CLI

1. [Installer et configurer Azure CLI](/cli/azure/install-azure-cli?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).
2. Vérifiez que vous utilisez la version 2.0.18 ou une version ultérieure de l’interface Azure CLI en saisissant la commande `az --version`. Si ce n’est pas le cas, installez la version la plus récente.
3. Connectez-vous à Azure avec la commande `az login`.
4. Inscrivez-vous à la préversion en entrant les commandes suivantes :

    ```azurecli-interactive
    az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
    az provider register --name Microsoft.Network
    ```

5. Vérifiez que vous êtes inscrit à la préversion en saisissant la commande suivante :

    ```azurecli-interactive
    az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
    ```

    N’effectuez pas les étapes décrites dans les sections Portail, Interface de ligne de commande Azure, PowerShell ou Modèle Resource Manager de cet article tant que la sortie **RegistrationState** que vous avez reçue après avoir entré la commande précédente n’est pas **Registered** pour les deux abonnements.

## <a name="next-steps"></a>Étapes suivantes

- Familiarisez-vous bien avec les [comportements et contraintes importants de l’homologation de réseaux virtuels](virtual-network-manage-peering.md#requirements-and-constraints) avant d’en créer une pour une utilisation en production.
- Apprenez-en davantage sur tous les [paramètres d’homologation de réseaux virtuels](virtual-network-manage-peering.md#create-a-peering).
- Découvrez comment [créer une topologie de réseau de type hub et spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) avec l’homologation de réseaux virtuels.
