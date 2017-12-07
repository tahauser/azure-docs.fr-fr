---
title: "Guide de démarrage rapide Azure - Transférer des objets vers/à partir du stockage Blob Azure à l’aide d’Azure CLI | Microsoft Docs"
description: "Apprenez rapidement à transférer des objets vers/à partir du stockage Blob Azure à l’aide d’Azure CLI"
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/19/2017
ms.author: tamram
ms.openlocfilehash: a300294c83cb206e6211985c736e3ff01bb1ab43
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-the-azure-cli"></a>Transférer des objets vers/à partir du stockage Blob Azure à l’aide d’Azure CLI

L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide de démarrage rapide décrit en détail l’utilisation d’Azure CLI pour charger et télécharger des données vers et à partir du stockage Blob Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Créez un conteneur.

Les objets blob sont toujours chargés dans un conteneur. Les conteneurs vous permettent d’organiser des groupes d’objets blob comme des fichiers dans des répertoires sur votre ordinateur.

Créez un conteneur pour stocker des objets blob avec la commande [az storage container create](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Charger un objet blob

Le stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. La plupart des fichiers stockés dans le stockage Blob sont stockés comme des objets blob de blocs. Les objets blob d’ajout sont utilisés quand les données doivent être ajoutées à un objet blob existant sans modifier son contenu existant, par exemple, pour la journalisation. Les objets blob de pages stockent les fichiers de disque dur virtuel des machines virtuelles IaaS.

Dans cet exemple, nous chargeons un objet blob dans le conteneur que nous avons créé à la dernière étape avec la commande [az storage blob upload](/cli/azure/storage/blob#upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Cette opération crée l’objet blob s’il n’existe pas déjà, et le remplace s’il existe. Chargez autant de fichiers que vous le souhaitez avant de continuer.

## <a name="list-the-blobs-in-a-container"></a>Création d'une liste d'objets blob dans un conteneur

Listez les objets blob d’un conteneur avec la commande [az storage blob list](/cli/azure/storage/blob#list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Téléchargement d’un objet blob

Utilisez la commande [az storage blob download](/cli/azure/storage/blob#download) pour télécharger un objet blob que vous avez chargé précédemment.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Transfert de données avec AzCopy

L’utilitaire [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) est une autre option hautes performances pour transférer des données par script avec le stockage Azure. Vous pouvez utiliser AzCopy pour transférer des données vers et à partir du stockage Blob, Table et Fichier.

Exemple rapide : voici la commande AzCopy qui permet de charger un fichier appelé *myfile.txt* dans le conteneur *mystoragecontainer*.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, y compris du compte de stockage que vous avez créé dans ce guide de démarrage rapide, supprimez le groupe de ressources avec la commande [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et un conteneur du stockage Blob Azure. Pour en savoir plus sur l’utilisation des objets blob dans le stockage Azure, consultez le didacticiel sur l’utilisation du stockage Blob Azure.

> [!div class="nextstepaction"]
> [Guide pratique des opérations de stockage Blob avec Azure CLI](storage-how-to-use-blobs-cli.md)
