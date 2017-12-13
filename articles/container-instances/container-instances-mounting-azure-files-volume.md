---
title: Monter un volume Azure Files dans Azure Container Instances
description: "Découvrir comment monter un volume Azure Files pour conserver l’état avec Azure Container Instances"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/05/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: b2e8e27cecb4d1225e378690063b42f5d5242868
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>Monter un partage de fichiers Azure avec Azure Container Instances

Par défaut, les conteneurs Azure Container Instances sont sans état. Si le conteneur se bloque ou s’arrête, son état est entièrement perdu. Pour conserver l’état au-delà de la durée de vie du conteneur, vous devez monter un volume à partir d’un stockage externe. Cet article explique comment monter un partage de fichiers Azure pour une utilisation avec Azure Container Instances.

## <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure

Avant d’utiliser un partage de fichiers Azure avec Azure Container Instances, vous devez le créer. Exécutez le script suivant pour créer un compte de stockage et y héberger le partage de fichiers, et le partage proprement dit. Comme le nom du compte de stockage doit être globalement unique, le script ajoute une valeur aléatoire à la chaîne de base.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Obtenir les détails d’accès au compte de stockage

Pour monter un partage de fichiers Azure en tant que volume dans Azure Container Instances, vous avez besoin de trois valeurs : le nom du compte de stockage, le nom du partage et la clé d’accès de stockage.

Si vous avez utilisé le script ci-dessus, le nom du compte de stockage a été créé avec une valeur aléatoire à la fin. Pour interroger la chaîne finale (y compris la partie aléatoire), utilisez les commandes suivantes :

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

Le nom du partage étant déjà connu (c’est-à-dire *acishare* dans le script ci-dessus), il ne reste que la clé du compte de stockage, qui peut être trouvée à l’aide de la commande suivante :

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="deploy-the-container-and-mount-the-volume"></a>Déployer le conteneur et monter le volume

Pour monter un partage de fichiers Azure en tant que volume dans un conteneur, spécifiez le point de montage du volume et le partage lorsque vous créez le conteneur avec [az container create](/cli/azure/container#az_container_create). Si vous avez accompli les étapes précédentes, vous pouvez monter le partage que vous avez créé avant à l’aide de la commande suivante pour créer un conteneur :

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image seanmckenna/aci-hellofiles \
    --ip-address Public \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

## <a name="manage-files-in-mounted-volume"></a>Gérer les fichiers dans le volume monté

Après le démarrage du conteneur, vous pouvez utiliser l’application web simple déployée via l’image [aci/seanmckenna-hellofiles](https://hub.docker.com/r/seanmckenna/aci-hellofiles/) pour gérer les fichiers du partage de fichiers Azure sur le chemin de montage que vous avez spécifié. Obtenez l’adresse IP de l’application web avec la commande [az container show](/cli/azure/container#az_container_show) :

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles -o table
```

Vous pouvez utiliser le [portail Azure](https://storageexplorer.com) ou un outil comme l’[Explorateur Stockage Microsoft Azure](https://portal.azure.com) pour récupérer et inspecter le fichier écrit sur le partage de fichiers.

## <a name="next-steps"></a>Étapes suivantes

Découvrir la relation existante entre [Azure Container Instances et les orchestrateurs de conteneurs](container-instances-orchestrator-relationship.md).
