---
title: "Schéma d’événement d’abonnement Azure Event Grid"
description: "Décrit les propriétés fournies pour les événements d’abonnement avec Azure Event Grid."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 23249b92b4e99628d49bbd811b4ad1f1dc9cc9b0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Schéma d’événement Azure Event Grid pour les abonnements

Cet article fournit les propriétés et les schémas des événements d’abonnement Azure. Pour une présentation des schémas d’événement, consultez [Schéma d’événement Azure Event Grid](event-schema.md).

Les abonnements Azure et les groupes de ressources émettent les mêmes types d’événements. Les types d’événements sont liés aux modifications des ressources. La principale différence est que les groupes de ressources émettent des événements pour les ressources appartenant au groupe de ressources, alors que les abonnements Azure émettent des événements pour les ressources de l’abonnement.

## <a name="available-event-types"></a>Types d’événement disponibles

Les abonnements Azure émettent des événements de gestion à partir d’Azure Resource Manager, par exemple lors de la création d’une machine virtuelle ou de la suppression d’un compte de stockage.

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | Déclenché quand une opération de création ou de mise à jour de ressource réussit. |
| Microsoft.Resources.ResourceWriteFailure | Déclenché quand une opération de création ou de mise à jour de ressource échoue. |
| Microsoft.Resources.ResourceWriteCancel | Déclenché quand une opération de création ou de mise à jour de ressource est annulée. |
| Microsoft.Resources.ResourceDeleteSuccess | Déclenché quand une opération de suppression de ressource réussit. |
| Microsoft.Resources.ResourceDeleteFailure | Déclenché quand une opération de suppression de ressource échoue. |
| Microsoft.Resources.ResourceDeleteCancel | Déclenché quand une opération de suppression de ressource est annulée. Cet événement se produit lorsque le déploiement d’un modèle est annulé. |

## <a name="example-event"></a>Exemple d’événement

L’exemple suivant montre le schéma d’un d’événement de création de ressource : 

```json
[
  {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
      "dataVersion": "",
      "metadataVersion": "1"
  }
]
```

Le schéma de l’événement de suppression d’une ressource est similaire :

```json
[{
  "topic":"/subscriptions/{subscription-id}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Propriétés d’événement

Un événement contient les données générales suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| rubrique | chaîne | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| subject | chaîne | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| eventType | chaîne | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | chaîne | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | chaîne | Identificateur unique de l’événement. |
| données | objet | Données d’événement d’abonnement. |
| dataVersion | chaîne | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | chaîne | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| autorisation | chaîne | Autorisation demandée pour l’opération. |
| réclamations | chaîne | Propriétés des revendications. |
| correlationId | chaîne | ID d’opération pour le dépannage. |
| httpRequest | chaîne | Détails de l’opération. |
| resourceProvider | chaîne | Fournisseur de ressources qui effectue l’opération. |
| resourceUri | chaîne | URI de la ressource dans l’opération. |
| operationName | chaîne | Opération effectuée. |
| status | chaîne | L’état de l’opération. |
| subscriptionId | chaîne | ID d’abonnement de la ressource. |
| tenantId | chaîne | ID de locataire de la ressource. |

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
