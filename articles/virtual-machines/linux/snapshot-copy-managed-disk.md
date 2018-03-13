---
title: "Créer une capture instantanée d’un disque dur virtuel dans Azure | Microsoft Docs"
description: "Découvrez comment créer une copie d’un disque dur virtuel dans Azure pour l’utiliser comme sauvegarde ou pour la résolution de problèmes."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: 152c5a1103d32af27f689086cfcc9cc1a7acc5d3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-snapshot"></a>Créer un instantané 

Prenez une capture instantanée d’un disque dur virtuel de système d’exploitation ou de données pour la sauvegarde ou pour résoudre les problèmes de la machine virtuelle. Une capture instantanée est une copie complète en lecture seule d’un disque dur virtuel. 

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Utiliser Azure CLI 2.0 pour prendre une capture instantanée

L’exemple suivant nécessite l’installation d’Azure CLI 2.0 et la connexion à votre compte Azure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Les étapes suivantes montrent comment effectuer une capture instantanée à l’aide de la commande `az snapshot create` avec le paramètre `--source-disk`. L’exemple suivant suppose qu’il existe une machine virtuelle nommée `myVM` créée avec un disque de système d’exploitation géré dans le groupe de ressources `myResourceGroup`.

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

Le résultat suivant doit s’afficher :

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>Utiliser le portail Azure pour prendre une capture instantanée 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans l’angle supérieur gauche, cliquez sur **Créer une ressource** et recherchez **Capture instantanée**.
3. Dans le panneau Capture instantanée, cliquez sur **Créer**.
4. Entrez un **nom** pour la capture instantanée.
5. Sélectionnez un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md#resource-groups) existant ou tapez le nom d’un nouveau groupe. 
6. Sélectionnez un emplacement de centre de données Azure.  
7. Dans **Disque source**, sélectionnez le disque géré dont vous souhaitez obtenir une capture instantanée.
8. Sélectionnez le **type de compte** à utiliser pour stocker la capture instantanée. Nous vous recommandons d’utiliser le type **Standard_LRS**, sauf si vous avez besoin de la stocker sur un disque hautes performances.
9. Cliquez sur **Créer**.

Si vous envisagez d’utiliser la capture instantanée pour créer un disque géré et l’attacher à une machine virtuelle qui doit être hautement performante, utilisez le paramètre `--sku Premium_LRS` avec la commande `az snapshot create`. Cela permet de créer la capture instantanée et de la stocker en tant que disque géré Premium. Les disques gérés Premium sont plus performants, car il s’agit de disques SSD, mais ils coûtent plus cher que des disques Standard (HDD).


## <a name="next-steps"></a>Étapes suivantes

 Créez une machine virtuelle à partir d’une capture instantanée en créant un disque managé à partir de la capture instantanée, puis en attachant le nouveau disque managé comme disque de système d’exploitation. Pour plus d’informations, consultez le script [Créer une machine virtuelle à partir d’une capture instantanée](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json).

