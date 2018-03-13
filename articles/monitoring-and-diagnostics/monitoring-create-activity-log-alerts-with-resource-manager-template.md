---
title: "Créer une alerte de journal d’activité avec un modèle Resource Manager | Documents Microsoft"
description: "Soyez informé lorsque vos ressources Azure sont créées."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: ancav
ms.openlocfilehash: b30912c44bd66f8c6fca548dc905f750e05c8621
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Créer une alerte de journal d’activité avec un modèle Resource Manager
Cet article explique comment vous pouvez utiliser un [modèle Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) pour configurer les alertes de journal d’activité. À l’aide de modèles, vous pouvez facilement configurer des nombreuses alertes activées selon des conditions de journal d’événements d’activité spécifiques dans le cadre de votre processus de déploiement automatisé.

Étapes élémentaires :

1. Créez un modèle sous la forme d’un fichier JSON qui décrit comment créer l’alerte de journal d’activité.

2. Déployez le modèle à l’aide de [n’importe quelle méthode de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Modèle Resource Manager pour une alerte de journal d’activité
Pour créer une alerte de journal d’activité à l’aide d’un modèle Resource Manager, créez une ressource de type `microsoft.insights/activityLogAlerts`. Puis, renseignez toutes les propriétés associées. Voici un modèle qui crée une alerte de journal d’activité.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Visitez notre [galerie Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights) pour obtenir des exemples de modèle d’alertes du journal d’activité.

> [!NOTE]

> Vous pouvez également créer des règles d’alerte de journal d’activité à l’aide de l’expérience utilisateur améliorée dans Surveiller > [Alertes (préversion)](monitoring-overview-unified-alerts.md). Pour plus d’informations sur la façon de les créer, voir [cet article](monitoring-activity-log-alerts-new-experience.md).

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [alertes](monitoring-overview-alerts.md).
- En savoir plus sur le guide pratique pour ajouter [des groupes d’actions à l’aide d’un modèle Resource Manager](monitoring-create-action-group-with-resource-manager-template.md).
- En savoir plus sur le guide pratique pour [créer une alerte de journal d’activité pour surveiller toutes les opérations du moteur de mise à l’échelle automatique dans votre abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- En savoir plus sur le guide pratique pour [créer une alerte de journal d’activité pour surveiller tous les échecs d’opérations de mise à l’échelle automatique dans votre abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
