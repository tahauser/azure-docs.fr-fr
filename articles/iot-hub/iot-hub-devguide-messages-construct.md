---
title: "Présentation du format des messages Azure IoT Hub | Microsoft Docs"
description: "Le guide du développeur décrit le format et le contenu attendu des messages IoT Hub."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 3d5b500964ee37dbd347858edd35812e1d217499
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="create-and-read-iot-hub-messages"></a>Créer et lire des messages IoT Hub

Pour prendre en charge une interopérabilité transparente entre les différents protocoles, IoT Hub définit un format de message commun pour tous les protocoles d’appareil. Ce format est utilisé pour les messages [appareil-à-cloud][lnk-d2c] et [cloud-à-appareil][lnk-c2d]. Un [message IoT Hub][lnk-messaging] comprend les éléments suivants :

* Un ensemble de *propriétés système*. Propriétés interprétées ou définies par IoT Hub. Cet ensemble est prédéterminé.
* Un ensemble de *propriétés de l’application*. Dictionnaire de propriétés de chaîne que l’application peut définir et auquel elle peut accéder sans avoir à désérialiser le corps du message. IoT Hub ne modifie jamais ces propriétés.
* Un corps binaire opaque.

Les valeurs et les noms de propriétés peuvent contenir uniquement des caractères alphanumériques ASCII, plus les caractères ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}```, quand vous effectuez les opérations suivantes :  

* Envoyer des messages appareil-à-cloud à l’aide du protocole HTTPS.
* Envoyer des messages cloud-à-appareil.

Pour plus d’informations sur l’encodage et le décodage des messages envoyés à l’aide de différents protocoles, consultez [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks].

Le tableau suivant répertorie l’ensemble des propriétés système dans les messages IoT Hub.

| Propriété | Description |
| --- | --- |
| MessageId |Identificateur correspondant au message défini par l’utilisateur utilisé pour les modèles demande-réponse. Format : une chaîne qui respecte la casse (jusqu’à 128 caractères) de caractères alphanumériques 7 bits ASCII + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Numéro de séquence |Un numéro (unique par file d’attente d’appareil) affecté par IoT Hub à chaque message cloud-à-appareil. |
| À |Une destination spécifiée dans les messages [cloud-à-appareil][lnk-c2d]. |
| ExpiryTimeUtc |Date et heure d’expiration du message. |
| EnqueuedTime |Date et heure de réception du message [Cloud-à-appareil][lnk-c2d] par IoT Hub. |
| CorrelationId |Une propriété de chaîne d’un message de réponse qui contient généralement l'ID du message de la demande dans les modèles demande-réponse. |
| UserId |Un ID utilisé pour spécifier l’origine des messages. Lorsque des messages sont générés par IoT Hub, la propriété est définie sur `{iot hub name}`. |
| Ack |Un générateur de messages de commentaires. Cette propriété est utilisée dans les messages cloud-à-appareil pour demander à IoT Hub de générer des messages de commentaires à la suite de la consommation du message par l’appareil. Valeurs possibles : **none** (par défaut) : aucun message de commentaires n’est généré ; **positive** : recevoir un message de commentaires si le message est achevé ; **negative** : recevoir un message de commentaires si le message a expiré (ou si le nombre maximal de remises a été atteint) sans être achevé par l’appareil, ou **full** : propriétés à la fois positive et négative. Pour plus d’informations, consultez [Commentaires sur les messages][lnk-feedback]. |
| ConnectionDeviceId |Un ID défini par IoT Hub sur les messages appareil-à-cloud. Elle contient la propriété **deviceId** de l’appareil qui a envoyé le message. |
| ConnectionDeviceGenerationId |Un ID défini par IoT Hub sur les messages appareil vers cloud. Elle contient la propriété **generationId** (conformément aux [Propriétés d’identité des appareils][lnk-device-properties]) de l’appareil qui a envoyé le message. |
| ConnectionAuthMethod |Une méthode d’authentification définie par IoT Hub sur les messages appareil-à-cloud. Cette propriété contient des informations sur la méthode d’authentification utilisée pour authentifier l’appareil qui a envoyé le message. Pour plus d’informations, consultez [Prévention d’usurpation d’identité entre un appareil et le cloud][lnk-antispoofing]. |

## <a name="message-size"></a>Taille des messages

IoT Hub mesure la taille des messages d’une façon indépendante du protocole, en considérant uniquement la charge utile réelle. La taille en octets est calculée comme la somme des éléments suivants :

* La taille du corps en octets.
* La taille en octets de toutes les valeurs des propriétés système du message.
* La taille en octets de tous les noms et valeurs des propriétés des utilisateurs.

Les noms et les valeurs des propriétés sont limités aux caractères ASCII ; ainsi, la longueur des chaînes est égale à la taille en octets.

## <a name="next-steps"></a>étapes suivantes

Pour plus d’informations sur les limites de taille des messages dans IoT Hub, consultez [Quotas et limitation IoT Hub][lnk-quotas].

Pour savoir comment créer et lire des messages IoT Hub dans divers langages de programmation, consultez les didacticiels de [prise en main][lnk-get-started].

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
