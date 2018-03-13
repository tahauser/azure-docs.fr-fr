---
title: "Créer une homologation de réseaux virtuels Azure - Resource Manager - Même abonnement | Microsoft Docs"
description: "Découvrez comment créer une homologation de réseaux virtuels entre des réseaux virtuels créés via Resource Manager dans le même abonnement Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anavin;jdial
ms.openlocfilehash: 70fe948070147c01922fab68fb55a0f00c26a0f3
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="create-a-virtual-network-peering---resource-manager-same-subscription"></a>Créer une homologation de réseaux virtuels - Resource Manager - Même abonnement

Dans ce didacticiel, vous apprendrez à créer une homologation de réseaux virtuels entre des réseaux virtuels créés via Resource Manager. Les deux réseaux virtuels doivent se trouver dans le même abonnement. Homologuer deux réseaux virtuels permet aux ressources de différents réseaux virtuels de communiquer entre elles avec la même bande passante et latence, comme si les ressources se trouvaient sur le même réseau virtuel. En savoir plus sur l’[homologation de réseaux virtuels](virtual-network-peering-overview.md). 

Les étapes de création d’une homologation de réseaux virtuels sont différentes, selon que les réseaux virtuels sont dans le même abonnement ou dans des abonnements différents, et selon le [modèle de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) utilisé pour les créer. Découvrez comment créer une homologation de réseaux virtuels dans d’autres scénarios en cliquant sur le scénario souhaité dans le tableau suivant :

|Modèle de déploiement Azure  | Abonnement Azure  |
|--------- |---------|
|[Deux modèles Resource Manager](create-peering-different-subscriptions.md) |Différent|
|[Un modèle Resource Manager, un modèle classique](create-peering-different-deployment-models.md) |Identique|
|[Un modèle Resource Manager, un modèle classique](create-peering-different-deployment-models-subscriptions.md) |Différent|

Vous ne pouvez pas créer d’homologation de réseaux virtuels entre deux réseaux virtuels déployés via le modèle de déploiement classique. Si vous avez besoin de connecter des réseaux virtuels tous deux créés par le biais du modèle de déploiement classique, vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure. 

Ce didacticiel permet d’homologuer des réseaux virtuels situés dans la même région. La capacité à homologuer des réseaux virtuels dans des régions différentes est une fonctionnalité en préversion pour le moment. Effectuez les étapes indiquées dans [S’inscrire à l’homologation de réseaux virtuels mondiaux](#register) avant d’essayer d’homologuer des réseaux virtuels dans des régions différentes, sans quoi l’homologation échoue. La capacité à connecter des réseaux virtuels situés dans des régions différentes avec une [passerelle VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure est généralement disponible et ne nécessite pas d’inscription.

Vous pouvez utiliser le [portail Azure](#portal), [l’interface de ligne de commande](#cli) (CLI) Azure, Azure [PowerShell](#powershell) ou un [modèle Azure Resource Manager](#template) pour créer une homologation de réseaux virtuels. Cliquez sur les liens des outils précédents pour accéder directement à la procédure permettant de créer une homologation de réseaux virtuels à l’aide de l’outil de votre choix.

## <a name="portal"></a>Créer une homologation - portail Azure

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
4. Répétez les étapes 2 et 3 en spécifiant les valeurs suivantes à l’étape 3 :
    - **Nom** : *myVnet2*
    - **Espace d’adressage** : *10.1.0.0/16*
    - **Nom du sous-réseau** : *par défaut*
    - **Espace d’adressage de sous-réseau** : *10.1.0.0/24*
    - **Abonnement** : sélectionnez votre abonnement
    - **Groupe de ressources** : sélectionnez **Use existing** (Utiliser existant), puis *myResourceGroup*
    - **Emplacement** : *États-Unis de l’Est*
5. Dans le champ **Rechercher des ressources** située en haut du portail, tapez *myResourceGroup*. Cliquez sur **myResourceGroup** quand il apparaît dans les résultats de la recherche. Un panneau s’affiche pour le groupe de ressources **myresourcegroup**. Le groupe de ressources contient les deux réseaux virtuels créés dans les étapes précédentes.
6. Cliquez sur **myVNet1**.
7. Dans le panneau **myVnet1** qui s’affiche, cliquez sur **Homologations** dans la liste verticale des options sur le côté gauche du panneau.
8. Dans le panneau **myVnet1 - Homologations** qui s’est affiché, cliquez sur **+ Ajouter**.
9. Dans le panneau **Ajouter l’homologation** qui s’affiche, entrez ou sélectionnez les options suivantes et cliquez sur **OK** :
     - **Nom** : *myVnet1ToMyVnet2*
     - **Modèle de déploiement de réseau virtuel** : sélectionnez **Resource Manager**. 
     - **Abonnement** : sélectionnez votre abonnement
     - **Réseau virtuel** : cliquez sur **Choisir un réseau virtuel**, puis sur **myVnet2**.
     - **Autoriser l’accès au réseau virtuel :** vérifiez que l’option **Activé** est sélectionnée.
    Il n’y aucun autre paramètre utilisé dans ce didacticiel. Pour en savoir plus sur tous les paramètres d’homologation, consultez [Create, change, or delete a virtual network peering](virtual-network-manage-peering.md#create-a-peering) (Créer, modifier ou supprimer une homologation de réseaux virtuels).
10. Après avoir cliqué sur **OK** à l’étape précédente, le panneau **Ajouter l’homologation** se ferme et vous pouvez à nouveau voir le panneau **myVnet1 - Homologations**. Après quelques secondes, l’homologation que vous avez créée apparaît dans le panneau. **Lancé** est indiqué dans la colonne **ÉTAT D’APPAIRAGE** pour l’homologation **myVnet1ToMyVnet2** que vous avez créée. Vous avez effectué une homologation de Vnet1 à Vnet2, mais vous devez maintenant effectuer une homologation de myVnet2 à myVnet1. L’homologation doit être créée dans les deux directions pour permettre aux ressources des réseaux virtuels de communiquer entre elles.
11. Répétez les étapes 5 à 10 pour myVnet2. Nommez l’homologation *myVnet2ToMyVnet1*.
12. Quelques secondes après avoir cliqué sur **OK** pour créer l’homologation pour MyVnet2, l’homologation **myVnet2ToMyVnet1** que vous venez de créer est répertoriée avec **Connecté** dans la colonne **ÉTAT D’APPAIRAGE**.
13. Répétez les étapes 5 à 7 pour MyVnet1. **L’ÉTAT D’APPAIRAGE** pour l’homologation **myVnet1ToVNet2** est également défini sur **Connecté** maintenant. L’homologation est établie avec succès une fois que vous voyez **Connecté** dans la colonne **ÉTAT D’APPAIRAGE** pour les deux réseaux virtuels de l’homologation.
14. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
15. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-portal) de cet article.

Les ressources Azure que vous créez dans un réseau virtuel sont désormais en mesure de communiquer entre elles via leurs adresses IP. Si vous utilisez la résolution de noms Azure par défaut pour les réseaux virtuels, les ressources dans les réseaux virtuels ne sont pas en mesure de résoudre les noms dans les réseaux virtuels. Si vous souhaitez résoudre les noms dans les réseaux virtuels d’une homologation, vous devez créer votre propre serveur DNS. Apprenez à configurer la [résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="cli"></a>Créer une homologation - interface de ligne de commande Azure

Le script suivant :

- Requiert l’interface de ligne de commande Azure version 2.0.4 ou ultérieure. Pour connaître la version de l’interface, exécutez la commande `az --version`. Si vous devez effectuer une mise à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Fonctionne dans un interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI dans le client Windows, consultez la page [Running the Azure CLI in Windows (Exécution d’Azure CLI dans Windows)](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Au lieu d’installer l’interface CLI et ses dépendances, vous pouvez utiliser Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Cliquez sur le bouton **Essayez-le** dans le script qui suit, ce qui appelle un Cloud Shell vous permettant de vous connecter à votre compte Azure. Pour exécuter le script, cliquez sur le bouton **Copier** et collez le contenu dans votre Cloud Shell.

1. Créez un groupe de ressources et deux réseaux virtuels.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    ```

2. Créez une homologation de réseaux virtuels entre les deux réseaux virtuels.
 
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    ```

3. Après l’exécution du script, passez en revue les homologations pour chaque réseau virtuel. 

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    Exécutez une nouvelle fois la commande précédente, en remplaçant *myVnet1* par *myVnet2*. La sortie des deux commandes affiche **Connecté** dans la colonne **PeeringState**.

     Les ressources Azure que vous créez dans un réseau virtuel sont désormais en mesure de communiquer entre elles via leurs adresses IP. Si vous utilisez la résolution de noms Azure par défaut pour les réseaux virtuels, les ressources dans les réseaux virtuels ne sont pas en mesure de résoudre les noms dans les réseaux virtuels. Si vous souhaitez résoudre les noms dans les réseaux virtuels d’une homologation, vous devez créer votre propre serveur DNS. Apprenez à configurer la [résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

4. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
5. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-cli) de cet article.


## <a name="powershell"></a>Créer une homologation - PowerShell

1. Installez la dernière version du module PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Pour démarrer une session PowerShell, accédez à **Démarrer**, entrez **powershell**, puis cliquez sur **PowerShell**.
3. Connectez-vous à Azure à partir de PowerShell en entrant la commande `login-azurermaccount`. Le compte auquel vous vous connectez doit avoir les autorisations nécessaires pour créer une homologation de réseaux virtuels. Consultez la section [Autorisations](#permissions) de cet article pour plus d’informations.
4. Créez un groupe de ressources et deux réseaux virtuels. Pour exécuter le script, copiez le script suivant, collez-le dans PowerShell, puis appuyez sur `Enter` lorsque la dernière ligne apparaît à l’écran :

    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    ```

5. Créez une homologation de réseaux virtuels entre les deux réseaux virtuels. Copiez le script suivant, collez-le dans PowerShell, puis appuyez sur `Enter` lorsque la dernière ligne apparaît à l’écran :
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    ```
6. Pour passer en revue les sous-réseaux du réseau virtuel, copiez la commande suivante, collez-la dans PowerShell et appuyez sur `Enter` :

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Exécutez une nouvelle fois la commande précédente, en remplaçant *myVnet1* par *myVnet2*. La sortie des deux commandes affiche **Connecté** dans la colonne **PeeringState**.
7. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
8. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-powershell) de cet article.

Les ressources Azure que vous créez dans un réseau virtuel sont désormais en mesure de communiquer entre elles via leurs adresses IP. Si vous utilisez la résolution de noms Azure par défaut pour les réseaux virtuels, les ressources dans les réseaux virtuels ne sont pas en mesure de résoudre les noms dans les réseaux virtuels. Si vous souhaitez résoudre les noms dans les réseaux virtuels d’une homologation, vous devez créer votre propre serveur DNS. Apprenez à configurer la [résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="template"></a>Créer une homologation - modèle Resource Manager

1. Faites référence au modèle Azure Resource Manager [Créer une homologation de réseaux virtuels](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering). Des instructions sont fournies avec le modèle pour le déployer à l’aide du portail Azure, de PowerShell ou de l’interface de ligne de commande Azure. Connectez-vous à l’outil de votre choix pour déployer le modèle avec un compte qui dispose des autorisations nécessaires pour créer une homologation de réseaux virtuels. Consultez la section [Autorisations](#permissions) de cet article pour plus d’informations.
2. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
3. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete) de cet article, soit avec le portail Azure, PowerShell ou l’interface de ligne de commande Azure.

## <a name="permissions"></a>Autorisations

Les comptes que vous utilisez pour créer une homologation de réseaux virtuels doivent avoir les autorisations ou le rôle nécessaires. Par exemple, si vous avez effectué une homologation entre deux réseaux virtuels nommés VNet1 et VNet2, votre compte doit posséder les autorisations ou le rôle minimum suivants pour chaque réseau virtuel :
    
|Réseau virtuel|Rôle|Autorisations|
|---|---|---|
|VNet1|[Collaborateur de réseau](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|VNet2|[Collaborateur de réseau](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

Apprenez-en davantage sur les [rôles intégrés](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) et l’affectation d’autorisations spécifiques aux [rôles personnalisés](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Gestionnaire des ressources uniquement).

## <a name="delete"></a>Supprimer des ressources
Lorsque vous aurez terminé ce didacticiel, vous souhaiterez peut-être supprimer les ressources que vous avez créées, afin que leur utilisation ne soit pas facturée. La suppression d’un groupe de ressources supprime également toutes les ressources qu’il contient.

### <a name="delete-portal"></a>Portail Azure

1. Dans le champ de recherche du portail, entrez **myResourceGroup**. Dans les résultats de la recherche, cliquez sur **myResourceGroup**.
2. Dans le panneau **myResourceGroup**, cliquez sur l’icône **Supprimer**.
3. Pour confirmer la suppression, dans le champ **TAPEZ LE NOM DU GROUPE DE RESSOURCES :**, entrez **myResourceGroup**, puis cliquez sur **Supprimer**.

### <a name="delete-cli"></a>Interface CLI Azure

Entrez la commande suivante :

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Entrez la commande suivante :

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -force
```

## <a name="register"></a>Préversion de l’inscription à l’homologation de réseaux virtuels mondiaux

L’appairage de réseaux virtuels au sein d’une même région est généralement possible. L’appairage de réseaux virtuels dans des régions différentes est actuellement en version préliminaire. Consultez [Virtual network updates](https://azure.microsoft.com/en-us/updates/?product=virtual-network) (Mises à jour du réseau virtuel) pour les régions disponibles. Pour homologuer des réseaux virtuels dans différentes régions, vous devez d’abord vous inscrire à la préversion, en effectuant les étapes suivantes (dans l’abonnement dans lequel se trouve chaque réseau virtuel à homologuer) à l’aide d’Azure PowerShell ou d’Azure CLI :

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
