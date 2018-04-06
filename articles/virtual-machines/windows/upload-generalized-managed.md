---
title: Créer une machine virtuelle Azure gérée à partir d’un disque dur virtuel local généralisé | Microsoft Docs
description: Chargez un disque dur virtuel généralisé sur Azure et utilisez-le pour créer des machines virtuelles dans le modèle de déploiement du Gestionnaire de ressources.
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
ms.date: 03/26/2018
ms.author: cynthn
ms.openlocfilehash: cf9255126adcec9a2d9d280211e46a6b5f93e14c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Charger un disque dur virtuel généralisé et l’utiliser pour créer des machines virtuelles dans Azure

Cette rubrique vous guide dans l’utilisation de PowerShell pour charger un disque dur virtuel d’une machine virtuelle généralisée vers Azure, créer une image à partir du disque dur virtuel, et créer une machine virtuelle à partir de cette image. Vous pouvez charger un disque dur virtuel exporté à partir d’un outil de virtualisation local ou d’un autre cloud. L’utilisation de la fonctionnalité [Managed Disks](managed-disks-overview.md) pour la nouvelle machine virtuelle simplifie la gestion de celle-ci et fournit une meilleure disponibilité lorsque la machine virtuelle est placée dans un groupe à haute disponibilité. 

Si vous souhaitez utiliser un exemple de script, voir [Exemple de script pour charger un disque dur virtuel spécialisé sur Azure et créer une machine virtuelle](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Avant de commencer

- Avant de charger un disque dur virtuel dans Azure, vous devez apprendre à [préparer un disque dur virtuel Windows ou un disque VHDX à charger sur Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Consultez [Planification de la migration vers Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) avant de démarrer la migration vers [Managed Disks](managed-disks-overview.md).
- Cet article nécessite l’utilisation du module AzureRM version 5.6 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM.Compute` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="generalize-the-source-vm-using-sysprep"></a>Généraliser la machine virtuelle source à l’aide de Sysprep

Sysprep supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

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
6. Une fois l’opération Sysprep terminée, elle arrête la machine virtuelle. Ne redémarrez pas la machine virtuelle.


## <a name="get-the-storage-account"></a>Obtention du compte de stockage

Vous avez besoin d’un compte de stockage dans Azure pour stocker l’image de la machine virtuelle téléchargée. Vous pouvez utiliser un compte de stockage existant ou en créer un. 

Si vous prévoyez d’utiliser le disque dur virtuel pour créer un disque géré pour une machine virtuelle, l’emplacement du compte de stockage doit être le même que celui où vous allez créer la machine virtuelle.

Pour afficher les comptes de stockage disponibles, tapez :

```azurepowershell
Get-AzureRmStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Téléchargement du disque dur virtuel vers votre compte de stockage

Utilisez l’applet de commande [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) pour charger le disque dur virtuel vers un conteneur de votre compte de stockage. Cet exemple télécharge le fichier *myVHD.vhd* à partir de *C:\Users\Public\Documents\Virtual hard disks\"* vers un compte de stockage nommé *mystorageaccount* dans le groupe de ressources *myResourceGroup*. Le fichier est placé dans le conteneur nommé *mycontainer* et le nouveau nom de fichier est *myUploadedVHD.vhd*.

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

### <a name="other-options-for-uploading-a-vhd"></a>Autres options de téléchargement d’un disque dur virtuel
 
Vous pouvez également télécharger un disque dur virtuel sur votre compte de stockage en utilisant l’un des moyens suivants :

- [AZCopy](http://aka.ms/downloadazcopy)
- [API de copie d’un objet blob de stockage Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Téléchargement d’objets blob dans Storage Explorer](https://azurestorageexplorer.codeplex.com/)
- [Référence sur l’API REST du service Import/Export Storage](https://msdn.microsoft.com/library/dn529096.aspx)
-   Nous recommandons l’utilisation de du service d’importation/exportation si l’estimation du temps de téléchargement est de plus de 7 jours. Vous pouvez utiliser [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) pour estimer le temps à partir de la taille des données et de l’unité de transfert. 
    Le service Import/Export permet de copier sur un compte de stockage Standard. Vous devez effectuer une copie d’un compte de stockage Standard vers un compte de stockage Premium à l’aide d’un outil tel qu’AzCopy.

> [!IMPORTANT]
> Si vous utilisez AzCopy pour télécharger votre disque dur virtuel vers Azure, vérifiez que vous avez défini [/BlobType:page](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) avant d’exécuter le script de téléchargement. Si la destination est un objet blob et si cette option n’est pas spécifiée, AzCopy crée par défaut un objet blob de blocs.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Créer une image gérée à partir du disque dur virtuel téléchargé 

Créez une image managée à l’aide de votre disque dur virtuel généralisé de système d’exploitation. Remplacez les valeurs par vos propres informations.


Commencez par définir des paramètres :

```powershell
$location = "East US" 
$imageName = "myImage"
```

Créez l’image à l’aide de votre disque dur virtuel généralisé de système d’exploitation.

```powershell
$imageConfig = New-AzureRmImageConfig `
   -Location $location
$imageConfig = Set-AzureRmImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzureRmImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Création de la machine virtuelle

Maintenant que vous avez une image, vous pouvez créer une ou plusieurs nouvelles machines virtuelles à partir de l’image. Cet exemple crée une machine virtuelle nommée *myVM* à partir de l’image *myImage*, dans le groupe de ressources *myResourceGroup*.


```powershell
New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre nouvelle machine virtuelle, accédez à la machine virtuelle dans le [portail](https://portal.azure.com), cliquez sur **Se connecter**, puis ouvrez le fichier RDP Bureau à distance. Utilisez les informations d’identification de compte de votre machine virtuelle d’origine pour vous connecter à votre nouvelle machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle Azure exécutant Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

