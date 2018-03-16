---
title: "Créer une machine virtuelle Linux zonale avec l’interface de ligne de commande Azure | Microsoft Docs"
description: "Créer une machine virtuelle Linux dans une zone de disponibilité avec l’interface de ligne de commande Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: e31eb02fda7ade027225c428c5b15804ebc6f182
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Créer une machine virtuelle Linux dans une zone de disponibilité avec l’interface de ligne de commande Azure

Cet article aborde l’utilisation de l’interface de ligne de commande Azure pour créer une machine virtuelle Linux dans une zone de disponibilité Azure. Une [zone de disponibilité](../../availability-zones/az-overview.md) est une zone physiquement séparée dans une région Azure. Utilisez les zones de disponibilité pour protéger vos applications et données dans l’éventualité peu probable de défaillance ou de perte d’un centre de données entier.

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]

Assurez-vous que vous avez installé la dernière version de [l’interface de ligne de commande Azure 2.0](/cli/azure/install-az-cli2) et que vous vous êtes connecté à un compte Azure avec [az login](/cli/azure/reference-index#az_login).


## <a name="check-vm-sku-availability"></a>Vérifier la disponibilité de la référence SKU de machine virtuelle
La disponibilité des tailles de machine virtuelle, ou des références SKU, peut varier selon la région et le fuseau horaire. Pour vous aider à planifier l’utilisation des Zones de disponibilité, vous pouvez répertorier les références SKU de machine virtuelle disponibles par zone et par région Azure. Cela permet de s’assure que vous choisissez une taille de machine virtuelle appropriée et obtenez la résilience de votre choix entre les zones. Pour plus d’informations sur les différents types de machine virtuelle et les tailles, consultez [Vue d’ensemble des tailles de machine virtuelle](sizes.md).

Vous pouvez afficher les références SKU de machine virtuelle disponibles avec la commande [az vm list-skus](/cli/azure/vm#az_vm_list_skus). L’exemple suivant répertorie les références SKU de machine virtuelle disponibles dans la région *eastus2* :

```azurecli
az vm list-skus --location eastus2 --output table
```

La sortie est similaire à l’exemple condensé suivant, qui présente les Zones de disponibilité dans lesquelles chaque taille de machine virtuelle est disponible :

```azurecli
ResourceType      Locations  Name               Tier       Size     Zones
----------------  ---------  -----------------  ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2    Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2    Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s       Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s       Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3    Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3    Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3     Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3     Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create).  

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Un groupe de ressources doit être créé avant les machines virtuelles. Dans cet exemple, un groupe de ressources nommé *myResourceGroupVM* est créé dans la région *eastus2*. Est des États-Unis 2 est une des régions Azure qui prennent en charge les zones de disponibilité en préversion.

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus2
```

Le groupe de ressources est spécifié lors de la création ou de la modification d’une machine virtuelle, qui peut être vue dans cet article.

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). 

Lorsque vous créez une machine virtuelle, plusieurs options sont disponibles, comme l’image de système d’exploitation, les informations d’identification d’administration ou le dimensionnement des disques. Dans cet exemple, une machine virtuelle nommée *myVM* est créée et exécute Ubuntu Server. La machine virtuelle est créée dans la zone de disponibilité *1*. Par défaut, la machine virtuelle est créée à la taille *Standard_DS1_v2*. Cette taille est prise en charge dans la préversion des zones de disponibilité.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys --zone 1
```

La création de la machine virtuelle peut prendre plusieurs minutes. Une fois la machine virtuelle créée, l’interface Azure CLI fournit des informations concernant cette machine virtuelle. Notez la valeur `zones` qui indique la zone de disponibilité dans laquelle la machine virtuelle s’exécute. 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="zone-for-ip-address-and-managed-disk"></a>Zone pour l’adresse IP et le disque géré

Lorsque la machine virtuelle est déployée dans une zone de disponibilité, les ressources d’adresse IP et de disque géré sont déployées dans la même zone de disponibilité. Les exemples suivants obtiennent des informations sur ces ressources.

Utilisez d’abord la commande [az vm list-ip-addresses](/cli/azure/vm#az_vm_list_ip_addresses) pour renvoyer le nom de ressource d’adresse IP publique dans *myVM*. Dans cet exemple, le nom est stocké dans une variable qui est utilisée dans une étape ultérieure.

```azurecli-interactive
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Vous pouvez désormais obtenir des informations sur l’adresse IP :

```azurecli-interactive
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

Le résultat montre que l’adresse IP se trouve dans la même zone de disponibilité que la machine virtuelle :

```azurecli-interactive
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

De même, vérifiez que le disque géré de la machine virtuelle se trouve dans la zone de disponibilité. Utilisez la commande [az vm show](/cli/azure/vm#az_vm_show) pour renvoyer l’ID du disque. Dans cet exemple, l’ID du disque est stocké dans une variable qui est utilisée dans une étape ultérieure. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Vous pouvez désormais obtenir des informations sur le disque géré :

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

Le résultat montre que le disque géré se trouve dans la même zone de disponibilité que la machine virtuelle :

```azurecli-interactive
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/Subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2017-09-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```
 


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer une machine virtuelle dans une zone de disponibilité. Apprenez-en davantage sur les [régions et la disponibilité](regions-and-availability.md) des machines virtuelles Azure.




