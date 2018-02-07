---
title: "Rechercher et supprimer les disques managés et non managés Azure non attachés | Microsoft Docs"
description: "Comment rechercher et supprimer les disques (disques durs virtuels/objets blob de pages) managés et non managés Azure non attachés à l’aide d’Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: a846d3578d40b19762f185381c92bdf8e225b185
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Rechercher et supprimer les disques managés et non managés Azure non attachés
Quand vous supprimez une machine virtuelle dans Azure, les disques attachés ne sont pas supprimés par défaut. Cela empêche les pertes de données en cas de suppression accidentelle de machines, mais vous continuez à payer inutilement pour les disques non attachés. Utilisez cet article pour rechercher et supprimer tous les disques non attachés et faire des économies. 


## <a name="find-and-delete-unattached-managed-disks"></a>Rechercher et supprimer les disques managés non attachés 

Le script suivant montre comment rechercher les [disques managés](managed-disks-overview.md) non attachés à l’aide de la propriété *ManagedBy*. Il effectue une itération sur tous les disques managés dans un abonnement et vérifie si la propriété *ManagedBy* a la valeur null pour rechercher des disques managés non attachés. La propriété *ManagedBy* stocke l’ID de ressource de la machine virtuelle à laquelle un disque managé est attaché.

Nous vous recommandons vivement de commencer par exécuter le script en affectant 0 à la variable *deleteUnattachedDisks* pour afficher tous les disques non attachés. Après avoir examiné les disques non attachés, exécutez le script en affectant 1 à *deleteUnattachedDisks* pour supprimer tous les disques non attachés.

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Rechercher et supprimer les disques non managés non attachés 

Les disques non managés sont des fichiers VHD stockés comme [objets blob de pages] (/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) dans les [comptes de stockage Azure](../../storage/common/storage-create-storage-account.md). Le script suivant montre comment rechercher les disques non managés non attachés (objets blob de pages) à l’aide de la propriété *LeaseStatus*. Il effectue une itération sur tous les disques non managés dans tous les comptes de stockage dans un abonnement et vérifie si la propriété *LeaseStatus* est déverrouillée pour trouver les disques non managés non attachés. La propriété *LeaseStatus* est verrouillée si un disque non managé est attaché à une machine virtuelle.

Nous vous recommandons vivement de commencer par exécuter le script en affectant 0 à la variable *deleteUnattachedVHDs* pour afficher tous les disques non attachés. Après avoir examiné les disques non attachés, exécutez le script en affectant 1 à *deleteUnattachedVHDs* pour supprimer tous les disques non attachés.


```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=1

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                  }
                  else{

                        $_.ICloudBlob.Uri.AbsoluteUri

                  }

            }
        
        }

    }

}

```

## <a name="next-steps"></a>Étapes suivantes

[Supprimer le compte de stockage](../../storage/common/storage-create-storage-account.md)




