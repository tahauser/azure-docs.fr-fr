---
title: "Charger un disque dur virtuel généralisé pour créer plusieurs machines virtuelles dans Azure | Microsoft Docs"
description: "Chargez un disque dur virtuel généralisé sur un compte de stockage Azure pour créer une machine virtuelle Windows à utiliser avec le modèle de déploiement Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: d8c700d7b23f0e86b4a190e3c1f1143d962a8d63
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Charger un disque dur virtuel généralisé sur Azure pour créer une machine virtuelle

Cette rubrique décrit le chargement d’un disque non géré généralisé sur un compte de stockage, puis la création d’une machine virtuelle à l’aide du disque chargé. Toutes les informations de votre compte personnel sont supprimées d’une image de disque dur virtuel généralisé à l’aide de Sysprep. 

Si vous souhaitez créer une machine virtuelle à partir d’un disque dur virtuel spécialisé qui se trouve dans un compte de stockage, consultez [Créer une machine virtuelle à partir d’un disque dur virtuel spécialisé](sa-create-vm-specialized.md).

Cette rubrique décrit l’utilisation de comptes de stockage, mais nous recommandons aux clients de plutôt passer à l’utilisation de Managed Disks. Pour obtenir une présentation complète de la préparation, du chargement et de la création d’une machine virtuelle à l’aide de disques managés, consultez [Créer une machine virtuelle à partir d’un disque dur virtuel généralisé chargé sur Azure à l’aide de Managed Disks](upload-generalized-managed.md).



## <a name="prepare-the-vm"></a>Préparation de la machine virtuelle

Toutes les informations de votre compte personnel sont supprimées d’un disque dur virtuel généralisé à l’aide de Sysprep. Si vous prévoyez d’utiliser le disque dur virtuel en tant qu’image pour créer des machines virtuelles, vous devez :
  
  * [Préparer un disque dur virtuel Windows à charger sur Azure](prepare-for-upload-vhd-image.md). 
  * Généraliser la machine virtuelle à l’aide de Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Généraliser une machine virtuelle Windows avec Sysprep
Cette section vous montre comment généraliser votre machine virtuelle Windows de façon à l’utiliser comme image. Sysprep supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

Vérifiez que les rôles serveur exécutés sur la machine sont pris en charge par Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Si vous exécutez Sysprep avant de charger votre disque dur virtuel vers Azure pour la première fois, vérifiez que vous avez [préparé votre machine virtuelle](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) avant d’exécuter Sysprep. 
> 
> 

1. Connectez-vous à la machine virtuelle Windows
2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par **%windir%\system32\sysprep**, puis exécutez `sysprep.exe`.
3. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case **Généraliser** est cochée.
4. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
5. Cliquez sur **OK**.
   
    ![Démarrer Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Une fois l’opération Sysprep terminée, elle arrête la machine virtuelle. 

> [!IMPORTANT]
> Ne redémarrez pas la machine virtuelle tant que vous n’avez pas terminé de télécharger le disque dur virtuel dans Azure ou de créer une image à partir de la machine virtuelle. Si la machine virtuelle est accidentellement redémarrée, exécutez Sysprep pour la généraliser à nouveau.
> 
> 


## <a name="upload-the-vhd"></a>Charger le disque dur virtuel

Chargez le disque dur virtuel sur un compte de stockage Azure.

### <a name="log-in-to-azure"></a>Connexion à Azure
Si vous n’avez pas déjà installé PowerShell version 1.4 ou ultérieure, lisez [Installer et configurer Azure PowerShell](/powershell/azure/overview).

1. Ouvrez Azure PowerShell et connectez-vous à votre compte Azure. Une fenêtre contextuelle s’ouvre pour vous permettre d’entrer les informations d’identification de votre compte Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Obtenez les ID d’abonnement de vos abonnements disponibles.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Définissez l’abonnement approprié à l’aide de l’ID d’abonnement. Remplacez `<subscriptionID>` par l’ID de l’abonnement approprié.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Obtention du compte de stockage
Vous avez besoin d’un compte de stockage dans Azure pour stocker l’image de la machine virtuelle téléchargée. Vous pouvez utiliser un compte de stockage existant ou en créer un. 

Pour afficher les comptes de stockage disponibles, tapez :

```powershell
Get-AzureRmStorageAccount
```

Si vous voulez utiliser un compte de stockage existant, passez à la section [Charger l’image de la machine virtuelle](#upload-the-vm-vhd-to-your-storage-account).

Si vous devez créer un compte de stockage, procédez comme suit :

1. Vous avez besoin du nom du groupe de ressources dans lequel doit être créé le compte de stockage. Pour rechercher tous les groupes de ressources dans votre abonnement, tapez :
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Pour créer un groupe de ressources nommé **MyResourceGroup** dans la région **Ouest des États-Unis**, tapez :

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Créez un compte de stockage nommé **mystorageaccount** dans ce groupe de ressources en utilisant l’applet de commande [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) :
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Démarrer le chargement 

Utilisez l’applet de commande [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) pour charger l’image vers un conteneur de votre compte de stockage. Cet exemple télécharge le fichier **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` vers un compte de stockage nommé **mystorageaccount** dans le groupe de ressources **myResourceGroup**. Le fichier est placé dans le conteneur nommé **mycontainer** et le nouveau nom de fichier est **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Si l’opération réussit, vous obtenez une réponse semblable à celle-ci :

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

En fonction de votre connexion réseau et de la taille de votre fichier VHD, l’exécution de cette commande peut prendre un certain temps.


## <a name="create-a-new-vm"></a>Créer une machine virtuelle 

Vous pouvez maintenant utiliser le fichier VHD chargé pour créer une machine virtuelle. 

### <a name="set-the-uri-of-the-vhd"></a>Définir l’URI du disque dur virtuel

L’URI du disque dur virtuel à utiliser est au format : https://**moncomptedestockage**.blob.core.windows.net/**monconteneur**/**mondisquedurvirtuel**.vhd. Dans cet exemple, le disque dur virtuel nommé **mondisquedurvirtuel** se trouve dans le compte de stockage **moncomptedestockage** dans le conteneur **monconteneur**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Créez un réseau virtuel
Créez le réseau virtuel et le sous-réseau du [réseau virtuel](../../virtual-network/virtual-networks-overview.md).

1. Créez le sous-réseau. L’exemple suivant crée un sous-réseau nommé **mySubnet** dans le groupe de ressources **myResourceGroup** avec le préfixe d’adresse **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Création du réseau virtuel. L’exemple suivant crée un réseau virtuel nommé **myVnet** à l’emplacement **Ouest des États-Unis** avec le préfixe d’adresse **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Création d'une adresse IP publique et une interface réseau
Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

1. Créez une adresse IP publique. Cet exemple crée une adresse IP publique nommée **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Créez la carte réseau. Cet exemple crée une carte réseau nommée **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Créer le groupe de sécurité réseau et une règle RDP
Pour pouvoir vous connecter à votre machine virtuelle avec le protocole RDP, vous devez disposer d’une règle de sécurité qui autorise l’accès RDP sur le port 3389. 

Cet exemple crée un groupe de sécurité réseau nommé **myNsg** qui contient une règle nommée **myRdpRule** autorisant le trafic RDP sur le port 3389. Pour plus d’informations sur les groupes de sécurité réseau, consultez [Ouverture de ports sur une machine virtuelle dans Azure avec PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Créer une variable pour le réseau virtuel
Créez une variable pour le réseau virtuel terminé. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Création de la machine virtuelle
Le script PowerShell suivant montre comment définir les configurations de machine virtuelle et utiliser l’image de la machine virtuelle téléchargée comme source de la nouvelle installation.



```powershell
# Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Vérifier que la machine virtuelle a été créée
Lorsque vous avez terminé, vous devez voir la machine virtuelle nouvellement créée dans le [Portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles** ou en utilisant les commandes PowerShell suivantes :

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Étapes suivantes
Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, consultez [Gestion des machines virtuelles Azure à l’aide de modèles Resource Manager et de PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


