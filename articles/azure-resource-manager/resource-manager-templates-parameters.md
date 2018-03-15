---
title: "Section des paramètres des modèles Azure Resource Manager | Microsoft Docs"
description: "Décrit la section des paramètres des modèles Azure Resource Manager à l’aide de la syntaxe JSON déclarative."
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
ms.date: 01/19/2018
ms.author: tomfitz
ms.openlocfilehash: 5a519908f43193e41da9237a236d720fe2db58eb
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Section des paramètres des modèles Azure Resource Manager
C’est dans la section des paramètres du modèle que vous pouvez spécifier les valeurs que vous pouvez saisir lors du déploiement des ressources. Ces valeurs de paramètre vous permettent de personnaliser le déploiement grâce à des valeurs adaptées à un environnement particulier (par exemple développement, test et production). Il est inutile de fournir des paramètres dans votre modèle, mais sans les paramètres, votre modèle déploie toujours les mêmes ressources avec les mêmes noms, emplacements et propriétés.

Vous êtes limité à 255 paramètres dans un modèle. Vous pouvez réduire le nombre de paramètres en utilisant des objets contenant plusieurs propriétés, comme indiqué dans cet article.

## <a name="define-and-use-a-parameter"></a>Définir et utiliser un paramètre

L’exemple suivant illustre une définition de paramètre simple. Il définit le nom du paramètre et indique qu’il nécessite une valeur de chaîne. Le paramètre accepte uniquement des valeurs qui ont un sens pour son usage prévu. Il indique une valeur par défaut quand aucune valeur n’est fournie au cours du déploiement. Enfin, le paramètre inclut une description de son utilisation. 

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

Dans le modèle, vous référencez la valeur du paramètre à l’aide de la syntaxe suivante :

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="available-properties"></a>Propriétés disponibles

L’exemple précédent a seulement illustré quelques-unes des propriétés que vous utilisez dans la section des paramètres. Les propriétés disponibles sont les suivantes :

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

| Nom de l'élément | Obligatoire | Description |
|:--- |:--- |:--- |
| nom_paramètre |OUI |Nom du paramètre. Doit être un identificateur JavaScript valide. |
| Type |OUI |Type de la valeur du paramètre. Les types et valeurs autorisés sont : **string**, **secureString**, **int**, **bool**, **object**, **secureObject** et **array**. |
| defaultValue |Non  |Valeur par défaut du paramètre, si aucune valeur n'est fournie pour le paramètre. |
| allowedValues |Non  |Tableau des valeurs autorisées pour le paramètre afin de vous assurer que la bonne valeur a bien été fournie. |
| minValue |Non  |Valeur minimale pour les paramètres de type int, cette valeur est inclusive. |
| maxValue |Non  |Valeur maximale pour les paramètres de type int. Cette valeur est inclusive. |
| minLength |Non  |Valeur minimale pour les paramètres de type string, secureString et array. Cette valeur est inclusive. |
| maxLength |Non  |Valeur maximale pour les paramètres de type string, secureString et array. Cette valeur est inclusive. |
| description |Non  |Description du paramètre qui apparaît aux utilisateurs dans le portail. |

## <a name="template-functions-with-parameters"></a>Fonctions de modèle avec des paramètres

Quand vous fournissez la valeur par défaut d’un paramètre, vous pouvez utiliser la plupart des fonctions de modèle. Vous pouvez utiliser une autre valeur de paramètre pour générer une valeur par défaut. Le modèle suivant illustre l’utilisation de fonctions avec les valeurs par défaut :

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Vous ne pouvez pas utiliser la fonction `reference` dans la section des paramètres. Les paramètres étant évalués avant le déploiement, la fonction `reference` ne peut pas obtenir l’état d’exécution d’une ressource. 

## <a name="objects-as-parameters"></a>Objets en tant que paramètres

Il peut s’avérer plus facile d’organiser des valeurs connexes en les transmettant en tant qu’objets. Cette approche réduit également le nombre de paramètres dans le modèle.

Définissez le paramètre dans votre modèle, puis spécifiez un objet JSON au lieu d’une valeur unique au cours du déploiement. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Référencez ensuite les sous-propriétés du paramètre en utilisant l’opérateur point.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="recommendations"></a>Recommandations
Les informations suivantes peuvent être utiles lorsque vous travaillez avec des paramètres :

* Réduisez l’utilisation des paramètres. Si possible, utilisez une variable ou une valeur littérale. Utilisez des paramètres uniquement pour les scénarios suivants :
   
   * les paramètres que vous souhaitez utiliser varient en fonction de l’environnement (référence SKU, taille, capacité) ;
   * les noms de ressources que vous souhaitez spécifier pour faciliter l’identification ;
   * les valeurs que vous utilisez souvent pour effectuer d’autres tâches (par exemple, le nom d’utilisateur administrateur) ;
   * les clés secrètes (notamment les mots de passe) ;
   * le nombre ou le tableau de valeurs à utiliser lors de la création de plusieurs instances d’un type de ressource.
* Utilisez la casse mixte pour les noms de paramètre.
* Fournissez une description dans les métadonnées pour chaque paramètre :

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Définissez des valeurs par défaut pour les paramètres (à l’exception des mots de passe et des clés SSH). En fournissant une valeur par défaut, le paramètre devient facultatif durant le déploiement. La valeur par défaut peut être une chaîne vide. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Utilisez **SecureString** pour tous les mots de passe et secrets. Si vous transmettez des données sensibles dans un objet JSON, utilisez le type **secureObject**. Il est impossible de lire les paramètres du modèle dont le type est secureString ou secureObject après le déploiement de la ressource. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Utilisez un paramètre pour indiquer l’emplacement et partagez cette valeur de paramètre autant que possible avec les ressources qui sont susceptibles de se trouver dans le même emplacement. Cette approche réduit le nombre de fois où les utilisateurs sont invités à fournir des informations d’emplacement. Si un type de ressource est uniquement pris en charge dans un nombre limité d’emplacements, envisagez de spécifier un emplacement valide directement dans le modèle, ou ajoutez un autre paramètre d’emplacement. Si une organisation limite les régions autorisées pour les utilisateurs, l’expression **resourceGroup().location** peut empêcher un utilisateur de déployer le modèle. Par exemple, un utilisateur crée un groupe de ressources dans une région. Un deuxième utilisateur doit effectuer un déploiement vers ce groupe de ressources, mais n’a pas accès à la région. 
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[parameters('location')]",
         ...
     }
   ]
   ```
    
* Évitez d’utiliser un paramètre ou une variable pour la version de l’API pour un type de ressource. Les propriétés de ressource et les valeurs peuvent varier selon le numéro de version. IntelliSense dans des éditeurs de code n’est pas en mesure de déterminer le schéma correct lorsque la version de l’API est définie sur un paramètre ou une variable. Au lieu de cela, codez en dur la version de l’API dans le modèle.
* Évitez d’indiquer dans votre modèle un nom de paramètre correspondant à un paramètre dans la commande de déploiement. Resource Manager élimine ce risque de conflit de noms en ajoutant le suffixe **FromTemplate** au paramètre du modèle. Par exemple, si vous incluez dans votre modèle un paramètre nommé **ResourceGroupName**, celui-ci est en conflit avec le paramètre **ResourceGroupName** dans l’applet de commande [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). Pendant le déploiement, vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**.

## <a name="example-templates"></a>Exemples de modèles

Ces exemples de modèles montrent quelques scénarios d’utilisation de paramètres. Déployez-les pour tester la façon dont les paramètres sont gérés dans différents cas de figure.

|Modèle  |Description  |
|---------|---------|
|[Paramètres avec fonctions pour les valeurs par défaut](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Montre comment utiliser les fonctions de modèle durant la définition des valeurs par défaut des paramètres. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de paramètres. |
|[Objet de paramètre](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Montre comment utiliser un objet pour un paramètre. Le modèle ne déploie aucune ressource. Il crée et retourne des valeurs de paramètres. |

## <a name="next-steps"></a>Étapes suivantes

* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
* Pour plus d’informations sur la saisie des valeurs de paramètre au cours du déploiement, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md). 
* Pour plus d’informations sur les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour plus d’informations sur l’utilisation d’un objet de paramètre, consultez [Utiliser un objet en tant que paramètre dans un modèle Azure Resource Manager](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).