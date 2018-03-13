---
title: "Créer un compte Azure Machine Learning - Expérimentation avec un modèle Azure Resource Manager | Microsoft Docs"
description: "Cet article fournit un exemple pour créer un compte Azure Machine Learning - Expérimentation à l’aide d’un modèle Azure Resource Manager."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 585666a521ba8e1fae274687cbd709baba871590
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Configurer le service Azure Machine Learning - Expérimentation

## <a name="overview"></a>Vue d'ensemble
Un espace de travail, un projet et un compte de service Azure Machine Learning - Expérimentation sont des ressources Azure. Vous pouvez donc les déployer à l’aide de modèles Resource Manager. Les modèles Azure Resource Manager sont des fichiers JSON qui définissent les ressources nécessaires au déploiement de votre solution. Pour comprendre les concepts associés au déploiement et à la gestion de vos solutions Azure, voir [Présentation d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Déploiement d’un modèle
Le déploiement d’un modèle ne nécessite que quelques étapes dans l’interface de ligne de commande Azure ou dans le portail Azure.

### <a name="deploy-a-template-from-the-command-line"></a>Déployer un modèle depuis la ligne de commande
À l’aide de l’interface de ligne de commande, une seule commande suffit pour déployer un modèle sur un groupe de ressources existant.
Voici comment créer un modèle.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Déployer un modèle depuis le portail Azure
Si vous préférez, vous pouvez également utiliser le portail Azure pour créer et déployer un modèle. Effectuez les étapes suivantes :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services** et recherchez « modèles ».
3. Sélectionnez **Modèles**.
4. Cliquez sur **+ Ajouter** et copiez les informations de modèle. 
5. Sélectionnez le modèle créé à l’étape 4, puis cliquez sur **Déployer**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Créer un modèle à partir d’une ressource Azure existante dans le portail Azure
Si vous disposez déjà d’un compte Azure Machine Learning - Expérimentation, dans le [portail Azure](https://portal.azure.com), vous pouvez générer un modèle à partir de cette ressource. 

1. Accédez à un compte Azure Machine Learning - Expérimentation dans le [portail Azure](https://portal.azure.com).
2. Sous **Paramètres**, cliquez sur **Script d’automatisation**.
3. Téléchargez le modèle. 

Vous pouvez également modifier manuellement les fichiers de modèle. Voici des exemples de fichier de modèle et de fichier de paramètres. 

### <a name="template-file-example"></a>Exemple de fichier de modèle
Créez un fichier appelé « template-file.json » avec le contenu ci-dessous. 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>parameters 
Créez un fichier avec le contenu ci-dessous et enregistrez-le sous le nom <parameters.json>. 

Vous pouvez changer trois valeurs. 
* accountName : nom du compte Expérimentation.
* location : une des régions Azure prises en charge.
* storageAccountSku : Azure ML prend uniquement en charge le stockage standard, pas le stockage premium. Pour plus d’informations sur le stockage, consultez [Introduction au stockage](https://docs.microsoft.com/azure/storage/common/storage-introduction). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes
* [Créer et installer Azure Machine Learning](quickstart-installation.md)
