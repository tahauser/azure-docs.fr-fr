---
title: "Exemple de script Azure CLI - Rotation des clés d’accès au compte de stockage | Microsoft Docs"
description: "Créez un compte de stockage Azure, puis récupérez ses clés d’accès et organisez leur rotation."
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
ms.author: marsma
ms.openlocfilehash: 743c7b01e82721e855b4c33bf2d36714ca8c6468
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Créer un compte de stockage et faire tourner ses clés d’accès

Ce script crée un compte de stockage Azure, affiche les clés d’accès au nouveau compte, puis renouvelle (fait tourner) les clés.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, le compte de stockage et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer le compte de stockage, récupérer ses clés d’accès et les faire tourner. Chaque élément du tableau renvoie à une documentation spécifique de la commande.

| Commande | Remarques |
|---|---|
| [az group create](/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](/cli/azure/storage/account#create) | Crée un compte de stockage Azure dans le groupe de ressources spécifié. |
| [az storage account keys list](/cli/azure/storage/account/keys#list) | Affiche les clés de l’accès au compte de stockage spécifié. |
| [az storage account keys renew](/cli/azure/storage/account/keys#renew) | Régénère la clé d’accès au compte de stockage principal ou secondaire. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de stockage dans les [exemples Azure CLI pour le stockage d’objets blob Azure](../blobs/storage-samples-blobs-cli.md).
