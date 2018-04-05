---
title: Restreindre l’accès réseau aux ressources PaaS - Azure PowerShell | Microsoft Docs
description: Découvrez comment limiter et restreindre l’accès réseau aux ressources Azure, telles que le service Stockage Azure et Azure SQL Database, à l’aide de points de terminaison de service de réseau virtuel en utilisant Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 7e402af74babda2ce32d4a1597c61d71aba89b9e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Restreindre l’accès réseau aux ressources PaaS avec des points de terminaison de service réseau virtuel en utilisant Azure PowerShell

Les points de terminaison de service de réseau virtuel permettent de restreindre l’accès réseau à certaines ressources du service Azure en n’autorisant leur accès qu’à partir d’un sous-réseau du réseau virtuel. Vous pouvez également supprimer l’accès Internet aux ressources. Les points de terminaison de service fournissent une connexion directe entre votre réseau virtuel et les services Azure pris en charge, ce qui vous permet d’utiliser l’espace d’adressage privé de votre réseau virtuel pour accéder aux services Azure. Le trafic destiné aux ressources Azure via les points de terminaison de service reste toujours sur le serveur principal de Microsoft Azure. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un réseau virtuel avec un sous-réseau
> * Ajouter un sous-réseau et activer un point de terminaison de service
> * Créer une ressource Azure et autoriser l’accès réseau à cette ressource uniquement à partir d’un sous-réseau
> * Déployer une machine virtuelle sur chaque sous-réseau
> * Vérifier l’accès à une ressource à partir d’un sous-réseau
> * Vérifier que l’accès à une ressource est refusé à partir d’un sous-réseau et d’Internet

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez ` Get-Module -ListAvailable AzureRM` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Avant de créer un réseau virtuel, vous devez créer un groupe de ressources pour le réseau virtuel et toutes les autres ressources créées dans cet article. Créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant permet de créer un groupe de ressources nommé *myResourceGroup* : 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Créez un réseau virtuel avec [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L’exemple suivant permet de créer un réseau virtuel nommé *myVirtualNetwork* avec le préfixe d’adresse *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Créez une configuration de sous-réseau à l’aide de la commande [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). L’exemple suivant crée une configuration de sous-réseau pour un sous-réseau nommé *Public* :

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Créez le sous-réseau dans le réseau virtuel en écrivant les configurations de sous-réseaux dans le réseau virtuel à l’aide de [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) :

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Activer un point de terminaison de service 

Vous ne pouvez activer des points de terminaison de service que pour les services qui prennent en charge les points de terminaison de service. Affichez les services d’un emplacement Azure donné avec [Get-AzureRmVirtualNetworkAvailableEndpointService](/powershell/module/azurerm.network/get-azurermvirtualnetworkavailableendpointservice). L’exemple suivant retourne la liste des services de la région *eastus* pour lesquels les points de terminaison de service ont été activés. La liste des services retournés augmente avec chaque nouvelle activation des points de terminaison de service.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkAvailableEndpointService -Location eastus | Select Name
``` 

Créez un autre sous-réseau dans le réseau virtuel. Dans cet exemple, un sous-réseau nommé *Private* est créé avec un point de terminaison de service *Microsoft.Storage* : 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-and-from-a-subnet"></a>Restreindre l’accès réseau vers et à partir d’un sous-réseau

Créez des règles de sécurité de groupe de sécurité réseau avec [New-AzureRmNetworkSecurityRuleGroup](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). La règle suivante autorise un accès sortant vers les adresses IP publiques affectées au service Stockage Azure : 

```azurepowershell-interactive
$rule1 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

La règle suivante refuse l’accès à toutes les adresses IP publiques. La règle précédente remplace cette règle, du fait de sa priorité plus élevée, ce qui permet d’accéder aux adresses IP publiques du Stockage Azure.

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Deny-internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

La règle suivante autorise le trafic du protocole RDP (Remote Desktop Protocol) entrant dans le sous-réseau à partir de n’importe quel endroit. Les connexions Bureau à distance sont autorisées dans le sous-réseau, afin que vous puissiez vérifier l’accès réseau à une ressource dans une étape ultérieure.

```azurepowershell-interactive
$rule3 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Créez un groupe de sécurité réseau avec [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). L’exemple suivant crée un groupe de sécurité réseau nommé *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Associez le groupe de sécurité réseau au sous-réseau *Private* avec [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig), puis écrivez la configuration du sous-réseau dans le réseau virtuel. L’exemple suivant associe le groupe de sécurité réseau *myNsgPrivate* au sous-réseau *Private* :

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Restreindre l’accès réseau à une ressource

Les étapes nécessaires pour restreindre l’accès réseau aux ressources créées par le biais des services Azure avec activation des points de terminaison varient d’un service à l’autre. Pour connaître les étapes à suivre, consultez la documentation relative à chacun des services. La suite de cet article comprend des étapes permettant de restreindre l’accès réseau pour un compte Stockage Azure.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Créez un compte de stockage Azure avec [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Remplacez `<replace-with-your-unique-storage-account-name>` par un nom qui n’existe dans aucun autre emplacement Azure. Le nom doit comprendre entre 3 et 24 caractères, correspondant à des chiffres et à des lettres en minuscules.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzureRmStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Une fois le compte de stockage créé, récupérez la clé du compte de stockage dans une variable avec [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) :

```azurepowershell-interactive
$storageAcctKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

La clé sera utilisée pour créer un partage de fichiers lors d’une prochaine étape. Entrez `$storageAcctKey` et notez la valeur, car vous devez également l’entrer dans une étape ultérieure lorsque vous mapperez le partage de fichiers à un lecteur d’une machine virtuelle.

### <a name="create-a-file-share-in-the-storage-account"></a>Créer un partage de fichiers dans le compte de stockage

Créez un contexte pour votre compte de stockage et votre clé avec [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext). Celui-ci encapsule le nom et la clé du compte de stockage :

```azurepowershell-interactive
$storageContext = New-AzureStorageContext $storageAcctName $storageAcctKey
```

Créez un partage de fichiers avec [New-AzureStorageShare](/powershell/module/azure.storage/new-azurestorageshare) :

$share = New-AzureStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Refuser tout accès réseau au compte de stockage

Par défaut, les comptes de stockage acceptent les connexions réseau provenant des clients de n’importe quel réseau. Pour limiter l’accès aux réseaux sélectionnés, définissez l’action par défaut sur *Refuser* avec [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset). Une fois l’accès réseau refusé, le compte de stockage n’est plus accessible par aucun des réseaux.

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Activer l’accès réseau à partir d’un sous-réseau

Récupérez le réseau virtuel créé avec [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), puis récupérez l’objet de sous-réseau privé dans une variable avec [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) :

```azurepowershell-interactive
$privateSubnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzureRmVirtualNetworkSubnetConfig `
  -Name "Private"
```

Autorisez l’accès réseau au compte de stockage à partir du sous-réseau *Private* avec [Add-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzureRmStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Pour tester l’accès réseau à un compte de stockage, déployez une machine virtuelle sur chaque sous-réseau.

### <a name="create-the-first-virtual-machine"></a>Créer la première machine virtuelle

Créez une machine virtuelle dans le sous-réseau *Public* avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Lors de l’exécution de la commande qui suit, vous êtes invité à saisir vos informations d’identification. Les valeurs que vous saisissez sont configurées comme le nom d’utilisateur et le mot de passe pour la machine virtuelle. L’option `-AsJob` crée la machine virtuelle en arrière-plan. Vous pouvez donc passer à l’étape suivante.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Une sortie similaire à la sortie suivante est renvoyée :

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-virtual-machine"></a>Créer la deuxième machine virtuelle

Créez une machine virtuelle dans le sous-réseau *Private* :

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

La création de la machine virtuelle par Azure ne nécessite que quelques minutes. Ne passez pas à l’étape suivante tant qu’Azure n’a pas terminé la création de la machine virtuelle et retourné de sortie à PowerShell. 

## <a name="confirm-access-to-storage-account"></a>Vérifier l’accès au compte de stockage

Utilisez [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) pour retourner l’adresse IP publique d’une machine virtuelle. L’exemple suivant retourne l’adresse IP publique de la machine virtuelle *myVmPrivate* :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Remplacez `<publicIpAddress>` dans la commande suivante par l’adresse IP publique adresse retournée à partir de la commande précédente, puis entrez la commande suivante : 

```powershell
mstsc /v:<publicIpAddress>
```

Un fichier .rdp (Remote Desktop Protocol) est créé et téléchargé sur votre ordinateur. Ouvrez le fichier .rdp téléchargé. Si vous y êtes invité, sélectionnez **Connexion**. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle. Vous devrez peut-être sélectionner **Plus de choix**, puis **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle. Sélectionnez **OK**. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez l’avertissement, sélectionnez **Oui** ou **Continuer** pour poursuivre le processus de connexion.

Sur la machine virtuelle *myVmPrivate*, mappez le partage de fichiers Azure au lecteur Z à l’aide de PowerShell. Avant d’exécuter les commandes qui suivent, remplacez `<storage-account-key>` et `<storage-account-name>` par les valeurs que vous avez fournies ou récupérées dans [Créer un compte de stockage](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```
PowerShell retourne un résultat semblable à l’exemple suivant :

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Le partage de fichiers Azure est correctement mappé au lecteur Z.

Vérifiez que la machine virtuelle ne dispose d’aucune connexion sortante à d’autres adresses IP publiques :

```powershell
ping bing.com
```

Vous ne recevez aucune réponse, car le groupe de sécurité réseau associé au sous-réseau *Private* n’autorise pas l’accès sortant aux adresses IP publiques autres que les adresses affectées au service Stockage Azure.

Fermez les sessions Bureau à distance sur la machine virtuelle *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Vérifier que l’accès au compte de stockage est refusé

Obtenir l’adresse IP publique de la machine virtuelle *myVmPublic* :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Remplacez `<publicIpAddress>` dans la commande suivante par l’adresse IP publique adresse retournée à partir de la commande précédente, puis entrez la commande suivante : 

```powershell
mstsc /v:<publicIpAddress>
```

Sur la machine virtuelle *myVmPublic*, essayez de mapper le partage de fichiers Azure au lecteur Z. Avant d’exécuter les commandes qui suivent, remplacez `<storage-account-key>` et `<storage-account-name>` par les valeurs que vous avez fournies ou récupérées dans [Créer un compte de stockage](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

L’accès au partage est refusé et vous recevez une erreur `New-PSDrive : Access is denied`. L’accès est refusé car la machine virtuelle *myVmPublic* est déployée sur le sous-réseau *Public*. Le sous-réseau *Public* ne dispose pas d’un point de terminaison de service activé pour Stockage Azure, et le compte de stockage autorise uniquement l’accès réseau à partir du sous-réseau *Private* et non à partir du sous-réseau *Public*.

Fermez les sessions Bureau à distance sur la machine virtuelle *myVmPublic*.

Sur votre ordinateur, essayez d’afficher les partages de fichier du compte de stockage avec la commande suivante :

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

L’accès est refusé et vous recevez une erreur *Get-AzureStorageFile : The remote server returned an error: (403) Forbidden. HTTP Status Code: 403 - HTTP Error Message: This request is not authorized to perform this operation* (indiquant que la requête n’est pas autorisée à effectuer cette opération), car votre ordinateur ne fait pas partie du sous-réseau *Private* du réseau virtuel *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin d’un groupe de ressources, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour le supprimer et toutes les ressources qu’il contient :

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez activé un point de terminaison de service pour un sous-réseau de réseau virtuel. Vous avez vu que les points de terminaison de service peuvent être activés pour les ressources déployées à l’aide de différents services Azure. Vous avez créé un compte Stockage Azure et un accès réseau à ce compte de stockage, limité aux ressources du sous-réseau du réseau virtuel. Avant de créer des points de terminaison de service sur des réseaux virtuels de production, il est recommandé de bien se familiariser avec les [points de terminaison de service](virtual-network-service-endpoints-overview.md).

Si votre compte comporte plusieurs réseaux virtuels, vous pouvez relier deux réseaux virtuels pour que les ressources de chaque réseau virtuel puissent communiquer entre elles. Passez au tutoriel suivant pour savoir comment connecter deux réseaux virtuels.

> [!div class="nextstepaction"]
> [Connecter des réseaux virtuels](./tutorial-connect-virtual-networks-powershell.md)
