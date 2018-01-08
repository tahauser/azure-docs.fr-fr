---
title: "Démarrage rapide Azure - Créer un compte de stockage à l’aide d’Azure CLI | Microsoft Docs"
description: "Apprenez rapidement à créer un compte de stockage à l’aide d’Azure CLI."
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
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: ed956e3d27d315e0ce4901c2c38d50652f77c09a
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>Créer un compte de stockage à l’aide d’Azure CLI

L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide de démarrage rapide détaille l’utilisation d’Azure CLI pour créer un compte de stockage Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Cet exemple crée un groupe de ressources nommé *myResourceGroup* dans la région *eastus*.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

Si vous ne savez pas quelle région spécifier pour le paramètre `--location`, vous pouvez récupérer la liste des régions prises en charge pour votre abonnement avec la commande [az account list-locations](/cli/azure/account#list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-storage-account"></a>Créer un compte de stockage à usage général

Il existe plusieurs types de comptes de stockage appropriés pour différents scénarios d’utilisation, chacun d’eux prenant en charge un ou plusieurs des services de stockage (objets blob, fichiers, tables ou files d’attente). Le tableau suivant détaille les types de comptes de stockage disponibles.

|**Type de compte de stockage**|**Édition Standard à usage général**|**Édition Premium à usage général**|**Stockage d’objets blob, niveaux d’accès froid et chaud**|
|-----|-----|-----|-----|
|**Services pris en charge**| Services d’objet blob, de fichier, de table et de file d’attente | Service d'objets blob | Service d'objets blob|
|**Types d’objets blob pris en charge**|Objets blob de blocs, de pages et d’ajout | Objets blob de pages | Objets blob de blocs et objets blob d’ajout|

Créez un compte de stockage à usage général avec la commande [az storage account create](/cli/azure/storage/account#create).

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --kind Storage \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, y compris du compte de stockage que vous avez créé dans ce guide de démarrage rapide, supprimez le groupe de ressources avec la commande [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un groupe de ressources et un compte de stockage standard à usage général. Pour savoir comment transférer des données à destination et à partir de votre compte de stockage, passez au démarrage rapide du Stockage Blob.

> [!div class="nextstepaction"]
> [Transférer des objets à destination/à partir du stockage Blob Azure à l’aide d’Azure CLI](../blobs/storage-quickstart-blobs-cli.md)