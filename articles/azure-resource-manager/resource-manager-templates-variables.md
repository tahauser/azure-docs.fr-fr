---
title: "Variables du modèle Azure Resource Manager | Microsoft Docs"
description: "Explique comment définir des variables dans des modèles Azure Resource Manager suivant une syntaxe JSON déclarative."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: 8d9f227ad1f450cf6cdfca1dafb1b51bc6f6c9f9
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Section Variables des modèles Azure Resource Manager
Dans la section des variables, vous définissez des valeurs pouvant être utilisées dans votre modèle. Il est inutile de définir des variables, mais elles simplifient souvent votre modèle en réduisant les expressions complexes.

## <a name="define-and-use-a-variable"></a>Définir et utiliser une variable

L’exemple suivant montre une définition de variable. Il crée une valeur de chaîne pour le nom d’un compte de stockage. Il utilise plusieurs fonctions de modèle pour obtenir une valeur de paramètre, et la concatène en une chaîne unique.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

La variable est utilisée lors de la définition de la ressource.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>Définitions disponibles

L’exemple précédent a montré un moyen de définir une variable. Vous pouvez utiliser l’une des définitions suivantes :

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": {
                <properties-to-repeat>
            }
        }
    ]
}
```

## <a name="configuration-variables"></a>Variables de configuration

Vous pouvez utiliser les types JSON complexes pour définir les valeurs associées d’un environnement. 

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

Dans Paramètres, créez une valeur qui indique les valeurs de configuration à utiliser.

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

Récupérez les paramètres actuels ainsi :

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>Utiliser l’élément copy dans la définition de la variable

Vous pouvez utiliser la syntaxe **copy** pour créer une variable avec un tableau de plusieurs éléments. Vous fournissez le nombre d’éléments (count). Chaque élément contient les propriétés dans l’objet **input**. Vous pouvez utiliser copy dans une variable ou pour créer la variable. Si vous définissez une variable et que vous utilisez **copy** dans cette variable, vous créez un objet possédant une propriété tableau. Si vous utilisez **copy** au niveau supérieur et que vous définissez une ou plusieurs variables dedans, vous créez un ou plusieurs tableaux. L’exemple suivant présente les deux approches :

```json
"variables": {
    "disk-array-on-object": {
        "copy": [
            {
                "name": "disks",
                "count": 3,
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
            "count": 3,
            "input": {
                "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        }
    ]
},
```

La variable **disk-array-on-object** contient l’objet suivant avec un tableau nommé **disks** :

```json
{
  "disks": [
    {
      "name": "myDataDisk1",
      "diskSizeGB": "1",
      "diskIndex": 0
    },
    {
      "name": "myDataDisk2",
      "diskSizeGB": "1",
      "diskIndex": 1
    },
    {
      "name": "myDataDisk3",
      "diskSizeGB": "1",
      "diskIndex": 2
    }
  ]
}
```

La variable **disks-top-level-array** contient le tableau suivant :

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  }
]
```

Vous pouvez également spécifier plusieurs objets lorsque vous utilisez la fonction de copie pour créer des variables. L'exemple suivant est une définition de deux tableaux en tant que variables. Le premier est nommé **disks-top-level-array** et comporte cinq éléments. Le second s’appelle **a-different-array** et possède trois éléments.

```json
"variables": {
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 5,
            "input": {
                "name": "[concat('oneDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        },
        {
            "name": "a-different-array",
            "count": 3,
            "input": {
                "name": "[concat('twoDataDisk', copyIndex('a-different-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('a-different-array')]"
            }
        }
    ]
},
```

Cette approche fonctionne bien pour vérifier que les valeurs des paramètres sont au bon format pour une valeur de modèle. L’exemple suivant met en forme des valeurs de paramètres pour les utiliser dans la définition des règles de sécurité :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "securityRules": {
      "type": "array"
    }
  },
  "variables": {
    "copy": [
      {
        "name": "securityRules",
        "count": "[length(parameters('securityRules'))]",
        "input": {
          "name": "[parameters('securityRules')[copyIndex('securityRules')].name]",
          "properties": {
            "description": "[parameters('securityRules')[copyIndex('securityRules')].description]",
            "priority": "[parameters('securityRules')[copyIndex('securityRules')].priority]",
            "protocol": "[parameters('securityRules')[copyIndex('securityRules')].protocol]",
            "sourcePortRange": "[parameters('securityRules')[copyIndex('securityRules')].sourcePortRange]",
            "destinationPortRange": "[parameters('securityRules')[copyIndex('securityRules')].destinationPortRange]",
            "sourceAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].destinationAddressPrefix]",
            "access": "[parameters('securityRules')[copyIndex('securityRules')].access]",
            "direction": "[parameters('securityRules')[copyIndex('securityRules')].direction]"
          }
        }
      }
    ]
  },
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[variables('securityRules')]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="recommendations"></a>Recommandations
Les informations suivantes peuvent être utiles lorsque vous travaillez avec des variables :

* Utilisez des variables pour les valeurs que vous devez utiliser plusieurs fois dans un modèle. Si une valeur est utilisée une seule fois, une valeur codée en dur rend votre modèle plus facile à lire.
* Vous ne pouvez pas utiliser la fonction [référence](resource-group-template-functions-resource.md#reference) dans la section **variables** du modèle. La fonction **référence** dérive sa valeur de l’état d’exécution de la ressource. Toutefois, les variables sont résolues lors de l’analyse initiale du modèle. Construisez des valeurs qui ont besoin de la fonction **référence** directement dans la section **ressources** ou **outputs** du modèle.
* Ajoutez des variables pour les noms de ressource qui doivent être uniques.

## <a name="example-templates"></a>Exemples de modèles

Ces exemples de modèles montrent quelques scénarios d’utilisation de variables. Déployez-les pour tester la façon dont les variables sont gérées dans différents cas de figure. 

|Modèle  |Description  |
|---------|---------|
| [définitions de variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Montre les différents types de variables. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de variables. |
| [variable de configuration](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Illustre l’utilisation d’une variable qui définit des valeurs de configuration. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de variables. |
| [règles de sécurité réseau](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) et [fichier de paramètres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Crée un tableau au bon format pour attribuer des règles de sécurité à un groupe de sécurité réseau. |


## <a name="next-steps"></a>Étapes suivantes
* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
* Pour plus d’informations sur les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour combiner plusieurs modèles lors du déploiement, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Vous devrez peut-être utiliser des ressources qui existent au sein d'un groupe de ressources différent. Ce scénario est classique quand vous utilisez des comptes de stockage ou des réseaux virtuels qui sont partagés entre plusieurs groupes de ressources. Pour plus d'informations, consultez la [fonction resourceId](resource-group-template-functions-resource.md#resourceid).