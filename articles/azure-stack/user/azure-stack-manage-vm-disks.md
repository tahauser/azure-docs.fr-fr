---
title: "Gérer les disques de machine virtuelle dans Azure Stack | Microsoft Docs"
description: "Approvisionnez les disques des machines virtuelles d’Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 0c36e2eaaf2d266842b2b7de0b0c8dc0ed1e0145
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2017
---
# <a name="virtual-machine-disk-storage-for-azure-stack"></a>Stockage de disques de machine virtuelle pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Azure Stack prend en charge l’utilisation des [disques non gérés](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks) d’une machine virtuelle en tant que disque de système d’exploitation (SE) et disque de données. Pour utiliser des disques non gérés, vous allez créer un [compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account), puis stocker les disques en tant qu’objets blob de pages dans des conteneurs au sein du compte de stockage. Ces disques sont alors désignés en tant que disques de machine virtuelle.

Pour améliorer les performances et réduire les coûts de gestion du système Azure Stack, nous vous recommandons de placer chaque disque de machine virtuelle dans un conteneur distinct. Un conteneur doit contenir un disque du système d’exploitation ou un disque de données, mais pas les deux en même temps. Toutefois, il n’existe aucune restriction empêchant de placer les deux disques dans le même conteneur.

Si vous ajoutez un ou plusieurs disques de données à une machine virtuelle, pensez à utiliser les conteneurs supplémentaires comme emplacement de stockage de ces disques. À l’instar du disque de données, le disque du système d’exploitation des machines virtuelles supplémentaires doit être dans son propre conteneur distinct.

Lorsque vous créez plusieurs machines virtuelles, vous pouvez réutiliser un même compte de stockage pour chaque nouvelle machine virtuelle. Seuls les conteneurs que vous créez doivent être uniques.  

Pour ajouter des disques à une machine virtuelle, utilisez le portail de l’utilisateur ou PowerShell.

| Méthode | Options
|-|-|
|[Portail de l’utilisateur](#use-the-portal-to-add-additional-disks-to-a-vm)|- Ajoutez de nouveaux disques de données à une machine virtuelle qui a été approvisionnée précédemment. Les nouveaux disques sont créés par Azure Stack. </br> </br>- Ajoutez un fichier .vhd existant en tant que disque à une machine virtuelle qui a été approvisionnée précédemment. Pour ce faire, vous devez tout d’abord préparer et charger le fichier .vhd vers Azure Stack. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | - Créez une machine virtuelle en utilisant un disque de système d’exploitation, et ajoutez parallèlement un ou plusieurs disques de données à cette machine virtuelle. |


## <a name="use-the-portal-to-add-additional-disks-to-a-vm"></a>Ajouter des disques supplémentaires à une machine virtuelle à l’aide du portail
Par défaut, lorsque vous utilisez le portail pour créer une machine virtuelle pour la plupart des éléments du marketplace, seul un disque de système d’exploitation est créé. Les disques créés par Azure sont appelés des disques gérés.

Une fois que vous avez approvisionné une machine virtuelle, vous pouvez utiliser le portail pour ajouter un disque de données nouveau ou existant à cette machine virtuelle. Chaque disque supplémentaire doit être placé dans un conteneur distinct. Les disques que vous ajoutez à une machine virtuelle sont appelés des disques non gérés.

### <a name="use-the-portal-to-attach-a-new-data-disk-to-a-vm"></a>Utiliser le portail pour attacher un nouveau disque de données à une machine virtuelle

1.  Dans le portail, sélectionnez **Machines virtuelles**.    
    ![Exemple : tableau de bord de machine virtuelle](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Sélectionnez une machine virtuelle qui a déjà été approvisionnée.   
    ![Exemple : sélectionner une machine virtuelle dans le tableau de bord](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  Pour la machine virtuelle, cliquez sur **Disques** > **Attacher un nouveau disque**.       
    ![Exemple : attacher un disque à la machine virtuelle](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  Dans le volet **Attacher un nouveau disque**, cliquez sur **Emplacement**. Par défaut, l’emplacement est défini sur le conteneur qui renferme le disque du système d’exploitation.      
    ![Exemple : définir l’emplacement de disque](media/azure-stack-manage-vm-disks/disk-location.png)

5.  Sélectionnez le **compte de stockage** à utiliser. Sélectionnez ensuite le **Conteneur** dans lequel vous souhaitez placer le disque de données. Dans la page **Conteneurs**, vous pouvez créer un conteneur si vous le souhaitez. Vous pouvez ensuite modifier l’emplacement du nouveau disque et le définir sur son propre conteneur. Si vous utilisez un conteneur distinct pour chaque disque, vous répartissez le positionnement du disque de données, ce qui peut améliorer les performances. Pour enregistrer la sélection, cliquez sur **Sélectionner**.     
    ![Exemple : sélectionner un conteneur](media/azure-stack-manage-vm-disks/select-container.png)

6.  Dans la page **Attacher un nouveau disque**, mettez à jour les paramètres **Nom**, **Type**, **Taille** et **Mise en cache de l’hôte** du disque. Cliquez ensuite sur **OK** pour enregistrer la configuration du nouveau disque de la machine virtuelle.  
    ![Exemple : attachement de disque terminé](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Après qu’Azure Stack a créé le disque et l’a attaché à la machine virtuelle, le nouveau disque est répertorié dans les paramètres de disque de la machine virtuelle sous **DISQUES DE DONNÉES**.   
    ![Exemple : afficher un disque](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>Ajouter un disque de données existant à une machine virtuelle
1.  [Préparez un fichier .vhd](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) à utiliser comme disque de données pour une machine virtuelle. Chargez ce fichier .vhd vers un compte de stockage que vous utilisez avec la machine virtuelle à laquelle vous souhaitez attacher le fichier .vhd.

  Pour stocker le fichier .vhd, pensez à utiliser un conteneur autre que le conteneur du disque du système d’exploitation.   
  ![Exemple : charger un fichier VHD](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  Une fois que le fichier .vhd est chargé, vous êtes prêt à attacher le disque dur virtuel (VHD) à une machine virtuelle. Dans le menu de gauche, cliquez sur **Machines virtuelles**.  
 ![Exemple : sélectionner une machine virtuelle dans le tableau de bord](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Sélectionnez la machine virtuelle dans la liste.    
  ![Exemple : sélectionner une machine virtuelle dans le tableau de bord](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  Dans la page de la machine virtuelle, cliquez sur **Disques** > **Attacher un disque existant**.   
  ![Exemple : attacher un disque existant](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  Dans la page **Attacher un disque existant**, cliquez sur **Fichier VHD**. La page **Comptes de stockage** s’ouvre.    
  ![Exemple : sélectionner un fichier VHD](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  Sous **Comptes de stockage**, sélectionnez le compte à utiliser, puis un conteneur qui renferme le fichier .vhd que vous avez chargé précédemment. Sélectionnez le fichier .vhd, puis cliquez sur **Sélectionner** pour enregistrer la sélection.    
  ![Exemple : sélectionner un conteneur](media/azure-stack-manage-vm-disks/select-container2.png)

7.  Sous **Attacher un disque existant**, le fichier que vous avez sélectionné est répertorié sous **Fichier VHD**. Mettez à jour le paramètre **Mise en cache de l’hôte** du disque, puis cliquez sur **OK** pour enregistrer la configuration du nouveau disque de la machine virtuelle.    
  ![Exemple : attacher le fichier VHD](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Après qu’Azure Stack a créé le disque et l’a attaché à la machine virtuelle, le nouveau disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.   
  ![Exemple : terminer l’attachement de disque](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Utiliser PowerShell pour ajouter plusieurs disques non gérés à une machine virtuelle
Vous pouvez utiliser PowerShell pour approvisionner une machine virtuelle et ajouter un nouveau disque de données, ou attacher un fichier **.vhd** préexistant en tant que disque de données.

La cmdlet **Add-AzureRmVMDataDisk** ajoute un disque de données à une machine virtuelle. Vous pouvez ajouter un disque de données lorsque vous créez une machine virtuelle. Vous pouvez également ajouter un disque de données à une machine virtuelle existante. Spécifiez le paramètre **VhdUri** pour distribuer les disques vers différents conteneurs.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Ajouter des disques de données à une nouvelle machine virtuelle
Les exemples suivants utilisent des commandes PowerShell pour créer une machine virtuelle comportant trois disques de données, chacun étant placé dans un conteneur différent.

La première commande crée un objet machine virtuelle avant de le stocker dans la variable *$VirtualMachine*. La commande attribue un nom et une taille à la machine virtuelle.
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

Les trois commandes suivantes attribuent les chemins d’accès de trois disques de données aux variables *$DataDiskVhdUri01*, *$DataDiskVhdUri02* et *$DataDiskVhdUri03*. Définissez un nom de chemin d’accès différent dans l’URL pour répartir les disques dans différents conteneurs.     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

Les trois commandes finales ajoutent des disques de données à la machine virtuelle stockée dans *$VirtualMachine*. Chaque commande spécifie le nom, l’emplacement et des propriétés supplémentaires du disque. L’URI de chaque disque est stocké dans *$DataDiskVhdUri01*, *$DataDiskVhdUri02* et *$DataDiskVhdUri03*.
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

Les commandes PowerShell suivantes permettent d’ajouter la configuration du réseau et du disque du système d’exploitation à la machine virtuelle, puis de démarrer la nouvelle machine virtuelle.
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Ajouter des disques de données à une machine virtuelle existante
Les exemples suivants utilisent des commandes PowerShell pour ajouter trois disques de données à une machine virtuelle existante.
La première commande récupère la machine virtuelle nommée VirtualMachine à l’aide de la cmdlet **Get-AzureRmVM**. La commande stocke la machine virtuelle dans la variable *$VirtualMachine* .
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
Les trois commandes suivantes attribuent les chemins d’accès de trois disques de données aux variables $DataDiskVhdUri01, $DataDiskVhdUri02 et $DataDiskVhdUri03.  Les différents noms de chemin d’accès dans le paramètre vhduri indiquent différents conteneurs pour le placement des disques.
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  Les trois commandes suivantes ajoutent les disques de données à la machine virtuelle stockée dans la variable *$VirtualMachine*. Chaque commande spécifie le nom, l’emplacement et des propriétés supplémentaires du disque. L’URI de chaque disque est stocké dans *$DataDiskVhdUri01*, *$DataDiskVhdUri02* et *$DataDiskVhdUri03*.
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  La commande finale met à jour l’état de la machine virtuelle stockée dans *$VirtualMachine* dans -*ResourceGroupName*.
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les machines virtuelles Azure Stack, consultez [Considérations relatives aux machines virtuelles dans Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
