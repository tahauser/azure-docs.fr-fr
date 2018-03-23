---
title: Déploiement de plusieurs instances de ressources Azure | Microsoft Docs
description: Utilisez l’opération de copie et les tableaux dans un modèle Azure Resource Manager pour effectuer une itération à plusieurs reprises lors du déploiement de ressources.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 8dfb664c7041d70f3ece812edb76df38a35e41f1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Déployer plusieurs instances d’une ressource ou d’une propriété dans des modèles Azure Resource Manager
Cet article montre comment déployer de manière conditionnelle une ressource et comment procéder à une itération dans votre modèle Azure Resource Manager pour créer plusieurs instances d’une ressource.

## <a name="conditionally-deploy-resource"></a>Déployer une ressource de manière conditionnelle

Quand vous devez décider au cours du déploiement s’il faut créer ou non une instance d’une ressource, utilisez l’élément `condition`. La valeur de cet élément est résolue en true ou false. Lorsque la valeur est true, la ressource est déployée. Lorsque la valeur est false, la ressource n’est pas déployée. Par exemple, pour spécifier si un nouveau compte de stockage est déployé ou si un compte de stockage existant est utilisé, utilisez :

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

## <a name="resource-iteration"></a>Itération de ressource
Quand vous devez décider au cours du déploiement s’il faut créer une ou plusieurs instances d’une ressource, ajoutez un élément `copy` au type de ressource. Dans l’élément copy, vous indiquez le nombre d’itérations et un nom pour cette boucle. La valeur de décompte doit être un entier positif et ne pas dépasser 800. 

La ressource à créer plusieurs fois prend le format suivant :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Notez que le nom de chaque ressource inclut la fonction `copyIndex()`, qui renvoie l’itération actuelle de la boucle. `copyIndex()` est basé sur zéro. Si bien que l’exemple suivant :

```json
"name": "[concat('storage', copyIndex())]",
```

Crée les noms suivants :

* storage0
* storage1
* storage2.

Pour décaler la valeur d’index, vous pouvez transmettre une valeur dans la fonction copyIndex(). Le nombre d’itérations à effectuer est toujours spécifié dans l’élément copy, mais la valeur de copyIndex est décalée en fonction de la valeur spécifiée. Si bien que l’exemple suivant :

```json
"name": "[concat('storage', copyIndex(1))]",
```

Crée les noms suivants :

* storage1
* storage2
* storage3

L’opération copy se révèle utile lorsque vous travaillez avec des tableaux, car vous pouvez itérer sur chaque élément du tableau. Utilisez la fonction `length` sur le tableau pour spécifier le nombre d’itérations, et `copyIndex` pour récupérer l’index actuel dans le tableau. Si bien que l’exemple suivant :

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

Crée les noms suivants :

* storagecontoso
* storagefabrikam
* storagecoho

Par défaut, Resource Manager crée les ressources en parallèle. Par conséquent, l’ordre de création n’est pas garanti. Toutefois, vous souhaiterez peut-être spécifier que les ressources soient déployées en séquence. Par exemple, lors de la mise à jour d’un environnement de production, vous souhaiterez échelonner les mises à jour afin que seulement un certain nombre soient mises à jour à un moment donné.

Pour déployer en série plusieurs instances d’une ressource, affectez à `mode` la valeur **serial** et à `batchSize` le nombre d’instances à déployer à la fois. Avec le mode série, Resource Manager crée une dépendance sur les instances précédentes de la boucle, afin de ne pas démarrer un lot tant que le précédent n’est pas terminé.

Par exemple, pour déployer en série des comptes de stockage deux à la fois, utilisez :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            }
        }
    ],
    "outputs": {}
}
``` 

La propriété mode accepte également **parallel**, qui est la valeur par défaut.

## <a name="property-iteration"></a>Itération de propriété

Pour créer des valeurs multiples pour une propriété sur une ressource, ajoutez un tableau `copy` dans l’élément Propriétés. Ce tableau contient des objets possédant tous les propriétés suivantes :

* name : nom de la propriété pour laquelle créer plusieurs valeurs
* count : nombre de valeurs à créer
* input : objet contenant les valeurs à assigner à la propriété  

L’exemple suivant montre comment appliquer `copy` à la propriété dataDisks sur une machine virtuelle :

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
          "name": "dataDisks",
          "count": 3,
          "input": {
              "lun": "[copyIndex('dataDisks')]",
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Notez que, lorsque vous utilisez `copyIndex` à l’intérieur d’une itération de propriété, vous devez fournir le nom de l’itération. Il est inutile de fournir le nom quand l’itération de propriété est utilisé avec une itération de ressource.

Le Gestionnaire des ressources développe le tableau `copy` durant le déploiement. Le nom du tableau devient celui de la propriété. Les valeurs d’entrée deviennent les propriétés de l’objet. Le modèle déployé devient :

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
          {
              "lun": 0,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 1,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 2,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Vous pouvez utiliser des itérations de ressource et de propriété ensemble. Référencez l’itération de propriété par son nom.

```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[concat(parameters('vnetname'), copyIndex())]",
    "apiVersion": "2016-06-01",
    "copy":{
        "count": 2,
        "name": "vnetloop"
    },
    "location": "[resourceGroup().location]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "copy": [
            {
                "name": "subnets",
                "count": 2,
                "input": {
                    "name": "[concat('subnet-', copyIndex('subnets'))]",
                    "properties": {
                        "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
                    }
                }
            }
        ]
    }
}
```

## <a name="variable-iteration"></a>Itération de variable

Pour créer plusieurs instances d’une variable, utilisez l’élément `copy` dans la section des variables. Vous pouvez créer plusieurs instances d’objets avec des valeurs associées, puis attribuer ces valeurs à des instances de la ressource. Vous pouvez utiliser la copie pour créer un objet avec une propriété de tableau ou un tableau. L’exemple suivant présente les deux approches :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        }
      ]
    },
    "copy": [
      {
        "name": "disks-top-level-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('disks-top-level-array')]"
        }
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleArray": {
      "value": "[variables('disks-top-level-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="depend-on-resources-in-a-loop"></a>En fonction des ressources dans une boucle
Vous spécifiez qu’une ressource est déployée après une autre ressource à l’aide de l’élément `dependsOn`. Pour déployer une ressource qui dépend de la collection de ressources dans une boucle, vous pouvez utiliser le nom de la boucle de copie dans l’élément dependsOn. L’exemple suivant montre comment déployer trois comptes de stockage avant de déployer la machine virtuelle. La définition complète de la machine virtuelle n’est pas affichée. Notez que le nom de l’élément de copie a la valeur `storagecopy` et que l’élément dependsOn pour la machine virtuelle est également défini sur `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

## <a name="iteration-for-a-child-resource"></a>Itération d’une ressource enfant
Vous ne pouvez pas utiliser une boucle de copie pour une ressource enfant. Pour créer plusieurs instances d’une ressource que vous définissez généralement comme imbriquée dans une autre ressource, vous devez plutôt créer cette ressource comme une ressource de niveau supérieur. Vous définissez la relation avec la ressource parente par le biais des propriétés type et name.

Par exemple, supposons que vous définissez généralement un jeu de données comme une ressource enfant dans une fabrique de données.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

Pour créer plusieurs instances de jeux de données, vous devez le déplacer en dehors de la fabrique de données. Le jeu de données doit être au même niveau que la fabrique de données, mais il est toujours une ressource enfant de la fabrique de données. Vous conservez la relation entre le jeu de données et la fabrique de données par le biais des propriétés type et name. Étant donné que le type ne peut plus peut être déduit à partir de sa position dans le modèle, vous devez fournir le type qualifié complet au format : `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Pour établir une relation parent/enfant avec une instance de la fabrique de données, fournissez un nom pour le jeu de données incluant le nom de la ressource parente. Utilisez le format : `{parent-resource-name}/{child-resource-name}`.  

L’exemple ci-après illustre l’implémentation :

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="example-templates"></a>Exemples de modèles

Les exemples suivants montrent des scénarios courants pour la création de plusieurs propriétés ou ressources.

|Modèle  |Description  |
|---------|---------|
|[Copie de stockage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Déploie plusieurs comptes de stockage avec un numéro d’index dans le nom. |
|[Copie de stockage en série](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Déploie plusieurs comptes de stockage un à la fois. Le nom inclut le numéro d’index. |
|[Copie de stockage avec tableau](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Déploie plusieurs comptes de stockage. Le nom contient une valeur tirée d’un tableau. |
|[Machine virtuelle avec réseau virtuel, adresse IP publique et stockage existants ou nouveaux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) |Déploie de manière conditionnelle des ressources nouvelles ou existantes avec une machine virtuelle. |
|[Déploiement de machine virtuelle avec un nombre variable de disques de données](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Déploie plusieurs disques de données avec une machine virtuelle. |
|[Copie de variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Illustre les différentes façons d’itérer sur des variables. |
|[Règles de sécurité multiples](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Déploie plusieurs règles de sécurité sur un groupe de sécurité réseau. Crée les règles de sécurité à partir d’un paramètre. |

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les sections d’un modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour savoir comment déployer votre modèle, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

