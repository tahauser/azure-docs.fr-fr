---
title: "Comprendre le registre des identités d’Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur - Description du registre des identités IoT Hub et de la manière de l’utiliser pour gérer vos appareils. Contient des informations sur l’importation et l’exportation d’identités d’appareils en bloc."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0706eccd-e84c-4ae7-bbd4-2b1a22241147
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50020f007096b45b843515ff765e40c550fcf4e3
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Comprendre le registre des identités dans votre IoT Hub

Chaque IoT Hub a un registre des identités contenant des informations sur les appareils autorisés à se connecter à l’IoT Hub. Pour qu’un appareil puisse se connecter à un Hub, une entrée correspondant à cet appareil doit figurer dans le registre des identités de l’IoT Hub. Un appareil doit également s’authentifier auprès de l’IoT Hub à l’aide des informations d’identification stockées dans le registre des identités.

L’ID d’appareil stocké dans le registre des identités respecte la casse.

À un niveau supérieur, le registre des identités est une collection compatible REST de ressources d’identité d’appareil. Lorsque vous ajoutez une entrée au registre des identités, IoT Hub crée un jeu de ressources par appareil, comme une file d’attente contenant des messages cloud vers appareil en transit.

Utilisez le registre des identités lorsque vous avez besoin d’effectuer les actions suivantes :

* Approvisionner des appareils qui se connectent à votre IoT Hub.
* Contrôler l’accès par appareil aux points de terminaison côté appareil de votre concentrateur.

> [!NOTE]
> Le registre des identités ne contient pas de métadonnées spécifiques de l’application.

## <a name="identity-registry-operations"></a>Opérations du registre d’identité

Le registre des identités IoT Hub expose les opérations suivantes :

* Création d’une identité d’appareil
* Mise à jour d’une identité d’appareil
* Récupération d’une identité d’appareil par ID
* Suppression d’une identité d’appareil
* Création de listes contenant jusqu’à 1 000 identités
* Exportation de toutes les identités vers le stockage Blob Azure
* Importation de toutes les identités depuis le stockage Blob Azure

Toutes ces opérations peuvent utiliser un accès concurrentiel optimiste, comme spécifié dans [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> La seule façon de récupérer toutes les identités dans une registre des identités d’IoT Hub consiste à utiliser la fonctionnalité [Exporter][lnk-export].

Un registre des identités IoT Hub :

* ne contient pas de métadonnées de l’application ;
* est accessible en tant que dictionnaire à l’aide de la clé **deviceId** .
* ne prend pas en charge les requêtes expressives.

Une solution IoT possède généralement une zone de stockage distincte spécifique à la solution qui contient les métadonnées propres à l’application. Dans une solution de développement intelligente, par exemple, la zone de stockage spécifique à la solution doit enregistrer l’espace dans lequel un capteur de température sera déployé.

> [!IMPORTANT]
> Utilisez le registre des identités uniquement pour les opérations de gestion et d’approvisionnement. Les opérations à haut débit ne doivent pas dépendre de l’exécution d’opérations dans le registre des identités au moment de leur exécution. Par exemple, la vérification de l’état de la connexion d’un appareil avant l’envoi d’une commande n’est pas un modèle pris en charge. Veillez à vérifier les [taux de limitation][lnk-quotas] pour le registre des identités et le modèle de [pulsation de l’appareil][lnk-guidance-heartbeat].

## <a name="disable-devices"></a>Désactivation d’appareils

Vous pouvez désactiver les appareils en mettant à jour la propriété **status** d’une identité dans le registre des identités. Généralement, cette propriété est utilisée dans deux scénarios :

* Au cours d’un processus d’orchestration d’approvisionnement. Pour plus d’informations, voir [Approvisionnement des appareils][lnk-guidance-provisioning].
* Si, pour une raison quelconque, vous pensez qu’un appareil est compromis ou non autorisé.

## <a name="import-and-export-device-identities"></a>Importer et exporter les identités des appareils

Utilisez des opérations asynchrones sur le [point de terminaison de fournisseur de ressources IoT Hub][lnk-endpoints] pour exporter des identités d’appareils en bloc à partir du registre des identités d’un IoT Hub. Les exportations sont des tâches à long terme qui utilisent un conteneur d’objets blob fourni par le client pour enregistrer les données relatives à l’identité des appareils lues dans le registre des identités.

Utilisez des opérations asynchrones sur le [point de terminaison de fournisseur de ressources IoT Hub][lnk-endpoints] pour importer des identités d’appareils en bloc vers le registre des identités d’un IoT Hub. Les importations sont des tâches à long terme qui utilisent des données dans un conteneur d’objets blob, fourni par le client, pour écrire les données relatives à l’identité des appareils dans le registre des identités.

Pour plus d’informations sur l’importation et l’exportation d’API, consultez [IoT Hub - API REST de fournisseur de ressources][lnk-resource-provider-apis]. Pour en savoir plus sur l’exécution de tâches d’importation et d’exportation, voir [Gestion en bloc des identités d’appareils IoT Hub][lnk-bulk-identity].

## <a name="device-provisioning"></a>Approvisionnement des appareils

Les données d’appareil qu’une solution IoT donnée stocke dépendent des exigences spécifiques de cette solution. Mais une solution doit au minimum stocker les clés d’authentification et les identités des appareils. Azure IoT Hub inclut un registre d’identité qui peut stocker des valeurs pour chaque appareil, comme les ID, les clés d’authentification et les codes d’état. Une solution peut utiliser d’autres services Azure, tels que le stockage de tables, le stockage Blob ou Cosmos DB pour stocker des données d’appareil supplémentaires.

*Approvisionnement des appareils* est le processus d'ajout des données d'appareil initial aux magasins dans votre solution. Pour permettre à un nouvel appareil de se connecter à votre hub, vous devez ajouter un ID et des clés d’appareil au registre des identités d’IoT Hub. Dans le cadre du processus d’approvisionnement, vous devrez peut-être initialiser les données spécifiques à l’appareil dans d’autres magasins de la solution. Vous pouvez également utiliser le service de d’approvisionnement des appareils IoT Hub pour activer l’approvisionnement sans contact et juste-à-temps vers un ou plusieurs hubs IoT sans qu’une intervention humaine soit nécessaire. Pour plus d’informations, consultez la [documentation relative au service d’approvisionnement][lnk-dps].

## <a name="device-heartbeat"></a>Pulsation des appareils

Le registre des identités IoT Hub contient un champ appelé **connectionState**. Vous ne devez utiliser le champ **connectionState** que pendant le développement et le débogage. Les solutions IoT ne doivent pas interroger le champ au moment de l’exécution. Par exemple, n’interrogez pas le champ **connectionState** pour vérifier si un appareil est connecté avant d’envoyer un message cloud vers appareil ou un SMS.

Si votre solution IoT a besoin de savoir si un appareil est connecté, vous devez implémenter le *modèle de pulsation*.

Dans le modèle par pulsations, l’appareil envoie des messages appareil-à-cloud au moins une fois par durée fixe (par exemple, au moins une fois par heure). Ainsi, même si un appareil n’a pas de données à envoyer, il envoie toujours un message appareil-à-cloud vide (généralement avec une propriété qui l’identifie comme pulsation). Côté service, la solution gère un mappage avec la dernière pulsation reçue pour chaque appareil. Si la solution ne reçoit pas de message de pulsation dans le temps imparti de la part de l’appareil, elle suppose qu’il y a un problème avec l’appareil.

Une implémentation plus complexe pourrait inclure les informations de la [surveillance des opérations][lnk-devguide-opmon] pour identifier les appareils qui ne parviennent pas à se connecter ou à communiquer. Quand vous implémentez le modèle par pulsations, veillez à vérifier les [quotas et limitations IoT Hub][lnk-quotas].

> [!NOTE]
> Si une solution IoT utilise uniquement l’état de la connexion de l’appareil pour déterminer si elle doit envoyer des messages cloud vers appareil, et que ces messages ne sont pas diffusés à de larges groupes d’appareils, envisagez d’utiliser un modèle de *délai d’expiration court* plus simple. Ce modèle permet d’obtenir le même résultat qu’en maintenant l’état de la connexion de l’appareil avec sa pulsation, tout en étant plus efficace. Si vous demandez des accusés de réception des messages, IoT Hub peut vous informer sur les appareils en mesure de recevoir des messages et ceux qui ne le sont pas.

## <a name="device-lifecycle-notifications"></a>Notifications de cycle de vie des appareils

IoT Hub peut avertir votre solution IoT lorsqu’une identité d’appareil est créée ou supprimée, en envoyant des notifications de cycle de vie des appareils. Pour ce faire, votre solution IoT doit créer un itinéraire et définir la source de données *DeviceLifecycleEvents*. Par défaut, aucune notification du cycle de vie n’est envoyée. Autrement dit, aucun itinéraire n’existe préalablement. Le message de notification inclut le corps et les propriétés.

Propriétés : les propriétés système du message ont pour préfixe le symbole `'$'`.

| NOM | Valeur |
| --- | --- |
$content-type | application/json |
$iothub-enqueuedtime |  Heure d’envoi de la notification |
$iothub-message-source | deviceLifecycleEvents |
$content-encoding | utf-8 |
opType | **createDeviceIdentity** ou **deleteDeviceIdentity** |
hubName | Nom de l’IoT Hub |
deviceId | ID de l’appareil |
operationTimestamp | Horodatage ISO8601 de l’opération |
iothub-message-schema | deviceLifecycleNotification |

Corps : cette section est au format JSON et représente le double de l’identité d’appareil créé. Par exemple,

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Propriétés d’identité des appareils

Les identités des appareils sont représentées sous forme de documents JSON avec les propriétés suivantes :

| Propriété | Options | Description |
| --- | --- | --- |
| deviceId |obligatoire, en lecture seule sur les mises à jour |Une chaîne qui respecte la casse (jusqu’à 128 caractères) de caractères alphanumériques 7 bits ASCII plus certains caractères spéciaux :`- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |obligatoire, en lecture seule |Une chaîne qui respecte la casse, générée par IoT Hub, d’une longueur maximale de 128 caractères. Cette valeur permet de distinguer les appareils dotés du même **deviceId**lorsqu’ils ont été supprimés et recréés. |
| etag |obligatoire, en lecture seule |Une chaîne représentant un ETag faible pour l’identité d’appareil, conformément à [RFC7232][lnk-rfc7232]. |
| auth |facultatif |Un objet composite contenant des informations d’authentification et des éléments de sécurité. |
| auth.symkey |facultatif |Un objet composite contenant une clé primaire et une clé secondaire, stockées au format base64. |
| status |required |Un indicateur d’accès. Peut être **Activé** ou **Désactivé**. Si la propriété est définie sur **Activé**, l’appareil est autorisé à se connecter. Si la propriété est définie sur **Désactivé**, cet appareil ne peut pas accéder à un point de terminaison de l’appareil. |
| statusReason |facultatif |Une chaîne de 128 caractères qui stocke le motif de l’état de l’identité de l’appareil. Tous les caractères UTF-8 sont autorisés. |
| statusUpdateTime |en lecture seule |Un indicateur temporel, indiquant la date et l’heure de la dernière mise à jour de l’état. |
| connectionState |en lecture seule |Un champ indiquant l’état de la connexion : **Connecté** ou **Déconnecté**. Ce champ représente la vue IoT Hub de l’état de connexion de l’appareil. **Important**  : ce champ doit être utilisé uniquement à des fins de développement et de débogage. L’état de la connexion est mis à jour uniquement pour les appareils utilisant les protocoles AMQP ou MQTT. Cet état est basé sur les pings au niveau du protocole (tests ping MQTT ou AMQP) et peut avoir un délai maximum de 5 minutes seulement. Pour ces raisons, de faux positifs peuvent survenir. Par exemple : un appareil peut être signalé comme étant connecté, alors qu’il est déconnecté. |
| connectionStateUpdatedTime |en lecture seule |Un indicateur temporel, indiquant la date et la dernière heure de mise à jour de l’état de la connexion. |
| lastActivityTime |en lecture seule |Un indicateur temporel, indiquant la date et la dernière heure de connexion de l’appareil, de réception d’un message ou d’envoi d’un message. |

> [!NOTE]
> L’état de la connexion peut uniquement représenter la vue IoT Hub de l’état de la connexion. Les mises à jour à cet état peuvent être différées en fonction des conditions et des configurations du réseau.

## <a name="additional-reference-material"></a>Matériel de référence supplémentaire

Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* La rubrique [Points de terminaison IoT Hub][lnk-endpoints] décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion.
* La rubrique [Référence - Quotas et limitation IoT Hub][lnk-quotas] décrit les quotas et le comportement de limitation qui s’appliquent au service IoT Hub.
* La section [Azure IoT device et service SDK][lnk-sdks] répertorie les Kits de développement logiciel (SDK) en différents langages que vous pouvez utiliser pour le développement d’applications d’appareil et de service qui interagissent avec IoT Hub.
* La rubrique [Référence - Langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages][lnk-query] décrit le langage de requête permettant de récupérer à partir d’IoT Hub des informations sur des jumeaux d’appareil et des travaux.
* La rubrique [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt] fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>étapes suivantes

À présent que vous savez comment utiliser le registre des identités IoT Hub, vous serez peut-être intéressé par les rubriques suivantes du Guide du développeur Iot Hub :

* [Contrôler l’accès à IoT Hub][lnk-devguide-security]
* [Utiliser des représentations d’appareil pour synchroniser les données d’état et de configuration][lnk-devguide-device-twins]
* [Appeler une méthode directe sur un appareil][lnk-devguide-directmethods]
* [Planifier des travaux sur plusieurs appareils][lnk-devguide-jobs]

Pour mettre en pratique certains des concepts décrits dans cet article, consultez le didacticiel IoT Hub suivant :

* [Mise en route d’Azure IoT Hub][lnk-getstarted-tutorial]

Pour savoir comment utiliser le service d’approvisionnement des appareils IoT Hub afin d’activer l’approvisionnement sans contact et juste-à-temps, consultez : 

* [Service Azure IoT Hub Device Provisioning][lnk-dps]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
