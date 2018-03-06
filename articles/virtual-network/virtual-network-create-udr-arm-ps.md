---
title: "Créer un routage défini par l'utilisateur pour router le trafic réseau via une appliance virtuelle réseau - PowerShell | Microsoft Docs"
description: "Découvrez comment créer un routage défini par l’utilisateur pour remplacer le routage par défaut d’Azure en routant le trafic réseau via une appliance virtuelle réseau."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 70ddec1c7ba76ef7f42048896079e5c5fa2bf60c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-user-defined-route---powershell"></a>Créer un routage défini par l’utilisateur - PowerShell

Ce didacticiel explique comment créer un routage (ou itinéraire) défini par l'utilisateur pour router le trafic entre deux sous-réseaux d’un [réseau virtuel](virtual-networks-overview.md) via une appliance virtuelle réseau. Une appliance virtuelle réseau est une machine virtuelle qui exécute une application réseau telle qu’un pare-feu. Pour en savoir plus sur les appliances virtuelles réseau préconfigurées que vous pouvez déployer dans un réseau virtuel Azure, voir [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Lorsque vous créez des sous-réseaux dans un réseau virtuel, Azure crée des [routes système](virtual-networks-udr-overview.md#system-routes) par défaut qui permettent aux ressources dans tous les sous-réseaux de communiquer entre elles, comme illustré dans l’image suivante :

![Itinéraires par défaut](./media/create-user-defined-route/default-routes.png)

Dans ce didacticiel, vous allez créer un réseau virtuel comprenant des sous-réseaux public, privé et de périmètre (DMZ), comme illustré dans l’image qui suit. Généralement, des serveurs web peuvent être déployés sur un sous-réseau public, et une application ou un serveur de base de données peuvent être déployés sur un sous-réseau privé. Vous créez une machine virtuelle faisant office d’appliance virtuelle réseau dans le sous-réseau de périmètre (DMZ) et, éventuellement, une machine virtuelle dans chaque sous-réseau communiquant via l’appliance virtuelle réseau. Tout le trafic entre les sous-réseaux privés et publics est routé via l’appliance, comme indiqué dans l’image suivante :

![Itinéraires définis par l’utilisateur](./media/create-user-defined-route/user-defined-routes.png)

Cet article explique comment créer un routage (ou itinéraire) défini par l'utilisateur à l’aide du modèle de déploiement Resource Manager, qui est le modèle de déploiement que nous recommandons d’utiliser lors de la création de routages définis par l'utilisateur. Si vous avez besoin créer un routage défini par l’utilisateur (classique), voir [Création des itinéraires définis par l’utilisateur](virtual-network-create-udr-classic-ps.md). Si vous ne connaissez pas les modèles de déploiement Azure, consultez [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pour en savoir plus sur les routages définis par l’utilisateur, voir [Vue d’ensemble des routages définis par l’utilisateur](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Créer des itinéraires et une appliance virtuelle réseau

Vous pouvez installer et configurer la dernière version du module PowerShell [AzureRM](https://www.powershellgallery.com/packages/AzureRM/) sur votre PC, ou cliquer simplement le bouton **Essayez** dans n’importe quel script pour exécuter les scripts dans Azure Cloud Shell. Le module PowerShell AzureRM est installé dans Cloud Shell.
 
1. **Condition préalable** : créez un réseau virtuel comprenant deux sous-réseaux en procédant de la manière décrite dans [Créer un réseau virtuel](#create-a-virtual-network).
2. Si vous exécutez PowerShell depuis votre ordinateur, connectez-vous à Azure avec votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) à l’aide de la commande `login-azurermaccount`. Si vous utilisez Cloud Shell, vous êtes automatiquement connecté. Vous devrez peut-être redémarrer Cloud Shell pour quitter l’interpréteur de commandes Bash utilisé lors de la création du réseau virtuel requis.
3. Définissez quelques variables utilisées dans les étapes :

    ```azurepowershell-interactive
    $rgName="myResourceGroup"
    $location="eastus"
    ```

4. Créez un sous-réseau de périmètre (DMZ) dans le réseau virtuel existant :

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName 
    Add-AzureRmVirtualNetworkSubnetConfig `
      -Name 'DMZ' `
      -AddressPrefix "10.0.2.0/24" `
      -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzureRmVirtualNetwork
    ```

5. Créez une adresse IP publique statique pour la machine virtuelle de l’appliance virtuelle réseau (NVA).

    ```azurepowershell-interactive
    $Pip = New-AzureRmPublicIpAddress `
      -AllocationMethod Static `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name myPublicIp-myVm-Nva
    
6. Create a network interface in the *DMZ* subnet, assign a static private IP address to it, and enable IP forwarding for the network interface. By assigning a static IP address to the network interface, you ensure that it doesn't change for the life of the virtual machine the network interface is attached to. IP forwarding must be enabled for each network interface that receives traffic not addressed to an IP address assigned to it.

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig `
      -Name DMZ `
      -VirtualNetwork $virtualNetwork
    $nic = New-AzureRmNetworkInterface `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name 'myNic-Nva' `
      -SubnetId $subnet.Id `
      -PrivateIpAddress 10.0.2.4 `
      -EnableIPForwarding `
      -PublicIpAddressId $Pip.Id 
    ```

7. Créez la machine virtuelle de la NVA. La NVA peut être une machine virtuelle exécutant le système d’exploitation Windows ou Linux. Pour créer la machine virtuelle, copiez le script d’un de ces systèmes d’exploitation et collez-le dans votre session PowerShell. Si vous créez une machine virtuelle Windows, collez le script dans un éditeur de texte, modifiez la "valeur" de la variable $cred, puis collez le texte modifié dans votre session PowerShell :

    **Linux**

    ```azurepowershell-interactive
    # Define the admin user name and a blank password.
    $securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

    # Define the virtual machine configuration.
    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Linux `
      -ComputerName 'myVm-Nva' `
      -Credential $cred -DisablePasswordAuthentication| `
      Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 14.04.2-LTS `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"    

    # Create the virtual machine
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

    **Windows**

    ```azurepowershell-interactive
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."

    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName 'myVm-Nva' `
      -Credential $cred | `
      Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id
    
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

8. Par défaut, Azure route le trafic entre tous les sous-réseaux au sein d’un réseau virtuel. Créez un itinéraire pour modifier le routage par défaut d’Azure, de sorte que le trafic en provenance du sous-réseau *Public* soit routé vers l’appliance virtuelle réseau, plutôt que directement vers le sous-réseau *Privé*.

    ```azurepowershell-interactive    
    $routePrivate = New-AzureRmRouteConfig `
      -Name 'ToPrivateSubnet' `
      -AddressPrefix 10.0.1.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

9. Créez une table de routage pour le sous-réseau *Public*.

    ```azurepowershell-interactive
    $routeTablePublic = New-AzureRmRouteTable `
      -Name 'myRouteTable-Public' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePrivate
    ```
    
10. Associez la table de routage au sous-réseau Public. En associant une table de routage à un sous-réseau, Azure achemine tout le trafic sortant du sous-réseau selon les routages de la table de routage. Une table de routage peut être associée à zéro ou plusieurs sous-réseaux, tandis qu’un sous-réseau peut être associé à zéro ou une table de routage.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Public' `
      -AddressPrefix 10.0.0.0/24 `
      -RouteTable $routeTablePublic | `
    Set-AzureRmVirtualNetwork
    ```

11. Créez un routage pour le trafic entre le sous-réseau *Privé* et le sous-réseau *Public* via la machine virtuelle de la NVA.

    ```azurepowershell-interactive    
    $routePublic = New-AzureRmRouteConfig `
      -Name 'ToPublicSubnet' `
      -AddressPrefix 10.0.0.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

12. Créez la table de routage du sous-réseau *Privé*.

    ```azurepowershell-interactive
    $routeTablePrivate = New-AzureRmRouteTable `
      -Name 'myRouteTable-Private' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePublic
    ```
      
13. Associez la table de routage au sous-réseau *Privé*.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Private' `
      -AddressPrefix 10.0.1.0/24 `
      -RouteTable $routeTablePrivate | `
    Set-AzureRmVirtualNetwork
    ```
    
14. **Facultatif :** créez une machine virtuelle dans les sous-réseaux public et privé, et validez le fait que la communication entre les machines virtuelles est acheminée via l’appliance virtuelle réseau en procédant de la manière décrite dans [Valider le routage](#validate-routing).
15. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-resources).

## <a name="validate-routing"></a>Valider le routage

1. Si ce n’est pas encore fait, suivez les étapes décrites dans [Créer des itinéraires et une appliance virtuelle réseau](#create-routes-and-network-virtual-appliance).
2. Cliquez sur le bouton **Essayez** dans la zone qui suit, ce qui a pour effet d’ouvrir Azure Cloud Shell. Si vous y êtes invité, connectez-vous à Azure à l’aide votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p). Azure Cloud Shell est un interpréteur de commandes Bash gratuit, avec l’interface de ligne de commande Azure préinstallée. 

    Les scripts suivants créent deux machines virtuelles, l’une dans le sous-réseau *Public*, et l’autre dans le sous-réseau *Privé*. Les scripts permettent également le transfert IP pour l’interface réseau au sein du système d’exploitation de l’appliance virtuelle réseau pour permettre au système d’exploitation de router le trafic via l’interface réseau. Une appliance virtuelle réseau de production inspecte généralement le trafic avant de le router mais, dans ce didacticiel, l’appliance virtuelle réseau route simplement le trafic sans l’inspecter. 

    Cliquez sur le bouton **Copier** dans les scripts **Linux** ou **Windows** qui suivent, et collez le contenu du script dans un éditeur de texte. Modifiez le mot de passe dans la variable *adminPassword*, puis collez le script dans Azure Cloud Shell. Exécutez le script pour le système d’exploitation que vous avez sélectionné lorsque vous avez créé l’appliance virtuelle réseau à l’étape 7 de la procédure [Créer des itinéraires et une appliance virtuelle réseau](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Validez la communication entre les machines virtuelles des sous-réseaux public et privé. 

    - Ouvrez une connexion [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) ou [Bureau à distance](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows) à l’adresse IP publique de la machine virtuelle *myVm-Public*.
    - À partir d’une invite de commandes sur la machine virtuelle *myVm-Public*, entrez `ping myVm-Private`. Vous recevez des réponses parce que l’appliance virtuelle réseau route le sous-réseau public vers le sous-réseau privé.
    - À partir de la machine virtuelle *myVm-Public*, exécutez une commande de suivi de l’itinéraire entre les machines virtuelles des sous-réseaux public et privé. Entrez la commande appropriée qui suit, selon le système d’exploitation installé sur les machines virtuelles des sous-réseaux Public et Privé :
        - **Windows** : à partir d'une invite de commandes, exécutez la commande `tracert myvm-private`.
        - **Ubuntu** : exécutez la commande `tracepath myvm-private`.
      Le trafic transite par 10.0.2.4 (l’appliance virtuelle réseau) avant d’atteindre 10.0.1.4 (la machine virtuelle dans le sous-réseau Privé). 
    - Exécutez les étapes précédentes en vous connectant à la machine virtuelle *myVm-Private* et en effectuant un test ping sur la machine virtuelle *myVm-Public*. La commande de suivi de l’itinéraire montre la communication transitant via 10.0.2.4 avant d’atteindre 10.0.0.4 (la machine virtuelle dans le sous-réseau Public).
    
      > [!NOTE]
      > Les étapes précédentes vous permettent de confirmer le routage entre les adresses IP privées Azure. Si vous voulez transférer ou rediriger via proxy le trafic vers des adresses IP publiques par le biais d’une appliance virtuelle réseau :
      > - L’application doit fournir la traduction d’adresses réseau ou la fonctionnalité de proxy. Si vous utilisez la traduction d’adresses réseau, l’appliance doit convertir l’adresse IP source en sa propre adresse, puis transférer cette requête à l’adresse IP publique. Si l’appliance a traduit l’adresse source ou redirige via proxy, Azure traduit l’adresse IP privée de l’appliance virtuelle réseau en adresse IP publique. Pour plus d’informations sur les différentes méthodes qu’Azure utilise pour traduire des adresses IP privées en adresses IP publiques, consultez [Présentation des connexions sortantes](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
      > - Itinéraire supplémentaire dans la table de routage tel que le préfixe : 0.0.0.0/0, le type de tronçon suivant VirtualAppliance et l’adresse IP de tronçon suivant 10.0.2.4 (dans l’exemple de script précédent).
      >
    - **Facultatif** : utilisez la fonctionnalité de tronçon suivant d'Azure Network Watcher pour valider le tronçon suivant entre deux machines virtuelles dans Azure. Avant d’utiliser Network Watcher, vous devez [créer une instance Azure Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour la région dans laquelle vous souhaitez l’utiliser. Ce didacticiel utilise la région Est des États-Unis. Une fois que vous avez activé une instance Network Watcher pour la région, entrez la commande suivante pour consulter les informations du tronçon suivant entre les machines virtuelles des sous-réseaux Public et Privé :
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       Le résultat renvoie *10.0.2.4* pour **nextHopIpAddress** et *VirtualAppliance* pour **nextHopType**.

> [!NOTE]
> Pour illustrer les concepts de ce didacticiel, des adresses IP publiques sont attribuées aux machines virtuelles des sous-réseaux Public et Privé, et l'accès à tous les ports réseau est activé dans Azure pour les deux machines virtuelles. Lorsque vous créez des machines virtuelles pour la production, vous ne pouvez pas leur attribuer des adresses IP publiques mais vous pouvez filtrer le trafic réseau vers le sous-réseau Privé en déployant en amont une appliance virtuelle réseau ou en attribuant un groupe de sécurité réseau aux sous-réseaux, à l'interface réseau, ou aux deux. Pour en savoir plus sur les groupes de sécurité réseau, voir [Groupes de sécurité réseau](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Ce didacticiel nécessite de disposer d’un réseau virtuel avec deux sous-réseaux. Pour créer rapidement un réseau virtuel, cliquez sur le bouton **Essayez** dans la zone qui suit. Cliquer sur le bouton **Essayez** a pour effet d’ouvrir [Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Si Azure Cloud Shell exécute PowerShell ou un interpréteur de commandes Bash, dans cette section, l’interpréteur de commandes Bash est utilisé pour créer le réseau virtuel. L’interface de ligne de commande Azure est installée dans l'interpréteur de commandes Bash. Si Azure Cloud Shell vous y invite, connectez-vous à Azure à l’aide de votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p). Pour créer le réseau virtuel utilisé dans ce didacticiel, cliquez sur le bouton **Copier** dans la zone suivante, puis collez le script dans Azure Cloud Shell :

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Pour en savoir plus sur la façon d’utiliser le portail, PowerShell ou un modèle Azure Resource Manager afin de créer un réseau virtuel, voir [Créer un réseau virtuel](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Supprimer des ressources

Lorsque vous aurez terminé ce didacticiel, vous souhaiterez peut-être supprimer les ressources créées afin que leur utilisation ne soit pas facturée. La suppression d’un groupe de ressources supprime également toutes les ressources qu’il contient. Dans PowerShell, entrez la commande suivante :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

- Créez une [appliance virtuelle réseau hautement disponible](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Souvent, plusieurs interfaces réseau et adresses IP sont attribuées aux appliances virtuelles réseau. Découvrez comment [ajouter des interfaces réseau à une machine virtuelle existante](virtual-network-network-interface-vm.md#vm-add-nic) et [ajouter des adresses IP à une interface réseau existante](virtual-network-network-interface-addresses.md#add-ip-addresses). Même si au moins deux interfaces réseau peuvent être attribuées à des machines virtuelles, quelle que soit leur taille, chaque taille de machine virtuelle prend en charge un nombre maximal d’interfaces réseau. Pour connaître le nombre d’interfaces réseau pris en charge par chaque la taille de machine virtuelle, consultez les tailles de machine virtuelle [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) et [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Créez un itinéraire défini par l’utilisateur pour forcer le trafic local via une [connexion VPN site à site](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
