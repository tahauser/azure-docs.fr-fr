---
title: Gestion du partage de fichiers Azure à l’aide d’Azure CLI
description: Découvrez comment utiliser Azure CLI pour gérer Azure Files.
services: storage
documentationcenter: na
author: wmgries
manager: jeconnoc
editor: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 066a43b553be18a5a0bc889fff441824df301b98
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-using-the-azure-cli"></a>Gestion du partage de fichiers Azure à l’aide d’Azure CLI
[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés dans Windows, Linux et macOS. Ce guide vous explique les bases de l’utilisation du partage de fichiers avec Azure CLI. Découvrez comment : 

> [!div class="checklist"]
> * Créer un groupe de ressources et un compte de stockage
> * Crée un partage de fichiers Azure 
> * Créer un répertoire
> * Charger un fichier 
> * Téléchargement d’un fichier
> * Créer et utiliser un instantané de partage

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous décidez d’installer et d’utiliser Azure CLI localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Pour déterminer la version, exécutez la commande **az --version**. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

Par défaut, les commandes d’Azure CLI renvoient JSON (JavaScript Object Notation), qui est de fait le moyen d’envoyer et de recevoir des messages d’API REST. Pour simplifier l’utilisation des réponses JSON, quelques exemples dans ce guide utilisent le paramètre de requête sur les commandes d’Azure CLI. Ce paramètre utilise le [langage de requête JMESPath](http://jmespath.org/) pour analyser JSON. Vous pouvez en savoir plus sur la manipulation des résultats des commandes d’Azure CLI en suivant le [didacticiel JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Si vous n’avez pas déjà un groupe de ressources Azure, vous pouvez en créer un avec la commande [az group create](/cli/azure/group#create). 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *Est des États-Unis*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
Un compte de stockage est un pool partagé de stockage dans lequel vous pouvez déployer un partage de fichiers Azure, ou d’autres ressources de stockage comme les objets blob ou les files d’attente. Un compte de stockage peut contenir un nombre illimité de partages de fichiers, et un partage peut stocker un nombre illimité de fichiers, dans les limites de capacité du compte de stockage.

Cet exemple crée un compte de stockage nommé `mystorageaccount<random number>` avec la commande [az storage account create](/cli/azure/storage/account#create), puis place le nom de ce compte de stockage dans la variable `$STORAGEACCT`. Le nom d’un compte de stockage doit être unique. Utilisez `$RANDOM` pour ajouter un nombre à la fin du compte de stockage pour le rendre unique. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Obtenir la clé du compte de stockage
Une clé de compte de stockage sert à contrôler l’accès aux ressources dans un compte de stockage. Ces clés sont automatiquement créées en même temps que le compte de stockage. Vous pouvez récupérer les clés du compte de stockage avec la commande [az storage account keys list](/cli/azure/storage/account/keys#list). 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure
Vous pouvez désormais créer votre premier partage de fichiers Azure. Vous pouvez créer des partages de fichiers avec la commande [az storage share create](/cli/azure/storage/share#create). Cet exemple crée un partage de fichiers Azure nommé `myshare`. 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!Important]  
> Le nom des partages ne doit contenir que des minuscules, des nombres et des traits d’union uniques, mais ne peut commencer par un trait d’union. Pour plus d’informations sur la façon de nommer des partages de fichiers et des fichiers, consultez la rubrique [Affectation de noms et références aux partages, répertoires, fichiers et métadonnées](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Manipulation du contenu du partage de fichiers Azure
Maintenant que vous avez créé un partage de fichiers Azure, vous pouvez monter le partage de fichiers avec SMB sur [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) ou [macOS](storage-how-to-use-files-mac.md). Vous pouvez également manipuler votre partage de fichiers avec Azure CLI. Cette méthode est d’ailleurs plus avantageuse que le montage du partage de fichiers avec SMB, car toutes les requêtes faites avec Azure CLI sont faites avec l’API REST File, ce qui vous permet de créer, modifier et supprimer des fichiers et des répertoires dans votre partage de fichiers depuis :

- Bash Cloud Shell (qui ne peut pas monter les partages de fichiers sur SMB).
- Les clients qui ne peuvent pas monter de partages SMB, comme les clients locaux qui n’ont pas débloqué le port 445.
- Les scénarios sans serveur, comme dans [Azure Functions](../../azure-functions/functions-overview.md). 

### <a name="create-directory"></a>Créer un répertoire
Pour créer un répertoire nommé *myDirectory* à la racine de votre partage de fichiers Azure, exécutez la commande [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create).

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Charger un fichier
Pour montrer comment charger un fichier avec la commande [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload), nous devons d’abord créer un fichier dans le disque de travail d’Azure CLI Cloud Shell à charger. Dans l’exemple suivant, nous allons créer puis charger le fichier.

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Si vous exécutez Azure CLI en local, vous devez remplacer `~/clouddrive` par un chemin existant sur votre machine.

Après avoir chargé le fichier, vous pouvez exécuter la commande [`az storage file list`](/cli/azure/storage/file#az_storage_file_list) pour vérifier que le fichier a bien été chargé dans votre partage de fichiers Azure.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Téléchargement d’un fichier
Vous pouvez exécuter la commande [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) pour télécharger une copie du fichier chargé sur le disque de travail de Cloud Shell.

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Copie des fichiers
Une tâche courante consiste à copier les fichiers d’un partage de fichiers vers un autre partage de fichiers, ou dans/depuis un conteneur de stockage d’objets blob Azure. Pour illustrer cette fonctionnalité, vous pouvez créer un nouveau partage et copier le fichier chargé dans ce nouveau partage avec la commande [az storage file copy](/cli/azure/storage/file/copy). 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Maintenant, si vous répertoriez les fichiers dans le nouveau partage, vous devez voir votre fichier copié.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Bien que la commande `az storage file copy start` soit pratique pour déplacer des fichiers ad-hoc entre des partages de fichiers Azure et des conteneurs de stockage d’objets blob Azure, nous recommandons AzCopy pour des déplacements plus volumineux (en termes de nombre ou de taille des fichiers déplacés). En savoir plus sur [AzCopy pour Linux](../common/storage-use-azcopy-linux.md) et [AzCopy pour Windows](../common/storage-use-azcopy.md). AzCopy doit être installé en local (il n’est pas disponible dans Cloud Shell) 

## <a name="create-and-modify-share-snapshots"></a>Créer et modifier des instantanés de partage
Avec un partage de fichiers Azure, vous pouvez aussi créer des instantanés de partage. Un instantané conserve un point dans le temps pour un partage de fichiers Azure. Les instantanés de partage sont similaires aux technologies de systèmes d’exploitation que vous connaissez peut-être déjà comme :
- Les instantanés du [Gestionnaire de Volume logique (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pour les systèmes Linux
- Les instantanés du [système de fichiers Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pour macOS. 
- Le [service VSS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) pour les systèmes de fichiers Windows comme NTFS et ReFS

Vous pouvez supprimer un instantané de partage à l’aide de la commande [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot).

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Parcourir le contenu des instantanés de partage
Vous pouvez parcourir le contenu d’un instantané de partage en envoyant le timestamp de l’instantané de partage capturé dans la variable `$SNAPSHOT` dans la commande `az storage file list`.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Répertorier les instantanés de partage
Vous pouvez afficher la liste d’instantanés pris pour votre partage avec la commande suivante.

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Restaurer à partir d’un instantané de partage
Vous pouvez restaurer un fichier avec la commande `az storage file copy start` que nous avons utilisée plus tôt. Nous allons d’abord supprimer le fichier `SampleUpload.txt` que nous avons chargé afin de le restaurer depuis l’instantané.

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Supprimer un instantané de partage
Pour supprimer un instantané de capture, exécutez la commande [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete), avec la variable contenant la référence `$SNAPSHOT` du paramètre `--snapshot`.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Supprimer des ressources
Quand vous avez terminé, vous pouvez exécuter la commande [`az group delete`](/cli/azure/group#delete) pour supprimer le groupe de ressources et toutes les ressources associées. 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Vous pouvez aussi supprimer les ressources une par une :
- Pour supprimer les partages de fichiers Azure que nous avons créés pour ce guide de démarrage rapide.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --delete-snapshots include

az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2" \
    --delete-snapshots include
```

- Pour supprimer le compte de stockage (ce qui supprimera par la même occasion les partages de fichiers créés ainsi que toutes les autres ressources de stockage créées telles qu’un conteneur de stockage d’objets blob Azure).

```azurecli-interactive
az storage account delete \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --yes
```

## <a name="next-steps"></a>Étapes suivantes
- [Gestion des partages de fichiers avec le portail Azure](storage-how-to-use-files-portal.md)
- [Gestion des partages de fichiers avec Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Gestion des partages de fichiers avec l’Explorateur Stockage](storage-how-to-use-files-storage-explorer.md)
- [Planification d’un déploiement Azure Files](storage-files-planning.md)