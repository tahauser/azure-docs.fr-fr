---
title: Bien comprendre le chargement de fichiers Azure IoT Hub | Microsoft Docs
description: "Guide du développeur - utilisez la fonctionnalité de chargement de fichier de IoT Hub pour gérer le chargement de fichiers depuis un appareil vers un conteneur d’objets blob de stockage Azure."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a0427925-3e40-4fcd-96c1-2a31d1ddc14b
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 7bf1ba333f36dcfa8959320566bcb771f37cfe22
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="upload-files-with-iot-hub"></a>Chargement de fichiers avec IoT Hub

Comme nous l’avons expliqué dans l’article [Référence - Points de terminaison IoT Hub][lnk-endpoints], un appareil peut initier un chargement de fichiers en envoyant une notification par le biais d’un point de terminaison côté appareil (**/devices/{deviceId}/files**). Lorsqu’un appareil indique à IoT Hub la fin d’un chargement, IoT Hub envoie un message de notification de chargement de fichiers via le point de terminaison côté service (**/messages/servicebound/filenotifications**).

Au lieu de distribuer les messages via sa propre plate-forme, IoT Hub joue le rôle de répartiteur vers un compte Stockage Azure associé. Un appareil demande à IoT Hub un jeton de stockage spécifique au fichier que l’appareil souhaite télécharger. L’appareil utilise l’URI SAP pour télécharger le fichier vers le stockage. Une fois le téléchargement terminé, l’appareil envoie une notification à IoT Hub pour l’en informer. IoT Hub vérifie que le chargement de fichiers est terminé, puis ajoute un message de notification de chargement de fichiers au point de terminaison côté service dédié à la notification de fichiers.

Avant de charger un fichier vers IoT Hub à partir d’un appareil, vous devez configurer votre hub en [l’associant à un compte de stockage Azure][lnk-associate-storage].

Votre appareil peut ensuite [initialiser un chargement][lnk-initialize] puis [notifier IoT Hub][lnk-notify] lorsque le chargement est terminé. Éventuellement, lorsqu’un appareil notifie IoT Hub que le chargement est terminé, le service peut générer un [message de notification][lnk-service-notification].

### <a name="when-to-use"></a>Quand utiliser

Utilisez le chargement des fichiers pour envoyer des fichiers multimédias et de gros traitements télémétriques par lots chargés par des appareils connectés par intermittence ou compressés pour économiser de la bande passante.

Reportez-vous à [l’aide sur la communication appareil-à-cloud][lnk-d2c-guidance] en cas de doute entre l’utilisation des propriétés signalées, des messages appareil-à-cloud ou du chargement de fichiers.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Association d’un compte Stockage Azure à IoT Hub

Pour utiliser la fonctionnalité de téléchargement de fichier, vous devez d’abord lier un compte Stockage Azure à IoT Hub. Vous pouvez terminer ce travail en utilisant le [Portail Azure][lnk-management-portal], ou en exécutant un programme par le biais de [l’API REST de fournisseur de ressources IoT Hub][lnk-resource-provider-apis]. Une fois que vous avez associé un compte Stockage Azure à IoT Hub, le service retourne un URI SAP vers un appareil lorsque ce dernier initie une demande de téléchargement de fichier.

> [!NOTE]
> Les kits [Azure IoT SDK][lnk-sdks] gèrent automatiquement la récupération de l’URI SAP, le chargement du fichier et l’envoi d’une notification à IoT Hub pour l’informer de la fin du chargement.


## <a name="initialize-a-file-upload"></a>Initialiser un téléchargement de fichier
IoT Hub a un point de terminaison spécifique aux appareils pour demander une URI SAS pour le stockage afin de télécharger un fichier. Pour initier le processus de chargement de fichiers, l’appareil envoie une requête POST à `{iot hub}.azure-devices.net/devices/{deviceId}/files` avec le corps JSON suivant :

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub renvoie les données suivantes. L’appareil l’utilise pour télécharger le fichier :

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Déconseillé : initialiser un téléchargement de fichier avec une commande GET

> [!NOTE]
> Cette section décrit les fonctionnalités déconseillées pour la réception d’une URI SAS d’IoT Hub. Utilisez la méthode POST décrite précédemment.

IoT Hub utilise deux points de terminaison REST pour prendre en charge le téléchargement de fichier, le premier afin d’obtenir l’URI SAP pour le stockage et le second pour informer IoT hub de la fin du téléchargement. L’appareil lance le processus de téléchargement de fichier en envoyant une commande GET à IoT Hub à `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. L’IoT Hub renvoie :

* Un URI SAS spécifique au fichier à charger.
* Un ID de corrélation à utiliser une fois le chargement terminé.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notifier IoT Hub de la fin du téléchargement d’un fichier

L’appareil est chargé de télécharger le fichier vers le stockage à l’aide des kits SDK Stockage Azure. Une fois le chargement terminé, l’appareil envoie une requête POST à `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` avec le corps JSON suivant :

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

La valeur de `isSuccess` est une valeur booléenne indiquant si le fichier a été téléchargé avec succès. Le code d’état de `statusCode` est l’état pour le téléchargement du fichier vers le stockage et `statusDescription` correspond à `statusCode`.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence suivantes vous fournissent des informations supplémentaires sur le téléchargement de fichiers depuis un appareil.

## <a name="file-upload-notifications"></a>Notifications de téléchargement de fichier

Lorsqu’un appareil informe IoT Hub de la fin d’un chargement, IoT Hub peut éventuellement générer un message de notification contenant le nom et l’emplacement de stockage du fichier.

Comme l’explique la section [Points de terminaison][lnk-endpoints], IoT Hub fournit des notifications de chargement de fichiers sous la forme de messages par le biais d’un point de terminaison côté service (**/messages/servicebound/fileuploadnotifications**). La sémantique de réception des notifications de chargement de fichiers est identique à celle des messages cloud-à-appareil et présente le même [cycle de vie des messages][lnk-lifecycle]. Chaque message récupéré à partir du point de terminaison de notification de téléchargement de fichier est un enregistrement JSON qui possède les propriétés suivantes :

| Propriété | Description |
| --- | --- |
| EnqueuedTimeUtc |Horodatage indiquant la date et l’heure de création de la notification. |
| deviceId |**DeviceId** de l’appareil qui a téléchargé le fichier. |
| BlobUri |URI du fichier téléchargé. |
| BlobName |Nom du fichier téléchargé. |
| LastUpdatedTime |Horodatage indiquant la date et l’heure de dernière mise à jour du fichier. |
| BlobSizeInBytes |Taille du fichier téléchargé. |

**Exemple**. Voici un exemple illustrant le corps de message de notification de téléchargement de fichier.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Options de configuration de notification de téléchargement de fichier

Chaque IoT Hub expose les options de configuration suivantes pour les notifications de téléchargement de fichier :

| Propriété | Description | Plage et valeur par défaut |
| --- | --- | --- |
| **enableFileUploadNotifications** |Indique si les notifications de téléchargement de fichier sont écrites dans le point de terminaison de notification de fichier. |Valeur booléenne. Par défaut : True. |
| **fileNotifications.ttlAsIso8601** |Durée de vie par défaut des notifications de téléchargement de fichier. |Intervalle ISO_8601 jusqu’à 48h (minimum 1 minute). Par défaut : 1 heure. |
| **fileNotifications.lockDuration** |Durée de verrouillage de la file d’attente des notifications de téléchargement de fichiers. |5 à 300 secondes (5 secondes au minimum). Par défaut : 60 secondes. |
| **fileNotifications.maxDeliveryCount** |Nombre maximal de diffusions pour la file d’attente de notification de téléchargement de fichier. |1 à 100. Par défaut : 100. |

## <a name="additional-reference-material"></a>Matériel de référence supplémentaire

Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* La rubrique [Points de terminaison IoT Hub][lnk-endpoints] décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion.
* La rubrique [Référence - Quotas et limitation IoT Hub][lnk-quotas] décrit les quotas et le comportement de limitation qui s’appliquent au service IoT Hub.
* La section [Azure IoT device et service SDK][lnk-sdks] répertorie les Kits de développement logiciel (SDK) en différents langages que vous pouvez utiliser pour le développement d’applications d’appareil et de service qui interagissent avec IoT Hub.
* La rubrique [Référence - Langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages][lnk-query] décrit le langage de requête permettant de récupérer à partir d’IoT Hub des informations sur des jumeaux d’appareil et des travaux.
* La rubrique [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt] fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous savez comment télécharger des fichiers depuis des appareils avec IoT Hub, vous serez peut-être intéressé par les rubriques suivantes du Guide du développeur IoT :

* [Gérer les identités des appareils dans IoT Hub][lnk-devguide-identities]
* [Contrôler l’accès à IoT Hub][lnk-devguide-security]
* [Utiliser des représentations d’appareil pour synchroniser les données d’état et de configuration][lnk-devguide-device-twins]
* [Appeler une méthode directe sur un appareil][lnk-devguide-directmethods]
* [Planifier des travaux sur plusieurs appareils][lnk-devguide-jobs]

Pour mettre en pratique certains des concepts décrits dans cet article, consultez le didacticiel IoT Hub suivant :

* [Charger des fichiers à partir d’appareils vers le cloud avec IoT Hub][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
