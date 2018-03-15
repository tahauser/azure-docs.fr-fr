---
title: "Structure et syntaxe du modèle Azure Resource Manager | Microsoft Docs"
description: "Décrit la structure et les propriétés des modèles Azure Resource Manager à l’aide de la syntaxe JSON déclarative."
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
ms.date: 12/13/2017
ms.author: tomfitz
ms.openlocfilehash: 89e4b52e7d306bd495c426bcf775f59d0f30eb55
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Section Ressources des modèles Azure Resource Manager

Dans la section des ressources, vous définissez les ressources déployées ou mises à jour. Cette section gagne en complexité, car vous devez connaître les types que vous déployez pour fournir les valeurs adéquates.

## <a name="available-properties"></a>Propriétés disponibles

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

| Nom de l'élément | Obligatoire | Description |
|:--- |:--- |:--- |
| condition | Non  | Valeur booléenne indiquant si la ressource est déployée. |
| apiVersion |OUI |La version de l'API REST à utiliser pour la création de la ressource. |
| Type |OUI |Type de la ressource. Cette valeur est une combinaison de l’espace de noms du fournisseur de ressources et du type de ressource (comme **Microsoft.Storage/storageAccounts**). |
| Nom |OUI |Nom de la ressource. Le nom doit respecter les restrictions de composant d'URI définies dans le document RFC3986. En outre, les services Azure qui exposent le nom de la ressource à des parties externes valident du nom pour s’assurer qu’il ne s’agit pas d’une tentative d’usurpation d’identité. |
| location |Varie |Emplacements géographiques de la ressource fournie pris en charge. Vous pouvez sélectionner l’un des emplacements disponibles, mais en général, il est judicieux de choisir celui qui est proche de vos utilisateurs. En règle générale, il est également judicieux de placer dans la même région les ressources qui interagissent entre elles. La plupart des types de ressources nécessitent un emplacement, mais certains types (par exemple, une affectation de rôle) ne nécessitent aucun emplacement. |
| tags |Non  |Balises associées à la ressource. Appliquer des balises pour organiser logiquement des ressources dans votre abonnement. |
| commentaires |Non  |Vos commentaires pour documenter les ressources dans votre modèle |
| copy |Non  |Si plusieurs instances sont nécessaires, le nombre de ressources à créer. Le mode par défaut est parallèle. Spécifiez le mode série si vous ne souhaitez pas que toutes les ressources soient déployées en même temps. Pour plus d’informations, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Non  |Les ressources qui doivent être déployées avant le déploiement de cette ressource. Resource Manager évalue les dépendances entre les ressources et les déploie dans le bon ordre. Quand les ressources ne dépendent les unes des autres, leur déploiement se fait en parallèle. La valeur peut être une liste séparée par des virgules de noms de ressource ou d’identificateurs de ressource uniques. Répertoriez uniquement les ressources qui sont déployées dans ce modèle. Les ressources qui ne sont pas définies dans ce modèle doivent déjà exister. Évitez d’ajouter des dépendances inutiles, car cela risque de ralentir votre déploiement et de créer des dépendances circulaires. Pour savoir comment définir des dépendances, consultez [Définition de dépendances dans les modèles Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Non  |Paramètres de configuration spécifiques aux ressources. Les valeurs de propriétés sont identiques à celles que vous fournissez dans le corps de la requête pour l’opération d’API REST (méthode PUT) pour créer la ressource. Vous pouvez également spécifier une copie en groupe pour créer plusieurs instances d’une propriété. |
| les ressources |Non  |Ressources enfants qui dépendent de la ressource qui est définie. Fournissez uniquement des types de ressources qui sont autorisés par le schéma de la ressource parente. Le type complet de la ressource enfant inclut le type de ressource parente, par exemple **Microsoft.Web/sites/extensions**. La dépendance envers la ressource parente n’est pas induite. Vous devez la définir explicitement. |

## <a name="resource-specific-values"></a>Valeurs propres à la ressource

**L’apiVersion**, le **type** et les **Propriétés** sont différents pour chaque type de ressource. Pour déterminer les valeurs de ces propriétés, consultez [Référence de modèle](/azure/templates/).

## <a name="resource-names"></a>Noms de ressource
Il existe généralement trois types de noms de ressource avec lesquels vous travaillez dans Resource Manager :

* Des noms de ressources qui doivent être uniques
* Des noms de ressources qui ne doivent pas obligatoirement être uniques, mais pour lesquels vous choisissez un nom qui vous permettra d’identifier la ressource
* Des noms de ressources qui peuvent être génériques

### <a name="unique-resource-names"></a>Noms de ressource uniques
Vous devez fournir un nom de ressource unique pour tout type de ressource disposant d’un point de terminaison d’accès aux données. Certains types de ressource courants nécessitent un nom unique, notamment :

* Azure Storage<sup>1</sup> 
* Fonctionnalité Web Apps d’Azure App Service
* SQL Server
* Azure Key Vault
* Cache Redis Azure
* Azure Batch
* Azure Traffic Manager
* Recherche Azure
* Azure HDInsight

<sup>1</sup> Les noms de compte de stockage doivent être en minuscules, comporter 24 caractères au maximum et ne pas comprendre de traits d’union.

Quand vous définissez le nom, vous pouvez soit créer manuellement un nom unique, soit utiliser la fonction [uniqueString()](resource-group-template-functions-string.md#uniquestring) pour générer un nom. Vous pouvez également ajouter un préfixe ou un suffixe au résultat.**uniqueString**. La modification du nom unique peut vous aider à identifier plus facilement le type de ressource à partir du nom. Par exemple, vous pouvez générer un nom unique pour un compte de stockage avec la variable suivante :

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Noms de ressource pour l’identification
Pour certains types de ressources, les noms ne doivent pas nécessairement être uniques. Pour ces types de ressources, vous pouvez fournir un nom qui identifie le contexte de la ressource et le type de ressource.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>Noms de ressource génériques
Pour les types de ressources qui sont accessibles en grande partie par le biais d’une autre ressource, vous pouvez utiliser un nom générique codé en dur dans le modèle. Par exemple, vous pouvez définir un nom générique standard pour les règles de pare-feu sur un serveur SQL :

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>Lieu
Lorsque vous déployez un modèle, vous devez fournir un emplacement pour chaque ressource. Différents types de ressources sont pris en charge à différents emplacements. Pour afficher une liste des emplacements disponibles pour votre abonnement pour un type de ressource particulier, utilisez Azure PowerShell ou Azure CLI. 

L’exemple suivant utilise PowerShell pour obtenir les emplacements pour le type de ressource `Microsoft.Web\sites` :

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

L’exemple suivant utilise Azure CLI 2.0 pour obtenir les emplacements pour le type de ressource `Microsoft.Web\sites` :

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

Après avoir déterminé les emplacements pris en charge pour vos ressources, définissez cet emplacement dans votre modèle. Le moyen le plus simple pour définir cette valeur consiste à créer un groupe de ressources dans un emplacement qui prend en charge les types de ressources, puis de définir chaque emplacement sur `[resourceGroup().location]`. Vous pouvez redéployer le modèle dans des groupes de ressources à des emplacements différents et ne pas modifier les valeurs dans le modèle ou les paramètres. 

L’exemple suivant illustre le déploiement d’un compte de stockage au même emplacement que le groupe de ressources :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Si vous devez coder en dur l’emplacement dans votre modèle, indiquez le nom de l’une des régions prises en charge. L’exemple suivant montre un compte de stockage qui est toujours déployé dans la région Nord du centre des États-Unis :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>Balises
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

### <a name="add-tags-to-your-template"></a>Ajouter des balises à votre modèle

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Ressources enfants

Dans certains types de ressources, vous pouvez également définir un tableau de ressources enfants. Les ressources enfants sont des ressources qui n’existent que dans le contexte d’une autre ressource. Par exemple, une base de données SQL ne peut pas exister sans serveur SQL Server. Elle est donc un enfant du serveur. Vous pouvez définir la base de données dans la définition du serveur.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

En cas d’imbrication, le type est défini sur `databases`, mais son type de ressource complet est `Microsoft.Sql/servers/databases`. Vous ne fournissez pas `Microsoft.Sql/servers/`, car il est déduit à partir du type de ressource parent. Le nom de la ressource enfant est défini sur `exampledatabase`, mais le nom complet inclut le nom parent. Vous ne fournissez pas `exampleserver`, car il est déduit à partir de la ressource parent.

Le format du type de la ressource enfant est : `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Le format du nom de la ressource enfant est : `{parent-resource-name}/{child-resource-name}`

Toutefois, vous n’êtes pas obligé de définir la base de données dans le serveur. Vous pouvez définir la ressource enfant au niveau supérieur. Vous pouvez utiliser cette approche si la ressource parent n’est pas déployée dans le même modèle, ou si voulez utiliser `copy` pour créer plusieurs ressources enfant. Dans le cadre de cette approche, vous devez fournir le type de ressource complet et inclure le nom de la ressource parent dans le nom de la ressource enfant.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Lors de la création d’une référence complète à une ressource, l’ordre utilisé pour combiner les segments de type et de nom n’est pas une simple concaténation des deux.  Au lieu de cela, utilisez après l’espace de noms une séquence de paires *type/nom* du moins spécifique au plus spécifique :

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Par exemple : 

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` est correct `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` n’est pas correct

## <a name="recommendations"></a>Recommandations
Les informations suivantes peuvent être utiles lorsque vous travaillez avec des ressources :

* Spécifiez des **commentaires** pour chaque ressource dans le modèle pour aider les autres contributeurs à comprendre l’objectif de la ressource :
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Si vous utilisez un *point de terminaison public* dans votre modèle (par exemple, un point de terminaison public Azure Blob Storage), *ne codez pas en dur* l’espace de noms. Utilisez la fonction **référence** pour récupérer l’espace de noms dynamiquement. Cette approche vous permet de déployer le modèle dans différents environnements d’espace de noms publics sans modifier manuellement le point de terminaison dans le modèle. Définissez la version de l’API sur la version que vous utilisez pour le compte de stockage dans votre modèle :
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Si le compte de stockage est déployé dans le même modèle que vous créez, il est inutile de spécifier l’espace de noms du fournisseur lors du référencement de la ressource. L’exemple suivant montre la syntaxe simplifiée :
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Si vous avez d’autres valeurs dans votre modèle configuré avec un espace de noms public, modifiez les valeurs de manière à ce qu’elles reflètent la même fonction de **référence**. Par exemple, vous pouvez définir la propriété **storageUri** du profil de diagnostic de la machine virtuelle :
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Vous pouvez également référencer un compte de stockage existant dans un autre groupe de ressources :

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Affectez des adresses IP publiques à une machine virtuelle uniquement si une application le nécessite. Pour vous connecter à une machine virtuelle (VM) pour le débogage, ou à des fins d’administration ou de gestion, utilisez les règles NAT entrantes, une passerelle de réseau virtuel ou une jumpbox.
   
     Pour plus d’informations sur la connexion aux machines virtuelles, consultez :
   
   * [Exécuter des machines virtuelles pour une architecture à plusieurs niveaux dans Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configurer l’accès WinRM pour les machines virtuelles dans Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Autoriser l’accès externe à votre machine virtuelle à l’aide du portail Azure](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Autoriser l’accès externe à votre machine virtuelle à l’aide de PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Autoriser l’accès externe à votre machine virtuelle Linux à l’aide de l’interface de ligne de commande Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* La propriété **domainNameLabel** pour les adresses IP publiques doit être unique. La valeur **domainNameLabel** doit comporter entre 3 et 63 caractères et respecter les règles spécifiées par cette expression régulière : `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Comme la fonction **uniqueString** génère une chaîne de 13 caractères, le paramètre **dnsPrefixString** est limité à 50 caractères :

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Lors de l’ajout d’un mot de passe à une extension de script personnalisé, utilisez la propriété **commandToExecute** dans la propriété **protectedSettings** :
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Pour garantir que les clés secrètes sont chiffrées lorsqu’elles sont transmises comme paramètres à des machines virtuelles et à des extensions, utilisez la propriété **protectedSettings** des extensions appropriées.
   > 
   > 


## <a name="next-steps"></a>Étapes suivantes
* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
* Pour plus d’informations sur les fonctions que vous pouvez utiliser dans un modèle, consultez [Fonctions des modèles Azure Resource Manager](resource-group-template-functions.md).
* Pour combiner plusieurs modèles lors du déploiement, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Vous devrez peut-être utiliser des ressources qui existent au sein d'un groupe de ressources différent. Ce scénario est classique quand vous utilisez des comptes de stockage ou des réseaux virtuels qui sont partagés entre plusieurs groupes de ressources. Pour plus d'informations, consultez la [fonction resourceId](resource-group-template-functions-resource.md#resourceid).
* Pour plus d’informations sur les restrictions de noms de ressource, consultez [Conventions d’affectation de noms recommandées pour les ressources Azure](../guidance/guidance-naming-conventions.md).