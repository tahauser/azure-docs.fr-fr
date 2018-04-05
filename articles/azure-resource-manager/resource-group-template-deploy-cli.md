---
title: Déployer des ressources avec Azure CLI et un modèle | Microsoft Docs
description: Utilisez Azure Resource Manager et Azure CLI pour déployer des ressources sur Azure. Les ressources sont définies dans un modèle Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: tomfitz
ms.openlocfilehash: f920165448f4197778faec07c9d49e0bddf1b555
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Déployer des ressources à l’aide de modèles Resource Manager et dAzure CLI

Cet article explique comment utiliser Azure CLI 2.0 avec les modèles Resource Manager pour déployer vos ressources dans Azure. Si vous n’avez pas une bonne connaissance des concepts de déploiement et de gestion de solutions Azure, consultez [Vue d’ensemble d’Azure Resource Manager](resource-group-overview.md).  

Le modèle Resource Manager que vous déployez peut être un fichier local sur votre ordinateur ou un fichier externe qui se trouve dans un dépôt comme GitHub. Le modèle que vous déployez dans le cadre de cet article est disponible dans la section [Exemple de modèle](#sample-template) ou en tant que [modèle de compte de stockage dans GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Si Azure CLI n’est pas installé, vous pouvez utiliser le [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deploy-local-template"></a>Déployer un modèle local

Au moment de déployer des ressources dans Azure, vous effectuez les opérations suivantes :

1. Connexion à votre compte Azure
2. Créez un groupe de ressources qui sert de conteneur pour les ressources déployées. Le nom du groupe de ressources ne peut contenir que des caractères alphanumériques, des points, des traits de soulignement, des traits d'union et des parenthèses. Il peut comprendre jusqu’à 90 caractères. Il ne peut pas se terminer par un point.
3. Déploiement dans le groupe de ressources du modèle qui définit les ressources à créer

Un modèle peut inclure des paramètres qui permettent de personnaliser le déploiement. Par exemple, vous pouvez indiquer des valeurs qui sont adaptées à un environnement particulier (par exemple, de développement, de test ou de production). L’exemple de modèle définit un paramètre pour la référence (SKU) de compte de stockage. 

L’exemple suivant crée un groupe de ressources et déploie un modèle à partir de votre ordinateur local :

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

Le déploiement peut prendre plusieurs minutes. Au terme, vous voyez un message qui inclut le résultat :

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>Déployer un modèle externe

Au lieu de stocker les modèles Resource Manager sur votre ordinateur local, vous pouvez les stocker dans un emplacement externe. Vous pouvez stocker des modèles dans un dépôt de contrôle de code source (par exemple, GitHub). Vous pouvez aussi les stocker dans un compte de stockage Azure pour mettre en place un accès partagé dans votre organisation.

Pour déployer un modèle externe, utilisez le paramètre **template-uri**. Pour déployer l’exemple de modèle à partir de GitHub, utilisez l’URI figurant dans l’exemple.
   
```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
    --parameters storageAccountType=Standard_GRS
```

L’exemple précédent nécessite un URI accessible publiquement pour le modèle, ce qui convient pour la plupart des scénarios, sachant que votre modèle ne doit pas inclure de données sensibles. Si vous avez besoin de spécifier des données sensibles (par exemple, un mot de passe d’administrateur), passez cette valeur en tant que paramètre sécurisé. Toutefois, si vous ne souhaitez pas que votre modèle soit accessible au public, vous pouvez le protéger en le stockant dans un conteneur de stockage privé. Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton de signature d’accès partagé (SAS), consultez [Déployer un modèle privé avec un jeton SAS](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Dans Azure Cloud Shell, utilisez les commandes suivantes :

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Déployer sur plus d’un groupe de ressources ou abonnement

En général, vous déployez toutes les ressources dans votre modèle sur un seul groupe de ressources. Toutefois, il existe des scénarios dans lesquels vous pouvez souhaiter déployer simultanément un ensemble de ressources à placer dans des groupes de ressources ou des abonnements différents. Vous ne pouvez pas déployer sur plus de cinq groupes de ressources dans un déploiement. Pour plus d’informations, voir [Déployer des ressources Azure sur plusieurs groupes de ressources et des abonnements](resource-manager-cross-resource-group-deployment.md).

## <a name="parameter-files"></a>Fichiers de paramètres

Au lieu de passer des paramètres en tant que valeurs inline dans votre script, il peut s’avérer plus facile d’utiliser un fichier JSON qui contient les valeurs des paramètres. Le fichier de paramètres doit être au format suivant :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Notez que la section des paramètres comprend un nom de paramètre qui correspond au paramètre défini dans votre modèle (storageAccountType). Le fichier de paramètres contient une valeur pour le paramètre. Cette valeur est transmise automatiquement au modèle pendant le déploiement. Vous pouvez créer plusieurs fichiers de paramètres pour différents scénarios de déploiement, puis transmettre le fichier de paramètres approprié. 

Copiez l’exemple précédent et enregistrez-le dans un fichier nommé `storage.parameters.json`.

Pour transmettre un fichier de paramètres local, utilisez `@` pour spécifier un fichier local nommé storage.parameters.json.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>Tester le déploiement d’un modèle

Pour tester votre modèle et vos valeurs de paramètres sans réellement déployer toutes les ressources, utilisez [az group deployment validate](/cli/azure/group/deployment#az_group_deployment_validate). 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

Si aucune erreur n’est détectée, la commande retourne des informations sur le déploiement de test. En particulier, notez que **error** a la valeur Null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Si une erreur est détectée, la commande retourne un message d’erreur. Par exemple, la tentative de transmission d’une valeur incorrecte pour la référence (SKU) du compte de stockage retourne l’erreur suivante :

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Si votre modèle comporte une erreur de syntaxe, la commande retourne une erreur indiquant qu’il n’a pas pu analyser le modèle. Le message indique le numéro de ligne et la position de l’erreur d’analyse.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Pour utiliser le mode complet, utilisez le paramètre `mode` :

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

## <a name="sample-template"></a>Exemple de modèle

Le modèle suivant est utilisé pour les exemples de cet article. Copiez et enregistrez-le dans un fichier nommé storage.json. Pour comprendre comment ce modèle est créé, consultez [Créer votre premier modèle Azure Resource Manager](resource-manager-create-first-template.md).  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes
* Les exemples de cet article permettent de déployer des ressources dans un groupe de ressources pour votre abonnement par défaut. Pour utiliser un autre abonnement, consultez [Gérer plusieurs abonnements Azure](/cli/azure/manage-azure-subscriptions-azure-cli).
* Pour accéder à un exemple de script complet qui déploie un modèle, consultez la page [Déploiement d’un modèle Azure Resource Manager](resource-manager-samples-cli-deploy.md).
* Pour comprendre comment définir des paramètres dans votre modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déploiement d’un modèle privé avec un jeton SAP](resource-manager-cli-sas-token.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).
