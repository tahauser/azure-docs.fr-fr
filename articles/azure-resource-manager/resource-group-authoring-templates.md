---
title: "Structure et syntaxe du modèle Azure Resource Manager | Microsoft Docs"
description: "Décrit la structure et les propriétés des modèles Azure Resource Manager à l’aide de la syntaxe JSON déclarative."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: tomfitz
ms.openlocfilehash: b8d1988a8705e0708e412c24fb5b49f5ece31429
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Comprendre la structure et la syntaxe des modèles Azure Resource Manager
Cet article décrit la structure d’un modèle Azure Resource Manager. Elle présente les différentes sections d’un modèle et les propriétés disponibles dans ces sections. Le modèle se compose d’un JSON et d’expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement. Pour obtenir un didacticiel étape par étape permettant de créer un modèle, voir [Créer votre premier modèle Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Format de modèle
Dans sa structure la plus simple, un modèle contient les éléments suivants :

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| Nom de l'élément | Requis | Description |
|:--- |:--- |:--- |
| $schema |Oui |Emplacement du fichier de schéma JSON qui décrit la version du langage du modèle. Utilisez l’URL indiquée dans l’exemple précédent. |
| contentVersion |Oui |Version du modèle (par exemple, 1.0.0.0). Vous pouvez fournir n’importe quelle valeur pour cet élément. Quand vous déployez des ressources à l'aide du modèle, cette valeur permet de vous assurer que le bon modèle est utilisé. |
| parameters |Non |Valeurs fournies lors de l'exécution du déploiement pour personnaliser le déploiement des ressources. |
| variables |Non |Valeurs utilisées en tant que fragments JSON dans le modèle pour simplifier les expressions du langage du modèle. |
| les ressources |Oui |Types de ressource déployés ou mis à jour dans un groupe de ressources. |
| outputs |Non |Valeurs retournées après le déploiement. |

Chaque élément contient des propriétés que vous définissez. L’exemple suivant présente la syntaxe complète d’un modèle :

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  
        "<parameter-name>" : {
            "type" : "<type-of-parameter-value>",
            "defaultValue": "<default-value-of-parameter>",
            "allowedValues": [ "<array-of-allowed-values>" ],
            "minValue": <minimum-value-for-int>,
            "maxValue": <maximum-value-for-int>,
            "minLength": <minimum-length-for-string-or-array>,
            "maxLength": <maximum-length-for-string-or-array-parameters>,
            "metadata": {
                "description": "<description-of-the parameter>" 
            }
        }
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
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
    },
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

Cet article décrit les sections du modèle de manière plus approfondie.

## <a name="expressions-and-functions"></a>Expressions et fonctions
La syntaxe de base du modèle est JSON. Toutefois, les expressions et fonctions étendent les valeurs JSON disponibles dans le modèle.  Les expressions sont écrites dans les littéraux de chaîne JSON dont le premier et dernier caractères sont les crochets : `[` et `]`, respectivement. La valeur de l’expression est évaluée lorsque le modèle est déployé. Bien qu’écrit sous la forme d’un littéral de chaîne, le résultat de l’évaluation de l’expression peut être d’un type différent de JSON, tel qu’un tableau ou un entier, en fonction de l’expression réelle.  Pour avoir une chaîne littérale qui commence par un crochet `[`, sans qu’elle soit interprétée comme une expression, ajoutez un crochet supplémentaire. La chaîne commence alors par `[[`.

En général, vous utilisez des expressions avec des fonctions pour effectuer des opérations de configuration du déploiement. Comme en JavaScript, les appels de fonction sont formatés comme suit : `functionName(arg1,arg2,arg3)`. Pour référencer des propriétés, vous utilisez les opérateurs point et [index].

L'exemple suivant vous indique comment utiliser plusieurs fonctions lors de la construction de valeurs :

```json
"variables": {
    "location": "[resourceGroup().location]",
    "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
    "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

Pour obtenir la liste complète des fonctions de modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md). 

## <a name="parameters"></a>parameters
C’est dans la section des paramètres du modèle que vous pouvez spécifier les valeurs que vous pouvez saisir lors du déploiement des ressources. Ces valeurs de paramètre vous permettent de personnaliser le déploiement grâce à des valeurs adaptées à un environnement particulier (par exemple développement, test et production). Il est inutile de fournir des paramètres dans votre modèle, mais sans les paramètres, votre modèle déploie toujours les mêmes ressources avec les mêmes noms, emplacements et propriétés.

Vous définissez des paramètres avec la structure suivante :

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| Nom de l'élément | Requis | Description |
|:--- |:--- |:--- |
| nom_paramètre |Oui |Nom du paramètre. Doit être un identificateur JavaScript valide. |
| type |Oui |Type de la valeur du paramètre. La liste des types autorisés est présentée après ce tableau. |
| defaultValue |Non |Valeur par défaut du paramètre, si aucune valeur n'est fournie pour le paramètre. |
| allowedValues |Non |Tableau des valeurs autorisées pour le paramètre afin de vous assurer que la bonne valeur a bien été fournie. |
| minValue |Non |Valeur minimale pour les paramètres de type int, cette valeur est inclusive. |
| maxValue |Non |Valeur maximale pour les paramètres de type int. Cette valeur est inclusive. |
| minLength |Non |Valeur minimale pour les paramètres de type string, secureString et array. Cette valeur est inclusive. |
| maxLength |Non |Valeur maximale pour les paramètres de type string, secureString et array. Cette valeur est inclusive. |
| Description |Non |Description du paramètre qui apparaît aux utilisateurs dans le portail. |

Les valeurs et types autorisés sont :

* **string**
* **secureString**
* **int**
* **bool**
* **object** 
* **secureObject**
* **array**

Pour spécifier un paramètre comme facultatif, fournissez une valeur defaultValue (peut être une chaîne vide). 

Si vous spécifiez dans votre modèle un nom de paramètre qui correspond à un paramètre de la commande servant à déployer le modèle, il existe une ambiguïté potentielle concernant les valeurs que vous fournissez. Resource Manager élimine ce risque de confusion en ajoutant le suffixe **romTemplate** au paramètre du modèle. Par exemple, si vous incluez dans votre modèle un paramètre nommé **ResourceGroupName**, celui-ci est en conflit avec le paramètre **ResourceGroupName** dans l’applet de commande [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). Pendant le déploiement, vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**. En général, vous devez éviter cette confusion en ne nommant pas les paramètres avec un nom identique à celui des paramètres utilisés pour les opérations de déploiement.

> [!NOTE]
> Tous les mots de passe, clés et autres secrets doivent utiliser le type **secureString** . Si vous transmettez des données sensibles dans un objet JSON, utilisez le type **secureObject**. Il est impossible de lire les paramètres du modèle dont le type est secureString ou secureObject après le déploiement de la ressource. 
> 
> Par exemple, l’entrée suivante dans l’historique de déploiement indique la valeur pour une chaîne et un objet, mais pas pour secureString et secureObject.
>
> ![afficher les valeurs de déploiement](./media/resource-group-authoring-templates/show-parameters.png)  
>

L’exemple suivant vous indique comment définir les paramètres :

```json
"parameters": {
    "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
    },
    "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
    },
    "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
    },
    "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
    }
}
```

Pour plus d’informations sur la saisie des valeurs de paramètre au cours du déploiement, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md). 

## <a name="variables"></a>variables
Dans la section des variables, vous définissez des valeurs pouvant être utilisées dans votre modèle. Il est inutile de définir des variables, mais elles simplifient souvent votre modèle en réduisant les expressions complexes.

Vous définissez des variables avec la structure suivante :

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    }
}
```

L'exemple suivant vous indique comment définir une variable qui est construite à partir de deux valeurs de paramètre :

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

L'exemple suivant vous indique une variable qui est un type complexe de JSON et des variables qui sont construites à partir d'autres variables :

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
"variables": {
    "environmentSettings": {
        "test": {
            "instancesSize": "Small",
            "instancesCount": 1
        },
        "prod": {
            "instancesSize": "Large",
            "instancesCount": 4
        }
    },
    "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
    "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
    "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
}
```

Vous pouvez utiliser la syntaxe **copy** pour créer une variable avec un tableau de plusieurs éléments. Vous fournissez le nombre d’éléments (count). Chaque élément contient les propriétés dans l’objet **input**. Vous pouvez utiliser copy dans une variable ou pour créer la variable. L’exemple suivant présente les deux approches :

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

## <a name="resources"></a>Ressources
Dans la section des ressources, vous définissez les ressources déployées ou mises à jour. Cette section gagne en complexité, car vous devez connaître les types que vous déployez pour fournir les valeurs adéquates. Concernant les valeurs spécifiques aux ressources (apiVersion, type et properties) que vous devez définir, voir [Définir les ressources des modèles Azure Resource Manager](/azure/templates/). 

Vous définissez des ressources avec la structure suivante :

```json
"resources": [
  {
      "condition": "<boolean-value-whether-to-deploy>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>",
          "mode": "<serial-or-parallel>",
          "batchSize": "<number-to-deploy-serially>"
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nom de l'élément | Requis | Description |
|:--- |:--- |:--- |
| condition | Non | Valeur booléenne indiquant si la ressource est déployée. |
| apiVersion |Oui |La version de l'API REST à utiliser pour la création de la ressource. |
| type |Oui |Type de la ressource. Cette valeur est une combinaison de l’espace de noms du fournisseur de ressources et du type de ressource (comme **Microsoft.Storage/storageAccounts**). |
| name |Oui |Nom de la ressource. Le nom doit respecter les restrictions de composant d'URI définies dans le document RFC3986. En outre, les services Azure qui exposent le nom de la ressource à des parties externes valident du nom pour s’assurer qu’il ne s’agit pas d’une tentative d’usurpation d’identité. |
| location |Varie |Emplacements géographiques de la ressource fournie pris en charge. Vous pouvez sélectionner l’un des emplacements disponibles, mais en général, il est judicieux de choisir celui qui est proche de vos utilisateurs. En règle générale, il est également judicieux de placer dans la même région les ressources qui interagissent entre elles. La plupart des types de ressources nécessitent un emplacement, mais certains types (par exemple, une affectation de rôle) ne nécessitent aucun emplacement. Voir [Définir l’emplacement des ressources dans les modèles Azure Resource Manager](resource-manager-template-location.md). |
| tags |Non |Balises associées à la ressource. Voir [Appliquer des balises aux ressources dans les modèles Azure Resource Manager](resource-manager-template-tags.md). |
| commentaires |Non |Vos commentaires pour documenter les ressources dans votre modèle |
| copy |Non |Si plusieurs instances sont nécessaires, le nombre de ressources à créer. Le mode par défaut est parallèle. Spécifiez le mode série si vous ne souhaitez pas que toutes les ressources soient déployées en même temps. Pour plus d’informations, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Non |Les ressources qui doivent être déployées avant le déploiement de cette ressource. Resource Manager évalue les dépendances entre les ressources et les déploie dans le bon ordre. Quand les ressources ne dépendent les unes des autres, leur déploiement se fait en parallèle. La valeur peut être une liste séparée par des virgules de noms de ressource ou d’identificateurs de ressource uniques. Répertoriez uniquement les ressources qui sont déployées dans ce modèle. Les ressources qui ne sont pas définies dans ce modèle doivent déjà exister. Évitez d’ajouter des dépendances inutiles, car cela risque de ralentir votre déploiement et de créer des dépendances circulaires. Pour savoir comment définir des dépendances, consultez [Définition de dépendances dans les modèles Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Non |Paramètres de configuration spécifiques aux ressources. Les valeurs de propriétés sont identiques à celles que vous fournissez dans le corps de la requête pour l’opération d’API REST (méthode PUT) pour créer la ressource. Vous pouvez également spécifier une copie en groupe pour créer plusieurs instances d’une propriété. Pour plus d’informations, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md). |
| les ressources |Non |Ressources enfants qui dépendent de la ressource qui est définie. Fournissez uniquement des types de ressources qui sont autorisés par le schéma de la ressource parente. Le type complet de la ressource enfant inclut le type de ressource parente, par exemple **Microsoft.Web/sites/extensions**. La dépendance envers la ressource parente n’est pas induite. Vous devez la définir explicitement. |

La section des ressources contient un tableau des ressources à déployer. Au sein de chaque ressource, vous pouvez également définir un tableau de ressources enfant. Par conséquent, la structure de la section de ressources peut ressembler à ce qui suit :

```json
"resources": [
  {
      "name": "resourceA",
  },
  {
      "name": "resourceB",
      "resources": [
        {
            "name": "firstChildResourceB",
        },
        {   
            "name": "secondChildResourceB",
        }
      ]
  },
  {
      "name": "resourceC",
  }
]
```      

Pour plus d’informations sur la définition des ressources enfants, voir [Définir le nom et le type d’une ressource enfant dans un modèle Resource Manager](resource-manager-template-child-resource.md).

L’élément **condition** indique si la ressource est déployée. La valeur de cet élément est résolue en true ou false. Par exemple, pour spécifier si un compte de stockage est déployé, utilisez :

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

Pour obtenir un exemple d’utilisation d’une ressource nouvelle ou existante, voir [Modèle de condition New ou Existing](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResources.NewOrExisting.json).

Pour spécifier si une machine virtuelle est déployée avec un mot de passe ou une clé SSH, définissez deux versions de la machine virtuelle dans votre modèle, puis utilisez l’élément **condition** pour différencier l’utilisation. Transmettre un paramètre qui spécifie le scénario à déployer.

```json
{
    "condition": "[equals(parameters('passwordOrSshKey'),'password')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'password')]",
    "properties": {
        "osProfile": {
            "computerName": "[variables('vmName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
        },
        ...
    },
    ...
},
{
    "condition": "[equals(parameters('passwordOrSshKey'),'sshKey')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'ssh')]",
    "properties": {
        "osProfile": {
            "linuxConfiguration": {
                "disablePasswordAuthentication": "true",
                "ssh": {
                    "publicKeys": [
                        {
                            "path": "[variables('sshKeyPath')]",
                            "keyData": "[parameters('adminSshKey')]"
                        }
                    ]
                }
            }
        },
        ...
    },
    ...
}
``` 

Pour obtenir un exemple d’utilisation d’un mot de passe ou d’une clé SSH pour déployer une machine virtuelle, voir [Modèle de condition Username ou SSH](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResourcesUsernameOrSsh.json).

## <a name="outputs"></a>Sorties
Dans la section des sorties, vous spécifiez des valeurs retournées à partir du déploiement. Par exemple, vous pouvez retourner l'URI d'accès à une ressource déployée.

L'exemple suivant illustre la structure de la définition d'une sortie :

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Nom de l'élément | Requis | Description |
|:--- |:--- |:--- |
| outputName |Oui |Nom de la valeur de sortie. Doit être un identificateur JavaScript valide. |
| type |Oui |Type de la valeur de sortie. Les valeurs de sortie prennent en charge les mêmes types que les paramètres d'entrée du modèle. |
| value |Oui |Expression du langage du modèle évaluée et retournée sous forme de valeur de sortie. |

L'exemple suivant montre une valeur retournée dans la section des sorties.

```json
"outputs": {
    "siteUri" : {
        "type" : "string",
        "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

Pour plus d’informations sur le fonctionnement de la sortie, consultez [Partage d’état dans les modèles Azure Resource Manager](best-practices-resource-manager-state.md).

## <a name="template-limits"></a>Limites de modèle

Limitez la taille de votre modèle à 1 Mo et celle de chaque fichier de paramètres à 64 ko. La limite de 1 Mo s’applique à l’état final du modèle une fois développé avec les définitions des ressources itératives et les valeurs des variables et des paramètres. 

Vous devez également respecter les limites suivantes :

* 256 paramètres
* 256 variables
* 800 ressources (incluant le nombre de copies)
* 64 valeurs de sortie
* 24 576 caractères dans une expression de modèle

Vous pouvez dépasser certaines limites de modèle en utilisant un modèle imbriqué. Pour plus d’informations, consultez l’article [Utilisation de modèles liés lors du déploiement des ressources Azure](resource-group-linked-templates.md). Pour réduire le nombre de paramètres, de variables ou de sorties, vous pouvez combiner plusieurs valeurs dans un même objet. Pour plus d’informations, consultez l’article [Objects as parameters](resource-manager-objects-as-parameters.md) (Utiliser un objet en tant que paramètre).

## <a name="next-steps"></a>Étapes suivantes
* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
* Pour plus d’informations sur les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour combiner plusieurs modèles lors du déploiement, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Vous devrez peut-être utiliser des ressources qui existent au sein d'un groupe de ressources différent. Ce scénario est classique quand vous utilisez des comptes de stockage ou des réseaux virtuels qui sont partagés entre plusieurs groupes de ressources. Pour plus d'informations, consultez la [fonction resourceId](resource-group-template-functions-resource.md#resourceid).
