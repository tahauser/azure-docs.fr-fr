---
title: "Exemple de script Azure CLI - Supprimer des conteneurs par préfixe | Microsoft Docs"
description: "Supprimez des conteneurs d’objets blob Stockage Azure selon un préfixe de nom de conteneur."
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
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: 9e93dc14a4729011f74c5eafe94528608b89116f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Supprimer des conteneurs selon un préfixe de nom de conteneur

Ce script crée d’abord quelques exemples de conteneurs dans le stockage d’objets blob Azure, puis supprime certains des conteneurs selon un préfixe dans le nom du conteneur.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, les conteneurs restants, ainsi que toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour supprimer des conteneurs selon un préfixe de nom de conteneur. Chaque élément du tableau renvoie à une documentation spécifique de la commande.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Crée un compte de stockage Azure dans le groupe de ressources spécifié. |
| [az storage container create](/cli/azure/storage/container#az_storage_container_create) | Crée un conteneur dans Stockage Blob Azure. |
| [az storage container list](/cli/azure/storage/container#az_storage_container_list) | Répertorie les conteneurs inclus dans un compte de stockage Azure. |
| [az storage container delete](/cli/azure/storage/container#az_storage_container_delete) | Supprime des conteneurs dans un compte de stockage Azure. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de stockage dans les [exemples Azure CLI pour Stockage Azure](../blobs/storage-samples-blobs-cli.md).
