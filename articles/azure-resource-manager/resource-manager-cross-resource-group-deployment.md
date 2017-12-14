---
title: "Déployer des ressources Azure sur plusieurs groupes de ressources et des abonnements | Microsoft Docs"
description: "Montre comment cibler plusieurs groupes de ressources et des abonnements Azure pendant le déploiement."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2017
ms.author: tomfitz
ms.openlocfilehash: 763f46b9b5be7edf06ee0604bfc51a2482405b60
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Déployer des ressources Azure sur plusieurs groupes de ressources et des abonnements

En général, vous déployez toutes les ressources dans votre modèle sur un seul groupe de ressources. Toutefois, il existe des scénarios dans lesquels vous pouvez souhaiter déployer simultanément un ensemble de ressources à placer dans des groupes de ressources ou des abonnements différents. Par exemple, vous voudrez peut-être déployer la machine virtuelle de sauvegarde destinée à Azure Site Recovery sur un groupe de ressources et un emplacement distincts. Resource Manager vous permet d’utiliser des modèles imbriqués pour cibler des groupes de ressources ou des abonnements différents du groupe de ressources ou de l’abonnement utilisés pour le modèle parent.

Le groupe de ressources est le conteneur de cycle de vie de l’application et sa collection de ressources. Vous créez le groupe de ressources en dehors du modèle et spécifiez le groupe de ressources à cibler lors du déploiement. Pour voir une présentation des groupes de ressources, consultez la page [Présentation d’Azure Resource Manager](resource-group-overview.md).

## <a name="specify-a-subscription-and-resource-group"></a>Spécifier un groupe de ressources et un abonnement

Pour cibler une autre ressource, vous devez utiliser un modèle imbriqué ou lié au cours du déploiement. Le type de ressource `Microsoft.Resources/deployments` fournit des paramètres pour `subscriptionId` et `resourceGroup`. Ces propriétés permettent de spécifier un autre groupe de ressources et un autre abonnement pour le déploiement imbriqué. Tous les groupes de ressources doivent exister avant l’exécution du déploiement. Si vous ne spécifiez ni l’ID d’abonnement ni le groupe de ressources, ce sont l’abonnement et le groupe de ressources depuis le modèle parent qui sont utilisés.

L’exemple suivant déploie deux comptes de stockage, un dans le groupe de ressources spécifié pendant le déploiement, et l’autre dans un groupe de ressources précisé dans le paramètre `secondResourceGroup` :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Si vous définissez `resourceGroup`sur le nom d’un groupe de ressources qui n’existe pas, le déploiement échoue.

## <a name="deploy-the-template"></a>Déployer le modèle

Pour déployer un exemple de modèle, utilisez une version d’Azure PowerShell ou d’Azure CLI datant de mai 2017 ou d’une date ultérieure. Pour ces exemples, utilisez le [modèle à abonnements multiples](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) dans GitHub.

### <a name="two-resource-groups-in-the-same-subscription"></a>Deux groupes de ressources dans le même abonnement

Pour PowerShell, déployez deux comptes de stockage sur deux groupes de ressources dans le même abonnement en utilisant :

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Pour Azure CLI, déployez deux comptes de stockage sur deux groupes de ressources dans le même abonnement en utilisant :

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Une fois le déploiement terminé, deux groupes de ressources s’affichent. Chaque groupe de ressources contient un compte de stockage.

### <a name="two-resource-groups-in-different-subscriptions"></a>Deux groupes de ressources dans différents abonnements

Pour PowerShell, déployez deux comptes de stockage sur deux abonnements en utilisant :

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Pour Azure CLI, déployez deux comptes de stockage sur deux abonnements en utilisant :

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

## <a name="use-the-resourcegroup-function"></a>Utiliser la fonction resourceGroup()

Pour des déploiements entre groupes de ressources, la [fonction resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) produit un résultat différent selon la façon dont vous spécifiez le modèle imbriqué. 

Si vous incorporez un modèle dans un autre, la résolution de resourceGroup() dans le modèle imbriqué est le groupe de ressources parent. Un modèle incorporé utilise le format suivant :

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

Si vous liez à un modèle séparé, la résolution de resourceGroup() dans le modèle lié est le groupe de ressources imbriqué. Un modèle lié utilise le format suivant :

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

Pour tester les différentes façons de résoudre `resourceGroup()`, déployez un [exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) qui retourne l’objet de groupe de ressources pour le modèle parent, le modèle inline et le modèle lié. La résolution dans les modèles parent et inline est le même groupe de ressources. La résolution dans le modèle lié est le groupe de ressources lié.

Pour PowerShell, utilisez la commande suivante :

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

## <a name="next-steps"></a>Étapes suivantes

* Pour comprendre comment définir des paramètres dans votre modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour obtenir des conseils sur la résolution des erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes de déploiement Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Pour plus d’informations sur le déploiement d’un modèle qui nécessite un jeton SAP, consultez [Déploiement d’un modèle privé avec un jeton SAP](resource-manager-powershell-sas-token.md).
