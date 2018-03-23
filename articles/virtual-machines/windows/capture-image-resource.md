---
title: Créer une image managée dans Azure | Microsoft Docs
description: Créer une image managée d’une machine virtuelle ou d’un disque dur virtuel généralisé(e) dans Azure. Les images peuvent être utilisées pour créer différentes machines virtuelles utilisant des disques gérés.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: cynthn
ms.openlocfilehash: f18defbec79638cf2c3363e6429ddcac01b1502e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Créer une image managée d’une machine virtuelle généralisée dans Azure

Une ressource d’image managée peut être créée à partir d’une machine virtuelle généralisée stockée comme un disque géré ou non géré dans un compte de stockage. L’image peut ensuite être utilisée pour créer plusieurs machines virtuelles. 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Généraliser la machine virtuelle Windows à l’aide de Sysprep

Sysprep supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

Vérifiez que les rôles serveur exécutés sur la machine sont pris en charge par Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Une fois que vous avez exécuté sysprep sur une machine virtuelle, elle est considérée comme *généralisée* et ne peut pas être redémarrée. Le processus de généralisation d’une machine virtuelle n’est pas réversible. Si vous devez conserver le fonctionnement de machine virtuelle d’origine, vous devez prendre une [copie de la machine virtuelle](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) et généraliser la copie. 
>
> Si vous exécutez Sysprep avant de charger votre disque dur virtuel vers Azure pour la première fois, vérifiez que vous avez [préparé votre machine virtuelle](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) avant d’exécuter Sysprep.  
> 
> 

1. Connectez-vous à la machine virtuelle Windows
2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par **%windir%\system32\sysprep**, puis exécutez `sysprep.exe`.
3. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case **Généraliser** est cochée.
4. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
5. Cliquez sur **OK**.
   
    ![Démarrer Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Une fois l’opération Sysprep terminée, elle arrête la machine virtuelle. Ne redémarrez pas la machine virtuelle.


## <a name="create-a-managed-image-in-the-portal"></a>Créer une image managée dans le portail 

1. Ouvrez le [portail](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur Machines virtuelles, puis sélectionnez la machine virtuelle dans la liste.
3. Dans la page de la machine virtuelle, dans le menu supérieur, cliquez sur **Capturer**.
3. Dans **Nom**, tapez le nom à utiliser pour l’image.
4. Dans **Groupe de ressources**, sélectionnez **Créer** et tapez un nom, ou sélectionnez **Utiliser un groupe existant** et choisissez un groupe de ressources à utiliser dans la liste déroulante.
5. Pour supprimer la machine virtuelle source une fois que l’image a été créée, sélectionnez **Supprimer automatiquement cette machine virtuelle après avoir créé l’image**.
6. Lorsque vous avez terminé, cliquez sur **Créer**.
16. Une fois que l’image est créée, elle s’affiche en tant que ressource **Image** dans la liste des ressources du groupe de ressources.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Créer une image de machine virtuelle à l’aide de Powershell

La création d’une image directement à partir de la machine virtuelle permet de s’assurer qu’elle comprend tous les disques associés à la machine virtuelle, y compris le disque du système d’exploitation et tous les disques de données. Cet exemple montre comment créer une image gérée à partir d’une machine virtuelle qui utilise des disques gérés.


Avant de commencer, assurez-vous que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Exécutez la commande suivante pour l’installer. (Utilisez `Get-Module` pour vérifier la version dont vous disposez.)

```azurepowershell-interactive
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](/powershell/azure/overview).


1. Définissez des variables.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Assurez-vous que la machine virtuelle a été libérée.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Définissez l’état de la machine virtuelle sur **Généralisé**. 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Accédez à la machine virtuelle. 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Créez la configuration de l’image.

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Créez l’image.

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 
## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Créer une image à partir d’un disque géré à l’aide de PowerShell

Pour simplement créer une image du disque du système d’exploitation, créez une image en spécifiant l’ID du disque managé en tant que disque du système d’exploitation.

    
1. Définissez des variables. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Obtenez la machine virtuelle.

   ```azurepowershell-interactive
   $vm = Get-AzureRmVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Obtenez l’ID du disque géré.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Créez la configuration de l’image.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Créez l’image.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Créer une image à partir d’une capture instantanée à l’aide de Powershell

Vous pouvez créer une image gérée à partir d’une capture instantanée d’une machine virtuelle généralisée.

    
1. Définissez des variables. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Accédez à la capture instantanée.

   ```azurepowershell-interactive
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Créez la configuration de l’image.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Créez l’image.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-image-from-a-vhd-in-a-storage-account"></a>Créer une image à partir d’un disque dur virtuel dans un compte de stockage

Créez une image gérée à partir d’un disque dur virtuel de système d’exploitation généralisé dans un compte de stockage. Vous devez utiliser l’URI du disque dur virtuel dans le compte de stockage, dont le format est https://*mystorageaccount*.blob.core.windows.net/*container*/*vhd_filename.vhd*. Dans cet exemple, le disque dur virtuel que nous utilisons se trouve dans *mystorageaccount* dans un conteneur nommé *vhdcontainer* et le nom de fichier du disque dur virtuel est *osdisk.vhd*.


1.  Tout d’abord, définissez les paramètres communs :

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Arrêtez/libérez la machine virtuelle.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Indiquez que la machine virtuelle est généralisée.

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  Créez l’image à l’aide de votre disque dur virtuel généralisé de système d’exploitation.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Étapes suivantes
- Vous pouvez à présent [créer une machine virtuelle à partir de l’image managée généralisée](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

