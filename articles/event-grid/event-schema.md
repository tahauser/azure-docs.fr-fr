---
title: Schéma d’événement Azure Event Grid
description: Décrit les propriétés fournies pour les événements avec Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 03/22/2018
ms.author: babanisa
ms.openlocfilehash: 7af0e1cc8ae36774ef1cebf1bada6477888860d0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-event-grid-event-schema"></a>Schéma d’événement Azure Event Grid

Cet article décrit les propriétés et les schémas qui sont présents dans tous les événements. Les événements se composent de cinq propriétés de chaîne et d’un objet de données obligatoires. Les propriétés sont communes à tous les événements, quel que soit le serveur de publication. L’objet de données contient des propriétés spécifiques à chaque serveur de publication. Dans les rubriques du système, ces propriétés sont spécifiques au fournisseur de ressources tel que Stockage Azure ou Azure Event Hubs.

Les événements sont envoyés à Azure Event Grid sous forme de tableau qui peut contenir plusieurs objets d’événement. S’il n’existe qu’un seul événement, la longueur du tableau est de 1. La taille totale du tableau peut atteindre jusqu’à 1 Mo. Chaque événement du tableau est limité à 64 Ko.

Vous trouverez le schéma JSON pour l’événement Event Grid et la charge utile des données de chaque serveur de publication Azure dans le [magasin de schémas d’événement](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

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
    },
    "dataVersion": string,
    "metadataVersion": string
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
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Propriétés d’événement

Tous les événements contiennent les mêmes données de niveau supérieur suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| rubrique | chaîne | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| subject | chaîne | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| eventType | chaîne | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | chaîne | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | chaîne | Identificateur unique de l’événement. |
| données | objet | Données d’événement spécifiques au fournisseur de ressources. |
| dataVersion | chaîne | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | chaîne | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

Pour connaître les propriétés de l’objet de données, consultez la source de l’événement :

* [Abonnements Azure (opérations de gestion)](event-schema-subscriptions.md)
* [Stockage Blob](event-schema-blob-storage.md)
* [Concentrateurs d'événements](event-schema-event-hubs.md)
* [Service Bus](event-schema-service-bus.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Groupes de ressources (opérations de gestion)](event-schema-resource-groups.md)

Pour les rubriques personnalisées, l’éditeur d’événements détermine l’objet de données. Les données de premier niveau doivent contenir les mêmes champs que les événements standard définis par les ressources.

Lorsque vous publiez des événements dans des rubriques personnalisées, créez des objets pour vos événements permettant aux abonnés de savoir facilement si l’événement les intéresse. Les abonnés utilisent l’objet pour filtrer et router des événements. Envisagez de fournir le chemin d’accès à l’origine de l’événement, de sorte que les abonnés puissent filtrer par segments de ce chemin d’accès. Le chemin d’accès permet aux abonnés de filtrer les événements avec précision ou à grande échelle. Par exemple, si vous fournissez un chemin d’accès de trois segments comme `/A/B/C` dans l’objet, les abonnés peuvent filtrer par le premier segment `/A` pour obtenir un vaste ensemble d’événements. Ces abonnés obtiennent des événements avec des objets tels que `/A/B/C` ou `/A/D/E`. Les autres abonnés peuvent filtrer par `/A/B` pour obtenir un ensemble plus restreint d’événements.

Votre objet a parfois besoin d’être plus précis sur ce qui est arrivé. Par exemple, le serveur de publication **Comptes de stockage** fournit l’objet `/blobServices/default/containers/<container-name>/blobs/<file>` lorsqu’un fichier est ajouté à un conteneur. Un abonné peut filtrer par le chemin d’accès `/blobServices/default/containers/testcontainer` pour obtenir tous les événements pour ce conteneur, mais pas pour d’autres conteneurs du compte de stockage. Un abonné peut également filtrer ou router par le suffixe `.txt` pour n’utiliser que des fichiers texte.

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
