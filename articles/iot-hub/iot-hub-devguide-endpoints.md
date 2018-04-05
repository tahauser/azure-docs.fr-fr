---
title: Présentation des points de terminaison Azure IoT Hub | Microsoft Docs
description: Guide du développeur – informations de référence sur les points de terminaison côté appareil et côté service IoT Hub.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 17479fce776b0786e2fa32054eec8b0456871aae
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="reference---iot-hub-endpoints"></a>Référence - Points de terminaison IoT Hub

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Noms IoT Hub

Vous pouvez rechercher le nom de l’IoT Hub qui héberge vos points de terminaison dans le portail sur le panneau **Vue d’ensemble**. Par défaut, le nom DNS d’un IoT Hub ressemble à : `{your iot hub name}.azure-devices.net`.

Vous pouvez utiliser le DNS Azure afin de créer un nom DNS personnalisé pour votre IoT Hub. Pour plus d’informations, consultez [Use Azure DNS to provide custom domain settings for an Azure service](../dns/dns-custom-domain.md) (Utiliser DNS Azure pour fournir des paramètres de domaine personnalisé pour un service Azure).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Liste de points de terminaison IoT Hub intégrés

Azure IoT Hub est un service multilocataire qui propose ses fonctionnalités à différents acteurs. Le schéma qui suit illustre les différents points de terminaison proposés par IoT Hub.

![Points de terminaison IoT Hub][img-endpoints]

La liste ci-dessous décrit les points de terminaison :

* **Fournisseur de ressources**. Le fournisseur de ressources IoT Hub expose une interface [Azure Resource Manager][lnk-arm]. Cette interface permet aux propriétaires d’abonnement Azure de créer et de supprimer des IoT Hubs et de mettre à jour les propriétés IoT Hub. Les propriétés des IoT Hubs régissent les [stratégies de sécurité au niveau du hub][lnk-accesscontrol], par opposition au contrôle d’accès au niveau de l’appareil, et les options fonctionnelles pour les messages cloud-à-appareil et appareil-à-cloud. Le fournisseur de ressources IoT Hub vous permet également [d’exporter les identités des appareils][lnk-importexport].
* **Gestion d’identité de l’appareil**. Chaque IoT Hub expose un ensemble de points de terminaison HTTPS REST afin de gérer les identités des appareils (par exemple pour les opérations de création, de récupération, de mise à jour et de suppression). Les [identités des appareils][lnk-device-identities] sont utilisées pour l’authentification des appareils et le contrôle d’accès.
* **Gestion des représentations d’appareils**. Chaque IoT Hub expose un ensemble de points de terminaison REST HTTPS orientés service pour interroger et mettre à jour les [représentations d’appareil][lnk-twins] (mise à jour des balises et des propriétés).
* **Gestion des travaux**. Chaque IoT Hub expose un ensemble de points de terminaison REST HTTPS orientés service pour interroger et gérer les [travaux][lnk-jobs].
* **Points de terminaison des appareils**. Pour chaque appareil dans le registre des identités, IoT Hub expose un ensemble de points de terminaison :

  * *Envoyer des messages appareil-à-cloud*. Un appareil utilise ce point de terminaison pour [envoyer des messages appareil-à-cloud][lnk-d2c].
  * *Recevoir des messages cloud-à-appareil*. Un appareil utilise ce point de terminaison pour recevoir les [messages cloud-à-appareil][lnk-c2d] qui lui sont adressés.
  * *Initier des téléchargements de fichiers*. Un appareil utilise ce point de terminaison pour recevoir un URI SAP du Stockage Azure à partir d’IoT Hub pour [charger un fichier][lnk-upload].
  * *Récupérer et mettre à jour les propriétés d’une représentation d’appareil*. Un appareil utilise ce point de terminaison pour accéder aux propriétés de son [jumeau d’appareil][lnk-twins].
  * *Recevoir des requêtes de méthodes directes*. Un appareil utilise ce point de terminaison pour écouter les requêtes de [méthodes directes][lnk-methods].

    Ces points de terminaison sont exposés à l’aide des protocoles [MQTT v3.1.1][lnk-mqtt], HTTPS 1.1 et [AMQP 1.0][lnk-amqp]. Le protocole AMQP est également disponible sur [WebSockets][lnk-websockets], sur le port 443.

* **Points de terminaison de service**. Chaque IoT Hub expose un ensemble de points de terminaison pour que votre système principal de solution puisse communiquer avec vos appareils. À une exception près, ces points de terminaison sont uniquement exposés à l’aide du protocole [AMQP][lnk-amqp]. Le point de terminaison d’appel de méthode est exposé via le protocole HTTPS.
  
  * *Recevoir des messages Appareil vers cloud*. Ce point de terminaison est compatible avec [Azure Event Hubs][lnk-event-hubs]. Il peut être utilisé par un service principal pour lire les [messages appareil à cloud][lnk-d2c] envoyés par vos appareils. Vous pouvez créer des points de terminaison sur votre IoT Hub en plus de ce point de terminaison prédéfini.
  * *Envoyer des messages Cloud vers appareil et recevoir des accusés de remise*. Ces points de terminaison autorisent votre serveur principal à envoyer des [messages cloud-à-appareil][lnk-c2d] et à recevoir les accusés de réception ou d’expiration correspondants.
  * *Recevoir les notifications de fichier*. Ce point de terminaison de messagerie vous permet de recevoir des notifications lorsqu’un fichier est correctement téléchargé sur votre appareil. 
  * *Invocation de méthode directe*. Ce point de terminaison permet à un service principal d’appeler une [méthode directe][lnk-methods] sur un appareil.
  * *Recevoir les événements de surveillance des opérations*. Ce point de terminaison vous permet de recevoir les événements de surveillance des opérations si votre IoT hub a été configuré pour les émettre. Pour plus d’informations, consultez [Surveillance des opérations IoT Hub][lnk-operations-mon].

L’article [Kits Azure IoT SDK][lnk-sdks] décrit les différentes méthodes permettant d’accéder à ces points de terminaison.

Tous les points de terminaison IoT Hub utilisent le protocole [TLS][lnk-tls], et aucun point de terminaison n’est jamais exposé sur des canaux non chiffrés/non sécurisés.

## <a name="custom-endpoints"></a>Points de terminaison personnalisés

Vous pouvez lier des services Azure existants dans votre abonnement à votre hub IoT pour qu’ils jouent le rôle de points de terminaison pour le routage des messages. Ces points de terminaison de service jouent le rôle de points de terminaison de service et sont utilisés pour les routages de message. Les appareils ne peuvent pas écrire directement dans des points de terminaison supplémentaires. Pour en savoir plus sur les itinéraires de messages, consultez l’entrée du guide du développeur sur l’[envoi et la réception de messages avec IoT Hub][lnk-devguide-messaging].

IoT Hub prend actuellement en charge les services Azure suivants en tant que points de terminaison supplémentaires :

* Conteneurs de stockage Azure
* Event Hubs
* Files d'attente Service Bus
* Rubriques de Service Bus

IoT Hub doit pouvoir accéder en écriture à ces points de terminaison de service pour que le routage des messages fonctionne. Si vous configurez vos points de terminaison via le portail Azure, les autorisations nécessaires sont ajoutées pour vous. Veillez à configurer vos services pour prendre en charge le débit prévu. Lorsque vous configurez votre solution IoT pour la première fois, vous devrez peut-être surveiller vos points de terminaison supplémentaires et apporter les modifications nécessaires en fonction de la charge réelle.

Si un message correspond à plusieurs routages qui pointent vers le même point de terminaison, IoT Hub ne le remet qu’une seule fois à ce point de terminaison. Par conséquent, il est inutile de configurer une déduplication sur votre file d’attente ou votre rubrique Service Bus. Dans les files d’attente partitionnées, l’affinité de la partition assure le classement des messages.

Pour connaître les limites du nombre de points de terminaison que vous pouvez ajouter, consultez [Quotas et limitation][lnk-devguide-quotas].

### <a name="when-using-azure-storage-containers"></a>Lors de l’utilisation de conteneurs de stockage Azure

IoT Hub prend uniquement en charge l’écriture de données dans des conteneurs de stockage Azure en tant qu’objets blob au format [Apache Avro](http://avro.apache.org/). IoT Hub met en lots les messages et écrit les données dans un objet blob chaque fois que :

* Le lot atteint une certaine taille, ou
* Un certain laps de temps s’est écoulé.

IoT Hub n’écrit pas un objet blob vide s’il n’y a aucune donnée à écrire.

IoT Hub utilise par défaut la convention d’affectation de noms de fichiers suivante :

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Vous pouvez utiliser la convention d’affectation de noms de fichiers de votre choix, mais vous devez utiliser tous les jetons répertoriés.

### <a name="when-using-service-bus-queues-and-topics"></a>Lors de l’utilisation de files d’attente et de rubriques Service Bus

Les options **Sessions** ou **Détection des doublons** ne doivent pas être activées pour les files d’attente et rubriques Service Bus utilisées comme points de terminaison IoT Hub. Si l’une de ces options est activée, le point de terminaison s’affiche comme **Inaccessible** dans le portail Azure.

## <a name="field-gateways"></a>Passerelles de champ

Dans une solution IoT, une *passerelle de champ* se situe entre vos appareils et vos points de terminaison IoT Hub. Elle est généralement située près de vos appareils. Vos appareils communiquent directement avec la passerelle de champ à l’aide d’un protocole pris en charge. La passerelle de champ se connecte à un point de terminaison IoT Hub à l’aide d’un protocole pris en charge par ce dernier. Une passerelle de champ peut être un matériel dédié ou un ordinateur à faible puissance exécutant un logiciel de passerelle personnalisé.

Vous pouvez utiliser [Azure IoT Edge][lnk-iot-edge] pour implémenter une passerelle de champ. IoT Edge offre des fonctionnalités, comme la possibilité de multiplexer les communications à partir de plusieurs appareils sur la même connexion IoT Hub.

## <a name="next-steps"></a>Étapes suivantes

Les autres rubriques de référence de ce Guide du développeur IoT Hub comprennent :

* [Langage de requête IoT Hub pour les jumeaux d’appareils, les travaux et le routage des messages][lnk-devguide-query]
* [Quotas et limitation][lnk-devguide-quotas]
* [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md
