---
title: "Schéma d’événement d’abonnement Azure Event Grid"
description: "Décrit les propriétés fournies pour les événements d’abonnement avec Azure Event Grid."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 7dc10d0cc73960fac4759a0cebec8d294cf1b463
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
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
  }
}]
```

## <a name="event-properties"></a>Propriétés d’événement

Un événement comporte les données de niveau supérieur suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| rubrique | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. |
| subject | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| eventType | string | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | string | Identificateur unique de l’événement. |
| données | objet | Données d’événement d’abonnement. |

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| autorisation | string | Autorisation demandée pour l’opération. |
| réclamations | string | Propriétés des revendications. |
| correlationId | string | ID d’opération pour le dépannage. |
| httpRequest | string | Détails de l’opération. |
| resourceProvider | string | Fournisseur de ressources qui effectue l’opération. |
| resourceUri | string | URI de la ressource dans l’opération. |
| operationName | string | Opération effectuée. |
| status | string | L’état de l’opération. |
| subscriptionId | string | ID d’abonnement de la ressource. |
| tenantId | string | ID de locataire de la ressource. |

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
