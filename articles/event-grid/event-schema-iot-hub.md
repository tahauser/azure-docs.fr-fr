---
title: "Schéma Azure Event Grid pour IoT Hub | Microsoft Docs"
description: "Page de référence pour le format de schéma des événements et les propriétés d’IoT Hub"
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 29ad1233a344c3085286c27cb925b2dc9fb41f7e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>Schéma des événements Azure Event Grid pour IoT Hub

Cet article fournit les propriétés et les schémas des événements IoT Hub. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md). 

## <a name="available-event-types"></a>Types d’événement disponibles

IoT Hub émet les types d’événements suivants :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publié quand un appareil est inscrit auprès d’un hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publié quand un appareil est supprimé d’un hub IoT. | 

## <a name="example-event"></a>Exemple d’événement

Les schémas pour les événements DeviceCreated et DeviceDeleted ont la même structure. Cet exemple d’événement montre le schéma d’un événement déclenché quand un appareil est inscrit auprès d’un hub IoT :

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Propriétés d’événement

Tous les événements contiennent les mêmes données de niveau supérieur : 

| Propriété | type | Description |
| -------- | ---- | ----------- |
| id | chaîne | Identificateur unique de l’événement. |
| rubrique | chaîne | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| subject | chaîne | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| eventType | chaîne | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | chaîne | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| données | objet | Données d’événement IoT Hub.  |
| dataVersion | chaîne | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | chaîne | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

Le contenu de l’objet de données est différent pour chaque serveur de publication d’événements. Pour les événements IoT Hub, l’objet de données contient les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| hubName | chaîne | Nom du hub IoT où l’appareil a été créé ou supprimé. |
| deviceId | chaîne | Identificateur unique de l’appareil. Cette chaîne qui respecte la casse peut contenir jusqu’à 128 caractères et prend en charge les caractères alphanumériques 7 bits ASCII, ainsi que les caractères spéciaux suivants :`- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| operationTimestamp | chaîne | Horodatage ISO8601 de l’opération |
| opType | chaîne | Type d’événement spécifié pour cette opération par le hub IoT : `DeviceCreated` ou `DeviceDeleted`.
| twin | objet | Informations sur le jumeau d’appareil, qui est la représentation cloud des métadonnées d’appareil de l’application. | 
| deviceID | chaîne | Identificateur unique du jumeau d’appareil. | 
| etag | chaîne | Information qui décrit le contenu du jumeau d’appareil. Chaque etag est unique pour chaque jumeau d’appareil. | 
| status | chaîne | Indique si le jumeau d’appareil est activé ou désactivé. | 
| statusUpdateTime | chaîne | Horodatage ISO8601 de la dernière mise à jour de l’état du jumeau d’appareil. |
| connectionState | chaîne | Indique si l’appareil est connecté ou déconnecté. | 
| lastActivityTime | chaîne | Horodatage ISO8601 de la dernière activité. | 
| cloudToDeviceMessageCount | integer | Nombre de messages cloud-à-appareil envoyés à cet appareil. | 
| authenticationType | chaîne | Type d’authentification utilisé pour cet appareil : `SAS`, `SelfSigned` ou `CertificateAuthority`. |
| x509Thumbprint | chaîne | L’empreinte numérique est une valeur unique pour le certificat x509, et sert généralement à rechercher un certificat particulier dans un magasin de certificats. L’empreinte numérique, générée dynamiquement à l’aide de l’algorithme SHA-1, n’existe pas physiquement dans le certificat. | 
| primaryThumbprint | chaîne | Empreinte numérique principale pour le certificat x509. |
| secondaryThumbprint | chaîne | Empreinte numérique secondaire pour le certificat x509. | 
| version | integer | Entier qui est incrémenté chaque fois que le jumeau d’appareil est mis à jour. |
| desired | objet | Une partie des propriétés qui peuvent être écrites uniquement par le backend d’application et lues par l’appareil. | 
| reported | objet | Une partie des propriétés qui peuvent être écrites uniquement par l’appareil et lues par le backend d’application. |
| lastUpdated | chaîne | Horodatage ISO8601 de la dernière mise à jour de la propriété du jumeau d’appareil. | 

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour en savoir plus sur le fonctionnement conjoint d’IoT Hub et d’Event Grid, consultez [Réagir aux événements IoT Hub en utilisant Event Grid pour déclencher des actions](../iot-hub/iot-hub-event-grid.md).