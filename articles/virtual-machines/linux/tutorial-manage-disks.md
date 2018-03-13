---
title: "Gestion des disques Azure avec l’interface de ligne de commande Azure | Microsoft Docs"
description: "Didacticiel - Gestion des disques Azure avec l’interface de ligne de commande Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 87b410fdcd5901499e809f8d2b9a7b8788134cfc
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="manage-azure-disks-with-the-azure-cli"></a>Gestion des disques Azure avec l’interface de ligne de commande Azure

Les machines virtuelles utilisent des disques pour stocker leur système d’exploitation, leurs applications et leurs données. Lorsque vous créez une machine virtuelle, il est important de choisir une taille de disque et une configuration appropriées à la charge de travail prévue. Ce didacticiel décrit le déploiement et la gestion des disques de machine virtuelle. Vous en apprendrez davantage sur les points suivants :

> [!div class="checklist"]
> * Disques de système d’exploitation et disques temporaires
> * Disques de données
> * Disques Standard et Premium
> * Performances des disques
> * Attachement et préparation des disques de données
> * Redimensionnement des disques
> * Captures instantanées de disque


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel exige que vous exécutiez Azure CLI version 2.0.4 ou une version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="default-azure-disks"></a>Disques Azure par défaut

Lorsqu’une machine virtuelle Azure est créée, deux disques sont automatiquement attachés à celle-ci. 

**Disque de système d’exploitation** : la taille des disques de système d’exploitation peut atteindre 1 To ; ces disques hébergent le système d’exploitation des machines virtuelles. Le disque de système d’exploitation est nommé */dev/sda* par défaut. La configuration de la mise en cache de disque de système d’exploitation est optimisée pour les performances du système d’exploitation. En raison de cette configuration, le disque de système d’exploitation **ne doit pas** héberger d’applications ou de données. Pour héberger ce type de contenu, utilisez plutôt des disques de données, qui sont décrits plus loin dans cet article. 

**Disque temporaire** : les disques temporaires utilisent un disque SSD qui se trouve sur le même hôte Azure que la machine virtuelle. Les disques temporaires sont extrêmement performants et peuvent être utilisés pour des opérations telles que le traitement de données temporaires. Toutefois, si la machine virtuelle est déplacée vers un nouvel hôte, toutes les données stockées sur un disque temporaire sont supprimées. La taille du disque temporaire est déterminée par la taille de la machine virtuelle. Les disques temporaires sont nommés */dev/sdb* et ont un point de montage */mnt*.

### <a name="temporary-disk-sizes"></a>Tailles du disque temporaire

| type | Taille de la machine virtuelle | Taille maximale du disque temporaire (Go) |
|----|----|----|
| [Usage général](sizes-general.md) | Séries A et D | 800 |
| [Optimisé pour le calcul](sizes-compute.md) | Série F | 800 |
| [Mémoire optimisée](../virtual-machines-windows-sizes-memory.md) | Séries D et G | 6144 |
| [Optimisé pour le stockage](../virtual-machines-windows-sizes-storage.md) | Série L | 5630 |
| [GPU](sizes-gpu.md) | Série N | 1 440 |
| [Hautes performances](sizes-hpc.md) | Séries A et H | 2000 |

## <a name="azure-data-disks"></a>Disques de données Azure

Des disques de données supplémentaires peuvent être ajoutés pour installer des applications et stocker des données. Les disques de données doivent être utilisés dans les cas où un stockage des données durable et réactif est souhaité. Chaque disque de données possède une capacité maximale de 1 To. La taille de la machine virtuelle détermine le nombre de disques de données pouvant être attachés à cette machine virtuelle. Pour chaque processeur virtuel de la machine virtuelle, deux disques de données peuvent être attachés. 

### <a name="max-data-disks-per-vm"></a>Disques de données max. par machine virtuelle

| type | Taille de la machine virtuelle | Disques de données max. par machine virtuelle |
|----|----|----|
| [Usage général](sizes-general.md) | Séries A et D | 32 |
| [Optimisé pour le calcul](sizes-compute.md) | Série F | 32 |
| [Mémoire optimisée](../virtual-machines-windows-sizes-memory.md) | Séries D et G | 64 |
| [Optimisé pour le stockage](../virtual-machines-windows-sizes-storage.md) | Série L | 64 |
| [GPU](sizes-gpu.md) | Série N | 48 |
| [Hautes performances](sizes-hpc.md) | Séries A et H | 32 |

## <a name="vm-disk-types"></a>Type de disque de machine virtuelle

Azure propose deux types de disque.

### <a name="standard-disk"></a>Disque Standard

Le stockage Standard s’appuie sur des disques durs et offre un stockage économique qui n’en est pas moins performant. Les disques Standard constituent la solution idéale pour une charge de travail de développement et de test économique.

### <a name="premium-disk"></a>Disque Premium

Les disques Premium reposent sur un disque SSD à faible latence et hautes performances. Ils conviennent parfaitement aux machines virtuelles exécutant une charge de travail en production. Le stockage Premium prend en charge les machines virtuelles des séries DS, DSv2, GS et FS. Les disques Premium sont de trois types (P10, P20, P30), la taille du disque détermine le type de disque. Lorsque vous sélectionnez une taille de disque, la valeur est arrondie au type suivant. Par exemple, si la taille du disque est inférieure à 128 Go, le type de disque est P10. Si la taille du disque est entre 129 Go et 512 Go, le type de disque est P20. Pour toute taille de disque supérieure à 512 Go, le type de disques est P30.

### <a name="premium-disk-performance"></a>Performances du disque Premium

|Type de disque de stockage Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Taille du disque (arrondie) | 128 Go | 512 Go | 1 024 Go (1 To) |
| Nb max. d'E/S par seconde par disque | 500 | 2 300 | 5 000 |
Débit par disque | 100 Mo/s | 150 Mo/s | 200 Mo/s |

Bien que le tableau ci-dessus identifie le nombre max. d’E/S par seconde par disque, un niveau de performances plus élevé est possible en entrelaçant plusieurs disques de données. Par exemple, une machine virtuelle Standard_GS5 peut atteindre un nombre maximum d’E/S par seconde de 80 000. Pour plus d’informations sur le nombre max. d’E/S par seconde par machine virtuelle, consultez [Tailles des machines virtuelles Linux dans Azure](sizes.md).

## <a name="create-and-attach-disks"></a>Créer et attacher des disques

Des disques de données peuvent être créés et attachés lors de la création d’une machine virtuelle, mais ils peuvent également être créés et attachés à une machine virtuelle existante.

### <a name="attach-disk-at-vm-creation"></a>Attacher le disque lors de la création d’une machine virtuelle

Créez un groupe de ressources avec la commande [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). 

```azurecli-interactive 
az group create --name myResourceGroupDisk --location eastus
```

Créez une machine virtuelle avec la commande [az vm create]( /cli/azure/vm#az_vm_create). L’argument `--datadisk-sizes-gb` est utilisé pour spécifier qu’un disque supplémentaire doit être créé et attaché à la machine virtuelle. Pour créer et attacher plusieurs disques, utilisez une liste séparée par des espaces des valeurs de taille de disque. Dans l’exemple suivant, une machine virtuelle est créée avec deux disques de données, tous deux de 128 Go. La taille des disques étant de 128 Go, ces disques sont configurés en tant que disques P10, qui fournissent 500 E/S par seconde maximum par disque.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --data-disk-sizes-gb 128 128 \
  --generate-ssh-keys
```

### <a name="attach-disk-to-existing-vm"></a>Attacher un disque à une machine virtuelle existante

Pour créer et attacher un nouveau disque à une machine virtuelle existante, utilisez la commande [az vm disk attach](/cli/azure/vm/disk#az_vm_disk_attach). L’exemple suivant crée un disque Premium de 128 Go et l’attache à la machine virtuelle créée à l’étape précédente.

```azurecli-interactive 
az vm disk attach --vm-name myVM --resource-group myResourceGroupDisk --disk myDataDisk --size-gb 128 --sku Premium_LRS --new 
```

## <a name="prepare-data-disks"></a>Préparer les disques de données

Une fois qu’un disque a été attaché à la machine virtuelle, le système d’exploitation doit être configuré pour utiliser le disque. L’exemple suivant montre comment configurer manuellement un disque. Ce processus peut également être automatisé à l’aide de cloud-init, qui est présenté dans un [prochain didacticiel](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configuration manuelle

Créez une connexion SSH avec la machine virtuelle. Remplacez l’exemple d’adresse IP par l’adresse IP publique de la machine virtuelle.

```azurecli-interactive 
ssh 52.174.34.95
```

Partitionnez le disque avec `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Écrivez un système de fichiers dans la partition avec la commande `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Montez le nouveau disque afin qu’il soit accessible dans le système d’exploitation.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Le disque est désormais accessible via le point de montage *datadrive*, qui peut être vérifié en exécutant la commande `df -h`. 

```bash
df -h
```

La sortie indique le nouveau lecteur monté sur */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Pour vous assurer que le lecteur est remonté automatiquement après un redémarrage, vous devez l’ajouter au fichier */etc/fstab*. Pour ce faire, obtenez l’UUID du disque avec l’utilitaire `blkid`.

```bash
sudo -i blkid
```

La sortie affiche l’UUID du lecteur, `/dev/sdc1` dans cet exemple.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Ajoutez une ligne semblable à ce qui suit au fichier */etc/fstab*. Notez également que la fonctionnalité permettant d’écrire des barrières peut être désactivée à l’aide de *barrier=0*, cette configuration peut améliorer les performances de disque. 

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail,barrier=0   1  2
```

Maintenant que le disque a été configuré, fermez la session SSH.

```bash
exit
```

## <a name="resize-vm-disk"></a>Redimensionner le disque de machine virtuelle

Une fois qu’une machine virtuelle a été déployée, vous pouvez augmenter la taille du disque de système d’exploitation ou des disques de données attachés. Augmenter la taille d’un disque peut être utile lorsque vous avez besoin de plus d’espace de stockage ou d’un niveau de performances plus élevé (P10, P20 et P30). Notez que vous ne pouvez pas réduire la taille des disques.

Avant d’augmenter la taille du disque, l’ID ou le nom du disque est nécessaire. Utilisez la commande [az disk list](/cli/azure/disk#az_disk_list) pour renvoyer tous les disques d’un groupe de ressources. Notez le nom du disque que vous souhaitez redimensionner.

```azurecli-interactive 
az disk list -g myResourceGroupDisk --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table
```

La machine virtuelle doit également être libérée. Utilisez la commande [az vm deallocate]( /cli/azure/vm#az_vm_deallocate) pour arrêter la machine virtuelle et la libérer.

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

Utilisez la commande [az disk update](/cli/azure/vm/disk#az_vm_disk_update) pour redimensionner le disque. Cet exemple redimensionne un disque nommé *myDataDisk* pour que sa taille soit de 1 To.

```azurecli-interactive 
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

Une fois l’opération de redimensionnement terminée, démarrez la machine virtuelle.

```azurecli-interactive 
az vm start --resource-group myResourceGroupDisk --name myVM
```

Si vous avez redimensionné le disque de système d’exploitation, la partition est automatiquement étendue. Si vous avez redimensionné un disque de données, toutes les partitions en cours doivent être étendues dans le système d’exploitation des machines virtuelles.

## <a name="snapshot-azure-disks"></a>Prendre une capture instantanée des disques Azure

La capture instantanée du disque crée une copie en lecture seule à un moment donné du disque. Les captures instantanées de machine virtuelle Azure sont utiles pour enregistrer rapidement l’état d’une machine virtuelle avant d’apporter des modifications à la configuration. Si les modifications apportées à la configuration s’avèrent indésirables, l’état de la machine virtuelle peut être restauré à l’aide de la capture instantanée. Lorsqu’une machine virtuelle a plusieurs disques, une capture instantanée de chaque disque est prise. Pour les sauvegardes cohérentes des applications, pensez à arrêter la machine virtuelle avant de prendre des captures instantanées du disque. Vous pouvez également utiliser le [service Sauvegarde Azure](/azure/backup/), qui vous permet d’effectuer des sauvegardes automatisées alors que la machine virtuelle est en cours d’exécution.

### <a name="create-snapshot"></a>Création d’un instantané

Avant de créer une capture instantanée de disque de machine virtuelle, l’ID ou le nom du disque est nécessaire. Utilisez la commande [az vm show](https://docs.microsoft.com/cli/azure/vm#az_vm_show) pour renvoyer l’ID du disque. Dans cet exemple, l’ID du disque est stocké dans une variable pour qu’il puisse être utilisé dans une étape ultérieure.

```azurecli-interactive 
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Maintenant que vous avez l’ID du disque de machine virtuelle, la commande suivante crée une capture instantanée du disque.

```azurcli
az snapshot create -g myResourceGroupDisk --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Création d’un disque à partir d’une capture instantanée

Cet instantané peut ensuite être converti en disque qui peut être utilisé pour recréer la machine virtuelle.

```azurecli-interactive 
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Restauration de la machine virtuelle à partir de l’instantané

Pour illustrer la récupération de machine virtuelle, supprimez la machine virtuelle existante. 

```azurecli-interactive 
az vm delete --resource-group myResourceGroupDisk --name myVM
```

Créez une nouvelle machine virtuelle à l’aide du disque d’instantané.

```azurecli-interactive 
az vm create --resource-group myResourceGroupDisk --name myVM --attach-os-disk mySnapshotDisk --os-type linux
```

### <a name="reattach-data-disk"></a>Rattacher un disque de données

Tous les disques de données doivent être rattachés à la machine virtuelle.

Trouvez d’abord le nom du disque de données à l’aide de la commande [az disk list](https://docs.microsoft.com/cli/azure/disk#az_disk_list). Cet exemple place le nom du disque dans une variable nommée *datadisk*, qui est utilisée à l’étape suivante.

```azurecli-interactive 
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Utilisez la commande [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#az_vm_disk_attach) pour attacher le disque.

```azurecli-interactive 
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a apporté des connaissances concernant les disques de machine virtuelle, notamment concernant les points suivants :

> [!div class="checklist"]
> * Disques de système d’exploitation et disques temporaires
> * Disques de données
> * Disques Standard et Premium
> * Performances des disques
> * Attachement et préparation des disques de données
> * Redimensionnement des disques
> * Captures instantanées de disque

Passez au didacticiel suivant pour en apprendre davantage sur l’automatisation de la configuration de machine virtuelle.

> [!div class="nextstepaction"]
> [How to customize a Linux virtual machine on first boot](./tutorial-automate-vm-deployment.md) (Comment personnaliser une machine virtuelle Linux au premier démarrage)
