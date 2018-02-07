---
title: "Créer des machines virtuelles exécutant une pile SQL&#92;IIS&#92;.NET dans Azure | Microsoft Docs"
description: Didacticiel - Installer une pile Azure SQL, IIS, .NET sur une machine virtuelle Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6533ab205e07243e2f757ea0a66028e1d140c52b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>Installer une pile SQL&#92;IIS&#92;.NET dans Azure

Dans ce didacticiel, nous installons une pile SQL&#92;IIS&#92;.NET avec Azure PowerShell. Cette pile se compose de deux machines virtuelles exécutant Windows Server 2016 : une avec IIS et .NET, et l’autre avec SQL Server.

> [!div class="checklist"]
> * Créer une machine virtuelle avec New-AzVM
> * Installer IIS et le kit SDK .NET Core sur la machine virtuelle
> * Créer une machine virtuelle exécutant SQL Server
> * Installer l’extension SQL Server

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.1.1 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-iis-vm"></a>Créer une machine virtuelle IIS 

Dans cet exemple, nous utilisons l’applet de commande [New-AzVM](https://www.powershellgallery.com/packages/AzureRM.Compute.Experiments) de PowerShell Cloud Shell pour créer rapidement une machine virtuelle Windows Server 2016, puis installer IIS et le .NET Framework. Les machines virtuelles IIS et SQL partagent un groupe de ressources et le réseau virtuel. Nous allons donc créer des variables pour ces noms.

Cliquez sur le bouton **Essayer** situé en haut à droite du bloc de code pour lancer Cloud Shell dans cette fenêtre. Vous devez fournir les informations d’identification de la machine virtuelle à l’invite de commandes.

```azurepowershell-interactive
$vmName = "IISVM$(Get-Random)"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRMVm -Name $vmName -ResourceGroupName $resourceGroup -VirtualNetworkName $vNetName 
```

Installez IIS et le .NET Framework avec l’extension de script personnalisé.

```azurepowershell-interactive

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="azure-sql-vm"></a>Machine virtuelle Azure SQL

Nous allons utiliser une image préconfigurée de la Place de marché Azure d’un serveur SQL Server pour créer la machine virtuelle SQL. Nous créons d’abord la machine virtuelle, puis nous installons l’extension SQL Server dessus. 


```azurepowershell-interactive
# Create user object. You get a pop-up prompting you to enter the credentials for the VM.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroup
Add-AzureRmVirtualNetworkSubnetConfig -Name mySQLSubnet -VirtualNetwork $vNet -AddressPrefix "192.168.2.0/24"
Set-AzureRmVirtualNetwork -VirtualNetwork $vNet


# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location eastus `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow


# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location eastus `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name mySQLNic -ResourceGroupName $resourceGroup -Location eastus `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName mySQLVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName mySQLVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftSQLServer -Offer SQL2014SP2-WS2012R2 -Skus Enterprise -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM -ResourceGroupName $resourceGroup -Location eastus -VM $vmConfig
```

Utilisez [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) pour ajouter [l’extension SQL Server](/sql/virtual-machines-windows-sql-server-agent-extension.md) à la machine virtuelle SQL.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension -ResourceGroupName $resourceGroup -VMName mySQLVM -name "SQLExtension"
```

## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez installé une pile SQL&#92;IIS&#92;.NET avec Azure PowerShell. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle avec New-AzVM
> * Installer IIS et le kit SDK .NET Core sur la machine virtuelle
> * Créer une machine virtuelle exécutant SQL Server
> * Installer l’extension SQL Server

Passez au didacticiel suivant pour savoir comment sécuriser un serveur web IIS avec des certificats SSL.

> [!div class="nextstepaction"]
> [Sécuriser un serveur web IIS à l’aide de certificats SSL](tutorial-secure-web-server.md)

