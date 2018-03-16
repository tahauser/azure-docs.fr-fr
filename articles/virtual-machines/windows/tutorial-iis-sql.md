---
title: "Créer des machines virtuelles exécutant une pile SQL&#92;IIS&#92;.NET dans Azure | Microsoft Docs"
description: Didacticiel - Installer une pile Azure SQL, IIS, .NET sur une machine virtuelle Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ac6038f7600d6eb4c8d021998f9cfc40bd369332
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>Installer une pile SQL&#92;IIS&#92;.NET dans Azure

Dans ce didacticiel, nous installons une pile SQL&#92;IIS&#92;.NET avec Azure PowerShell. Cette pile se compose de deux machines virtuelles exécutant Windows Server 2016 : une avec IIS et .NET, et l’autre avec SQL Server.

> [!div class="checklist"]
> * Créer une machine virtuelle 
> * Installer IIS et le kit SDK .NET Core sur la machine virtuelle
> * Créer une machine virtuelle exécutant SQL Server
> * Installer l’extension SQL Server

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module AzureRM.Compute version 4.3.1 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM.Compute` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-iis-vm"></a>Créer une machine virtuelle IIS 

Dans cet exemple, nous utilisons la cmdlet [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) de PowerShell Cloud Shell pour créer rapidement une machine virtuelle Windows Server 2016, puis installer IIS et le .NET Framework. Les machines virtuelles IIS et SQL partagent un groupe de ressources et le réseau virtuel. Nous allons donc créer des variables pour ces noms.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Installez IIS et le .NET Framework avec l’extension de script personnalisé.

```azurepowershell-interactive
Set-AzureRmVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Créer un autre sous-réseau

Créez un second sous-réseau pour la machine virtuelle SQL. Obtenez le réseau virtuel à l’aide de [Get-AzureRmVirtualNetwork]{/powershell/module/azurerm.network/get-azurermvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzureRmVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Créez une configuration pour le sous-réseau en utilisant [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Mettre à jour le réseau virtuel avec les informations du nouveau sous-réseau en utilisant [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)
   
```azurepowershell-interactive   
$vNet | Set-AzureRmVirtualNetwork
```

## <a name="azure-sql-vm"></a>Machine virtuelle Azure SQL

Utilisez une image préconfigurée d’Azure marketplace d’un serveur SQL Server pour créer la machine virtuelle SQL. Nous créons d’abord la machine virtuelle, puis nous installons l’extension SQL Server dessus. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Utilisez [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) pour ajouter [l’extension SQL Server](/sql/virtual-machines-windows-sql-server-agent-extension.md) à la machine virtuelle SQL.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez installé une pile SQL&#92;IIS&#92;.NET avec Azure PowerShell. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle 
> * Installer IIS et le kit SDK .NET Core sur la machine virtuelle
> * Créer une machine virtuelle exécutant SQL Server
> * Installer l’extension SQL Server

Passez au didacticiel suivant pour savoir comment sécuriser un serveur web IIS avec des certificats SSL.

> [!div class="nextstepaction"]
> [Sécuriser un serveur web IIS à l’aide de certificats SSL](tutorial-secure-web-server.md)

