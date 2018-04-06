---
title: Router le trafic réseau - Azure PowerShell | Documents Microsoft
description: Découvrez comment acheminer le trafic réseau avec une table de routage à l’aide de PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f7be6aa58c6779150d3e79893e6e179d08611567
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Acheminer le trafic réseau avec une table de routage à l’aide de PowerShell

Par défaut, Azure achemine automatiquement le trafic entre tous les sous-réseaux au sein d’un réseau virtuel. Vous pouvez créer vos propres itinéraires pour remplacer le routage par défaut d’Azure. La possibilité de créer des itinéraires personnalisés est utile si, par exemple, vous souhaitez router le trafic entre des sous-réseaux via une appliance virtuelle réseau (NVA). Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer une table de routage
> * Créer un itinéraire
> * Créer un réseau virtuel comprenant plusieurs sous-réseaux
> * Associer une table de routage à un sous-réseau
> * Créer une appliance NVA qui route le trafic
> * Déployer des machines virtuelles sur différents sous-réseaux
> * Router le trafic d’un sous-réseau vers un autre via une NVA

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable AzureRM` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 

## <a name="create-a-route-table"></a>Créer une table de routage

Avant de pouvoir créer une table de routage, créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* pour toutes les ressources créées dans cet article. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Créez une table de routage avec [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable). L’exemple suivant crée une table de routage nommée *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Créer un itinéraire

Créez un itinéraire en récupérant l’objet de table de routage avec [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), créez un itinéraire avec [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), puis écrivez la configuration de l’itinéraire dans la table de routage avec [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associer une table de routage à un sous-réseau

Avant de pouvoir associer une table de routage à un sous-réseau, vous devez créer un réseau virtuel et un sous-réseau. Créez un réseau virtuel avec [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L’exemple suivant permet de créer un réseau virtuel nommé *myVirtualNetwork* avec le préfixe d’adresse *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Créez trois sous-réseaux en créant trois configurations de sous-réseau avec [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). L’exemple suivant crée trois configurations de sous-réseau pour les sous-réseaux *Public*, *Private* et *DMZ* :

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Écrivez les configurations de sous-réseaux dans le réseau virtuel à l’aide de la commande [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), ce qui crée les sous-réseaux dans le réseau virtuel :

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Associez la table de routage *myRouteTablePublic* au sous-réseau *Public* avec [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig), puis écrivez la configuration du sous-réseau dans le réseau virtuel avec [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

## <a name="create-an-nva"></a>Créer une NVA

Une appliance virtuelle réseau est une machine virtuelle qui exécute une fonction réseau, telle que le routage, la fonction de pare-feu ou l’optimisation WAN.

Avant de créer une machine virtuelle, créez une interface réseau.

### <a name="create-a-network-interface"></a>Créer une interface réseau

Avant de créer une interface réseau, vous devez récupérer l’ID de réseau virtuel avec [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), puis l’ID de sous-réseau avec [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Créez une interface réseau avec [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) dans le sous-réseau *DMZ* avec le transfert IP activé :

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Créer une machine virtuelle

Pour créer une machine virtuelle et lui attacher une interface réseau existante, vous devez d’abord créer une configuration de machine virtuelle avec [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). La configuration inclut l’interface réseau créée à l’étape précédente. Quand vous êtes invité à indiquer un nom d’utilisateur et un mot de passe, sélectionnez ceux qui vous permettront de vous connecter à la machine virtuelle. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Créez la machine virtuelle avec la configuration de la machine virtuelle et [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant crée une machine virtuelle nommée *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

L’option `-AsJob` crée la machine virtuelle en arrière-plan. Vous pouvez donc passer à l’étape suivante.

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel pour vérifier que le trafic provenant du sous-réseau *Public* est acheminé vers le sous-réseau *Private* via l’appliance virtuelle réseau lors d’une étape ultérieure. 

Créez une machine virtuelle dans le sous-réseau *Public* avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant crée une machine virtuelle nommée *myVmPublic* dans le sous-réseau *Public* du réseau virtuel *myVirtualNetwork*. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Créez une machine virtuelle dans le sous-réseau *Private*.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

La création de la machine virtuelle ne nécessite que quelques minutes. Attendez que la machine virtuelle ait été créée et qu’Azure ait retourné la sortie à PowerShell pour passer à l’étape suivante.

## <a name="route-traffic-through-an-nva"></a>Acheminer le trafic via une appliance virtuelle réseau

Utilisez [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) pour retourner l’adresse IP publique de la machine virtuelle *myVmPrivate*. L’exemple suivant retourne l’adresse IP publique de la machine virtuelle *myVmPrivate* :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Utilisez la commande suivante pour créer une session Bureau à distance avec la machine virtuelle *myVmPrivate* à partir de votre ordinateur local. Remplacez `<publicIpAddress>` par l’adresse IP retournée par la commande précédente.

```
mstsc /v:<publicIpAddress>
```

Ouvrez le fichier .rdp téléchargé. Si vous y êtes invité, sélectionnez **Connexion**.

Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle (il se peut que vous deviez choisir **Plus de choix**, puis **Utiliser un compte différent** pour spécifier les informations d’identification que vous avez entrées lors de la création de la machine virtuelle), puis sélectionnez **OK**. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Sélectionnez**Oui** pour poursuivre le processus de connexion. 

À une étape ultérieure, la commande tracert.exe est utilisée pour tester le routage. Tracert utilise le protocole ICMP (Internet Control Message Protocol), qui est refusé via le Pare-feu Windows. Autorisez le protocole ICMP (Internet Control Message Protocol) dans le pare-feu Windows en entrant la commande suivante de PowerShell :

```powershell
New-NetFirewallRule ???DisplayName ???Allow ICMPv4-In??? ???Protocol ICMPv4
```

Bien que cet article utilise tracert pour tester le routage, il n’est pas recommandé d’autoriser le protocole IMCP dans le pare-feu Windows lors de déploiements en production.

Activez le transfert IP sur le système d’exploitation de *myVmNva* en effectuant les étapes suivantes sur la machine virtuelle *myVmPrivate* :

Établissez une connexion Bureau à distance avec la machine virtuelle *myVmNva* à l’aide de la commande suivante dans PowerShell :

``` 
mstsc /v:myvmnva
```
    
Pour activer le transfert IP au sein du système d’exploitation, entrez la commande suivante dans PowerShell :

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Redémarrez la machine virtuelle, ce qui va également déconnecter la session Bureau à distance.

Tout en conservant la connexion à la machine virtuelle *myVmPrivate*, après le redémarrage de la machine virtuelle *myVmNva*, créez une session Bureau à distance sur la machine virtuelle *myVmPublic* avec la commande suivante :

``` 
mstsc /v:myVmPublic
```
    
Autorisez le protocole IMCP dans le Pare-feu Windows en entrant la commande suivante dans PowerShell :

```powershell
New-NetFirewallRule ???DisplayName ???Allow ICMPv4-In??? ???Protocol ICMPv4
```

Pour tester le routage du trafic réseau vers la machine virtuelle *myVmPrivate* à partir de la machine virtuelle *myVmPublic*, entrez la commande suivante dans PowerShell :

```
tracert myVmPrivate
```

La réponse ressemble à ce qui suit :
    
```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```
      
Vous voyez que le premier tronçon est 10.0.2.4, ce qui correspond à l’adresse IP privée de l’appliance virtuelle réseau. Le second tronçon est 10.0.1.4, ce qui correspond à l’adresse IP privée de la machine virtuelle *myVmPrivate*. L’itinéraire ajouté à la table de routage *myRouteTablePublic* et associé au sous-réseau *Public* a contraint Azure à acheminer le trafic via l’appliance virtuelle réseau et non directement au sous-réseau *Private*.

Fermez la session Bureau à distance sur la machine virtuelle *myVmPublic*. Cela n’interrompt pas la connexion à la machine virtuelle *myVmPrivate*.
Pour tester le routage du trafic réseau vers la machine virtuelle *myVmPublic* à partir de la machine virtuelle *myVmPrivate*, entrez la commande suivante à l’invite de commandes :

```
tracert myVmPublic
```

La réponse ressemble à ce qui suit :

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
    
Trace complete.
```

Vous pouvez voir que le trafic est routé directement de la machine virtuelle *myVmPrivate* vers la machine virtuelle *myVmPublic*. Par défaut, Azure achemine directement le trafic entre les sous-réseaux.

Fermez les sessions Bureau à distance sur la machine virtuelle *myVmPrivate*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin d’un groupe de ressources, utilisez la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe et toutes les ressources qu’il contient.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé une table de routage que vous avez associée à un sous-réseau. Vous avez créé une appliance virtuelle réseau qui a acheminé le trafic d’un sous-réseau public vers un sous-réseau privé. Vous pouvez déployer différentes appliances virtuelles réseau préconfigurées ayant des fonctions de réseau, telles que la fonction de pare-feu ou l’optimisation WAN, à partir de la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Avant de déployer des tables de routage dans un environnement de production, il est recommandé de bien se familiariser avec le [routage dans Azure](virtual-networks-udr-overview.md), la [gestion des tables de routage](manage-route-table.md) et les [limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Alors que vous pouvez déployer de nombreuses ressources Azure dans un réseau virtuel, les ressources pour certains services Azure PaaS ne peuvent pas être déployées dans un réseau virtuel. Cependant, vous pouvez toujours restreindre l’accès aux ressources de certains services Azure PaaS au trafic provenant uniquement d’un sous-réseau de réseau virtuel. Passez au tutoriel suivant pour apprendre à restreindre l’accès réseau aux ressources Azure PaaS.

> [!div class="nextstepaction"]
> [Restreindre l’accès réseau aux ressources PaaS](tutorial-restrict-network-access-to-resources-powershell.md)