---
title: Restaurer un disque de machine virtuelle avec Sauvegarde Azure | Microsoft Docs
description: "Découvrez comment restaurer un disque et créer une machine virtuelle récupérée dans Azure avec le service Sauvegarde et Recovery Services."
services: backup
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 2/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 571d40c46771f43ad5ea78fe92398de09e87393c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Restaurer un disque et créer une machine virtuelle récupérée dans Azure
Azure Backup crée des points de récupération stockés dans des coffres de récupération géoredondants. Quand vous effectuez une restauration à partir d’un point de récupération, vous pouvez restaurer la machine virtuelle entière ou des fichiers individuels. Cet article explique comment restaurer une machine virtuelle entière. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Répertorier et sélectionner des points de récupération
> * Restaurer un disque à partir d’un point de récupération
> * Créer une machine virtuelle à partir du disque restauré

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.18 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Prérequis
Ce didacticiel nécessite une machine virtuelle Linux qui a été protégée avec Sauvegarde Azure. Pour simuler une suppression accidentelle de machine virtuelle et le processus de récupération, vous créez une machine virtuelle à partir d’un disque à un point de récupération. Si vous avez besoin d’une machine virtuelle Linux qui a été protégée avec Sauvegarde Azure, consultez [Sauvegarder une machine virtuelle dans Azure avec l’interface CLI](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Présentation de la sauvegarde
Quand Azure lance une sauvegarde, l’extension de sauvegarde sur la machine virtuelle capture un instantané à un point dans le temps. L’extension de sauvegarde est installée sur la machine virtuelle quand la première sauvegarde est demandée. Sauvegarde Azure peut également capturer un instantané du stockage sous-jacent si la machine virtuelle n’est pas en cours d’exécution durant la sauvegarde.

Par défaut, Sauvegarde Azure effectue une sauvegarde cohérente du système de fichiers. Une fois que Sauvegarde Azure a pris l’instantané, les données sont transférées vers le coffre Recovery Services. Pour optimiser l’efficacité, Sauvegarde Azure identifie et transfère uniquement les blocs de données qui ont été modifiés depuis la sauvegarde précédente.

Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé.


## <a name="list-available-recovery-points"></a>Obtenir la liste des points de récupération disponibles
Pour restaurer un disque, vous sélectionnez un point de récupération en tant que source pour les données de récupération. Étant donné que la stratégie par défaut crée un point de récupération chaque jour et conserve ces derniers pendant 30 jours, vous pouvez conserver un ensemble de points de récupération qui vous permet de sélectionner un point particulier dans le temps pour la récupération. 

Pour afficher la liste des points de récupération disponibles, utilisez [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list). Le point de récupération **name** est utilisé pour récupérer des disques. Dans ce didacticiel, nous voulons le point de récupération le plus récent disponible. Le paramètre `--query [0].name` sélectionne le nom du point de récupération le plus récent comme suit :

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Restaurer un disque de machine virtuelle
Pour restaurer votre disque à partir du point de récupération, vous créez tout d’abord un compte de stockage Azure. Ce compte de stockage permet de stocker le disque restauré. À travers des étapes supplémentaires, le disque restauré est utilisé pour créer une machine virtuelle.

1. Pour créer un compte de stockage, utilisez la commande [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create). Le nom du compte de stockage doit être écrit entièrement en minuscules et être unique. Remplacez *mystorageaccount* par un nom unique :

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Restaurez le disque à partir de votre point de récupération avec [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks). Remplacez *mystorageaccount* par le nom du compte de stockage que vous avez créé à l’aide de la commande précédente. Remplacez *myRecoveryPointName* par le nom du point de récupération que vous avez obtenu dans la sortie de la commande [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list) précédente :

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>Effectuer le monitoring de la tâche de restauration
Pour effectuer le monitoring de l’état de la tâche de restauration, utilisez [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az_backup_job_list) :

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

La sortie est similaire à l’exemple suivant, qui montre que la tâche de restauration est *en cours* :

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Quand les rapports de la tâche de restauration sont à l’*état* *Terminé*, le disque a été restauré sur le compte de stockage.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>Convertir le disque restauré en disque managé
La tâche de restauration crée un disque non managé. Pour créer une machine virtuelle à partir du disque, celui-ci doit d’abord être converti en disque managé.

1. Obtenez les informations de connexion pour votre compte de stockage avec [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_show_connection_string). Remplacez *mystorageaccount* par le nom de votre compte de stockage comme suit :
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. Votre disque non managé est sécurisé dans le compte de stockage. Les commandes suivantes obtiennent des informations sur votre disque non managé et créent une variable nommée *uri* qui est utilisée dans l’étape suivante où vous créez le disque managé.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Vous pouvez à présent créer un disque managé à partir du disque récupéré avec [az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az_disk_create). La variable *uri* de l’étape précédente est utilisée en tant que source pour votre disque managé.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Une fois que vous disposez d’un disque managé à partir du disque restauré, nettoyez le disque non managé et le compte de stockage avec [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete). Remplacez *mystorageaccount* par le nom de votre compte de stockage comme suit :

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Créer une machine virtuelle à partir du disque restauré
La dernière étape consiste à créer une machine virtuelle à partir du disque managé.

1. Créez une machine virtuelle à partir du disque managé avec [az vm create](/cli/azure/vm?view=azure-cli-latest#az_vm_create) comme suit :

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Pour confirmer que votre machine virtuelle a été créée à partir du disque récupéré, répertoriez les machines virtuelles dans votre groupe de ressources avec [az vm list](/cli/azure/vm?view=azure-cli-latest#az_vm_list) comme suit :

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>étapes suivantes
Dans ce didacticiel, vous avez restauré un disque à partir d’un point de récupération, puis créé une machine virtuelle à partir du disque. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Répertorier et sélectionner des points de récupération
> * Restaurer un disque à partir d’un point de récupération
> * Créer une machine virtuelle à partir du disque restauré

Passez au didacticiel suivant pour en savoir plus sur la restauration de fichiers individuels à partir d’un point de récupération.

> [!div class="nextstepaction"]
> [Restaurer des fichiers sur une machine virtuelle dans Azure](tutorial-restore-files.md)

