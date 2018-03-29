---
title: Démarrage rapide Azure - Charger, télécharger et répertorier des objets blob dans Stockage Azure à l’aide de Azure CLI | Microsoft Docs
description: Dans le cadre de ce démarrage rapide, vous utilisez Azure CLI pour créer un compte de stockage et un conteneur. Ensuite, vous utilisez le CLI pour charger un objet blob dans Stockage Azure, télécharger un objet blob et répertorier les objets blob dans un conteneur.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: rogarana
ms.openlocfilehash: 673392f393d3fb5d7351c0b4ad4782179a99da2a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide de Azure CLI

L’interface de ligne de commande Azure (Azure CLI) est l’expérience de ligne de commande d’Azure pour gérer les ressources Azure. Vous pouvez l’utiliser dans votre navigateur avec Azure Cloud Shell. Vous pouvez également l’installer sur Windows, Linux ou MacOS, et l’exécuter à partir de la ligne de commande. Dans ce guide de démarrage rapide, vous apprenez à utiliser Azure CLI pour charger et télécharger des données vers et à partir du stockage Blob Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Exécutez `az --version` pour déterminer votre version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Créez un conteneur.

Les objets blob sont toujours chargés dans un conteneur. Vous pouvez organiser des groupes d’objets blob de la même façon que vous organisez vos fichiers dans les dossiers de l’ordinateur.

Créez un conteneur pour stocker des objets blob avec la commande [az storage container create](/cli/azure/storage/container#az_storage_container_create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Charger un objet blob

Le stockage Blob prend en charge les objets blob de blocs, d’ajout et de pages. La plupart des fichiers stockés dans le stockage Blob sont stockés comme des objets blob de blocs. Les objets blob d’ajout sont utilisés quand les données doivent être ajoutées à un objet blob existant sans modifier son contenu existant, par exemple, pour la journalisation. Les objets blob de pages stockent les fichiers de disque dur virtuel des machines virtuelles IaaS.

Commencez par créer un fichier à charger dans un objet blob.
Avec Azure Cloud Shell, procédez ainsi pour créer un fichier : utilisez `vi helloworld` lorsque le fichier s’ouvre, appuyez sur **Insérer**, tapez « Hello world », appuyez sur **Échap**, puis entrez `:x` et appuyez sur **Entrée**.

Dans cet exemple, vous chargez un objet blob dans le conteneur que vous avez créé à la dernière étape avec la commande [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Si vous avez fait appel à la méthode décrite précédemment pour créer un fichier dans l’environnement Azure Cloud Shell, vous pouvez utiliser cette commande CLI à la place. Normalement, vous devez spécifier un chemin d’accès, mais notez que cela est inutile ici, car le fichier a été créé dans le répertoire de base :

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name helloworld
    --file helloworld
```

Cette opération crée l’objet blob s’il n’existe pas déjà, et le remplace s’il existe. Chargez autant de fichiers que vous le souhaitez avant de continuer.

Pour charger plusieurs fichiers à la fois, vous pouvez utiliser la commande [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch).

## <a name="list-the-blobs-in-a-container"></a>Créer la liste des objets blob d’un conteneur

Listez les objets blob d’un conteneur avec la commande [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Télécharger un objet blob

Utilisez la commande [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download) pour télécharger l’objet blob chargé au préalable.

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

Si vous n’avez plus besoin des ressources de votre groupe de ressources, y compris du compte de stockage que vous avez créé dans ce guide de démarrage rapide, supprimez le groupe de ressources avec la commande [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à transférer des fichiers entre un disque local et un conteneur du stockage Blob Azure. Pour en savoir plus sur l’utilisation des objets blob dans le stockage Azure, consultez le didacticiel sur l’utilisation du stockage Blob Azure.

> [!div class="nextstepaction"]
> [Guide pratique des opérations de stockage Blob avec Azure CLI](storage-how-to-use-blobs-cli.md)
