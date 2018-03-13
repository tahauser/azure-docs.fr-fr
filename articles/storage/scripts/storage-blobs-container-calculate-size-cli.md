---
title: "Exemple de script Azure CLI - Calculer la taille d’un conteneur d’objets blob | Microsoft Docs"
description: "Calculez la taille d’un conteneur dans le stockage d’objets blob Azure en effectuant le total des tailles des objets blob inclus dans le conteneur."
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
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: f9213018969ab47ce2e78d8c119f22dedaff9452
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calculer la taille totale d’un conteneur de stockage d’objets blob

Ce script calcule la taille d’un conteneur dans le stockage d’objets blob Azure en effectuant le total des tailles des objets blob inclus dans le conteneur.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Ce script d’interface de ligne de commande fournit une estimation de taille pour le conteneur et ne doit pas être utilisé pour les calculs de facturation.

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, le conteneur et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour calculer la taille du conteneur de stockage d’objets blob. Chaque élément du tableau renvoie à une documentation spécifique de la commande.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage blob upload](/cli/azure/storage/account#az_storage_account_create) | Charge des fichiers locaux dans un conteneur de stockage d’objets blob Azure. |
| [az storage blob list](/cli/azure/storage/account/keys#az_storage_account_keys_list) | Répertorie les objets blob inclus dans un conteneur de stockage d’objets blob Azure. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de stockage dans les [exemples Azure CLI pour le stockage d’objets blob Azure](../blobs/storage-samples-blobs-cli.md).
