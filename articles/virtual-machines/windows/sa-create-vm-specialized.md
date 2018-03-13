---
title: "Créer une machine virtuelle à partir d’un disque spécialisé dans Azure | Microsoft Docs"
description: "Créer une machine virtuelle en attachant un disque non géré spécialisé, dans le modèle de déploiement Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 811cc6cea80acbe6cbbf4533c1f9a8c9c7f53702
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Création d’une machine virtuelle à partir d’un disque dur virtuel spécialisé dans un compte de stockage

Créez une machine virtuelle en attachant un disque non géré spécialisé en tant que disque du système d’exploitation à l’aide de Powershell. Un disque spécialisé est une copie du disque dur virtuel d’une machine virtuelle existante qui gère les comptes d’utilisateurs, les applications et les autres données d’état à partir de votre machine virtuelle d’origine. 

Deux options s'offrent à vous :
* [Charger un disque dur virtuel](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Copier le disque dur virtuel d’une machine virtuelle Azure existante](sa-create-vm-specialized.md#option-2-copy-an-existing-azure-vm)

## <a name="before-you-begin"></a>Avant de commencer
Si vous utilisez PowerShell, assurez-vous que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Exécutez la commande suivante pour l’installer.

```powershell
Install-Module AzureRM.Compute 
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](/powershell/azure/overview).


## <a name="option-1-upload-a-specialized-vhd"></a>Option 1 : Charger un disque dur virtuel spécialisé

Vous pouvez charger le disque dur virtuel à partir d’une machine virtuelle spécialisée créée avec un outil de virtualisation local tel que Hyper-V, ou d’une machine virtuelle exportée à partir d’un autre cloud.

### <a name="prepare-the-vm"></a>Préparation de la machine virtuelle
Vous pouvez charger un disque dur virtuel spécialisé créé à l’aide d’une machine virtuelle sur site ou un disque dur virtuel exporté à partir d’un autre cloud. Un disque dur virtuel spécialisé gère les comptes d’utilisateurs, les applications et les autres données d’état à partir de votre machine virtuelle d’origine. Si vous prévoyez d’utiliser le disque dur virtuel tel quel pour créer une machine virtuelle, veillez à effectuer les étapes suivantes. 
  
  * [Préparez un disque dur virtuel Windows à charger sur Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Ne généralisez pas** la machine virtuelle à l’aide de Sysprep.
  * Supprimez tous les outils de virtualisation invité et les agents installés sur la machine virtuelle (c’est-à-dire les outils VMware).
  * Vérifiez que la machine virtuelle est configurée pour extraire son adresse IP et ses paramètres DNS via DHCP. Cela permet de s’assurer que le serveur obtient une adresse IP dans le réseau virtuel lors de son démarrage. 


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
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Téléchargement du disque dur virtuel vers votre compte de stockage
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

Selon votre connexion réseau et la taille de votre fichier de disque dur virtuel, l’exécution de cette commande peut prendre un certain temps.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Option 2 : Copier le disque dur virtuel d’une machine virtuelle Azure existante

Vous pouvez copier un disque dur virtuel vers un autre compte de stockage pour l’utiliser lors de la création d’une nouvelle machine virtuelle dupliquée.

### <a name="before-you-begin"></a>Avant de commencer
Veillez à :

* Avoir à votre disposition des informations sur les **comptes de stockage source et de destination**. Pour la machine virtuelle source, vous devez disposer du nom du compte de stockage et du conteneur. En général, le nom du conteneur est **vhds**. Vous devez également disposer d’un compte de stockage de destination. Si ce n’est pas le cas, vous pouvez en créer un avec le portail (**Tous les services** > Comptes de stockage > Ajouter) ou l’applet de commande [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). 
* Télécharger et installer [l’outil AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Libérer la machine virtuelle
Libérez la machine virtuelle, ce qui libère le disque dur virtuel à copier. 

* **Portail** : cliquez sur **Machines virtuelles** > **myVM** &gt; Arrêter
* **PowerShell** : utilisez [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) pour arrêter (libérer) la machine virtuelle nommée **myVM** dans le groupe de ressources **myResourceGroup**.

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

**L’état** de la machine virtuelle dans le Portail Azure passe de **Arrêté** à **Arrêté (libéré)**.

### <a name="get-the-storage-account-urls"></a>Récupérer les URL des comptes de stockage
Vous avez besoin des URL des comptes de stockage source et de destination. Les URL ressemblent à : `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Si vous connaissez déjà le nom du compte de stockage et du conteneur, vous pouvez simplement remplacer les informations entre crochets pour créer votre URL. 

Vous pouvez utiliser le Portail Azure ou Azure PowerShell pour récupérer l’URL :

* **Portail** : cliquez sur **>** pour **Tous les services** > **Comptes de stockage** > *compte de stockage* > **Blobs** ; votre fichier de disque dur virtuel source se trouve probablement dans le conteneur **vhds**. Cliquez sur les **Propriétés** du conteneur et copiez le texte intitulé **URL**. Vous aurez besoin des URL des conteneurs source et de destination. 
* **PowerShell** : utilisez [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) pour récupérer les informations de la machine virtuelle nommée **myVM** dans le groupe de ressources **myResourceGroup**. Dans les résultats, recherchez **l’URI du disque dur virtuel** dans la section **Profil de stockage**. La première partie de l’URI est l’URL du conteneur et la dernière est le nom du disque dur virtuel du système d’exploitation de la machine virtuelle.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Récupérer les clés d’accès de stockage
Récupérez les clés d’accès des comptes de stockage source et de destination. Pour plus d’informations sur les clés d’accès, consultez la page [À propos des comptes de stockage Azure](../../storage/common/storage-create-storage-account.md).

* **Portail** : cliquez sur **Tous les services** > **Comptes de stockage** > *compte de stockage* > **Clés d’accès**. Copiez la clé portant le nom **key1**.
* **PowerShell** : utilisez [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) pour récupérer la clé de stockage du compte de stockage **mystorageaccount** dans le groupe de ressources **myResourceGroup**. Copiez la clé portant le nom **key1**.

```powershell
Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Copier le disque dur virtuel
Vous pouvez copier des fichiers entre comptes de stockage avec AzCopy. Si le conteneur de destination spécifié n’existe pas, il est créé pour vous. 

Pour utiliser AzCopy, ouvrez une invite de commandes sur votre machine locale et accédez au dossier dans lequel AzCopy est installé. Il peut être du type *C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy*. 

Pour copier tous les fichiers d’un conteneur, utilisez le commutateur **/S**. Cette procédure peut servir à copier le disque dur virtuel du système d’exploitation et tous les disques de données, s’ils se trouvent dans le même conteneur. Cet exemple montre comment copier tous les fichiers du conteneur **mysourcecontainer** dans le compte de stockage **mysourcestorageaccount** vers le conteneur **mydestinationcontainer** dans le compte de stockage **mydestinationstorageaccount**. Remplacez le nom des comptes de stockage et des conteneurs par les vôtres. Remplacez `<sourceStorageAccountKey1>` et `<destinationStorageAccountKey1>` par vos propres clés.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Si vous souhaitez copier un disque dur virtuel en particulier dans un conteneur par plusieurs fichiers, vous pouvez également spécifier le nom du fichier avec le commutateur /Pattern. Dans cet exemple, seul le fichier nommé **myFileName.vhd** est copié.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Une fois la copie terminée, vous recevez un message du type :

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Résolution de problèmes
* Lorsque vous utilisez AZCopy, si l’erreur « Le serveur n’a pas pu authentifier cette demande » s’affiche, vérifiez la valeur de l’en-tête d’autorisation : il doit être correctement formé et comporter notamment la signature. Si vous utilisez la clé 2 ou la clé de stockage secondaire, essayez d’utiliser la clé primaire ou la première clé de stockage.

## <a name="create-the-new-vm"></a>Créer la machine virtuelle 

Vous devez créer la mise en réseau et les autres ressources de machine virtuelle que la nouvelle machine virtuelle doit utiliser.

### <a name="create-the-subnet-and-vnet"></a>Créer le sous-réseau et le réseau virtuel

Créez le réseau virtuel et le sous-réseau du [réseau virtuel](../../virtual-network/virtual-networks-overview.md).

1. Créez le sous-réseau. L’exemple suivant crée un sous-réseau nommé **mySubnet** dans le groupe de ressources **myResourceGroup** et définit le préfixe d’adresse de sous-réseau sur **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Créez le réseau virtuel. Cet exemple définit le nom de réseau virtuel sur **myVnetName**, l’emplacement sur **West US** et le préfixe d’adresse du réseau virtuel sur **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Créer le groupe de sécurité réseau et une règle RDP
Pour pouvoir vous connecter à votre machine virtuelle avec le protocole RDP, vous devez disposer d’une règle de sécurité qui autorise l’accès RDP sur le port 3389. Du fait que le disque dur virtuel de la nouvelle machine virtuelle a été créé à partir d’une machine virtuelle spécialisée, une fois que la machine virtuelle est créée, vous pouvez utiliser un compte existant à partir de la machine virtuelle source qui a l’autorisation de se connecter à l’aide de RDP.
Vous devez le faire avant la création de l’interface réseau à laquelle elle sera associée.  
Cet exemple définit le nom du groupe de sécurité réseau sur **myNsg** et le nom de règle RDP sur **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Pour plus d’informations sur les points de terminaison et les règles NSG, consultez [Ouverture de ports sur une machine virtuelle dans Azure avec PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Créer une adresse IP publique et une carte réseau
Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

1. Créez l’adresse IP publique. Dans cet exemple, le nom d’adresse IP publique est défini sur **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Créez la carte réseau. Dans cet exemple, le nom de carte réseau est défini sur **myNicName**. Cette étape associe également le groupe de sécurité réseau créé précédemment à cette carte réseau.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Définir le nom et la taille de machine virtuelle

Cet exemple définit « myVM » en tant que nom de la machine virtuelle et « Standard_A2 » pour sa taille.
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Ajouter la carte réseau
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Configurer le disque du système d’exploitation

1. Définissez l’URI du disque dur virtuel que vous avez chargé ou copié. Dans cet exemple, le fichier de disque dur virtuel nommé **myOsDisk.vhd** est conservé dans un compte de stockage nommé **myStorageAccount** dans un conteneur portant le nom **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Ajoutez le disque du système d’exploitation. Dans cet exemple, lorsque le disque du système d’exploitation est créé, le terme « osDisk » est ajouté au nom de la machine virtuelle pour créer le nom du disque du système d’exploitation. Cet exemple spécifie également que ce disque dur virtuel basé sur Windows doit être attaché à la machine virtuelle en tant que disque de système d’exploitation.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Facultatif : si vous avez des disques de données qui doivent être attachés à la machine virtuelle, ajoutez les disques de données à l’aide des URL des disques durs virtuels de données et du numéro d’unité logique (LUN) approprié.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Si vous utilisez un compte de stockage, les URL des disques de données et des disques du système d’exploitation se présentent comme suit : `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Pour obtenir les URL, rendez-vous sur le portail, accédez au conteneur de stockage de destination, cliquez sur le disque dur virtuel du système d’exploitation ou de données qui a été copié, puis copiez le contenu de l’URL.


### <a name="complete-the-vm"></a>Exécuter la machine virtuelle 

Créez la machine virtuelle en utilisant les configurations que nous venons de créer.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Si la commande a été exécutée avec succès, vous obtiendrez une sortie similaire à celle-ci :

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Vérifier que la machine virtuelle a été créée
Vous devez voir la machine virtuelle nouvellement créée soit dans le [portail Azure](https://portal.azure.com) sous **Tous les services** > **Machines virtuelles**, soit en utilisant les commandes PowerShell suivantes :

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Étapes suivantes
Pour vous connecter à votre nouvelle machine virtuelle, accédez à la machine virtuelle dans le [portail](https://portal.azure.com), cliquez sur **Se connecter**, puis ouvrez le fichier RDP Bureau à distance. Utilisez les informations d’identification de compte de votre machine virtuelle d’origine pour vous connecter à votre nouvelle machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle Azure exécutant Windows](connect-logon.md).

