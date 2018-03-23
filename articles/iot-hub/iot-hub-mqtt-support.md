---
title: Présentation de la prise en charge de MQTT au niveau d’Azure IoT Hub | Microsoft Docs
description: 'Guide du développeur : prise en charge des appareils se connectant à un point de terminaison IoT Hub côté appareil en utilisant le protocole MQTT. Inclut des informations sur la prise en charge intégrée de MQTT dans les Azure IoT device SDK.'
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 1d71c27c-b466-4a40-b95b-d6550cf85144
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9acda980583319414cc9e8668424907947a257db
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2018
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Communication avec votre IoT Hub à l’aide du protocole MQTT

Grâce à IoT Hub, les appareils peuvent communiquer avec les points de terminaison d’appareil IoT Hub à l’aide de :

* [MQTT v3.1.1] [ lnk-mqtt-org] sur le port 8883.
* MQTT v3.1.1 sur WebSocket sur le port 443.

Toutes les communications des appareils avec IoT Hub doivent être sécurisées à l’aide de TLS/SSL. Par conséquent, IoT Hub ne prend pas en charge les connexions non sécurisées sur le port 1883.

## <a name="connecting-to-iot-hub"></a>Connexion à IoT Hub

Un appareil peut utiliser le protocole MQTT pour se connecter à un hub IoT à l’aide :

* Des bibliothèques fournies dans les [SDK Azure IoT][lnk-device-sdks].
* Ou du protocole MQTT directement.

## <a name="using-the-device-sdks"></a>Utilisation des Kits device SDK

Les [Kits device SDK][lnk-device-sdks] qui prennent en charge le protocole MQTT sont disponibles pour Java, Node.js, C, C# et Python. Les Kits device SDK utilisent la chaîne de connexion IoT Hub standard pour établir une connexion à un IoT Hub. Pour utiliser le protocole MQTT, le paramètre de protocole du client doit être défini sur **MQTT**. Par défaut, les Kits device SDK se connectent à un IoT Hub avec l’indicateur **CleanSession** défini sur **0**, et utilisent **QoS 1** pour l’échange de messages avec l’IoT Hub.

Quand un appareil est connecté à un hub IoT, les SDK d’appareils fournissent des méthodes qui permettent à l’appareil d’échanger des messages avec un hub IoT.

Le tableau suivant contient des liens vers des exemples de code pour chaque langage pris en charge et spécifie les paramètres à utiliser pour établir une connexion à IoT Hub à l’aide du protocole MQTT.

| Langage | Paramètre de protocole |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migration d’une application d’appareil d’AMQP vers MQTT

Si vous utilisez les [Kits device SDK][lnk-device-sdks], le passage du protocole AMQP au protocole MQTT nécessite de modifier le paramètre de protocole dans l’initialisation du client comme indiqué précédemment.

Lors de cette opération, vérifiez les éléments suivants :

* AMQP retourne des erreurs pour nombreuses conditions, tandis que MQTT met fin à la connexion. Par conséquent, votre logique de gestion des exceptions peut nécessiter certaines modifications.
* MQTT ne prend pas en charge les opérations *reject* lors de la réception de [messages cloud-à-appareil][lnk-messaging]. Si votre application de serveur principal doit recevoir une réponse de l’application pour appareil, envisagez d’utiliser des [méthodes directes][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Utilisation directe du protocole MQTT

Si un appareil ne peut pas utiliser les Kits device SDK, il peut toujours se connecter aux points de terminaison d’appareil publics à l’aide du protocole MQTT sur le port 8883. Dans le paquet **CONNECT** , l’appareil doit utiliser les valeurs suivantes :

* Pour le champ **ClientId**, utilisez le **deviceId**.

* Dans le champ **Username**, utilisez `{iothubhostname}/{device_id}/api-version=2016-11-14`, où `{iothubhostname}` est l’enregistrement CName complet du hub IoT.

    Par exemple, si le nom de votre hub IoT est **contoso.azure-devices.net** et si le nom de votre appareil est **MyDevice01**, le champ **Username** complet doit contenir :

    `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`

* Dans le champ **Password**, utilisez un jeton SAP. Le format du jeton SAP est identique pour les protocoles HTTPS et AMQP :

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Si vous utilisez l’authentification par certificat X.509, les mots de passe de jeton SAS ne sont pas obligatoires. Pour plus d’informations, voir [Configurer la sécurité X.509 dans votre Azure IoT Hub][lnk-x509]

  Pour plus d’informations sur la génération de jetons SAP, consultez la section consacrée aux appareils dans la rubrique [Utilisation de jetons de sécurité IoT Hub][lnk-sas-tokens].

  Lors du test, vous pouvez également utiliser l’outil [Explorateur d’appareils][lnk-device-explorer] pour générer rapidement un jeton SAP à copier et coller dans votre propre code :

  1. Accédez à l’onglet **Management** (Gestion) de **l’Explorateur d’appareils**.
  2. Cliquez sur **SAS Token** (Jeton SAP) en haut à droite.
  3. Dans **SASTokenForm**, sélectionnez votre appareil dans la liste déroulante **DeviceID**. Définissez votre **TTL**(Durée de vie).
  4. Cliquez sur **Generate** (Générer) pour créer votre jeton.

     Le jeton SAP généré présente la structure suivante :

     `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

     La partie de ce jeton à utiliser dans le champ **Password** (Mot de passe) pour la connexion avec le protocole MQTT est :

     `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Pour les paquets de connexion et de déconnexion MQTT, IoT Hub émet un événement sur le canal **Surveillance des opérations** . Cet événement comporte des informations supplémentaires qui peuvent vous aider à résoudre les problèmes de connectivité.

L’application de l’appareil peut spécifier un message **Will** dans le paquet **CONNECTER**. L’application de l’appareil doit utiliser `devices/{device_id}/messages/events/{property_bag}` ou `devices/{device_id}/messages/events/{property_bag}` comme nom de rubrique **Will** pour définir des messages **Will** à transmettre en tant que message de télémétrie. Dans ce cas, si la connexion réseau est fermée, mais qu’un paquet **DÉCONNECTER** n’a pas été préalablement reçu à partir de l’appareil, IoT Hub envoie le message **Will** fourni dans le paquet **CONNECTER** au canal de télémétrie. Le canal de télémétrie peut être soit le point de terminaison **Événements** par défaut, soit un point de terminaison personnalisé défini par le routage d’IoT Hub. Le message a la propriété **iothub-MessageType**, à laquelle une valeur de **Will** est affectée.

### <a name="tlsssl-configuration"></a>Configuration TLS/SSL

Pour utiliser le protocole MQTT directement, votre client *doit* se connecter via le protocole TLS/SSL. Sans cette étape, des erreurs de connexion se produiront.

Afin d’établir une connexion TLS, vous devrez peut-être télécharger et référencer le certificat racine Baltimore DigiCert. Ce certificat est celui utilisé par Azure pour sécuriser la connexion. Il se trouve dans le dépôt [Azure-iot-sdk-c][lnk-sdk-c-certs]. Pour plus d’informations sur ces certificats, consultez le [site web de Digicert][lnk-digicert-root-certs].

Voici un exemple d’implémentation de cette connexion à l’aide de la version de Python de la [bibliothèque Paho MQTT][lnk-paho] fournie par la fondation Eclipse.

Tout d’abord, installez la bibliothèque Paho à partir de votre environnement de ligne de commande :

```cmd/sh
pip install paho-mqtt
```

Ensuite, implémentez le client dans un script Python. Remplacez les espaces réservés comme suit :

* `<local path to digicert.cer>` est le chemin d’un fichier local qui contient le certificat racine DigiCert Baltimore. Vous pouvez créer ce fichier en copiant les informations de certificat à partir de [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) dans le SDK Azure IoT pour C. Incluez les lignes `-----BEGIN CERTIFICATE-----` et `-----END CERTIFICATE-----`, supprimez les marques `"` au début et à la fin de chaque ligne, et supprimez les caractères `\r\n` à la fin de chaque ligne.
* `<device id from device registry>` est l’ID d’un appareil que vous avez ajouté à votre hub IoT.
* `<generated SAS token>` est un jeton SAP pour l’appareil créé comme décrit précédemment dans cet article.
* `<iot hub name>` est le nom de votre hub IoT.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id, password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>Envoi de messages appareil-à-cloud

Après avoir correctement établi une connexion, un appareil peut envoyer des messages à IoT Hub en utilisant `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` en tant que **Nom de la rubrique**. L’élément `{property_bag}` permet à l’appareil d’envoyer des messages avec des propriétés supplémentaires dans un format codé URL. Par exemple : 

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Cet élément `{property_bag}` utilise le même encodage que celui utilisé pour les chaînes de requête dans le protocole HTTPS.

Voici une liste de comportements spécifiques à l’implémentation d’IoT Hub :

* IoT Hub ne prend pas en charge les messages QoS 2. Si un client d’appareil publie un message avec **QoS 2**, IoT Hub interrompt la connexion réseau.
* IoT Hub ne conserve pas les messages Retain. Si un appareil envoie un message avec l’indicateur **RETAIN** défini sur 1, IoT Hub ajoute la propriété d’application **x-opt-retain** au message. Dans ce cas, IoT Hub ne conserve pas le message, mais le transmet à l’application principale.
* IoT Hub ne prend en charge qu’une seule connexion MQTT active par appareil. Toute nouvelle connexion MQTT au nom du même ID d’appareil entraîne l’interruption de la connexion existante par IoT Hub.

Pour en savoir plus, reportez-vous au [Guide du développeur de messages][lnk-messaging].

### <a name="receiving-cloud-to-device-messages"></a>Réception de messages cloud-à-appareil

Pour recevoir des messages d’IoT Hub, l’appareil doit s’abonner en utilisant un `devices/{device_id}/messages/devicebound/#` en tant que **Filtre de rubrique**. Le caractère générique à plusieurs niveaux `#` dans le Filtre de rubrique est utilisé uniquement pour autoriser l’appareil à recevoir des propriétés supplémentaires dans le nom de la rubrique. IoT Hub n’autorise pas l’utilisation des caractères génériques `#` ou `?` pour filtrer les sous-rubriques. IoT Hub n’étant pas un broker de messagerie pub-sub à usage général, il prend uniquement en charge les noms de rubriques et les filtres de rubriques documentés.

L’appareil ne reçoit aucun message d’IoT Hub tant qu’il ne s’est pas abonné à son point de terminaison spécifique d’appareil, représenté par le filtre de rubrique `devices/{device_id}/messages/devicebound/#`. Une fois qu’un abonnement a été établi, l’appareil reçoit les messages cloud-à-appareil qui lui ont été envoyés après l’abonnement. Si l’appareil se connecte avec l’indicateur **CleanSession** défini sur **0**, l’abonnement est rendu persistant entre les différentes sessions. Dans ce cas, la prochaine fois que l’appareil se connecte avec **CleanSession 0**, il reçoit les messages en attente qui lui ont été envoyés quand il était déconnecté. Si l’appareil utilise l’indicateur **CleanSession** défini sur **1**, il ne reçoit pas les messages à partir d’IoT Hub jusqu’à ce qu’il s’abonne à son point de terminaison d’appareil.

IoT Hub remet les messages avec le **Nom de la rubrique** `devices/{device_id}/messages/devicebound/`, ou `devices/{device_id}/messages/devicebound/{property_bag}` quand il y a des propriétés de message. `{property_bag}` contient des paires clé/valeur codées URL de propriétés de message. Seules les propriétés d’application et les propriétés système définissables par l’utilisateur (comme **messageId** ou **correlationId**) sont incluses dans le jeu de propriétés. Les noms de propriété système ont le préfixe **$**, tandis que les noms de propriété d’application ne sont précédés d’aucun préfixe.

Quand une application d’appareil s’abonne à une rubrique avec **QoS 2**, IoT Hub accorde le niveau QoS 1 maximal dans le paquet **SUBACK**. Après cela, IoT Hub remet les messages à l’appareil à l’aide de QoS 1.

### <a name="retrieving-a-device-twins-properties"></a>Récupération des propriétés d’un jumeau d’appareil

Tout d’abord, un appareil s’abonne à `$iothub/twin/res/#` pour recevoir les réponses de l’opération. Ensuite, il envoie un message vide à la rubrique `$iothub/twin/GET/?$rid={request id}`, avec une valeur propagée pour **l’ID de la requête**. Le service envoie alors un message de réponse contenant les données de jumeau d’appareil sur la rubrique `$iothub/twin/res/{status}/?$rid={request id}`, en utilisant le même **ID de requête** que la requête.

L’ID de la requête peut avoir n’importe quelle valeur valide de propriété de message, conformément au [Guide du développeur de messages IoT Hub][lnk-messaging], et l’état est validé comme entier.

Le corps de la réponse contient la section properties du jumeau d’appareil. L’extrait suivant montre le corps de l’entrée du registre des identités limité au membre « propriétés », par exemple :

```json
{
    "properties": {
        "desired": {
            "telemetrySendFrequency": "5m",
            "$version": 12
        },
        "reported": {
            "telemetrySendFrequency": "5m",
            "batteryLevel": 55,
            "$version": 123
        }
    }
}
```

Les codes d’état possibles sont :

|Statut | Description |
| ----- | ----------- |
| 200 | Succès |
| 429 | Trop de demandes (limité), selon la [Limitation IoT Hub][lnk-quotas] |
| 5** | Erreurs de serveur |

Pour en savoir plus, reportez-vous au [Guide du développeur de jumeaux d’appareil][lnk-devguide-twin].

### <a name="update-device-twins-reported-properties"></a>Mettre à jour les propriétés signalées du jumeau d’appareil

La séquence suivante décrit comment un appareil met à jour les propriétés déclarées dans le jumeau d’appareil IoT Hub :

1. Un appareil doit tout d’abord s’abonner à la rubrique `$iothub/twin/res/#` pour recevoir des réponses d’opération de IoT Hub.

1. Un appareil envoie un message qui contient la mise à jour de jumeau d’appareil pour la rubrique `$iothub/twin/PATCH/properties/reported/?$rid={request id}`. Ce message contient une valeur **ID de requête**.

1. Le service envoie ensuite un message de réponse qui contient la nouvelle valeur ETag de la collection de propriétés déclarées dans la rubrique `$iothub/twin/res/{status}/?$rid={request id}`. Ce message de réponse utilise le même **ID de requête** que la requête.

Le corps du message de requête contient un document JSON qui contient de nouvelles valeurs pour les propriétés signalées. Chaque membre du document JSON met à jour ou ajoute le membre correspondant dans le document du jumeau d’appareil. Un membre défini sur `null` supprime le membre de l’objet conteneur. Par exemple : 

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Les codes d’état possibles sont :

|Statut | Description |
| ----- | ----------- |
| 200 | Succès |
| 400 | Demande incorrecte. JSON incorrect |
| 429 | Trop de demandes (limité), selon la [Limitation IoT Hub][lnk-quotas] |
| 5** | Erreurs de serveur |

Pour en savoir plus, reportez-vous au [Guide du développeur de jumeaux d’appareil][lnk-devguide-twin].

### <a name="receiving-desired-properties-update-notifications"></a>Réception de notifications de mise à jour des propriétés souhaitées

Lorsqu’un appareil est connecté, IoT Hub envoie des notifications à la rubrique `$iothub/twin/PATCH/properties/desired/?$version={new version}`, qui contient le contenu de la mise à jour effectuée par le serveur principal de la solution. Par exemple : 

```json
{
    "telemetrySendFrequency": "5m",
    "route": null
}
```

Comme pour les mises à jour de propriétés, les valeurs `null` signifient que le membre de l’objet JSON est en cours de suppression.

> [!IMPORTANT]
> IoT Hub génère des notifications de modification uniquement lorsque les appareils sont connectés. Veillez à implémenter le [flux de reconnexion des appareils][lnk-devguide-twin-reconnection] pour maintenir la synchronisation des propriétés souhaitées entre IoT Hub et l’application pour appareil.

Pour en savoir plus, reportez-vous au [Guide du développeur de jumeaux d’appareil][lnk-devguide-twin].

### <a name="respond-to-a-direct-method"></a>Répondre à une méthode directe

Tout d’abord, un appareil doit s’abonner à `$iothub/methods/POST/#`. IoT Hub envoie des requêtes de méthodes à la rubrique `$iothub/methods/POST/{method name}/?$rid={request id}`, avec un corps vide ou un code JSON valide.

Pour répondre, l’appareil envoie un message avec un corps vide ou JSON valide à la rubrique `$iothub/methods/res/{status}/?$rid={request id}`. Dans ce message, **request ID** doit correspondre à celui du message de requête, et **status** doit être un entier.

Pour en savoir plus, reportez-vous au [Guide du développeur de méthodes directes][lnk-methods].

### <a name="additional-considerations"></a>Considérations supplémentaires

Pour finir, si vous avez besoin de personnaliser le comportement du protocole MQTT côté cloud, étudiez la [passerelle de protocole Azure IoT][lnk-azure-protocol-gateway]. Ce logiciel vous permet de déployer une passerelle de protocole personnalisé à hautes performances qui communique directement avec IoT Hub. La passerelle de protocole Azure IoT vous permet de personnaliser le protocole de l’appareil pour prendre en charge des déploiements MQTT de type « brownfield » ou autres protocoles personnalisés. Toutefois, cette approche nécessite l’exécution et l’utilisation d’une passerelle de protocole personnalisée.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le protocole MQTT, consultez la [documentation de MQTT][lnk-mqtt-docs].

Pour en savoir plus sur la planification de votre déploiement IoT Hub, consultez :

* [Catalogue d’appareils certifiés Azure pour l’IoT][lnk-devices]
* [Prendre en charge des protocoles supplémentaires][lnk-protocols]
* [Comparer à Event Hubs][lnk-compare]
* [Mise à l’échelle, HA et DR][lnk-scaling]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur d’IoT Hub][lnk-devguide]
* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-x509]: iot-hub-security-x509-get-started.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
[lnk-sdk-c-certs]: https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c
[lnk-digicert-root-certs]: https://www.digicert.com/digicert-root-certificates.htm
[lnk-paho]: https://pypi.python.org/pypi/paho-mqtt
