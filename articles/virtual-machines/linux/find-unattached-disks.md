---
title: "Trouver et supprimer les disques gérés et non gérés Azure non attachés | Microsoft Docs"
description: "Comment rechercher et supprimer les disques Azure gérés et non gérés (disques durs virtuels/objets blob de pages) à l’aide d’Azure CLI"
services: virtual-machines-linux
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 9ada768cd4128b9dd6949b5a96c557496c6bb11c
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Trouver et supprimer les disques gérés et non gérés Azure non attachés
Lorsque vous supprimez une machine virtuelle dans Azure, les disques attachés ne sont pas supprimés par défaut. Cela empêche les pertes de données en cas de suppression accidentelle de machines, mais vous continuez à payer inutilement pour les disques non attachés. Utilisez cet article pour rechercher et supprimer tous les disques non attachés et faire des économies. 


## <a name="find-and-delete-unattached-managed-disks"></a>Trouver et supprimer les disques gérés non attachés 

Le script suivant montre comment rechercher les disques gérés non attachés à l’aide de la propriété ManagedBy.  Il effectue une itération sur tous les disques gérés dans un abonnement et vérifie si la propriété *ManagedBy* a la valeur null pour rechercher des disques gérés non attachés. La propriété *ManagedBy* stocke l’ID de ressource de la machine virtuelle à laquelle un disque géré est attaché. 

Nous recommandons vivement de commencer par exécuter le script en définissant la variable *deleteUnattachedDisks* sur 0 pour afficher tous les disques non attachés. Après avoir examiné les disques non attachés, exécutez le script en définissant *deleteUnattachedDisks* sur 1 pour supprimer tous les disques non attachés.

 ```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done

```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Trouver et supprimer les disques non gérés non attachés 

Les disques non gérés sont des fichiers VHD stockés en tant [qu’objets blob de pages](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) dans les [comptes de stockage Azure](../../storage/common/storage-create-storage-account.md). Le script suivant montre comment rechercher les disques non gérés non attachés (blobs de pages) à l’aide de la propriété LeaseStatus. Il effectue une itération sur tous les disques non gérés dans tous les comptes de stockage dans un abonnement et vérifie si la propriété *LeaseStatus* est déverrouillée pour trouver les disques non gérés non attachés. La propriété *LeaseStatus* est définie sur verrouillé si un disque non géré est attaché à une machine virtuelle. 

Nous recommandons vivement de commencer par exécuter le script en définissant la variable *deleteUnattachedVHDs* sur 0 pour afficher tous les disques non attachés. Après avoir examiné les disques non attachés, exécutez le script en définissant *deleteUnattachedVHDs* sur 1 pour supprimer tous les disques non attachés.


 ```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=1

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 

```

## <a name="next-steps"></a>étapes suivantes

[Supprimer le compte de stockage](../../storage/common/storage-create-storage-account.md)



