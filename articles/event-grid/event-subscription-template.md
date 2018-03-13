---
title: "Abonnement Azure Event Grid avec modèle"
description: "Créez un abonnement Event Grid avec un modèle Resource Manager."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: ee0b2c228ae4ea53c0ee9794529aa190334ceed9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>Utiliser un module Resource Manager pour l’abonnement Event Grid

Cet article explique comment utiliser un modèle Azure Resource Manager pour créer des abonnements Event Grid. Le format que vous utilisez diffère selon que vous vous abonnez à des événements d’un groupe de ressources ou à des événements d’un type de ressource particulier. Les deux formats sont présentés dans cet article.

## <a name="subscribe-to-resource-group-events"></a>S’abonner aux événements d’un groupe de ressources

Lors de l’abonnement aux événements d’un groupe de ressources, utilisez `Microsoft.EventGrid/eventSubscriptions` comme type de ressource. Pour le type de point d’événement, utilisez `WebHook` ou `EventHub`.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2018-01-01",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": ["All"]
                }
            }
        }
    ]
}
```

Lorsque vous déployez ce modèle dans un groupe de ressources, vous vous abonnez aux événements de ce groupe de ressources.

## <a name="subscribe-to-resource-events"></a>S’abonner à des événements de ressources

Lors de l’abonnement à des événements de ressources, vous associez l’abonnement à la ressource correspondante, y compris le type de ressource et le nom de la définition d’abonnement. Pour le type de ressource, utilisez `<provider-namespace>/<resource-type>/providers/eventSubscriptions`. Pour le nom, utilisez `<resource-name>/Microsoft.EventGrid/<subscription-name>`. Pour le type de point d’événement, utilisez `WebHook` ou `EventHub`.

L’exemple suivant montre comment s’abonner à des événements de stockage d’objets blob.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/myStorageSubscription')]",
            "apiVersion": "2018-01-01",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour découvrir Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Pour bien démarrer avec Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).