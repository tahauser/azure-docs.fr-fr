---
title: Monter un volume Azure Files dans Azure Container Instances
description: "Découvrir comment monter un volume Azure Files pour conserver l’état avec Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0f824dad7ba5b661941e952383025e5171f32e55
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>Monter un partage de fichiers Azure avec Azure Container Instances

Par défaut, les conteneurs Azure Container Instances sont sans état. Si le conteneur se bloque ou s’arrête, son état est entièrement perdu. Pour conserver l’état au-delà de la durée de vie du conteneur, vous devez monter un volume à partir d’un stockage externe. Cet article explique comment monter un partage de fichiers Azure pour une utilisation avec Azure Container Instances.

## <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure

Avant d’utiliser un partage de fichiers Azure avec Azure Container Instances, vous devez le créer. Exécutez le script suivant pour créer un compte de stockage afin d’héberger le partage de fichiers et le partage proprement dit. Comme le nom du compte de stockage doit être globalement unique, le script ajoute une valeur aléatoire à la chaîne de base.

```azurecli-interactive
# Change these four parameters
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Obtenir les détails d’accès au compte de stockage

Pour monter un partage de fichiers Azure en tant que volume dans Azure Container Instances, vous avez besoin de trois valeurs : le nom du compte de stockage, le nom du partage et la clé d’accès de stockage.

Si vous avez utilisé le script ci-dessus, le nom du compte de stockage a été créé avec une valeur aléatoire à la fin. Pour interroger la chaîne finale (y compris la partie aléatoire), utilisez les commandes suivantes :

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

Le nom du partage étant déjà connu (il s’agit de *acishare* dans le script ci-dessus), il ne reste que la clé du compte de stockage accessible à l’aide de la commande suivante :

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="store-storage-account-access-details-with-azure-key-vault"></a>Stocker les détails de l’accès au compte de stockage avec Azure Key Vault

Les clés de compte de stockage protégeant l’accès à vos données, nous vous recommandons de les stocker dans un coffre de clés Azure.

Créez un coffre de clés avec Azure CLI :

```azurecli-interactive
KEYVAULT_NAME=aci-keyvault
az keyvault create -n $KEYVAULT_NAME --enabled-for-template-deployment -g $ACI_PERS_RESOURCE_GROUP
```

Le commutateur `enabled-for-template-deployment` permet à Azure Resource Manager d’extraire les secrets de votre coffre de clés au moment du déploiement.

Stockez la clé de compte de stockage en tant que nouveau secret dans le coffre de clés :

```azurecli-interactive
KEYVAULT_SECRET_NAME=azurefilesstoragekey
az keyvault secret set --vault-name $KEYVAULT_NAME --name $KEYVAULT_SECRET_NAME --value $STORAGE_KEY
```

## <a name="mount-the-volume"></a>Monter le volume

Le montage d’un partage de fichiers Azure en tant que volume dans un conteneur est un processus en deux étapes. Tout d’abord, vous fournissez les détails du partage dans le cadre de la définition du groupe de conteneurs, puis vous spécifiez la manière dont vous souhaitez que le volume soit monté à l’intérieur d’un ou de plusieurs conteneurs du groupe.

Pour définir les volumes que vous souhaitez rendre disponibles pour le montage, ajoutez un tableau `volumes` à la définition de groupe de conteneurs dans le modèle Azure Resource Manager, puis référencez les volumes dans la définition des conteneurs individuels.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "type": "string"
    },
    "storageaccountkey": {
      "type": "securestring"
    }
  },
  "resources":[{
    "name": "hellofiles",
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2017-08-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
      "containers": [{
        "name": "hellofiles",
        "properties": {
          "image": "seanmckenna/aci-hellofiles",
          "resources": {
            "requests": {
              "cpu": 1,
              "memoryInGb": 1.5
            }
          },
          "ports": [{
            "port": 80
          }],
          "volumeMounts": [{
            "name": "myvolume",
            "mountPath": "/aci/logs/"
          }]
        }
      }],
      "osType": "Linux",
      "ipAddress": {
        "type": "Public",
        "ports": [{
          "protocol": "tcp",
          "port": "80"
        }]
      },
      "volumes": [{
        "name": "myvolume",
        "azureFile": {
          "shareName": "acishare",
          "storageAccountName": "[parameters('storageaccountname')]",
          "storageAccountKey": "[parameters('storageaccountkey')]"
        }
      }]
    }
  }]
}
```

Le modèle inclut le nom et la clé du compte de stockage en tant que paramètres qui peuvent être fournis dans un fichier de paramètres séparé. Pour remplir le fichier de paramètres, vous avez besoin de trois valeurs : le nom du compte de stockage, l’ID de ressource de votre coffre de clés Azure et le nom de secret du coffre de clés que vous avez utilisé pour stocker la clé de stockage. Si vous avez suivi les étapes précédentes, vous pouvez obtenir ces valeurs avec le script suivant :

```azurecli-interactive
echo $STORAGE_ACCOUNT
echo $KEYVAULT_SECRET_NAME
az keyvault show --name $KEYVAULT_NAME --query [id] -o tsv
```

Insérer les valeurs dans le fichier de paramètres :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "value": "<my_storage_account_name>"
    },
   "storageaccountkey": {
      "reference": {
        "keyVault": {
          "id": "<my_keyvault_id>"
        },
        "secretName": "<my_storage_account_key_secret_name>"
      }
    }
  }
}
```

## <a name="deploy-the-container-and-manage-files"></a>Déployer le conteneur et gérer des fichiers

Le modèle étant défini, vous pouvez créer le conteneur et monter son volume à l’aide d’Azure CLI. En supposant que le fichier de modèle est nommé *azuredeploy.json* et que le fichier de paramètres est nommé *azuredeploy.parameters.json*, la ligne de commande est la suivante :

```azurecli-interactive
az group deployment create --name hellofilesdeployment --template-file azuredeploy.json --parameters @azuredeploy.parameters.json --resource-group $ACI_PERS_RESOURCE_GROUP
```

Après le démarrage du conteneur, vous pouvez utiliser l’application web simple déployée via l’image **aci/seanmckenna-hellofiles** pour gérer les fichiers du partage de fichiers Azure sur le chemin de montage que vous avez spécifié. Obtenez l’adresse IP de l’application web avec la commande [az container show](/cli/azure/container#az_container_show) :

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles -o table
```

Vous pouvez utiliser un outil comme l’[Explorateur de stockage Microsoft Azure](https://storageexplorer.com) pour récupérer et inspecter le fichier écrit sur le partage de fichiers.

>[!NOTE]
> Pour en savoir plus sur l’utilisation de modèles Azure Resource Manager, les fichiers de paramètres et le déploiement avec Azure CLI, voir [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).

## <a name="next-steps"></a>Étapes suivantes

- Déployer votre premier conteneur en utilisant le [démarrage rapide](container-instances-quickstart.md) d’Azure Container Instances
- Découvrir la [relation entre Azure Container Instances et les orchestrateurs de conteneurs](container-instances-orchestrator-relationship.md)
