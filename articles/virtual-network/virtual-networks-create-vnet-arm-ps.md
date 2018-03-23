---
title: Créer un réseau virtuel Azure comprenant plusieurs sous-réseaux - PowerShell | Microsoft Docs
description: Découvrez comment créer un réseau virtuel comprenant des sous-réseaux à l’aide de PowerShell.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c90bdc9381bf98e5c1457e8e28f74105227d8f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-powershell"></a>Créer un réseau virtuel comprenant des sous-réseaux à l’aide de PowerShell

Un réseau virtuel permet à plusieurs types de ressources Azure de communiquer en privé entre elles et avec Internet. La création de plusieurs sous-réseaux dans un réseau virtuel permet de segmenter votre réseau afin que vous puissiez filtrer ou contrôler le flux du trafic entre les sous-réseaux. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créez un réseau virtuel
> * Créer un sous-réseau
> * Tester la communication réseau entre les machines virtuelles

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 3.6 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *EastUS*.

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

Le **préfixe d’adresse** est spécifié en notation CIDR. Le préfixe d’adresse spécifié inclut les adresses IP 10.0.0.0-10.0.255.254.

## <a name="create-a-subnet"></a>Créer un sous-réseau

Un sous-réseau est créé en créant d’abord une configuration de sous-réseau, puis en mettant à jour le réseau virtuel avec cette configuration. Créez une configuration de sous-réseau à l’aide de la commande [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). L’exemple suivant crée deux configurations de sous-réseaux pour les sous-réseaux *Public* et *Privé* :

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork
```

Le **préfixe d’adresse** spécifié pour un sous-réseau doit figurer dans le préfixe d’adresse défini pour le réseau virtuel. Azure DHCP attribue des adresses IP à partir d’un préfixe d’adresse de sous-réseau à des ressources déployées dans un sous-réseau. Azure attribue uniquement les adresses 10.0.0.4-10.0.0.254 à des ressources déployées dans le sous-réseau **Public**, car Azure réserve les quatre premières adresses (10.0.0.0-10.0.0.3 pour le sous-réseau, dans cet exemple) et la dernière adresse (10.0.0.255 pour le sous-réseau, dans cet exemple) dans chaque sous-réseau.

Écrivez les configurations de sous-réseaux dans le réseau virtuel à l’aide de la commande [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), ce qui crée les sous-réseaux dans le réseau virtuel :

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Avant de déployer des sous-réseaux et réseaux virtuels Azure pour une utilisation en production, il est recommandé de vous familiariser soigneusement avec les [considérations](manage-virtual-network.md#create-a-virtual-network) et [limites de réseau virtuel](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) de l’espace d’adressage. Une fois que les ressources sont déployées dans des sous-réseaux, certaines modifications de réseau virtuel et de sous-réseau (p. ex. la modification des plages d’adresses) peuvent nécessiter le redéploiement des ressources Azure existantes, déployées dans les sous-réseaux.

## <a name="test-network-communication"></a>Tester la communication réseau

Un réseau virtuel permet à plusieurs types de ressources Azure de communiquer en privé entre elles et avec Internet. Une machine virtuelle est l’un des types de ressource que vous pouvez déployer dans un réseau virtuel. Créez deux machines virtuelles dans le réseau virtuel pour pouvoir tester ultérieurement la communication réseau entre eux et Internet. 

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez une machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant crée une machine virtuelle nommée *myVmWeb* dans le sous-réseau *Public* du réseau virtuel *myVirtualNetwork*. L’option `-AsJob` crée la machine virtuelle en arrière-plan. Vous pouvez donc passer à l’étape suivante. Lorsque vous y êtes invité, entrez le nom d’utilisateur et le mot de passe qui vous serviront pour vous connecter à la machine virtuelle.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -ImageName "Win2016Datacenter" `
    -Name "myVmWeb" `
    -AsJob
```

Azure a automatiquement attribué 10.0.0.4 comme adresse IP privée de la machine virtuelle, 10.0.0.4 étant la première adresse IP disponible dans le sous-réseau *Public*. 

Créez une machine virtuelle dans le sous-réseau *Private*.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -ImageName "Win2016Datacenter" `
    -Name "myVmMgmt"
```

La création de la machine virtuelle prend quelques minutes. Bien que ce ne soit pas dans la sortie retournée, Azure a attribué 10.0.1.4 comme adresse IP privée de la machine virtuelle, 10.0.1.4 étant la première adresse IP disponible dans le sous-réseau *Private* du réseau *myVirtualNetwork*. 

Ne poursuivez pas les étapes restantes avant que la machine virtuelle ne soit créée et que PowerShell ne retourne la sortie.

Les machines virtuelles créées dans cet article ont une [interface réseau](virtual-network-network-interface.md) avec une adresse IP attribuée dynamiquement à l’interface réseau. Une fois que vous avez déployé la machine virtuelle, vous pouvez [ajouter des adresses IP publiques et privées, ou modifier la méthode d’attribution d’adresse IP pour qu’elle soit définie sur « statique »](virtual-network-network-interface-addresses.md#add-ip-addresses). Vous pouvez [ajouter des interfaces réseau](virtual-network-network-interface-vm.md#vm-add-nic), jusqu’à la limite prise en charge par le [taille de machine virtuelle](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que vous sélectionnez lorsque vous créez une machine virtuelle. Vous pouvez également [activer la virtualisation d’E/S racine unique](create-vm-accelerated-networking-powershell.md) pour une machine virtuelle, mais uniquement lorsque vous créez une machine virtuelle avec une taille de machine virtuelle qui prend en charge la fonctionnalité.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Communiquer entre les machines virtuelles et avec Internet

Vous pouvez vous connecter à l’adresse IP publique d’une machine virtuelle depuis Internet. Quand Azure a créé la machine virtuelle *myVmMgmt*, une adresse IP publique nommée *myVmMgmt* a été également créée et attribuée à la machine virtuelle. Bien qu’il ne soit pas obligatoire qu’une machine virtuelle ait une adresse IP publique attribuée, Azure attribue par défaut une adresse IP publique à chacune des machines virtuelles que vous créez. Pour communiquer à partir d’Internet vers une machine virtuelle, une adresse IP publique doit être attribuée à la machine virtuelle. Toutes les machines virtuelles peuvent communiquer en sortie avec Internet, qu’une adresse IP publique soit attribuée à la machine virtuelle ou non. Pour en savoir plus sur les connexions Internet sortantes dans Azure, consultez [Connexions sortantes dans Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Utilisez la commande [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) pour retourner l’adresse IP publique d’une machine virtuelle. L’exemple suivant retourne l’adresse IP publique de la machine virtuelle *myVmMgmt* :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Utilisez la commande suivante pour créer une session Bureau à distance avec la machine virtuelle *myVmMgmt* à partir de votre ordinateur local. Remplacez `<publicIpAddress>` par l’adresse IP retournée par la commande précédente.

```
mstsc /v:<publicIpAddress>
```

Un fichier .rdp (Remote Desktop Protocol) est créé, téléchargé sur votre ordinateur, puis ouvert. Entrez le nom d’utilisateur et le mot de passe choisis lors de la création de la machine virtuelle (il se peut que vous deviez choisir **Plus de choix**, puis **Utiliser un compte différent** pour spécifier les informations d’identification que vous avez entrées lors de la création de la machine virtuelle), puis cliquez sur **OK**. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur **Oui** ou **Continuer** pour continuer le processus de connexion. 

Dans une étape ultérieure, un test ping est utilisé pour communiquer avec la machine virtuelle *myVmMgmt* depuis la machine virtuelle *myVmWeb*. Le test ping utilise le protocole IMCP, qui est bloqué par défaut par le pare-feu Windows. Autorisez le protocole IMCP dans le pare-feu Windows en entrant la commande suivante depuis une invite de commandes :

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Bien que cet article utilise un test ping, il n’est pas recommandé d’autoriser le protocole IMCP dans le pare-feu Windows lors de déploiements de production.

Pour des raisons de sécurité, il est courant de limiter le nombre de machines virtuelles qui peuvent être connectées à distance à un réseau virtuel. Dans ce didacticiel, la machine virtuelle *myVmMgmt* est utilisée pour gérer la machine virtuelle *myVmWeb* dans le réseau virtuel. Utilisez la commande suivante pour établir la connexion d’un bureau à distance à la machine virtuelle *myVmWeb* à partir de la machine virtuelle *myVmMgmt* :

``` 
mstsc /v:myVmWeb
```

Pour communiquer avec la machine virtuelle *myVmMgmt* à partir de la machine virtuelle *myVmWeb*, entrez la commande suivante à partir d’une invite de commandes :

```
ping myvmmgmt
```

Le résultat ressemble à ce qui suit :
    
```
Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
Ping statistics for 10.0.1.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
      
Vous pouvez constater que l’adresse de la machine virtuelle *myVmMgmt* est 10.0.1.4. 10.0.1.4 était la première adresse IP disponible dans la plage d’adresses du sous-réseau *Privé* que vous avez déployé sur la machine virtuelle *myVmMgmt* à l’étape précédente.  Vous voyez que le nom de domaine complet de l’ordinateur virtuel est *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Bien que la partie *dar5p44cif3ulfq00wxznl3i3f* du nom de domaine soit différente pour votre machine virtuelle, les autres parties du nom de domaine sont les mêmes. Par défaut, toutes les machines virtuelles Azure utilisent le service Azure DNS par défaut. Toutes les machines virtuelles au sein d’un réseau virtuel peuvent résoudre les noms de toutes les autres machines virtuelles dans le même réseau virtuel à l’aide du service DNS par défaut d’Azure. Au lieu d’utiliser le service DNS par défaut d’Azure, vous pouvez utiliser votre propre serveur DNS ou la fonctionnalité de domaine privé du service Azure DNS. Pour plus d’informations, consultez [Résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) ou [Utilisation d’Azure DNS pour les domaines privés](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pour installer IIS pour Windows Server sur la machine virtuelle *myVmWeb*, entrez la commande suivante depuis une session PowerShell :

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Une fois l’installation d’IIS terminée, déconnectez la session à distance avec la machine virtuelle *myVmWeb*. Vous vous retrouvez donc dans la session à distance avec la machine virtuelle *myVmMgmt*. Ouvrez un navigateur web et accédez à l’adresse http://myvmweb. Vous voyez la page d’accueil IIS.

Déconnectez la session à distance avec la machine virtuelle *myVmMgmt*.

Obtenez l’adresse publique Azure attribuée à la machine virtuelle *myVmWeb* :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Sur votre ordinateur, accédez à l’adresse IP publique de la machine virtuelle *myVmWeb*. La tentative d’affichage de la page d’accueil IIS depuis votre ordinateur échoue. Cet échec se produit car, quand les machines virtuelles ont été déployées, Azure a créé par défaut un groupe de sécurité réseau pour chaque machine virtuelle. 

Un groupe de sécurité réseau contient des règles de sécurité qui autorisent ou rejettent le trafic réseau entrant et sortant en fonction du port et de l’adresse IP. Le groupe de sécurité réseau par défaut créé par Azure permet la communication sur tous les ports entre les ressources dans le même réseau virtuel. Pour les machines virtuelles Linux, le groupe de sécurité réseau par défaut refuse tout le trafic entrant à partir d’Internet sur tous les ports, à l’exception du port TCP 3389 (SSH). Par conséquent, par défaut, vous pouvez également vous connecter à distance directement sur la machine virtuelle *myVmWeb* à partir d’Internet, même si vous ne souhaitez pas ouvrir le port 3389 à un serveur web. Étant donné que la navigation web communique via le port 80, la communication depuis Internet échoue, car aucune règle n’est présente dans le groupe de sécurité réseau par défaut autorisant le trafic sur le port 80.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin d’un groupe de ressources, utilisez la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe et toutes les ressources qu’il contient.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment déployer un réseau virtuel comprenant plusieurs sous-réseaux. Vous avez également appris que, lorsque vous créez une machine virtuelle Windows, Azure crée une interface réseau qu’il associe à la machine virtuelle et crée un groupe de sécurité réseau qui autorise uniquement le trafic sur le port 3389, à partir d’Internet. Passez au didacticiel suivant pour apprendre à filtrer le trafic réseau vers des sous-réseaux, plutôt que vers des machines virtuelles individuelles.

> [!div class="nextstepaction"]
> [Filtrer le trafic réseau vers des sous-réseaux](./virtual-networks-create-nsg-arm-ps.md)
