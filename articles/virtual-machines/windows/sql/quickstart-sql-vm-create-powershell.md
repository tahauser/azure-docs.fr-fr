---
title: "Créer une machine virtuelle Windows SQL Server avec Azure PowerShell | Microsoft Docs"
description: "Ce didacticiel montre comment créer une machine virtuelle Windows SQL Server 2017 avec Azure PowerShell."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: daa5043a948e660b6c3e685e933855afff8f7671
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Démarrage rapide : créer une machine virtuelle Windows SQL Server avec Azure PowerShell

Ce démarrage rapide décrit les étapes de base de création d’une machine virtuelle SQL Server avec Azure PowerShell.

> [!TIP]
> Ce démarrage rapide vous présente les étapes de mise en service et de connexion rapide d’une machine virtuelle SQL. Pour plus d’informations sur les autres options Azure PowerShell pour la création de machines virtuelles SQL, consultez le [guide de mise en service des machines virtuelles SQL Server avec Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

> [!TIP]
> Si vous avez des questions sur les machines virtuelles SQL Server, consultez le [Forum aux Questions](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Obtenir un abonnement Azure

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


## <a id="powershell"></a> Obtenir Azure PowerShell

Ce démarrage rapide requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-powershell"></a>Configurer PowerShell

1. Ouvrez PowerShell et accédez à votre compte Azure en exécutant la commande **Add-AzureRmAccount**.

   ```PowerShell
   Add-AzureRmAccount
   ```

1. Un écran de connexion vous invitant à entrer vos informations d’identification devrait s’afficher. Utilisez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

1. Définissez une variable avec un nom unique de groupe de ressources. Pour simplifier la suite du démarrage rapide, les autres commandes utilisent ce nom comme base des autres noms de ressources.

   ```PowerShell
   $ResourceGroupName = "sqlvm1"
   ```

1. Définissez un emplacement de région Azure cible pour l’ensemble des ressources de machine virtuelle.

   ```PowerShell
   $Location = "East US"
   ```

1. Créez le groupe de ressources.

   ```PowerShell
   New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Configurer les paramètres réseau

1. Créez un réseau virtuel, un sous-réseau et une adresse IP publique. Ces ressources sont utilisées pour fournir une connectivité réseau à la machine virtuelle et la connecter à Internet.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Créer des groupes de sécurité réseau Configurez des règles autorisant les connexions du Bureau à distance et de SQL Server.

   ```PowerShell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Créez l’interface réseau.

   ```PowerShell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Créer la machine virtuelle SQL

1. Définissez les informations d’identification vous permettant de vous connecter à la machine virtuelle. Le nom d'utilisateur est azureadmin. Veillez à modifier le mot de passe avant d’exécuter la commande.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString 'Change.This!000' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Créez un objet de configuration de machine virtuelle, puis créez la machine virtuelle. La commande suivante crée une machine virtuelle SQL Server 2017 Developer Edition sur Windows Server 2016.

   ```PowerShell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzureRmVMConfig -VMName $VMName -VMSize Standard_DS13 | `
      Set-AzureRmVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate | `
      Set-AzureRmVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" | `
      Add-AzureRmVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > La création de la machine virtuelle nécessite quelques minutes.

## <a name="install-the-sql-iaas-agent"></a>Installer l’agent IaaS SQL

Pour obtenir les fonctionnalités d’intégration au portail et de machine virtuelle SQL, vous devez installer l’[extension de l’agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Pour installer l’agent sur la nouvelle machine virtuelle, exécutez la commande suivante après sa création.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Bureau à distance dans la machine virtuelle

1. La commande suivante permet de récupérer l’adresse IP publique associée à la nouvelle machine virtuelle.

   ```PowerShell
   Get-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Utilisez ensuite l’adresse IP renvoyée pour la passer en tant que paramètre de ligne de commande à **mstsc**, ceci pour démarrer une session de Bureau à distance dans la nouvelle machine virtuelle.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Lorsque l’invite de saisie des informations d’identification s’ouvre, entrez les données correspondant à un compte différent. Saisissez le nom d’utilisateur en le faisant précéder d’une barre oblique inverse (par exemple, `\azureadmin`), puis le mot de passe défini auparavant dans ce démarrage rapide.

## <a name="connect-to-sql-server"></a>Se connecter à SQL Server

1. Une fois connecté à la session du Bureau à distance, lancez **SQL Server Management Studio 2017** à partir du menu de démarrage.

1. Dans la boîte de dialogue **Se connecter au serveur**, conservez les valeurs par défaut. Le nom du serveur est le nom de la machine virtuelle. L’authentification est définie sur **Authentification Windows**. Cliquez sur **Connecter**.

Vous êtes désormais connecté localement à SQL Server. Si vous souhaitez vous connecter à distance, il vous faut [configurer la connectivité](virtual-machines-windows-sql-connect.md) à partir du portail ou manuellement.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez pas besoin que la machine virtuelle fonctionne en permanence, vous pouvez éviter les dépenses inutiles en l’arrêtant lorsque vous ne vous en servez pas. La commande suivante arrête la machine virtuelle, tout en la laissant disponible pour une utilisation future.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Vous pouvez aussi supprimer définitivement toutes les ressources associées à la machine virtuelle à l’aide de la commande **Remove-AzureRmResourceGroup**. Cette commande supprime la machine virtuelle de façon définitive, donc utilisez-la avec précaution.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé une machine virtuelle SQL Server 2017 à l’aide d’Azure PowerShell. Pour en savoir plus sur la façon de migrer vos données vers la nouvelle instance SQL Server, consultez l’article suivant.

> [!div class="nextstepaction"]
> [Migrer une base de données SQL Server vers SQL Server dans une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md)