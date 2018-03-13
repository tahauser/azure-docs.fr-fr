---
title: Guide de provisionnement des machines virtuelles SQL Server avec Azure PowerShell | Microsoft Docs
description: "Fournit une procédure et des commandes PowerShell permettant de créer une machine virtuelle Azure à l’aide des images de la galerie de machines virtuelles SQL Server."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: 2f94cf2ab84179161c8d0a4f2ae6f73ded1d65c3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Guide pratique pour provisionner des machines virtuelles SQL Server avec Azure PowerShell

Ce guide décrit vos options pour créer des machines virtuelles Windows SQL Server avec Azure PowerShell. Pour obtenir un exemple Azure PowerShell simplifié avec plusieurs valeurs par défaut, consultez la section [Démarrage rapide d’Azure PowerShell pour des machines virtuelles SQL](quickstart-sql-vm-create-powershell.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Cet article nécessite le module Azure PowerShell version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="configure-your-subscription"></a>Configurer votre abonnement

1. Ouvrez PowerShell et accédez à votre compte Azure en exécutant la commande **Add-AzureRmAccount**.

   ```PowerShell
   Add-AzureRmAccount
   ```

1. Un écran de connexion vous invitant à entrer vos informations d’identification devrait s’afficher. Utilisez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

## <a name="define-image-variables"></a>Définir des variables d’image
Pour simplifier la réutilisation et la création de scripts, commencez par définir plusieurs variables. Modifiez les valeurs des paramètres comme vous le souhaitez, mais sachez que des restrictions s’appliquent à la longueur des noms et aux caractères spéciaux en cas de modification des valeurs fournies.

### <a name="location-and-resource-group"></a>Emplacement et groupe de ressources
Utilisez deux variables pour définir la région des données et le groupe de ressources dans lequel vous allez créer les autres ressources de la machine virtuelle.

Apportez les modifications souhaitées, puis exécutez les applets de commande suivantes pour initialiser ces variables.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Propriétés de stockage
Utilisez les variables suivantes pour définir le compte de stockage et le type de stockage à utiliser par la machine virtuelle.

Apportez les modifications souhaitées, puis exécutez l’applet de commande suivante pour initialiser ces variables. Notez que, dans cet exemple, nous utilisons [Premium Storage](../premium-storage.md), qui est recommandé pour les charges de travail de production.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Propriétés du réseau
Utilisez les variables suivantes pour définir l’interface réseau, la méthode d’allocation TCP/IP, le nom du réseau virtuel, le nom du sous-réseau virtuel, la plage d’adresses IP du réseau virtuel, la plage d’adresses IP du sous-réseau et le libellé du nom de domaine public à utiliser par le réseau dans la machine virtuelle.

Apportez les modifications souhaitées, puis exécutez l’applet de commande suivante pour initialiser ces variables.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Propriétés de machine virtuelle
Utilisez les variables suivantes pour définir le nom de la machine virtuelle, le nom de l’ordinateur, la taille de la machine virtuelle et le nom du disque du système d’exploitation de la machine virtuelle.

Apportez les modifications souhaitées, puis exécutez l’applet de commande suivante pour initialiser ces variables.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Choisir une image SQL Server
Utilisez les variables suivantes pour définir l’image SQL Server à utiliser pour la machine virtuelle.

1. Tout d’abord, répertoriez toutes les offres d’image SQL Server avec la commande **Get-AzureRmVMImageOffer** :

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Dans ce didacticiel, utilisez les variables suivantes pour spécifier SQL Server 2017 sur Windows Server 2016.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Ensuite, affichez toutes les éditions disponibles pour votre offre.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Dans ce didacticiel, utilisez l’édition Developer de SQL Server 2017 (**SQLDEV**). La Developer Edition est librement concédée sous licence pour le développement et le test, et vous ne payez que pour le coût d’exécution de la machine virtuelle.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Avec le modèle de déploiement Resource Manager, le premier objet que vous créez est le groupe de ressources. Utilisez l’applet de commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) pour créer un groupe de ressources Azure et ses ressources avec le nom et l’emplacement du groupe de ressources définis par les variables que vous avez déjà initialisées.

Exécutez l’applet de commande suivante pour créer votre groupe de ressources.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Créer un compte de stockage
La machine virtuelle nécessite des ressources de stockage pour le disque du système d’exploitation ainsi que pour les données et fichiers journaux de SQL Server. Pour plus de simplicité, nous allons créer un seul disque pour les deux. Vous pouvez attacher des disques supplémentaires ultérieurement, à l’aide de l’applet de commande [Add-Azure Disk](/powershell/module/azure/add-azuredisk) , pour placer vos données et vos fichiers journaux SQL Server sur des disques dédiés. Utilisez l’applet de commande [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) pour créer un compte de stockage standard dans votre nouveau groupe de ressources, avec le nom du compte de stockage, le nom de référence du stockage et l’emplacement définis à l’aide des variables que vous avez déjà initialisées.

Exécutez l’applet de commande suivante pour créer votre compte de stockage.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> La création du compte de stockage peut prendre quelques minutes.

## <a name="create-network-resources"></a>Créer des ressources réseau
La machine virtuelle requiert plusieurs ressources réseau pour la connectivité réseau.

* Chaque machine virtuelle requiert un réseau virtuel.
* Un réseau virtuel doit avoir au moins un sous-réseau.
* Une interface réseau doit être définie avec une adresse IP privée ou publique.

### <a name="create-a-virtual-network-subnet-configuration"></a>Créer une configuration de sous-réseau de réseau virtuel
Commencez par créer une configuration de sous-réseau pour notre réseau virtuel. Dans notre didacticiel, nous allons créer un sous-réseau par défaut à l’aide de l’applet de commande [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Nous allons créer notre configuration de sous-réseau de réseau virtuel avec le nom et le préfixe d’adresse définis à l’aide des variables déjà initialisées.

> [!NOTE]
> Vous pouvez définir des propriétés supplémentaires dans la configuration de sous-réseau de réseau virtuel à l’aide de cette applet de commande, mais cela sort du cadre de ce didacticiel.

Exécutez l’applet de commande suivante pour créer la configuration de votre sous-réseau virtuel.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel
Ensuite, créez votre réseau virtuel à l’aide de l’applet de commande [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Créez le réseau virtuel dans votre nouveau groupe de ressources, avec le nom, l’emplacement et le préfixe d’adresse définis avec les variables que vous avez déjà initialisées, et à l’aide de la configuration de sous-réseau définie à l’étape précédente.

Exécutez l’applet de commande suivante pour créer votre réseau virtuel.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Créer une adresse IP publique
Maintenant que nous avons défini notre réseau virtuel, nous devons configurer une adresse IP pour la connectivité à la machine virtuelle. Dans ce didacticiel, nous allons créer une adresse IP publique à l’aide de l’adressage IP dynamique pour la connectivité Internet. Utilisez l’applet de commande [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) pour créer l’adresse IP publique dans le groupe de ressources déjà créé, avec le nom, l’emplacement, la méthode d’allocation et le libellé du nom de domaine DNS définis à l’aide des variables que vous avez déjà initialisées.

> [!NOTE]
> Vous pouvez définir des propriétés supplémentaires de l’adresse IP publique à l’aide de cette applet de commande, mais cela sort du cadre de ce didacticiel. Vous pouvez également créer une adresse privée ou une adresse avec une adresse statique, mais cela sort du cadre de ce didacticiel.

Exécutez l’applet de commande suivante pour créer votre adresse IP publique.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Créer le groupe de sécurité réseau
Pour sécuriser le trafic de la machine virtuelle et de SQL Server, créez un groupe de sécurité réseau.

1. Tout d’abord, créez une règle de groupe de sécurité réseau pour RDP pour autoriser les connexions du Bureau à distance.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Configurez une règle de groupe de sécurité réseau qui autorise le trafic sur le port TCP 1433. Cela permet de se connecter à SQL Server sur Internet.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Ensuite, créez le groupe de sécurité réseau.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Créer l’interface réseau
Nous sommes maintenant prêts à créer l’interface réseau que notre machine virtuelle utilisera. Nous allons appeler l’applet de commande [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) pour créer notre interface réseau dans le groupe de ressources déjà créé, et avec le nom, l’emplacement, le sous-réseau et l’adresse IP publique définis auparavant.

Exécutez l’applet de commande suivante pour créer votre interface réseau.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Configurer un objet de machine virtuelle
Maintenant que nous avons défini les ressources réseau et de stockage, nous pouvons définir les ressources de calcul de la machine virtuelle. Dans notre didacticiel, nous allons configurer la taille de la machine virtuelle et plusieurs propriétés du système d’exploitation, spécifier l’interface réseau créée auparavant, définir le Blob Storage et identifier le disque du système d’exploitation.

### <a name="create-the-vm-object"></a>Créer l’objet de machine virtuelle
Commencez par spécifier la taille de la machine virtuelle. Dans ce didacticiel, nous spécifions une DS13. Nous allons appeler l’applet de commande [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) pour créer une machine virtuelle configurable, avec le nom et la taille définis à l’aide des variables que vous avez déjà initialisées.

Exécutez l’applet de commande suivante pour créer l’objet de machine virtuelle.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Créer un objet d’informations d’identification pour stocker le nom et le mot de passe de l’administrateur local
Avant de définir les propriétés du système d’exploitation de la machine virtuelle, nous devons indiquer les informations d’identification du compte d’administrateur local sous la forme d’une chaîne de caractères sécurisée. Pour ce faire, utilisez l’applet de commande [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx).

Exécutez l’applet de commande suivante puis, dans la fenêtre de demande d’informations d’identification PowerShell, tapez le nom et le mot de passe à utiliser pour le compte d’administrateur local sur la machine virtuelle.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Configurer les propriétés du système d’exploitation de la machine virtuelle
Maintenant, nous allons définir les propriétés du système d’exploitation du machine virtuelle avec l’applet de commande [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) pour configurer le système d’exploitation Windows, exiger l’installation de l’[agent de machine virtuelle](../agent-user-guide.md), spécifier que l’applet de commande autorise la mise à jour automatique et définir le nom de la machine virtuelle, le nom de l’ordinateur et les informations d’identification à l’aide des variables que vous avez déjà initialisées.

Exécutez l’applet de commande suivante pour définir les propriétés du système d’exploitation de votre machine virtuelle.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Ajouter l’interface réseau à la machine virtuelle
Ensuite, nous allons ajouter l’interface réseau déjà créée à la machine virtuelle. Appelez l’applet de commande [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) pour ajouter l’interface réseau à l’aide de la variable d’interface réseau définie auparavant.

Exécutez l’applet de commande suivante pour définir l’interface réseau de votre machine virtuelle.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Définir l’emplacement de Blob Storage du disque à utiliser par la machine virtuelle
Ensuite, définissez l’emplacement de Blob Storage du disque à utiliser par la machine virtuelle à l’aide des variables définies auparavant.

Exécutez l’applet de commande suivante pour définir l’emplacement de Blob Storage.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Configurer les propriétés du disque du système d’exploitation de la machine virtuelle
Ensuite, configurez les propriétés du disque du système d’exploitation de la machine virtuelle. Utilisez l’applet de commande [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) pour spécifier que le système d’exploitation de la machine virtuelle proviendra d’une image, définir la mise en cache en lecture seule (car SQL Server est installé sur le même disque) et spécifier le nom de la machine virtuelle ainsi que le disque du système d’exploitation définis à l’aide des variables configurées auparavant.

Exécutez l’applet de commande suivante pour configurer les propriétés du disque du système d’exploitation de votre machine virtuelle.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Spécifier l’image de plateforme de la machine virtuelle
Notre dernière étape de configuration consiste à spécifier l’image de plateforme de notre machine virtuelle. Dans notre didacticiel, nous utilisons la dernière image en date de SQL Server 2016 CTP. Utilisez l’applet de commande [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) pour utiliser cette image définie par les variables configurées auparavant.

Exécutez l’applet de commande suivante pour définir l’image de plateforme de votre machine virtuelle.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Créer la machine virtuelle SQL
Une fois les étapes de configuration terminées, vous pouvez créer la machine virtuelle. Utilisez l’applet de commande [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) pour créer la machine virtuelle à l’aide des variables que nous avons définies.

Exécutez l’applet de commande suivante pour créer votre machine virtuelle.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

La machine virtuelle est créée.

> [!NOTE]
> Vous pouvez ignorer l’erreur concernant les diagnostics de démarrage. Un compte de stockage standard est créé pour les diagnostics de démarrage, car le compte de stockage spécifié pour le disque de la machine virtuelle est un compte Premium Storage.

## <a name="install-the-sql-iaas-agent"></a>Installer l’agent IaaS SQL
Les machines virtuelles SQL Server prennent en charge les fonctionnalités de gestion automatisées avec l’[Extension de l’Agent IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Pour installer l’agent sur la nouvelle machine virtuelle, exécutez la commande suivante après sa création.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>Supprimer une machine virtuelle de test

Si vous n’avez pas besoin que la machine virtuelle fonctionne en permanence, vous pouvez éviter les dépenses inutiles en l’arrêtant lorsque vous ne vous en servez pas. La commande suivante arrête la machine virtuelle, tout en la laissant disponible pour une utilisation future.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Vous pouvez aussi supprimer définitivement toutes les ressources associées à la machine virtuelle à l’aide de la commande **Remove-AzureRmResourceGroup**. Cette commande supprime la machine virtuelle de façon définitive, donc utilisez-la avec précaution.

## <a name="example-script"></a>Exemple de script
Le script suivant contient le script PowerShell complet pour ce didacticiel. Nous considérons que vous avez déjà configuré l’abonnement Azure à utiliser avec les commandes **Add-AzureRmAccount** et **Select-AzureRmSubscription**.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>Étapes suivantes
Une fois la machine virtuelle créée, vous pouvez :

- Vous connecter à la machine virtuel à l’aide du Bureau à distance (RDP).
- Configurer les paramètres de SQL Server dans le portail de votre machine virtuelle, notamment :
   - les [paramètres de stockage](virtual-machines-windows-sql-server-storage-configuration.md) ; 
   - les [tâche de gestion automatisées](virtual-machines-windows-sql-server-agent-extension.md).
- [Configurer la connectivité](virtual-machines-windows-sql-connect.md).
- Connecter les clients et les applications à la nouvelle instance SQL Server.

