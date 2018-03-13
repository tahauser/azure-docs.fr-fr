---
title: "Présentation des jumeaux d’appareil Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur - Utiliser des jumeaux d’appareil pour synchroniser les données d’état et de configuration entre IoT Hub et vos appareils"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 8a3da072-a5bf-46e5-8de4-24cdbb2a03fa
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5bf2d24d0d5eadfea5ec8fd239a115c05a54fe99
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Comprendre et utiliser les jumeaux d’appareil IoT Hub

Les *jumeaux d’appareil* sont des documents JSON qui stockent des informations sur l’état des appareils (métadonnées, configurations et conditions). Azure IoT Hub conserve un jumeau d’appareil pour chaque appareil que vous y connectez. Cet article aborde les points suivants :


* La structure du jumeau d’appareil : *tags* (balises), propriétés *desired* (souhaitées) et *reported* (signalées).
* Les opérations que les applications d’appareil et back-ends peuvent effectuer sur des jumeaux d’appareil.

Vous pouvez utiliser des jumeaux d’appareil pour répondre aux besoins suivants :

* Stocker les métadonnées spécifiques à l’appareil dans le cloud, par exemple, l’emplacement de déploiement d’un distributeur automatique.
* Signaler les informations d’état actuel, telles que les capacités disponibles et les conditions, à partir de votre application pour appareil, par exemple, un appareil connecté à votre hub IoT via un réseau mobile ou Wi-Fi.
* Synchroniser l’état des flux de travail de longue durée entre une application pour appareil et un back-end, par exemple, lorsque le back-end de solution spécifie une nouvelle version de microprogramme à installer et que l’application pour appareil rapporte les différentes étapes du processus de mise à jour.
* Interroger les métadonnées, la configuration ou l’état de vos appareils

Pour des conseils sur l’utilisation des propriétés signalées, des messages appareil-à-cloud ou du chargement de fichier, voir [Instructions pour la communication appareil-à-cloud][lnk-d2c-guidance].
Pour des conseils sur l’utilisation des propriétés souhaitées des méthodes directes ou des messages cloud-à-appareil, voir [Instructions pour la communication cloud-à-appareil][lnk-c2d-guidance].

## <a name="device-twins"></a>Jumeaux d’appareil
Les jumeaux d’appareil stockent des informations relatives aux appareils, dont l’utilité est la suivante :

* Ils permettent à un appareil et à un back-end de synchroniser les conditions et la configuration de l’appareil.
* Ils permettent à un back-end de solution d’interroger et de cibler des opérations de longue durée.

Le cycle de vie d’un jumeau d’appareil est lié à l’[identité d’appareil][lnk-identity] correspondante. Des jumeaux d’appareil sont implicitement créés et supprimés lors de la création ou de la suppression d’une identité d’appareil dans IoT Hub.

Un jumeau d’appareil est un document JSON incluant les éléments suivants :

* **Balises**. Une section du document JSON accessible en lecture et en écriture par le serveur principal de solution. Les balises ne sont pas visibles pour des applications pour appareil.
* **Propriétés souhaitées**. Utilisées en même temps que les propriétés signalées pour synchroniser une configuration ou une condition d’appareil. Le serveur principal de solution peut définir les propriétés souhaitées, et l’application d’appareil peut les lire. L’application d’appareil peut également recevoir des notifications sur les changements des propriétés souhaitées.
* **Propriétés signalées (Reported)**. Utilisées en même temps que les propriétés souhaitées pour synchroniser une configuration ou une condition d’appareil. L’application d’appareil peut définir les propriétés signalées, et le serveur principal de solution peut les lire et les interroger.
* **Propriétés d’identité des appareils**. La racine du document JSON du jumeau d’appareil contient les propriétés en lecture seule de l’identité d’appareil correspondante stockées dans le [registre des identités][lnk-identity].

![][img-twin]

L’exemple suivant montre un document JSON de jumeau d’appareil :

```json
{
    "deviceId": "devA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

L’objet racine contient les propriétés d’identité des appareils et des objets conteneur pour `tags` et les propriétés `reported` et `desired`. Le conteneur `properties` contient des éléments en lecture seule (`$metadata`, `$etag` et `$version`) décrits dans les sections [Métadonnées de jumeau d’appareil][lnk-twin-metadata] et [Accès concurrentiel optimiste][lnk-concurrency].

### <a name="reported-property-example"></a>Exemple de propriété signalée (Reported)
Dans l’exemple précédent, le jumeau d’appareil contient une propriété `batteryLevel` qui est signalée par l’application d’appareil. Cette propriété permet d’interroger des appareils et d’agir sur ceux-ci en fonction du dernier niveau signalé de charge de la batterie. D’autres exemples incluent une application d’appareil signalant des capacités d’appareil ou des options de connectivité.

> [!NOTE]
> Les propriétés signalées simplifient les scénarios où le serveur principal de solution s’intéresse à la dernière valeur connue d’une propriété. Si le back-end de la solution doit traiter une télémétrie d’appareil se présentant sous la forme de séquences d’événements horodatés, telles que des séries chronologiques, utilisez des [messages appareil-à-cloud][lnk-d2c].

### <a name="desired-property-example"></a>Exemple de propriété souhaitée
Dans l’exemple précédent, les propriétés souhaitées et signalées du jumeau d’appareil `telemetryConfig` sont utilisées par le serveur principal d’application et l’application d’appareil pour synchroniser la configuration de la télémétrie pour cet appareil. Par exemple : 

1. Le serveur principal de solution définit la propriété souhaitée avec la valeur de configuration souhaitée. Voici la partie du document contenant la propriété souhaitée (Desired) définie :

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. L’application d’appareil est informée de la modification, immédiatement si elle est connectée ou à la première de la reconnexion. L’application d’appareil signale ensuite la configuration mise à jour (ou une condition d’erreur à l’aide de la propriété `status`). Voici la partie contenant les propriétés signalées :

    ```json
    ...
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ...
    ```

3. Le serveur principal de solution peut suivre les résultats de l’opération de configuration sur de nombreux appareils en [interrogeant][lnk-query] les jumeaux d’appareil.

> [!NOTE]
> Les extraits de code précédents sont des exemples, optimisés pour la lisibilité, de manière possible d’encoder la configuration d’un appareil et son état. IoT Hub n’impose pas de schéma spécifique pour les propriétés souhaitées et signalées de jumeau d’appareil dans les jumeaux d’appareil.
> 
> 

Vous pouvez utiliser des jumeaux pour synchroniser des opérations de longue durée, telles que des mises à jour de microprogramme. Pour plus d’informations sur l’utilisation de propriétés pour synchroniser et suivre une opération de longue durée sur des appareils, voir [Utiliser des propriétés souhaitées pour configurer des appareils][lnk-twin-properties].

## <a name="back-end-operations"></a>Opérations principales
Le back-end de solution opère sur le jumeau d’appareil en utilisant les opérations atomiques suivantes, exposées par le biais du protocole HTTPS :

* **Récupérer le jumeau d’appareil par son ID**. Cette opération renvoie le contenu du document du jumeau d’appareil, à savoir les Tags (balises) et les propriétés système souhaitées (Desired) et signalées (Reported).
* **Mettre à jour partiellement le jumeau d’appareil**. Cette opération permet au serveur principal de solution de mettre à jour partiellement les Tags (balises) ou les propriétés souhaitées (Desired) dans un jumeau d’appareil. La mise à jour partielle est exprimée sous la forme d’un document JSON qui ajoute ou met à jour toute propriété. Les propriétés définies sur `null` sont supprimées. L’exemple suivant crée une propriété souhaitée avec la valeur `{"newProperty": "newValue"}`, remplace la valeur existante de `existingProperty` par `"otherNewValue"` et supprime `otherOldProperty`. Aucune autre modification n’est apportée aux autres propriétés souhaitées ou Tags existants :

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **Remplacer des propriétés souhaitées**. Cette opération permet au serveur principal de la solution de remplacer complètement toutes les propriétés souhaitées existantes, et de remplacer `properties/desired` par un nouveau document JSON.
* **Remplacer des Tags**. Cette opération permet au serveur principal de la solution de remplacer complètement tous les Tags existants, et de remplacer `tags` par un nouveau document JSON.
* **Recevoir des notifications jumelles**. Cette opération permet au back-end de la solution d’être notifié lorsque le jumeau est modifié. Pour ce faire, votre solution IoT doit créer un itinéraire et définir la source de données équivalente à *twinChangeEvents*. Par défaut, aucune notification jumelle n’est envoyée. Autrement dit, aucun itinéraire n’existe préalablement. Si le taux de variation est trop élevé, ou pour d’autres raisons, telles que des défaillances internes, IoT Hub peut envoyer une seule notification qui contient toutes les modifications. Par conséquent, si l’audit et la journalisation fiables de tous les états intermédiaires sont nécessaires pour votre application, vous devez utiliser des messages appareil-à-cloud. Le message de notification jumelle inclut le corps et les propriétés.

    - properties

    | NOM | Valeur |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  Heure d’envoi de la notification |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | ID de l’appareil |
    hubName | Nom de l’IoT Hub |
    operationTimestamp | Horodatage [ISO8601] de l’opération |
    iothub-message-schema | deviceLifecycleNotification |
    opType | « replaceTwin » ou « updateTwin » |

    Les propriétés système du message ont pour préfixe le symbole `'$'`.

    - body
        
    Cette section comprend toutes les modifications de double dans un format JSON. Il utilise le même format sous forme de correctif, à la différence près qu’il peut contenir toutes les sections jumelles : balises, properties.reported, properties.desired et qu’il contient les éléments « $metadata ». Par exemple,

    ```json
    {
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

Toutes les opérations précédentes prennent en charge [l’accès concurrentiel optimiste][lnk-concurrency] et requièrent l’autorisation **ServiceConnect** définie dans l’article [Sécurité][lnk-security].

En plus de ces opérations, le back-end de la solution peut :

* Interroger les jumeaux d’appareil à l’aide du [langage de requête d’IoT Hub][lnk-query] similaire à SQL.
* Effectuer des opérations sur les grands ensembles de jumeaux d’appareil à l’aide de [travaux][lnk-jobs].

## <a name="device-operations"></a>Opérations d’appareil
L’application d’appareil opère sur le jumeau d’appareil en utilisant les opérations atomiques suivantes :

* **Récupérer le jumeau d’appareil**. Cette opération renvoie le contenu du document du jumeau d’appareil, à savoir les Tags (Balises) et les propriétés système souhaitées (Desired) et signalées (Reported), pour l’appareil actuellement connecté.
* **Mettre à jour partiellement les propriétés signalées (Reported)**. Cette opération permet la mise à jour partielle des propriétés signalées de l’appareil actuellement connecté. Cette opération utilise le même format de mise à jour JSON que le serveur principal de solution utilise pour une mise à jour partielle des propriétés souhaitées.
* **Observer les propriétés souhaitées (Desired)**. L’appareil actuellement connecté peut choisir d’être informé des mises à jour des propriétés souhaitées au moment où elles se produisent. L’appareil reçoit la forme de mise à jour (remplacement partiel ou complet) exécutée par le serveur principal de la solution.

Toutes les opérations précédentes requièrent l’autorisation **DeviceConnect** définie dans l’article [Sécurité][lnk-security].

Les kits [Azure IoT device SDK][lnk-sdks] facilitent le recours aux opérations précédentes à partir d’un grand nombre de langages et de plateformes. Pour plus d’informations sur les détails des primitives d’IoT Hub concernant la synchronisation des propriétés souhaitées, consultez [Flux de reconnexion d’appareil][lnk-reconnection].

## <a name="tags-and-properties-format"></a>Format des Tags et propriétés
Les Tags (balises) ainsi que les propriétés souhaitées (Desired) et signalées (Reported) sont des objets JSON soumis aux restrictions suivantes :

* Toutes les clés dans des objets JSON sont des chaînes UNICODE UTF-8 de 64 octets respectant la casse. Les caractères autorisés excluent les caractères de contrôle UNICODE (segments C0 et C1), ainsi que `'.'`, `' '` et `'$'`.
* Toutes les valeurs figurant dans les objets JSON peuvent être des types JSON suivants : booléen, nombre, chaîne, objet. Les tableaux ne sont pas autorisés. La valeur maximale pour les entiers est 4503599627370495, tandis que la valeur minimale pour les entiers est-4503599627370496.
* Tous les objets JSON dans les balises ainsi que dans les propriétés souhaitées et signalées, peuvent avoir une profondeur maximale de 5. Par exemple, l’objet suivant est valide :

    ```json
    {
        ...
        "tags": {
            "one": {
                "two": {
                    "three": {
                        "four": {
                            "five": {
                                "property": "value"
                            }
                        }
                    }
                }
            }
        },
        ...
    }
    ```

* Aucune valeur de chaîne ne peut avoir une longueur supérieure à 4 Ko.

## <a name="device-twin-size"></a>Taille de jumeau d’appareil
IoT Hub impose une limite de taille de 8 Ko à chacune des valeurs totales respectives de `tags`, de `properties/desired` et de `properties/reported`, à l’exception des éléments en lecture seule.
La taille est calculée en comptant tous les caractères à l’exception des caractères de contrôle UNICODE (segments C0 et C1) et des espaces qui apparaissent en dehors d’une constante de chaîne.
IoT Hub rejette en générant une erreur toute opération susceptible d’augmenter la taille de ces documents au-delà de la limite.

## <a name="device-twin-metadata"></a>Métadonnées de jumeau d’appareil
IoT Hub tient à jour l’horodateur de la dernière mise à jour de chaque objet JSON dans les propriétés souhaitées et signalées du jumeau d’appareil. Les horodateurs sont exprimés en UTC et codés au format [ISO8601] `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Par exemple : 

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                }
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Ces informations sont conservées à chaque niveau (pas uniquement celui des feuilles de la structure JSON) afin de préserver les mises à jour qui suppriment des clés d’objet.

## <a name="optimistic-concurrency"></a>Accès concurrentiel optimiste
Les Tags ainsi que les propriétés souhaitées (Desired) et signalées (Reported) prennent en charge l’accès concurrentiel optimiste.
Les Tags ont un ETag, conforme à la norme [RFC7232], correspondant à leur représentation JSON. Vous pouvez utiliser les ETags dans des opérations de mise à jour conditionnelle à partir du serveur principal de solution pour assurer la cohérence.

Les propriétés souhaitées et signalées du jumeau d’appareil n’ont pas d’ETag, mais une valeur `$version` dont la nature incrémentielle est garantie. De même qu’un ETag, la version peut être utilisée par la partie effectuant la mise à jour afin d’assurer la cohérence des mises à jour. Par exemple, une application d’appareil pour une propriété signalée ou le serveur principal de solution pour une propriété souhaitée.

Les versions sont également utiles quand un agent observateur (par exemple, l’application d’appareil observant les propriétés souhaitées) doit concilier des concurrences entre les résultats d’une opération de récupération et d’une notification de mise à jour. Pour plus d’informations, voir [Flux de reconnexion d’appareil][lnk-reconnection].

## <a name="device-reconnection-flow"></a>Flux de reconnexion d’appareil
IoT Hub ne conserve pas les notifications de mise à jour de propriétés souhaitées pour les appareils déconnectés. Il en résulte qu’un appareil qui se connecte doit récupérer le document complet des propriétés souhaitées, en plus de s’abonner aux notifications de mise à jour. Étant donné la possibilité de concurrences entre les notifications de mise à jour et la récupération complète, le flux suivant doit être assuré :

1. L’application d’appareil se connecte à un hub IoT.
2. L’application d’appareil s’abonne aux notifications de mise à jour des propriétés souhaitées.
3. L’application d’appareil récupère le document complet pour les propriétés souhaitées.

L’application d’appareil peut ignorer toutes les notifications dont la `$version` a une valeur inférieure ou égale à la version du document complet récupéré. Cette approche est possible, car IoT Hub garantit que les numéros de version sont toujours incrémentiels.

> [!NOTE]
> Cette logique est déjà implémentée dans les kits [Azure IoT device SDK][lnk-sdks]. Cette description est utile uniquement lorsque l’application d’appareil ne peut utiliser aucun des kits Azure IoT device SDK et que vous devez programmer l’interface MQTT directement.
> 
> 

## <a name="additional-reference-material"></a>Matériel de référence supplémentaire
Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* L’article [Points de terminaison IoT Hub][lnk-endpoints] décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion.
* L’article [Quotas et limitation][lnk-quotas] décrit les quotas appliqués au service IoT Hub, et le comportement de limitation auquel s’attendre en cas d’utilisation du service.
* L’article sur les kits [Azure IoT device et service SDK][lnk-sdks] répertorie les kits SDK en différents langages que vous pouvez utiliser pour le développement d’applications d’appareil et de service qui interagissent avec IoT Hub.
* L’article [Langage de requête d’IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages][lnk-query] décrit le langage de requête d’IoT Hub permettant de récupérer, à partir d’IoT Hub, des informations relatives à vos jumeaux d’appareil et à vos travaux.
* L’article [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt] fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous savez ce que sont des jumeaux d’appareil, vous serez peut-être intéressé par les rubriques suivantes du Guide du développeur IoT Hub :

* [Appeler une méthode directe sur un appareil][lnk-methods]
* [Planifier des travaux sur plusieurs appareils][lnk-jobs]

Pour tenter de mettre en pratique certains des concepts décrits dans cet article, consultez les didacticiels IoT Hub suivants :

* [Utilisation d’un jumeau d’appareil][lnk-twin-tutorial]
* [Utilisation des propriétés de jumeau d’appareil][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png
