---
title: "Schéma d’événements Azure Event Grid pour Service Bus"
description: "Décrit les propriétés qui sont fournies pour les événements Service Bus avec Azure Event Grid"
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: article
ms.date: 02/21/2018
ms.author: babanisa
ms.openlocfilehash: 72780bff3807534efb456a9a7998f7d4de3c6f12
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Schéma des événements Azure Event Grid pour Service Bus

Cet article décrit les propriétés et le schéma des événements Service Bus. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).

## <a name="available-event-types"></a>Types d’événement disponibles

Service Bus émet les types d’événements suivants :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Événement levé lorsque des messages actifs sont présents dans une file d’attente ou un abonnement et qu’aucun récepteur n’est à l’écoute. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Événement levé lorsque des messages actifs sont présents dans une file d’attente de lettres mortes et qu’aucun récepteur n’est actif. |

## <a name="example-event"></a>Exemple d’événement

L’exemple suivant montre le schéma d’un événement avec des messages actifs sans récepteurs :

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Le schéma pour un événement de file d’attente de lettres mortes est similaire :

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
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
| données | objet | Données d’événement de stockage Blob. |
| dataVersion | chaîne | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | chaîne | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| nameSpaceName | chaîne | Espace de noms Service Bus dans lequel figure la ressource. |
| requestUri | chaîne | URI vers la file d’attente spécifique ou l’abonnement qui génère l’événement. |
| entityType | chaîne | Type d’entité Service Bus générant des événements (file d’attente ou abonnement). |
| queueName | chaîne | File d’attente contenant des messages actives en cas d’abonnement à une file d’attente. Valeur null si des rubriques / abonnements sont utilisés. |
| topicName | chaîne | Rubrique à laquelle appartient l’abonnement Service Bus contenant les messages actifs. Valeur null si une file d’attente est utilisée. |
| subscriptionName | chaîne | Abonnement Service Bus contenant les messages actifs. Valeur null si une file d’attente est utilisée. |

## <a name="next-steps"></a>étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
* Pour plus d’informations sur l’utilisation d’Azure Event Grid avec Service Bus, consultez la page [Vue d’ensemble de l’intégration d’Azure Service Bus et Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Essayez de [recevoir des événements Service Bus avec Functions ou Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
