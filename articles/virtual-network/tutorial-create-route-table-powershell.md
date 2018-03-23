---
title: Acheminer le trafic réseau - PowerShell | Microsoft Docs
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
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f91b143c75a82aa6760796770b3ae4d0e4ec53dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Acheminer le trafic réseau avec une table de routage à l’aide de PowerShell

Par défaut, Azure achemine automatiquement le trafic entre tous les sous-réseaux au sein d’un réseau virtuel. Vous pouvez créer vos propres itinéraires pour remplacer le routage par défaut d’Azure. La possibilité de créer des itinéraires personnalisés est utile si, par exemple, vous souhaitez acheminer le trafic entre des sous-réseaux via un pare-feu. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer une table de routage
> * Créer un itinéraire
> * Associer une table de routage à un sous-réseau de réseau virtuel
> * Tester le routage
> * Résoudre les problèmes de routage

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.4.1 ou ultérieure pour les besoins de cet article. Exécutez `Get-Module -ListAvailable AzureRM` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 

## <a name="create-a-route-table"></a>Créer une table de routage

Par défaut, Azure achemine le trafic entre tous les sous-réseaux dans un réseau virtuel. Pour en savoir plus sur les itinéraires par défaut d’Azure, consultez [Itinéraires système](virtual-networks-udr-overview.md). Pour remplacer le routage par défaut d’Azure, vous créez une table de routage qui contient des itinéraires et associez la table de routage à un sous-réseau de réseau virtuel.

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

Une table de routage peut contenir plusieurs itinéraires, ou aucun. Créez un itinéraire en récupérant l’objet de table de routage avec [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), créez un itinéraire avec [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), puis écrivez la configuration de l’itinéraire dans la table de routage avec [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

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

L’itinéraire dirige tout le trafic destiné au préfixe d’adresse 10.0.1.0/24 via une appliance virtuelle réseau avec l’adresse IP 10.0.2.4. L’appliance virtuelle réseau et le sous-réseau avec le préfixe d’adresse spécifié sont créés dans des étapes ultérieures. L’itinéraire remplace le routage par défaut d’Azure, qui achemine le trafic entre les sous-réseaux directement. Chaque itinéraire spécifie un type de tronçon suivant. Le type de tronçon suivant indique à Azure comment acheminer le trafic. Dans cet exemple, le type de tronçon suivant est *VirtualAppliance*. Pour en savoir plus sur tous les types de tronçons suivants disponibles dans Azure et quand les utiliser, consultez [Types de tronçons suivants](virtual-networks-udr-overview.md#custom-routes).

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

Les préfixes d’adresse doivent figurer dans le préfixe d’adresse défini pour le réseau virtuel. Les préfixes d’adresse de sous-réseau ne peuvent pas se chevaucher.

Écrivez les configurations de sous-réseaux dans le réseau virtuel à l’aide de la commande [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), ce qui crée les sous-réseaux dans le réseau virtuel :

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Vous pouvez associer une table de routage à plusieurs sous-réseaux, ou aucun. Un sous-réseau peut avoir zéro ou une table de routage associée. Le trafic sortant d’un sous-réseau est acheminé selon les itinéraires par défaut d’Azure et les itinéraires personnalisés que vous avez ajoutés à une table de routage que vous associez à un sous-réseau. Associez la table de routage *myRouteTablePublic* au sous-réseau *Public* avec [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig), puis écrivez la configuration du sous-réseau dans le réseau virtuel avec [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

Avant de déployer des tables de routage à utiliser en production, il est recommandé de bien vous familiariser avec le [routage dans Azure](virtual-networks-udr-overview.md) et les [limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Tester le routage

Pour tester le routage, vous allez créer une machine virtuelle qui sert d’appliance virtuelle réseau par laquelle passe l’itinéraire que vous avez créé lors d’une étape précédente. Après avoir créé l’appliance virtuelle réseau, vous allez déployer une machine virtuelle dans les sous-réseaux *Public* et *Private*. Vous allez ensuite acheminer le trafic entre le sous-réseau *Public* et le sous-réseau *Private* via l’appliance virtuelle réseau.

### <a name="create-a-network-virtual-appliance"></a>Créer une appliance virtuelle réseau

Une machine virtuelle exécutant une application réseau est souvent appelée appliance virtuelle réseau. Généralement, les appliances virtuelles réseau reçoivent le trafic réseau, effectuent une action, puis transfèrent ou suppriment le trafic réseau selon la logique configurée dans l’application réseau. 

#### <a name="create-a-network-interface"></a>Créer une interface réseau

Lors d’une étape précédente, vous avez créé un itinéraire qui indiquait une appliance virtuelle réseau comme type de tronçon suivant. Une machine virtuelle exécutant une application réseau est souvent appelée appliance virtuelle réseau. Dans les environnements de production, l’appliance virtuelle réseau que vous déployez est souvent une machine virtuelle préconfigurée. Plusieurs appliances virtuelles réseau sont disponibles dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). Dans cet article, une machine virtuelle de base est créée.

Une ou plusieurs interfaces réseau sont attachées à une machine virtuelle et lui permettent de communiquer avec le réseau. Pour qu’une interface réseau soit en mesure de transférer le trafic réseau qui lui est envoyé, mais qui n’est pas destiné à sa propre adresse IP, le transfert IP doit être activé pour l’interface réseau. Avant de créer une interface réseau, vous devez récupérer l’ID de réseau virtuel avec [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), puis l’ID de sous-réseau avec [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Créez une interface réseau avec [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) dans le sous-réseau *DMZ* avec le transfert IP activé :

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

#### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Pour créer une machine virtuelle et lui attacher une interface réseau existante, vous devez d’abord créer une configuration de machine virtuelle avec [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). La configuration inclut l’interface réseau créée à l’étape précédente. Quand vous êtes invité à indiquer un nom d’utilisateur et un mot de passe, sélectionnez ceux qui vous permettront de vous connecter à la machine virtuelle. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine configuration.
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

Créez la machine virtuelle à l’aide de la configuration de machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant crée une machine virtuelle nommée *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

L’option `-AsJob` crée la machine virtuelle en arrière-plan. Vous pouvez donc passer à l’étape suivante. Lorsque vous y êtes invité, entrez le nom d’utilisateur et le mot de passe qui vous serviront pour vous connecter à la machine virtuelle. Dans les environnements de production, l’appliance virtuelle réseau que vous déployez est souvent une machine virtuelle préconfigurée. Plusieurs appliances virtuelles réseau sont disponibles dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Azure a attribué 10.0.2.4 comme adresse IP privée de la machine virtuelle, 10.0.2.4 étant la première adresse IP disponible dans le sous-réseau *DMZ* de *myVirtualNetwork*.

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel pour pouvoir valider que le trafic provenant du sous-réseau *Public* est acheminé vers le sous-réseau *Private* via l’appliance virtuelle réseau dans une étape ultérieure. 

Créez une machine virtuelle dans le sous-réseau *Public* avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant crée une machine virtuelle nommée *myVmWeb* dans le sous-réseau *Public* du réseau virtuel *myVirtualNetwork*. 

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

Azure a attribué 10.0.0.4 comme adresse IP privée de la machine virtuelle, 10.0.1.4 étant la première adresse IP disponible dans le sous-réseau *Public* de *myVirtualNetwork*.

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

La création de la machine virtuelle prend quelques minutes. Azure a attribué 10.0.1.4 comme adresse IP privée de la machine virtuelle, 10.0.1.4 étant la première adresse IP disponible dans le sous-réseau *Private* de *myVirtualNetwork*. 

Ne passez pas à l’étape suivante avant que la machine virtuelle ne soit créée et qu’Azure ne retourne la sortie à PowerShell.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Acheminer le trafic via une appliance virtuelle réseau

Utilisez [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) pour activer le protocole ICMP entrant sur les machines virtuelles *myVmWeb* et *myVmMgmt* via le Pare-feu Windows pour l’utilisation de tracert afin de tester la communication entre les machines virtuelles dans une étape ultérieure :

```powershell-interactive
Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmWeb `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS

Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmMgmt `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS
```

L’exécution des commandes précédentes peut prendre quelques minutes. Ne passez pas à l’étape suivante tant que les commandes ne sont pas exécutées et que la sortie n’est pas retournée à PowerShell. Bien que cet article utilise tracert pour tester le routage, il n’est pas recommandé d’autoriser le protocole IMCP dans le Pare-feu Windows lors de déploiements en production.

Vous vous connectez à l’adresse IP publique d’une machine virtuelle depuis Internet. Utilisez la commande [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) pour retourner l’adresse IP publique d’une machine virtuelle. L’exemple suivant retourne l’adresse IP publique de la machine virtuelle *myVmMgmt* :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Utilisez la commande suivante pour créer une session Bureau à distance avec la machine virtuelle *myVmMgmt* à partir de votre ordinateur local. Remplacez `<publicIpAddress>` par l’adresse IP retournée par la commande précédente.

```
mstsc /v:<publicIpAddress>
```

Un fichier .rdp (Remote Desktop Protocol) est créé, téléchargé sur votre ordinateur, puis ouvert. Entrez le nom d’utilisateur et le mot de passe indiqués lors de la création de la machine virtuelle (il se peut que vous deviez choisir **Autres choix**, puis **Utiliser un autre compte** pour spécifier les informations d’identification que vous avez entrées lors de la création de la machine virtuelle), puis sélectionnez **OK**. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur **Oui** ou **Continuer** pour continuer le processus de connexion. 

Vous avez activé le transfert IP dans Azure pour l’interface réseau de la machine virtuelle dans [Activer le transfert IP](#enable-ip-forwarding). Dans la machine virtuelle, le système d’exploitation ou une application en cours d’exécution au sein de la machine virtuelle doit également être en mesure de transférer le trafic réseau. Quand vous déployez une appliance virtuelle réseau dans un environnement de production, l’appliance filtre, enregistre ou exécute généralement une autre fonction avant de transférer le trafic. Dans cet article, toutefois, le système d’exploitation transfère simplement tout le trafic qu’il reçoit. Activez le transfert IP au sein du système d’exploitation de la machine virtuelle *myVmNva* :

À partir d’une invite de commandes sur la machine virtuelle *myVmMgmt*, établissez une connexion Bureau à distance à la machine virtuelle *myVmNva* :

``` 
mstsc /v:myVmNva
```
    
Pour activer le transfert IP au sein du système d’exploitation de la machine virtuelle *myVmNva*, entrez la commande suivante dans PowerShell sur la machine virtuelle *myVmNva* :

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Redémarrez la machine virtuelle *myVmNva*, ce qui va également déconnecter la session Bureau à distance en vous laissant dans la session Bureau à distance que vous avez ouverte sur la machine virtuelle *myVmMgmt*.

Une fois la machine virtuelle *myVmNva* redémarrée, utilisez la commande suivante pour tester le routage du trafic réseau vers la machine virtuelle *myVmWeb* à partir de la machine virtuelle *myVmMgmt*.

```
tracert myvmweb
```

La réponse ressemble à ce qui suit :

```
Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
  
Trace complete.
```

Vous pouvez voir que le trafic est acheminé directement de la machine virtuelle *myVmMgmt* vers la machine virtuelle *myVmWeb*. Les itinéraires par défaut d’Azure acheminent directement le trafic entre les sous-réseaux.

Utilisez la commande suivante pour établir la connexion d’un bureau à distance à la machine virtuelle *myVmWeb* à partir de la machine virtuelle *myVmMgmt* :

``` 
mstsc /v:myVmWeb
```

Pour tester le routage du trafic réseau vers la machine virtuelle *myVmMgmt* à partir de la machine virtuelle *myVmWeb*, entrez la commande suivante à partir d’une invite de commandes :

```
tracert myvmmgmt
```

La réponse ressemble à ce qui suit :

```
Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```

Vous pouvez voir que le premier tronçon est 10.0.2.4, ce qui correspond à l’adresse IP privée de l’appliance virtuelle réseau. Le second tronçon est 10.0.1.4, l’adresse IP privée de la machine virtuelle *myVmMgmt*. L’itinéraire ajouté à la table de routage *myRouteTablePublic* et associé au sous-réseau *Public* a contraint Azure à acheminer le trafic via l’appliance virtuelle réseau et non directement au sous-réseau *Private*.

Fermez les sessions Bureau à distance sur les machines virtuelles *myVmWeb* et *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Résoudre les problèmes de routage

Comme vous l’avez appris lors des étapes précédentes, Azure applique les itinéraires par défaut, que vous pouvez éventuellement remplacer par vos propres itinéraires. Parfois, le trafic peut ne pas être acheminé comme vous le voulez. Utilisez [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) pour activer Network Watcher dans la région EastUS, si le service n’est pas déjà présent dans cette région :

```azurepowershell-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region:
$nw = New-AzureRmNetworkWatcher `
 -Location eastus `
 -Name myNetworkWatcher_eastus `
 -ResourceGroupName myResourceGroup
```

Utilisez [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) pour déterminer la façon dont le trafic est acheminé entre deux machines virtuelles. Par exemple, la commande suivante teste le routage du trafic à partir de la machine virtuelle *myVmWeb* (10.0.0.4) vers la machine virtuelle *myVmMgmt* (10.0.1.4) :

```azurepowershell-interactive
$vmWeb = Get-AzureRmVM `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup

Get-AzureRmNetworkWatcherNextHop `
  -DestinationIPAddress 10.0.1.4 `
  -NetworkWatcherName myNetworkWatcher_eastus `
  -ResourceGroupName myResourceGroup `
  -SourceIPAddress 10.0.0.4 `
  -TargetVirtualMachineId $vmWeb.Id
```
La sortie suivante est retournée après une courte attente :

```azurepowershell
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

La sortie vous informe que l’adresse IP du tronçon suivant pour le trafic entre *myVmWeb* et *myVmMgmt* est 10.0.2.4 (la machine virtuelle *myVmNva*), que le type de tronçon suivant est *VirtualAppliance* et que la table de routage à l’origine du routage est *myRouteTablePublic*.

Les itinéraires effectifs pour chaque interface réseau sont une combinaison des itinéraires par défaut d’Azure et des itinéraires que vous définissez. Affichez tous les itinéraires effectifs pour une interface réseau dans une machine virtuelle avec [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). Par exemple, pour afficher les itinéraires effectifs pour l’interface réseau *myVmWeb* dans la machine virtuelle *myVmWeb*, entrez la commande suivante :

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Tous les itinéraires par défaut et l’itinéraire que vous avez ajouté lors d’une étape précédente sont retournés.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin d’un groupe de ressources, utilisez la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe et toutes les ressources qu’il contient.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé une table de routage que vous avez associée à un sous-réseau. Vous avez créé une appliance virtuelle réseau qui a acheminé le trafic d’un sous-réseau public vers un sous-réseau privé. Alors que vous pouvez déployer de nombreuses ressources Azure dans un réseau virtuel, les ressources pour certains services Azure PaaS ne peuvent pas être déployées dans un réseau virtuel. Cependant, vous pouvez toujours restreindre l’accès aux ressources de certains services Azure PaaS au trafic provenant uniquement d’un sous-réseau de réseau virtuel. Passez à l’article suivant pour apprendre à restreindre l’accès réseau aux ressources Azure PaaS.

> [!div class="nextstepaction"]
> [Restreindre l’accès réseau aux ressources PaaS](virtual-network-service-endpoints-configure.md#azure-powershell)