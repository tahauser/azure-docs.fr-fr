---
title: "Créer des groupes d’actions avec les modèles Resource Manager | Microsoft Docs"
description: "Découvrez comment créer un groupe d’actions à l’aide d’un modèle Azure Resource Manager."
author: dkamstra
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: dukek
ms.openlocfilehash: 1f97bf1e1e5c2800c2cdc16a6bcc1837f178e408
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Créer un groupe d’actions avec un modèle Resource Manager
Cet article vous explique comment utiliser un [modèle Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) pour configurer les groupes d’action. À l’aide de modèles, vous pouvez configurer automatiquement des groupes d’actions qui peuvent être réutilisés dans certains types d’alertes. Ces groupes d’actions vous assurent que toutes les parties concernées sont averties lorsqu’une alerte est déclenchée.

Étapes élémentaires :

1. Créez un modèle sous la forme d’un fichier JSON qui décrit comment créer le groupe d’actions.

2. Déployez le modèle à l’aide de [n’importe quelle méthode de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Nous allons tout d’abord présenter comment créer un modèle Resource Manager pour un groupe d’actions où les définitions d’action sont codées en dur dans le modèle. Nous allons ensuite expliquer comment créer un modèle qui utilise les informations de configuration de webhook comme des paramètres d’entrée lorsque le modèle est déployé.

## <a name="resource-manager-templates-for-an-action-group"></a>Modèles Resource Manager pour un groupe d’actions

Pour créer un groupe d’actions à l’aide d’un modèle Resource Manager, vous créez une ressource de type `Microsoft.Insights/actionGroups`. Puis, renseignez toutes les propriétés associées. Voici deux exemples de modèles qui créent un groupe d’actions.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [groupes d’actions](monitoring-action-groups.md).
* En savoir plus sur les [alertes](monitoring-overview-alerts.md).
* En savoir pour sur l’ajout d’[alertes à l’aide d’un modèle Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
