---
title: "Présentation de la messagerie de cloud-à-appareil Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur : comment utiliser la messagerie de cloud-à-appareil avec IoT Hub. Inclut des informations sur le cycle de vie des messages et les options de configuration."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2017
ms.author: dobett
ms.openlocfilehash: 1b34e579f2ba40f4d77f7a3ba1841f59f795d292
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Envoyer des messages cloud-à-appareil à partir d’IoT Hub

Pour envoyer des notifications unidirectionnelles à l’application pour appareil à partir du back-end de votre solution, envoyez des messages cloud-à-appareil depuis votre hub IoT vers votre appareil. Pour une description des autres options cloud-à-appareil prises en charge par IoT Hub, consultez [Conseils pour les communications cloud-à-appareil][lnk-c2d-guidance].

Vous envoyez les messages cloud-à-appareil via un point de terminaison côté service (**/messages/devicebound**). Un appareil reçoit ensuite les messages via un point de terminaison spécifique de l’appareil (**/devices/{IdAppareil}/messages/devicebound**).

Pour cibler chaque message cloud-à-appareil sur un seul appareil, IoT Hub définit la propriété **to** sur **/devices/{deviceId}/messages/devicebound**.

Chaque file d’attente d’appareil peut contenir jusqu’à 50 messages cloud-à-appareil. Les tentatives d’envoi d’un nombre supérieur de messages au même appareil entraînent une erreur.

## <a name="the-cloud-to-device-message-lifecycle"></a>Cycle de vie des messages cloud-à-appareil

Pour garantir une remise des messages au moins une fois, IoT Hub conserve les messages cloud-à-appareil dans des files d’attente par appareil. Les appareils doivent explicitement confirmer l’ *achèvement* afin que IoT Hub supprime les messages de la file d’attente. Cette approche garantit la résilience contre les échecs de connectivité et d’appareils.

L’illustration suivante présente le graphe d’état du cycle de vie d’un message cloud-à-appareil dans IoT Hub.

![Cycle de vie des messages cloud-à-appareil][img-lifecycle]

Quand le service IoT Hub envoie un message à un appareil, il définit l’état du message sur **Enqueued** (En file attente). Quand un appareil veut *recevoir* un message, IoT Hub *verrouille* le message (en définissant son état sur **Invisible**), autorisant ainsi d’autres threads sur l’appareil à commencer à recevoir d’autres messages. Quand un thread d’appareil a fini de traiter un message, il notifie IoT Hub en *terminant* le message. Ensuite, IoT Hub définit l’état sur **Completed** (Terminé).

Un appareil peut également choisir de :

* *Rejeter* le message, ce qui amène IoT Hub à lui attribuer l’état **Dead lettered** (Lettre morte). Les appareils qui se connectent via le protocole MQTT ne peuvent pas rejeter les messages cloud-à-appareil.
* *Abandonner* le message, ce qui amène IoT Hub à replacer le message dans la file d’attente avec l’état **Enqueued**(En file attente). Les appareils qui se connectent par le biais du protocole MQTT ne peuvent pas abandonner les messages cloud-à-appareil.

Il est possible qu’un thread ne parvienne pas à traiter un message sans en avertir IoT Hub. Dans ce cas, les messages passent automatiquement de l’état **Invisible** à l’état **Enqueued** (En file d’attente) après un *délai d’attente de visibilité (ou de verrouillage)*. La valeur par défaut de ce délai est d’une minute.

La propriété **nombre maximal de remises** sur IoT Hub détermine le nombre maximal de fois qu’un message peut passer de l’état **En file d’attente** à l’état **Invisible**, et inversement. Une fois ce nombre de transitions atteint, IoT Hub attribue au message l’état **Dead lettered** (Lettre morte). De même, IoT Hub attribue à un message l’état **Dead lettered** (Lettre morte) à l’issue de son délai d’expiration (consultez [Durée de vie][lnk-ttl]).

La page [How to send cloud-to-device messages with IoT Hub][lnk-c2d-tutorial] (Guide pratique pour envoyer des messages cloud-à-appareil avec IoT Hub) explique comment envoyer des messages cloud-à-appareil depuis le cloud et comment les recevoir sur un appareil.

Généralement, un appareil achève un message cloud-à-appareil quand la perte du message n’affecte pas la logique d’application. C’est par exemple le cas quand l’appareil a rendu persistant le contenu du message localement ou qu’il a exécuté une opération correctement. Le message peut également transporter des informations temporaires, dont la perte n’aurait aucun impact sur les fonctionnalités de l’application. Quelquefois, pour les tâches longues, vous pouvez :

* Terminer le message cloud-à-appareil après la conservation de la description de la tâche dans le stockage local.
* Notifier le backend de la solution à l’aide d’un ou de plusieurs messages appareil-à-cloud à différents stades de la progression de la tâche.

## <a name="message-expiration-time-to-live"></a>Expiration du message (durée de vie)

Chaque message cloud-à-appareil est doté d’un délai d’expiration. Cette durée est définie par un des éléments suivants :

* La propriété **ExpiryTimeUtc** dans le service.
* IoT Hub utilisant la valeur *durée de vie* par défaut spécifiée comme propriété IoT Hub.

Consultez [Options de configuration cloud-à-appareil][lnk-c2d-configuration].

Un moyen courant de tirer parti de l’expiration du message et d’éviter l’envoi de messages à des appareils déconnectés consiste à définir des valeurs de durée de vie courtes. Cette approche produit le même résultat que la gestion de l’état de connexion de l’appareil, tout en étant plus efficace. Lorsque vous demandez des accusés de réception de messages, IoT Hub vous notifie quels sont les appareils :

* En mesure de recevoir des messages.
* Qui ne sont pas en ligne ou qui ont échoué.

## <a name="message-feedback"></a>Commentaires de messages

Lorsque vous envoyez un message cloud-à-appareil, le service peut demander la remise d’un commentaire de message concernant l’état final de ce message.

| Propriété Ack | Comportement |
| ------------ | -------- |
| **positive** | Si le message cloud-à-appareil prend l’état **Completed** (Terminé), IoT Hub génère un message de commentaire. |
| **negative** | Si le message cloud-à-appareil prend l’état **Dead lettered** (Lettre morte), IoT Hub génère un message de commentaire. |
| **full**     | IoT Hub génère un message de commentaires dans les deux cas. |

Si la propriété **Ack** est définie sur **full** et que vous ne recevez pas de message de commentaire, cela signifie que le message de commentaire a expiré. Le service ne peut pas savoir ce qui est arrivé au message d’origine. Dans la pratique, un service doit s'assurer qu'il peut traiter les commentaires avant leur expiration. Le délai d’expiration maximal est de deux jours, ce qui vous laisse le temps de faire refonctionner le service en cas de défaillance.

Comme l’explique la section [Points de terminaison][lnk-endpoints], IoT Hub fournit des commentaires sous la forme de messages par le biais d’un point de terminaison accessible au service (**/messages/servicebound/feedback**). La sémantique de réception des commentaires est identique à celle des messages cloud-à-appareil, et présente le même [cycle de vie des messages][lnk-lifecycle]. Chaque fois que c’est possible, des commentaires de messages sont mis en lot dans un seul message, au format suivant :

| Propriété     | Description |
| ------------ | ----------- |
| EnqueuedTime | Horodatage indiquant la date et l’heure de création du message. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Le corps est un tableau sérialisé JSON des enregistrements, chacun disposant des propriétés suivantes :

| Propriété           | Description |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Horodatage indiquant la date et l’heure du résultat du message. Par exemple, l’achèvement de l’appareil ou l’expiration du message. |
| OriginalMessageId  | **MessageId** du message cloud-à-appareil auquel se rapportent ces informations de commentaires. |
| StatusCode         | Chaîne obligatoire. Utilisé dans les messages de commentaires générés par IoT Hub. <br/> « Succès » <br/> « Expiré » <br/> « DeliveryCountExceeded »  <br/> « Rejeté » <br/> « Vidé » |
| Description        | Valeurs de chaîne pour **StatusCode**. |
| deviceId           | **DeviceId** de l’appareil cible du message cloud-à-appareil auquel se rapporte ce commentaire. |
| DeviceGenerationId | **DeviceGenerationId** de l’appareil cible du message cloud-à-appareil auquel se rapporte ce commentaire. |

Le service doit spécifier un **MessageId** pour le message cloud-à-appareil afin de pouvoir mettre en corrélation ses commentaires avec le message d’origine.

L’exemple suivant montre le corps d’un message de commentaire.

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Options de configuration cloud-à-appareil

Chaque hub IoT expose les options de configuration suivantes pour la messagerie cloud-à-appareil :

| Propriété                  | Description | Plage et valeur par défaut |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Durée de vie par défaut pour les messages cloud-à-appareil. | Intervalle ISO_8601 jusqu’à 2D (minimum 1 minute). Par défaut : 1 heure. |
| maxDeliveryCount          | Nombre de remises maximal pour les files d’attente par appareil cloud-à-appareil | 1 à 100. Par défaut : 10. |
| feedback.ttlAsIso8601     | Rétention des messages de commentaires liés au service. | Intervalle ISO_8601 jusqu’à 2D (minimum 1 minute). Par défaut : 1 heure. |
| feedback.maxDeliveryCount |Nombre de remises maximal pour la file d’attente de commentaires. | 1 à 100. Par défaut : 100. |

Pour plus d’informations sur la définition de ces options de configuration, consultez [Create IoT hubs][lnk-portal] (Créer des hubs IoT).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les SDK que vous pouvez utiliser pour recevoir des messages cloud-à-appareil, consultez [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks].

Pour essayer de recevoir des messages cloud-à-appareil, consultez le didacticiel [Send cloud-to-device][lnk-c2d-tutorial] (Envoyer des messages cloud-à-appareil).

[img-lifecycle]: ./media/iot-hub-devguide-messages-c2d/lifecycle.png

[lnk-portal]: iot-hub-create-through-portal.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-ttl]: #message-expiration-time-to-live
[lnk-c2d-configuration]: #cloud-to-device-configuration-options
[lnk-lifecycle]: #message-lifecycle
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
