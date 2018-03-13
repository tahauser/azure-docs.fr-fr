---
title: Personnaliser une machine virtuelle Windows dans Azure | Microsoft Docs
description: "Découvrez comment utiliser l’extension de script personnalisée pour automatiser les installations d’application sur les machines virtuelles Windows dans Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 63858da0a4a47d67ec659e922ab10f9f7bc97938
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Comment personnaliser une machine virtuelle Windows dans Azure
Pour configurer des machines virtuelles de manière rapide et cohérente, une certaine forme d’automatisation est généralement souhaitée. Une approche courante pour personnaliser une machine virtuelle Windows consiste à utiliser [l’extension de script personnalisé pour Windows](extensions-customscript.md). Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Utiliser une extension de script personnalisé pour installer IIS
> * Créer une machine virtuelle qui utilise l’extension de script personnalisé
> * Afficher un site IIS en cours d’exécution après l’application de l’extension

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.3 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 


## <a name="custom-script-extension-overview"></a>Vue d’ensemble de l’extension de script personnalisé
L’extension de script personnalisé télécharge et exécute des scripts sur des machines virtuelles Azure. Cette extension est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Des scripts peuvent être téléchargés à partir de Stockage Azure ou de GitHub, ou fournis dans le portail Azure lors de l’exécution de l’extension.

L’extension de script personnalisé s’intègre aux modèles Azure Resource Manager et peut être exécutée à l’aide de l’interface de ligne de commande Azure, de PowerShell, du portail Azure ou de l’API REST de machine virtuelle Azure.

Vous pouvez utiliser l’extension de script personnalisé avec les machines virtuelles Linux et Windows.


## <a name="create-virtual-machine"></a>Create virtual machine
Tout d’abord, définissez un nom d’utilisateur administrateur et un mot de passe pour la machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = Get-Credential
```

Vous pouvez maintenant créer la machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant permet de créer une machine virtuelle nommée *myVM* dans l’emplacement *EastUS*. S’ils n’existent pas, le groupe de ressources *myResourceGroupAutomate* et les ressources réseau prises en charge sont créés. Pour autoriser le trafic web, l’applet de commande ouvre également le port *80*.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Quelques minutes sont nécessaires à la création des ressources et de la machine virtuelle.


## <a name="automate-iis-install"></a>Automatiser l’installation d’IIS
Utilisez [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) pour installer l’extension de script personnalisé. L’extension exécute `powershell Add-WindowsFeature Web-Server` pour installer le serveur web IIS, puis met à jour la page *Default.htm* pour qu’elle affiche le nom d’hôte de la machine virtuelle :

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Tester le site web
Obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L’exemple suivant obtient l’adresse IP pour l’adresse *myPublicIPAddress* créée précédemment :

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Vous pouvez alors entrer l’adresse IP publique dans un navigateur web. Le site web s’affiche, avec notamment le nom d’hôte de la machine virtuelle sur laquelle l’équilibreur de charge a distribué le trafic comme dans l’exemple suivant :

![Site web IIS en cours d’exécution](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez automatisé l’installation d’IIS sur une machine virtuelle. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Utiliser une extension de script personnalisé pour installer IIS
> * Créer une machine virtuelle qui utilise l’extension de script personnalisé
> * Afficher un site IIS en cours d’exécution après l’application de l’extension

Passez au didacticiel suivant pour découvrir comment créer des images de machine virtuelle personnalisées.

> [!div class="nextstepaction"]
> [Créer des images de machine virtuelle personnalisées](./tutorial-custom-images.md)
