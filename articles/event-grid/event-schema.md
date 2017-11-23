---
title: "Schéma d’événement Azure Event Grid"
description: "Décrit les propriétés fournies pour les événements avec Azure Event Grid."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: babanisa
ms.openlocfilehash: caa709fdc2a59472ee812bde91f7300396aa5755
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema"></a>Schéma d’événement Azure Event Grid

Cet article décrit les propriétés et les schémas qui sont présents dans tous les événements. Les événements se composent de cinq propriétés de chaîne et d’un objet de données obligatoires. Les propriétés sont communes à tous les événements, quel que soit le serveur de publication. L’objet de données contient des propriétés spécifiques à chaque serveur de publication. Dans les rubriques du système, ces propriétés sont spécifiques au fournisseur de ressources tel que Stockage Azure ou Azure Event Hubs.

Les événements sont envoyés à Azure Event Grid sous forme de tableau qui peut contenir plusieurs objets d’événement. S’il n’existe qu’un seul événement, la longueur du tableau est de 1. La taille totale du tableau peut atteindre jusqu’à 1 Mo. Chaque événement du tableau est limité à 64 Ko.

## <a name="event-schema"></a>Schéma d’événement

L’exemple suivant montre les propriétés qui sont utilisées par tous les éditeurs d’événements :

```json
[
  {
    "topic": string,
    "subject": string,    
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    }
  }
]
```

Par exemple, voici le schéma publié pour un événement de stockage Blob Azure :

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
  }
]
```
 
## <a name="event-properties"></a>Propriétés d’événement

Tous les événements contiennent les mêmes données de niveau supérieur suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| rubrique | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. |
| subject | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| eventType | string | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | string | Identificateur unique de l’événement. |
| données | objet | Données d’événement spécifiques au fournisseur de ressources. |

Pour connaître les propriétés de l’objet de données, consultez la source de l’événement :

* [Abonnements Azure (opérations de gestion)](event-schema-subscriptions.md)
* [Stockage d’objets blob](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [Groupes de ressources (opérations de gestion)](event-schema-resource-groups.md)

Pour les rubriques personnalisées, l’éditeur d’événements détermine l’objet de données. Les données de premier niveau doivent contenir les mêmes champs que les événements standard définis par les ressources. Lors de la publication d’événements dans les rubriques personnalisées, pensez à modéliser l’objet de vos événements pour faciliter le routage et le filtrage.

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
