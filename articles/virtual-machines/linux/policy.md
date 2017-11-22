---
title: "Appliquer la sécurité avec des stratégies sur des machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Comment appliquer une stratégie à une machine virtuelle Azure Resource Manager Linux"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: singhkay
ms.openlocfilehash: 72abb01a3ce7f4dea2ee97219e9a406c69cda7c5
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Appliquer des stratégies aux machines virtuelles Linux avec Azure Resource Manager
Avec les stratégies, une organisation peut appliquer différentes conventions et règles à travers l'entreprise. L’application du comportement souhaité peut vous aider à atténuer les risques tout en contribuant à la réussite de l'organisation. Dans cet article, nous expliquons comment utiliser les stratégies d’Azure Resource Manager pour définir le comportement souhaité pour les machines virtuelles de votre organisation.

Pour une introduction aux stratégies, consultez la page [Qu’est-ce qu’Azure Policy ?](../../azure-policy/azure-policy-introduction.md).

## <a name="permitted-virtual-machines"></a>Machines virtuelles autorisées
Pour vous assurer que les machines virtuelles de votre entreprise sont compatibles avec une application, vous pouvez limiter les systèmes d’exploitation autorisés. Dans l’exemple de stratégie suivant, vous autorisez uniquement la création de machines virtuelles Ubuntu 14.04.2-LTS.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Utilisez un caractère générique pour modifier la stratégie précédente afin qu’elle autorise toutes les images Ubuntu LTS : 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Pour plus d’informations sur les champs de la stratégie, consultez les [alias de stratégie](../../azure-policy/policy-definition.md#aliases).

## <a name="managed-disks"></a>Disques gérés

Pour exiger l’utilisation de disques gérés, employez la stratégie suivante :

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Images de machines virtuelles

Pour des raisons de sécurité, vous pouvez exiger que seules les images personnalisées approuvées soient déployées dans votre environnement. Vous pouvez spécifier soit le groupe de ressources qui contient les images approuvées, soit les images approuvées.

L’exemple suivant nécessite des images provenant d’un groupe de ressources approuvées :

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

L’exemple suivant spécifie les ID des images approuvées :

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Extensions de machine virtuelle

Vous pouvez interdire l’utilisation de certains types d’extensions. Par exemple, une extension peut ne pas être compatible avec certaines images de machines virtuelles personnalisées. L’exemple suivant montre comment bloquer une extension. Il se base sur l’éditeur et sur le type pour déterminer quelle extension doit être bloquée.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>Étapes suivantes
* Après avoir défini une règle de stratégie (comme le montrent les exemples précédents), vous devez créer la définition de stratégie et l’attribuer à une étendue. L’étendue peut être un abonnement, un groupe de ressources ou une ressource. Pour assigner des stratégies, consultez [Utiliser le portail Azure pour attribuer et gérer les stratégies de ressources](../../azure-policy/assign-policy-definition.md), [Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure en utilisant PowerShell](../../azure-policy/assign-policy-definition-ps.md) et [Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure en utilisant l’interface de ligne de commande Azure](../../azure-policy/assign-policy-definition-cli.md).
* Pour une introduction aux stratégies de ressources, consultez la page [Qu’est-ce qu’Azure Policy ?](../../azure-policy/azure-policy-introduction.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](../../azure-resource-manager/resource-manager-subscription-governance.md).
