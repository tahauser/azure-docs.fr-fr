---
title: "Créer un réseau virtuel dans Azure - PowerShell | Microsoft Docs"
description: "Découvrez rapidement comment créer un réseau virtuel à l’aide de PowerShell. Un réseau virtuel permet à de nombreux types de ressources Azure de communiquer en privé."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: dd8203763eb6abd19e2b3483636dc4d80f7effdf
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Créer un réseau virtuel à l’aide de PowerShell

Dans cet article, vous allez apprendre à créer un réseau virtuel. Une fois le réseau virtuel créé, déployez deux machines virtuelles dans le réseau virtuel pour tester la communication réseau en privé entre elles.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module AzureRM PowerShell version 5.1.1 ou ultérieure pour les besoins de cet article. Pour trouver la version installée, exécutez ` Get-Module -ListAvailable AzureRM`. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*. Toutes les ressources Azure sont créées dans une localisation (ou région) Azure.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel avec [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L’exemple suivant crée un réseau virtuel par défaut nommé *myVirtualNetwork* dans la région *EastUS* :

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/24
```

Un ou plusieurs préfixes d’adresse sont affectés à tous les réseaux virtuels. L’espace d’adressage est spécifié en notation CIDR. L’espace d’adressage 10.0.0.0/24 englobe 10.0.0.0-10.0.0.254. Les réseaux virtuels comprennent zéro ou plusieurs sous-réseaux. Les ressources sont déployées dans un sous-réseau dans un réseau virtuel. 

Créez une configuration de sous-réseau à l’aide de la commande [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Tous les sous-réseaux ont un préfixe d’adresse qui existe dans le préfixe d’adresse du réseau virtuel. Dans cet exemple, une configuration de sous-réseau avec le même préfixe d’adresse que celui du réseau virtuel est créé :

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Bien que le préfixe d’adresse du sous-réseau englobe 10.0.0.0-10.0.0.254, seules les adresses 10.0.0.4-10.0.0.254 sont disponibles car Azure réserve les quatre premières adresses (0-3) et la dernière adresse de chaque sous-réseau. Étant donné que le préfixe d’adresse du sous-réseau est le même que celui du réseau virtuel, un seul sous-réseau peut exister dans ce réseau virtuel.

Écrivez la configuration du sous-réseau dans le réseau virtuel à l’aide de la commande [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), ce qui crée le sous-réseau :

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="test-network-communication"></a>Tester la communication réseau

Un réseau virtuel permet à plusieurs types de ressources Azure de communiquer en privé. Une machine virtuelle est l’un des types de ressource que vous pouvez déployer dans un réseau virtuel. Créez deux machines virtuelles dans le réseau virtuel pour pouvoir valider ultérieurement la communication en privé entre elles.

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez une machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Lors de l’exécution de cette étape, vous êtes invité à saisir vos informations d’identification. Les valeurs que vous saisissez sont configurées comme le nom d’utilisateur et le mot de passe pour la machine virtuelle. Une machine virtuelle doit être créée dans la même localisation que le réseau virtuel. Bien que cela soit le cas dans cet article, la machine virtuelle ne doit pas nécessairement figurer dans le même groupe de ressources que le réseau virtuel. Étant donné que le paramètre `-AsJob` permet à la commande de s’exécuter en arrière-plan, vous pouvez passer à la tâche suivante.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Une sortie semblable à celle de l’exemple suivant est retournée, et Azure démarre la création de la machine virtuelle en arrière-plan.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

Azure DHCP affecte automatiquement 10.0.0.4 à la machine virtuelle durant la création, car il s’agit de la première adresse disponible dans le sous-réseau *default*.

Créez une deuxième machine virtuelle. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```
La création de la machine virtuelle prend quelques minutes. Une fois créé, Azure retourne en sortie des informations sur la machine virtuelle créée. Bien que ceci ne figure pas dans les informations retournées, Azure a affecté *10.0.0.5* (la prochaine adresse disponible dans le sous-réseau) à la machine virtuelle *myVm2*.

### <a name="connect-to-a-virtual-machine"></a>Connexion à une machine virtuelle

Utilisez la commande [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) pour retourner l’adresse IP publique d’une machine virtuelle. Par défaut, Azure affecte une adresse IP routable Internet publique à chaque machine virtuelle. L’adresse IP publique est affectée à la machine virtuelle à partir d’un [pool d’adresses affecté à chaque région Azure](https://www.microsoft.com/download/details.aspx?id=41653). Si Azure a connaissance de l’adresse IP publique qui est affectée à une machine virtuelle, le système d’exploitation en cours d’exécution dans une machine virtuelle l’ignore. L’exemple suivant retourne l’adresse IP publique de la machine virtuelle *myVm1* :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name myVm1 -ResourceGroupName myResourceGroup | Select IpAddress
```

Utilisez la commande suivante pour créer une session Bureau à distance avec la machine virtuelle *myVm1* à partir de votre ordinateur local. Remplacez `<publicIpAddress>` par l’adresse IP retournée par la commande précédente.

```
mstsc /v:<publicIpAddress>
```

Un fichier .rdp (Remote Desktop Protocol) est créé, téléchargé sur votre ordinateur, puis ouvert. Entrez le nom d’utilisateur et le mot de passe spécifiés au moment de la création de la machine virtuelle, puis cliquez sur **OK**. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur **Oui** ou **Continuer** pour continuer le processus de connexion.

### <a name="validate-communication"></a>Valider la communication

Toute tentative de test ping sur une machine virtuelle Windows échoue car, par défaut, ce test n’est pas autorisé à franchir le pare-feu Windows. Pour autoriser un test ping sur *myVm1*, entrez la commande suivante à partir d’une invite de commandes :

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Pour valider la communication avec *myVm2*, entrez la commande suivante à partir d’une invite de commandes sur la machine virtuelle *myVm1*. Fournissez les informations d’identification que vous avez utilisées au moment de la création de la machine virtuelle, puis terminez la connexion :

```
mstsc /v:myVm2
```

La connexion Bureau à distance aboutit, ce qui s’explique par le fait que des adresses IP privées sont affectées aux deux machines virtuelles à partir du sous-réseau *default* et que, par défaut, le Bureau à distance est ouvert dans le pare-feu Windows. Vous pouvez vous connecter à *myVm2* par nom d’hôte, car Azure fournit automatiquement la résolution de nom DNS pour tous les hôtes au sein d’un réseau virtuel. À partir d’une invite de commandes, effectuez un test ping sur *myVm1* à partir de *myVm2*.

```
ping myvm1
```

Le test ping aboutit car vous l’avez autorisé à franchir le pare-feu Windows sur la machine virtuelle *myVm1* lors d’une étape précédente. Pour confirmer les communications sortantes à destination d’Internet, utilisez la commande suivante :

```
ping bing.com
```

Vous recevez quatre réponses de bing.com. Par défaut, toute machine virtuelle dans un réseau virtuel prend en charge les communications sortantes à destination d’Internet.

Fermez la session Bureau à distance. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin d’un groupe de ressources, utilisez la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe et toutes les ressources qu’il contient.

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez déployé un réseau virtuel par défaut avec un sous-réseau. Pour découvrir comment créer un réseau virtuel personnalisé avec plusieurs sous-réseaux, passez au didacticiel couvrant la création d’un réseau virtuel personnalisé.

> [!div class="nextstepaction"]
> [Créer un réseau virtuel personnalisé](virtual-networks-create-vnet-arm-pportal.md#powershell)
