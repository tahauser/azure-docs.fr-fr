---
title: "Présentation des méthodes directes Azure IoT Hub | Microsoft Docs"
description: "Guide de développeur - Utiliser des méthodes directes pour appeler du code sur vos appareils à partir d’une application de service."
services: iot-hub
documentationcenter: .net
author: nberdy
manager: timlt
editor: 
ms.assetid: 9f0535f1-02e6-467a-9fc4-c0950702102d
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 003b3f6ef8a6fbc1c6fcdfc58f7d35bf6c42c9ee
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Comprendre et appeler des méthodes directes à partir d’IoT Hub
IoT Hub vous donne la possibilité d’appeler des méthodes directes sur des appareils à partir du cloud. Les méthodes directes représentent une interaction de demande-réponse avec un appareil, similaire à un appel HTTP, dans la mesure où elles réussissent ou échouent immédiatement (après un délai d’attente spécifié par l’utilisateur). Cette approche est utile pour les scénarios où le plan d’action immédiate est différent selon que l’appareil a été en mesure ou non de répondre. Par exemple, envoyer un SMS de sortie de veille à un appareil s’il est hors connexion (un SMS est plus onéreux qu’un appel de méthode).
Chaque méthode d’appareil cible à un seul appareil. Les [travaux][lnk-devguide-jobs] offrent un moyen d’appeler des méthodes directes sur plusieurs appareils, et de planifier un appel de méthode pour des appareils déconnectés.

Toute personne disposant d’autorisations **Connexion de service** sur IoT Hub peut appeler une méthode sur un appareil.

Les méthodes directes suivent un modèle de requête-réponse et sont destinées aux communications qui nécessitent une confirmation immédiate de leur résultat. Par exemple, le contrôle interactif de l’appareil, tel que l’activation d’un ventilateur.

Reportez-vous à [l’aide sur la communication cloud-à-appareil][lnk-c2d-guidance] en cas de doute entre l’utilisation des propriétés de votre choix, des méthodes directes ou des messages cloud-à-appareil.

## <a name="method-lifecycle"></a>Cycle de vie de méthode
Les méthodes directes sont implémentées sur l’appareil et peuvent nécessiter de zéro à plusieurs entrées dans la charge utile pour s’instancier correctement. Vous appelez une méthode directe via un URI côté service (`{iot hub}/twins/{device id}/methods/`). Un appareil reçoit des méthodes directes via une rubrique MQTT spécifique de l’appareil (`$iothub/methods/POST/{method name}/`) ou via des liens AMQP (propriétés d’application `IoThub-methodname` et `IoThub-status`). 

> [!NOTE]
> Lorsque vous appelez une méthode directe sur un appareil, les noms et valeurs de propriété peuvent contenir uniquement des caractères alphanumériques US-ASCII imprimables, à l’exception des caractères suivants : ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

Les méthodes directes sont synchrones et réussissent ou échouent à l’issue du délai d’expiration (par défaut, 30 secondes, extensible à 3 600 secondes). Les méthodes directes sont utiles dans des scénarios interactifs où vous souhaitez qu’un appareil agisse si et seulement s’il est en ligne et reçoit des commandes. Par exemple, allumer une lumière à partir d’un téléphone. Dans ces scénarios, vous souhaitez constater immédiatement la réussite ou l’échec de la commande, de façon à ce que le service cloud puisse agir sur le résultat dès que possible. L’appareil peut renvoyer un corps de message résultant de la méthode, mais il n’est pas obligatoire que la méthode procède de la sorte. Il existe ni garantie de classement, ni sémantique de concurrence sur les appels de méthode.

Les méthodes directes sont exclusivement HTTPS côté cloud, et MQTT ou AMQP côté appareil.

La charge utile des requêtes et des réponses de méthodes correspond à un document JSON d’une taille pouvant aller jusqu’à 128 Ko.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Appeler une méthode directe à partir d’une application principale
### <a name="method-invocation"></a>Appel de méthode
Les appels de méthode directe sur un appareil sont des appels HTTPS qui comprennent les éléments suivants :

* l’*URI* spécifique de l’appareil (`{iot hub}/twins/{device id}/methods/`) ;
* la *méthode* POST ;
* Des *en-têtes* contenant l’autorisation, l’ID de demande, le type de contenu et l’encodage du contenu
* un *corps* JSON transparent au format suivant :

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

Le délai d’attente est exprimé en secondes. Si le délai d’attente n’est pas défini, sa valeur par défaut est de 30 secondes.

### <a name="response"></a>response
L’application principale reçoit une réponse qui comprend les éléments suivants :

* un *code d’état HTTP*, qui est utilisé pour des erreurs en provenance d’IoT Hub, dont l’erreur 404 pour les appareils non connectés ;
* Des *en-têtes* contenant l’ETag, l’ID de demande, le type de contenu et l’encodage du contenu
* un *corps* JSON au format suivant :

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Les propriétés `status` et `body` sont fournies par l’appareil et permettent de répondre avec le code d’état et/ou la description spécifiques de l’appareil.

## <a name="handle-a-direct-method-on-a-device"></a>Gérer une méthode directe sur un appareil
### <a name="mqtt"></a>MQTT
#### <a name="method-invocation"></a>Appel de méthode
Les appareils reçoivent des demandes de méthode directe sur la rubrique MQTT : `$iothub/methods/POST/{method name}/?$rid={request id}`

Le corps que l’appareil reçoit est au format suivant :

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Les demandes de méthode sont QoS 0.

#### <a name="response"></a>response
L’appareil envoie des réponses à `$iothub/methods/res/{status}/?$rid={request id}`, où :

* La propriété `status` est l’état d’exécution de la méthode fourni par l’appareil.
* La propriété `$rid`est l’ID de demande de l’appel de méthode reçu d’IoT Hub.

Le corps est défini par l’appareil et peut être n’importe quel état.

### <a name="amqp"></a>AMQP
#### <a name="method-invocation"></a>Appel de méthode
L’appareil reçoit des demandes de méthode directe en créant un lien de réception à l’adresse `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

Le message AMQP est transmis au lien de réception qui représente la demande de méthode. Il contient les éléments suivants :
* La propriété d’ID de corrélation, qui contient un ID de demande qui doit être transmis avec la réponse de la méthode correspondante
* Une propriété d’application nommée `IoThub-methodname`, qui contient le nom de la méthode appelée
* Le corps du message AMQP, qui contient la charge utile de la méthode en tant que JSON

#### <a name="response"></a>response
L’appareil crée un lien d’envoi pour retourner la réponse de la méthode à l’adresse`amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

La réponse de la méthode est retournée via le lien d’envoi et est structurée comme suit :
* La propriété d’ID de corrélation, qui contient l’ID de demande transmis dans le message de demande de la méthode
* Une propriété d’application nommée `IoThub-status`, qui contient l’état de la méthode fournie par l’utilisateur
* Le corps du message AMQP, qui contient la réponse de la méthode en tant que JSON

## <a name="additional-reference-material"></a>Matériel de référence supplémentaire
Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* La rubrique [Points de terminaison IoT Hub][lnk-endpoints] décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion.
* La rubrique [Limitation et quotas][lnk-quotas] décrit les quotas appliqués et le comportement de limitation auquel s’attendre quand vous utilisez IoT Hub.
* La section [Azure IoT device et service SDK][lnk-sdks] répertorie les Kits de développement logiciel (SDK) en différents langages que vous pouvez utiliser pour le développement d’applications d’appareil et de service qui interagissent avec IoT Hub.
* L’article [Langage de requête d’IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages][lnk-query] décrit le langage de requête d’IoT Hub permettant de récupérer, à partir d’IoT Hub, des informations relatives à vos jumeaux d’appareil et à vos travaux.
* La rubrique [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt] fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous savez comment utiliser les méthodes directes, vous serez peut-être intéressé par l’article suivant du Guide du développeur IoT Hub :

* [Planifier des travaux sur plusieurs appareils][lnk-devguide-jobs]

Si vous souhaitez tenter de mettre en pratique certains des concepts décrits dans cet article, vous serez peut-être intéressé par le didacticiel IoT Hub suivant :

* [Utiliser des méthodes directes][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
