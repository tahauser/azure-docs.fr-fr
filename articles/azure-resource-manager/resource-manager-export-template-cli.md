---
title: "Exporter un modèle Azure Resource Manager avec Azure CLI | Microsoft Docs"
description: "Utilisez Azure Resource Manager et l’interface de ligne de commande Azure pour exporter un modèle à partir d’un groupe de ressources."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: 15e7e811c7cb1777e34f1bfb629fa24a60f9e5cb
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="export-azure-resource-manager-templates-with-azure-cli"></a>Exporter des modèles Azure Resource Manager avec Azure CLI

Resource Manager vous permet d’exporter un modèle Resource Manager à partir de ressources existantes de votre abonnement. Vous pouvez utiliser le modèle généré pour découvrir la syntaxe du modèle, ou pour automatiser le redéploiement de votre solution en fonction des besoins.

Il est important de noter qu’il existe deux façons différentes d’exporter un modèle :

* Vous pouvez exporter le **modèle réel que vous avez utilisé pour un déploiement**. Le modèle exporté inclut l’ensemble des paramètres et des variables exactement comme ils apparaissent dans le modèle d’origine. Cette approche est utile lorsque vous avez besoin de récupérer un modèle.
* Vous pouvez exporter un **modèle généré qui représente l’état actuel du groupe de ressources**. Le modèle exporté n’est pas basé sur un modèle utilisé pour le déploiement. Au lieu de cela, il crée un modèle qui est un « instantané » ou une « sauvegarde » du groupe de ressources. Le modèle exporté a probablement de nombreuses valeurs codées en dur et pas autant de paramètres que vous pourriez généralement définir. Cette option permet de redéployer les ressources sur le même groupe de ressources. Pour utiliser ce modèle pour un autre groupe de ressources, vous devrez peut-être le modifier de façon significative.

Cette rubrique illustre les deux approches.

## <a name="deploy-a-solution"></a>Déployer une solution

Afin d’illustrer ces deux approches pour l’exportation d’un modèle, commençons par le déploiement d’une solution dans votre abonnement. Si vous disposez déjà d’un groupe de ressources dans votre abonnement que vous voulez exporter, il est inutile de déployer cette solution. Toutefois, le reste de cet article fait référence au modèle pour cette solution. L’exemple de script déploie un compte de stockage.

```azurecli
az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name NewStorage \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
```  

## <a name="save-template-from-deployment-history"></a>Enregistrer un modèle à partir de l’historique de déploiement

Vous pouvez récupérer un modèle à partir de votre historique de déploiement à l’aide de la commande [az group deployment export](/cli/azure/group/deployment#az_group_deployment_export). L’exemple suivant enregistre le modèle que vous déployez précédemment :

```azurecli
az group deployment export --name NewStorage --resource-group ExampleGroup
```

Il retourne le modèle. Copiez le JSON et enregistrez-le dans un fichier. Notez qu’il s’agit exactement du même modèle que celui que vous avez utilisé pour le déploiement. Les paramètres et les variables correspondent au modèle provenant de GitHub. Vous pouvez redéployer ce modèle.


## <a name="export-resource-group-as-template"></a>Exporter un groupe de ressources en tant que modèle

Au lieu de récupérer un modèle à partir de l’historique de déploiement, vous pouvez récupérer un modèle qui représente l’état actuel d’un groupe de ressources à l’aide de la commande [az group export](/cli/azure/group#az_group_export). Vous utilisez cette commande lorsque vous avez effectué de nombreuses modifications dans votre groupe de ressources et qu’aucun modèle existant ne représente toutes les modifications. Il s’agit d’un instantané du groupe de ressources, qui vous permet de refaire un déploiement sur le même groupe de ressources. Pour utiliser le modèle exporté pour d’autres solutions, vous devez le modifier considérablement.

```azurecli
az group export --name ExampleGroup
```

Il retourne le modèle. Copiez le JSON et enregistrez-le dans un fichier. Notez qu’il est différent du modèle dans GitHub. Le modèle a des paramètres différents et aucune variable. La référence (SKU) de stockage et l’emplacement sont codés en dur dans des valeurs. L’exemple suivant montre le modèle exporté, mais votre modèle a un nom de paramètre légèrement différent :

```json
{
  "parameters": {
    "storageAccounts_mcyzaljiv7qncstandardsa_name": {
      "type": "String",
      "defaultValue": null
    }
  },
  "variables": {},
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "resources": [
    {
      "tags": {},
      "dependsOn": [],
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "type": "Microsoft.Storage/storageAccounts",
      "location": "centralus",
      "name": "[parameters('storageAccounts_mcyzaljiv7qncstandardsa_name')]",
      "properties": {}
    }
  ],
  "contentVersion": "1.0.0.0"
}
```

Vous pouvez redéployer ce modèle, mais cela requiert de deviner un nom unique pour le compte de stockage. Le nom de votre paramètre est légèrement différent.

```azurecli
az group deployment create --name NewStorage --resource-group ExampleGroup \
  --template-file examplegroup.json \
  --parameters "{\"storageAccounts_mcyzaljiv7qncstandardsa_name\":{\"value\":\"tfstore0501\"}}"
```

## <a name="customize-exported-template"></a>Personnaliser un modèle exporté

Vous pouvez modifier ce modèle pour le rendre plus facile à utiliser et plus flexible. Pour autoriser plusieurs emplacements, modifiez la propriété d’emplacement de façon à utiliser le même emplacement que le groupe de ressources :

```json
"location": "[resourceGroup().location]",
```

Pour éviter d’avoir à deviner un nom unique pour le compte de stockage, supprimez le paramètre pour le nom du compte de stockage. Ajoutez un paramètre pour un suffixe de nom de stockage et une référence (SKU) de stockage :

```json
"parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
},
```

Ajoutez une variable qui construit le nom du compte de stockage avec la fonction uniqueString :

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

Définissez le nom du compte de stockage sur la variable :

```json
"name": "[variables('storageAccountName')]",
```

Définissez la référence SKU sur le paramètre :

```json
"sku": {
    "name": "[parameters('storageAccountType')]",
    "tier": "Standard"
},
```

Votre modèle doit maintenant ressembler à ceci :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), parameters('storageSuffix'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "[parameters('storageAccountType')]",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Redéployez le modèle modifié.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur l’utilisation du portail pour exporter un modèle, voir [Exporter un modèle Azure Resource Manager à partir de ressources existantes](resource-manager-export-template.md).
* Pour définir des paramètres dans le modèle, consultez [Création de modèles](resource-group-authoring-templates.md#parameters).
* Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).