---
title: "Structure de définition Azure Policy | Microsoft Docs"
description: "Explique comment Azure Policy utilise une définition de stratégie de ressource afin d’établir des conventions pour les ressources de votre organisation en décrivant quand la stratégie est appliquée et l’action à entreprendre."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/17/2018
ms.topic: article
ms.service: azure-policy
ms.custom: 
ms.openlocfilehash: ffff4a663b64342142f42a662905a290044e2dfb
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="azure-policy-definition-structure"></a>Structure de définition Azure Policy

Une définition de stratégie de ressource utilisée par Azure Policy vous permet d’établir des conventions pour les ressources de votre organisation en décrivant quand la stratégie est appliquée et l’action à entreprendre. En définissant des conventions, vous pouvez contrôler les coûts et gérer plus facilement vos ressources. Par exemple, vous pouvez spécifier que seuls certains types de machines virtuelles sont autorisés. Vous pouvez aussi exiger que toutes les ressources soient marquées. Toutes les ressources enfants héritent des stratégies. Ainsi, si une stratégie est appliquée à un groupe de ressources, elle est applicable à toutes les ressources appartenant à ce groupe de ressources.

Vous devez utiliser JSON pour créer une définition de stratégie. La définition de stratégie contient des éléments pour :

* le mode
* parameters
* le nom d’affichage
* description
* la règle de stratégie
  * évaluation logique
  * effet

Par exemple, le code JSON suivant illustre une stratégie qui limite les emplacements où les ressources sont déployées :

```json
{
  "properties": {
    "mode": "all",
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

Pour découvrir tous les exemples de modèles Azure Policy, consultez [Modèles pour Azure Policy](json-samples.md).

## <a name="mode"></a>Mode

Le **mode** détermine les types de ressources à évaluer pour une stratégie. Les modes pris en charge sont les suivants :
* `all` : évaluer les groupes de ressources et tous les types de ressources 
* `indexed` : évaluer uniquement les types de ressources qui prennent en charge les balises et l’emplacement

Nous vous recommandons de définir **mode** sur `all`. Toutes les définitions de stratégie créées via le portail utilisent le mode `all`. Si vous utilisez PowerShell ou Azure CLI, vous devez spécifier le paramètre **mode** et le définir sur `all`. 

## <a name="parameters"></a>parameters

Les paramètres permettent de simplifier la gestion des stratégies en réduisant le nombre de définitions de stratégies. Considérez les paramètres comme les champs d’un formulaire : `name`, `address`, `city`, `state`. Ces paramètres restent toujours les mêmes ; toutefois, leurs valeurs changent en fonction de la personne qui remplit le formulaire. Les paramètres fonctionnent de manière identique durant la création de stratégies. En incluant des paramètres dans une définition de stratégie, vous pouvez réutiliser cette stratégie pour différents scénarios avec des valeurs différentes.

Par exemple, vous pouvez définir une stratégie pour une propriété de ressource afin de limiter les emplacements sur lesquels les ressources peuvent être déployées. Dans ce cas, vous déclarez les paramètres suivants quand vous créez votre stratégie :


```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations",
      "strongType": "location"
    }
  }
}
```

Le type d’un paramètre peut être une chaîne ou un tableau. La propriété de métadonnées est utilisée pour des outils comme le portail Azure pour afficher des informations conviviales.

Dans la propriété de métadonnées, vous pouvez utiliser **strongType** pour fournir une liste à sélection multiple des options dans le portail Azure.  Les valeurs autorisées pour **strongType** incluent actuellement :

* `"location"`
* `"resourceTypes"`
* `"storageSkus"`
* `"vmSKUs"`

Dans la règle de stratégie, vous référencez des paramètres avec la syntaxe suivante :

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Nom d’affichage et description

Vous pouvez utiliser **displayName** et **description** pour distinguer la définition de stratégie et préciser le contexte d’utilisation.

## <a name="policy-rule"></a>Règle de stratégie

La règle de stratégie se compose de blocs **if** et **then**. Dans le bloc **if**, vous définissez une ou plusieurs conditions qui spécifient à quel moment la stratégie est mise en œuvre. Vous pouvez appliquer des opérateurs logiques à ces conditions pour définir avec précision le scénario d’une stratégie.

Dans le bloc **then**, vous définissez l’effet qui se produit lorsque les conditions de **si** sont remplies.

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>Opérateurs logiques

Les opérateurs logiques pris en charge sont les suivants :

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

La syntaxe **not** inverse le résultat de la condition. La syntaxe **allOf** (semblable à l’opération logique **And**) nécessite que toutes les conditions soient remplies. La syntaxe **anyOf** (semblable à l’opération logique **Of**) nécessite qu’au moins une des conditions soit remplie.

Vous pouvez imbriquer des opérateurs logiques. L’exemple suivant illustre une opération **not** imbriquée dans une opération **allOf**.

```json
"if": {
  "allOf": [
    {
      "not": {
        "field": "tags",
        "containsKey": "application"
      }
    },
    {
      "field": "type",
      "equals": "Microsoft.Storage/storageAccounts"
    }
  ]
},
```

### <a name="conditions"></a>Conditions

Une condition évalue si un champ (**field**) répond à certains critères. Les conditions prises en charge sont les suivantes :

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Lorsque vous utilisez la condition **like**, vous pouvez utiliser un caractère générique (*) dans la valeur.

Lorsque vous utilisez la condition de **correspondance**, indiquez `#` pour représenter un chiffre, `?` pour une lettre et tout autre caractère pour représenter ce caractère réel. Pour obtenir des exemples, consultez [Images de machine virtuelle approuvées](scripts/allowed-custom-images.md).

### <a name="fields"></a>Champs
Les conditions sont formées à partir de champs. Un champ représente des propriétés dans la charge utile de la requête de ressource qui est utilisée pour décrire l'état de la ressource.  

Les champs suivants sont pris en charge :

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*`
* alias de propriété : pour en obtenir la liste, consultez [Alias](#aliases).

### <a name="effect"></a>Résultat
La stratégie prend en charge les types d’effet suivants :

* **deny** : génère un événement dans le journal d’audit et fait échouer la requête.
* **audit** : génère un événement d’avertissement dans le journal d’audit, mais ne fait pas échouer la requête.
* **append** : ajoute l’ensemble de champs défini à la requête.
* **AuditIfNotExists** : active l’audit si une ressource n’existe pas.
* **DeployIfNotExists** : déploie une ressource si elle n’existe pas déjà. Actuellement, cet effet est uniquement pris en charge par le biais de stratégies intégrées.

Pour **append**, vous devez fournir les détails suivants :

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

La valeur peut être une chaîne ou un objet au format JSON.

Avec **AuditIfNotExists** et **DeployIfNotExists**, vous pouvez évaluer l’existence d’une ressource enfant et appliquer une règle et un effet correspondant quand cette ressource n’existe pas. Par exemple, vous pouvez exiger qu’un observateur réseau soit déployé pour tous les réseaux virtuels.
Pour obtenir un exemple d’audit quand une extension de machine virtuelle n’est pas déployée, consultez [Auditer si une extension n’existe pas](scripts/audit-ext-not-exist.md).


## <a name="aliases"></a>Alias

Les alias de propriété permettent d’accéder aux propriétés spécifiques d’un type de ressource. Les alias permettent de restreindre les valeurs ou les conditions autorisées pour la propriété d’une ressource. Chaque alias correspond aux chemins des différentes versions d’API d’un type de ressource donné. Lors de l’évaluation de la stratégie, le moteur de stratégie obtient le chemin de la propriété de cette version de l’API.

**Microsoft.Cache/Redis**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Définissez si le port du serveur Redis non-ssl (6379) est activé. |
| Microsoft.Cache/Redis/shardCount | Définissez le nombre de partitions à créer sur un cache de cluster Premium.  |
| Microsoft.Cache/Redis/sku.capacity | Définissez la taille du cache Redis à déployer.  |
| Microsoft.Cache/Redis/sku.family | Définissez la famille de références (SKU) à utiliser. |
| Microsoft.Cache/Redis/sku.name | Définissez le type de cache Redis à déployer. |

**Microsoft.Cdn/profiles**

| Alias | Description |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Définissez le nom du niveau tarifaire. |

**Microsoft.Compute/disks**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Définissez l’offre de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imagePublisher | Définissez le serveur de publication de l’image de plateforme ou de l’image de Place de marché utilisé pour créer la machine virtuelle. |
| Microsoft.Compute/imageSku | Définissez la référence SKU de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imageVersion | Définissez la version de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |


**Microsoft.Compute/virtualMachines**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Compute/imageId | Définissez l’identificateur de l’image utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imageOffer | Définissez l’offre de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imagePublisher | Définissez le serveur de publication de l’image de plateforme ou de l’image de Place de marché utilisé pour créer la machine virtuelle. |
| Microsoft.Compute/imageSku | Définissez la référence SKU de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imageVersion | Définissez la version de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/licenseType | Définissez si l’image ou le disque est sous licence en local. Cette valeur est utilisée uniquement pour les images qui contiennent le système d’exploitation Windows Server.  |
| Microsoft.Compute/virtualMachines/imageOffer | Définissez l’offre de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/virtualMachines/imagePublisher | Définissez le serveur de publication de l’image de plateforme ou de l’image de Place de marché utilisé pour créer la machine virtuelle. |
| Microsoft.Compute/virtualMachines/imageSku | Définissez la référence SKU de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/virtualMachines/imageVersion | Définissez la version de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Définissez l’URI du disque dur virtuel. |
| Microsoft.Compute/virtualMachines/sku.name | Définissez la taille de la machine virtuelle. |
| Microsoft.Compute/virtualMachines/availabilitySet.id | Définit l’ID du groupe à haute disponibilité de la machine virtuelle. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Définissez le nom du serveur de publication de l’extension. |
| Microsoft.Compute/virtualMachines/extensions/type | Définissez le type d’extension. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Définissez la version de l’extension. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Compute/imageId | Définissez l’identificateur de l’image utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imageOffer | Définissez l’offre de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imagePublisher | Définissez le serveur de publication de l’image de plateforme ou de l’image de Place de marché utilisé pour créer la machine virtuelle. |
| Microsoft.Compute/imageSku | Définissez la référence SKU de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/imageVersion | Définissez la version de l’image de plateforme ou de l’image de Place de marché utilisée pour créer la machine virtuelle. |
| Microsoft.Compute/licenseType | Définissez si l’image ou le disque est sous licence en local. Cette valeur est utilisée uniquement pour les images qui contiennent le système d’exploitation Windows Server. |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | Définissez le préfixe du nom de l’ordinateur pour toutes les machines virtuelles dans le groupe identique. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | Définisez l’URI de blob pour l’image utilisateur. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | Définissez les URL de conteneur utilisées afin de stocker les disques du système d’exploitation pour le groupe identique. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | Définissez la taille des machines virtuelles dans un groupe identique. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | Définissez le niveau des machines virtuelles dans un groupe identique. |

**Microsoft.Network/applicationGateways**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Définissez la taille de la passerelle. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Définissez le type de cette passerelle de réseau virtuel. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Définissez la référence SKU de la passerelle. |

**Microsoft.Sql/servers**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Définissez la version du serveur. |

**Microsoft.Sql/databases**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Définissez la modification de la base de données. |
| Microsoft.Sql/servers/databases/elasticPoolName | Définissez le nom du pool élastique dans lequel se trouve la base de données. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Définissez l’ID d’objectif de niveau de service configuré de la base de données. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Définissez le nom de l’objectif de niveau de service configuré de la base de données.  |

**Microsoft.Sql/elasticpools**

| Alias | Description |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Définissez la valeur DTU partagée totale pour le pool élastique de la base de données. |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Définissez la modification du pool élastique. |

**Microsoft.Storage/storageAccounts**

| Alias | Description |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Définissez le niveau d’accès utilisé pour la facturation. |
| Microsoft.Storage/storageAccounts/accountType | Définissez le nom de la référence SKU. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Définissez si le service chiffre les données lorsqu’elles sont stockées dans le service de stockage d’objets blob. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Définissez si le service chiffre les données lorsqu’elles sont stockées dans le service de stockage de fichiers. |
| Microsoft.Storage/storageAccounts/sku.name | Définissez le nom de la référence SKU. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Indiquez que seul le trafic https est autorisé vers le service de stockage. |
| Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id | Vérifiez si le point de terminaison du service de réseau virtuel est activé. |

## <a name="initiatives"></a>Initiatives

Les initiatives vous permettent de regrouper en un seul élément plusieurs définitions de stratégies associées pour simplifier les affectations et la gestion. Par exemple, vous pouvez regrouper toutes les définitions de stratégies de balisage associées en une même initiative. Au lieu d’attribuer chaque stratégie individuellement, vous appliquez l’initiative.

L’exemple suivant montre comment créer une initiative pour gérer deux balises : `costCenter` et `productName`. Il utilise deux stratégies intégrées pour appliquer la valeur de balise par défaut.


```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>étapes suivantes

- Découvrez les exemples de modèles Azure Policy en consultant [Modèles pour Azure Policy](json-samples.md).
