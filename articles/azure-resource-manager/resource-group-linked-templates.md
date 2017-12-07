---
title: "Lier des modèles pour un déploiement Azure | Microsoft Docs"
description: "Décrit comment utiliser des modèles liés dans un modèle Azure Resource Manager afin de créer une solution de modèle modulaire. Indique comment transmettre des valeurs de paramètres, spécifier un fichier de paramètres et créer dynamiquement des URL."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: tomfitz
ms.openlocfilehash: a86d4d8705c7093e3900a9738ddbd364db8bd3b8
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="using-linked-templates-when-deploying-azure-resources"></a>Utilisation de modèles liés lors du déploiement des ressources Azure

Pour déployer votre solution, vous pouvez utiliser un modèle unique ou un modèle principal avec plusieurs modèles liés. Pour les solutions petites et moyennes, un modèle unique est plus facile à comprendre et à gérer. Vous pouvez voir toutes les ressources et valeurs dans un seul fichier. Pour les scénarios avancés, les modèles liés vous permettent de diviser la solution en composants ciblés et de réutiliser des modèles.

Lorsque vous utilisez un modèle lié, vous créez un modèle principal qui reçoit les valeurs de paramètre au cours du déploiement. Le modèle principal contient tous les modèles liés et transmet des valeurs à ces modèles en fonction des besoins.

![modèles liés](./media/resource-group-linked-templates/nestedTemplateDesign.png)

## <a name="link-to-a-template"></a>Lier à un modèle

Pour créer un lien vers un autre modèle, ajoutez une ressource de **déploiement** à votre modèle principal.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <inline-template-or-external-template>
      }
  }
]
```

Les propriétés que vous fournissez pour la ressource de déploiement varient selon que vous établissez la liaison à un modèle externe ou que vous incorporez un modèle inclus dans le modèle principal.

### <a name="inline-template"></a>Modèle inclus

Pour incorporer le modèle lié, utilisez la propriété **template** et incluez le modèle.

```json
"resources": [
  {
    "apiVersion": "2017-05-10",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
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
            "name": "[variables('storageName')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
              "accountType": "Standard_LRS"
            }
          }
        ]
      },
      "parameters": {}
    }
  }
]
```

### <a name="external-template-and-external-parameters"></a>Modèle externe et paramètres externes

Pour établir un lien à un modèle externe et un fichier de paramètres, utilisez **templateLink** et **parametersLink**. Quand vous établissez un lien à un modèle, le service Resource Manager doit être en mesure d’y accéder. Vous ne pouvez pas spécifier un fichier local ni un fichier uniquement disponible sur votre réseau local. Vous pouvez seulement fournir une valeur URI qui inclut soit **http** soit **https**. Une possibilité consiste à placer votre modèle lié dans un compte de stockage et à utiliser l’URI de cet élément.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parametersLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

### <a name="external-template-and-inline-parameters"></a>Modèle externe et paramètres inclus

Ou bien, vous pouvez fournir le paramètre inclus. Pour passer une valeur du modèle principal au modèle lié, utilisez des **paramètres**.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

## <a name="using-variables-to-link-templates"></a>Utilisation de variables pour lier des modèles

Les exemples précédents représentaient des valeurs d’URL codées en dur pour les liens de modèle. Cette approche peut s’avérer efficace avec un modèle isolé, mais elle est incompatible avec le traitement d’un large ensemble des modèles modulaires. Au lieu de cela, vous pouvez créer une variable statique qui stocke une URL de base pour le modèle principal, avant de créer dynamiquement les URL pour les modèles liés à partir de cette URL de base. Cette approche permet notamment de déplacer ou de répliquer facilement le modèle. En effet, il vous suffit de modifier la variable statique dans le modèle principal. Le modèle principal transmet les URI appropriés au modèle décomposé.

L’exemple suivant indique comment utiliser une URL de base afin de créer deux URL pour des modèles liés (**sharedTemplateUrl** et **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Vous pouvez également utiliser [deployment()](resource-group-template-functions-deployment.md#deployment) pour obtenir l’URL de base pour le modèle actuel, qui permet d’obtenir l’URL d’autres modèles dans le même emplacement. Cette approche est utile si l’emplacement des modèles change (à cause des versions notamment) ou si vous voulez éviter de coder en dur les URL dans le fichier de modèle.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Obtenir des valeurs du modèle lié

Pour obtenir une valeur de sortie d’un modèle lié, récupérez la valeur de propriété à l’aide d’une syntaxe comme : `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Les exemples suivants montrent comment faire référence à un modèle lié pour récupérer une valeur de sortie. Le modèle lié retourne un message simple.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

Le modèle parent déploie le modèle lié et obtient la valeur retournée. Remarquez qu’il fait référence à la ressource de déploiement par son nom et qu’il utilise le nom de la propriété retournée par le modèle lié.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

Comme pour d’autres types de ressources, vous pouvez définir des dépendances entre le modèle lié et d’autres ressources. Par conséquent, lorsque d’autres ressources requièrent une valeur de sortie à partir du modèle lié, vous pouvez vous assurer que le modèle lié est déployé avant celles-ci. Sinon, lorsque le modèle lié s’appuie sur d’autres ressources, vous pouvez vous assurer que d’autres ressources sont déployées avant le modèle lié.

L’exemple suivant montre un modèle qui déploie une adresse IP publique et retourne l’ID de ressource :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Pour utiliser l’adresse IP publique du modèle précédent lors du déploiement d’un équilibreur de charge, établissez un lien vers le modèle et ajoutez une dépendance vis-à-vis de la ressource de déploiement. L’adresse IP publique sur l’équilibreur de charge est définie sur la valeur de sortie du modèle lié.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "name": "[parameters('loadBalancers_name')]",
            "apiVersion": "2017-06-01",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-templates-in-deployment-history"></a>Modèles liés dans l’historique de déploiement

Resource Manager traite chaque modèle lié comme un déploiement séparé dans l’historique de déploiement. Par conséquent, un modèle parent doté de trois modèles liés s’affiche dans l’historique de déploiement en tant que :

![Historique de déploiement](./media/resource-group-linked-templates/deployment-history.png)

Vous pouvez utiliser ces entrées distinctes dans l’historique pour récupérer les valeurs de sortie après le déploiement. Le modèle suivant crée une adresse IP publique et renvoie l’adresse IP :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

Le modèle suivant est lié au modèle précédent. Il crée trois adresses IP publiques.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            },
            "copy": {
                "count": 3,
                "name": "ip-loop"
            }
        }
    ]
}
```

Après le déploiement, vous pouvez récupérer les valeurs de sortie avec le script PowerShell suivant :

```powershell
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Ou bien, le script Azure CLI :

```azurecli
for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Sécurisation d’un modèle externe

Bien que le modèle lié doive être disponible en externe, il n’a pas besoin d’être accessible au public. Vous pouvez ajouter votre modèle dans un compte de stockage privé, uniquement accessible au propriétaire du compte de stockage. Ensuite, vous créez un jeton de signature d’accès partagé (SAP) pour autoriser l’accès en cours de déploiement. Vous ajoutez ce jeton SAP à l’URI pour le modèle lié. Même si le jeton est transmis sous forme de chaîne sécurisée, l’URI du modèle lié, y compris le jeton SAP, est enregistré dans les opérations de déploiement. Pour limiter l’exposition, définissez un délai d’expiration pour le jeton.

Le fichier de paramètres peut également être limité à l’accès avec un jeton SAP.

L’exemple suivant montre comment passer un jeton SAP lors de la liaison à un modèle :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Dans PowerShell, vous obtenez un jeton pour le conteneur et déployez les modèles avec :

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Dans l’interface de ligne de commande Azure, vous obtenez un jeton pour le conteneur et déployez les modèles avec le code suivant :

```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Exemples de modèles

### <a name="hello-world-from-linked-template"></a>Hello World à partir du modèle lié

Pour déployer le [modèle parent](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) et le [modèle lié](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json), utilisez PowerShell :

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/helloworldparent.json
```

Ou d’Azure CLI :

```azurecli-interactive
az group deployment create \
  -g examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/helloworldparent.json
```

### <a name="load-balancer-with-public-ip-address-in-linked-template"></a>Load Balancer avec l’adresse IP publique dans le modèle lié

Pour déployer le [modèle parent](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) et le [modèle lié](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json), utilisez PowerShell :

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json
```

Ou d’Azure CLI :

```azurecli-interactive
az group deployment create \
  -g examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json
```

### <a name="multiple-public-ip-addresses-in-linked-template"></a>Plusieurs adresses IP publiques dans le modèle lié

Pour déployer le [modèle parent](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) et le [modèle lié](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json), utilisez PowerShell :

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json
```

Ou d’Azure CLI :

```azurecli-interactive
az group deployment create \
  -g examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json
```

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir des informations sur la définition de l’ordre de déploiement de vos ressources, consultez [Définition de dépendances dans les modèles Azure Resource Manager](resource-group-define-dependencies.md).
* Pour savoir comment définir une seule ressource mais également créer de nombreuses instances de cette dernière, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
* Pour connaître les étapes de configuration d’un modèle dans un compte de stockage et de génération d’un jeton SAP, consultez [Déployer des ressources avec des modèles Resource Manager et Azure PowerShell](resource-group-template-deploy.md) ou [Déployer des ressources avec des modèles Resource Manager et l’interface de ligne de commande Azure](resource-group-template-deploy-cli.md).