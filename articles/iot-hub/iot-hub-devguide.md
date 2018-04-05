---
title: Guide du développeur pour Azure IoT Hub | Microsoft Docs
description: Le guide du développeur Azure IoT Hub porte sur les points de terminaison, la sécurité, le registre des identités, la gestion des appareils, les méthodes directes, les jumeaux d’appareil, les chargements de fichiers, les tâches, le langage de requête IoT Hub et la messagerie.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 31ccf65ccd2ab4e9673900efb1de357ab450b799
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="azure-iot-hub-developer-guide"></a>Guide du développeur Azure IoT Hub

Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT Hub vous offre :

* La sécurité des communications grâce aux informations d’identification de sécurité par appareil et au contrôle d’accès
* Plusieurs options de communication appareil-à-cloud et cloud-à-appareil à très grande échelle.
* Stockage requêtable de métadonnées et d’informations d’état par appareil.
* Une connectivité des appareils aisée avec les bibliothèques d’appareils pour les langages et les plateformes les plus courants.

Ce guide du développeur IoT Hub inclut les articles suivants :

* [L’aide sur la communication appareil-à-cloud][lnk-d2c-guidance] vous permet de choisir entre les messages appareil-à-cloud, les propriétés signalées des jumeaux d’appareil et le chargement de fichiers.
* [L’aide sur la communication cloud-à-appareil][lnk-c2d-guidance] vous permet de choisir entre les méthodes directes, les propriétés souhaitées des jumeaux d’appareil et les messages cloud-à-appareil.
* L’article [Messagerie appareil-à-cloud et cloud-à-appareil avec IoT Hub][devguide-messaging] décrit les fonctionnalités de messages (appareil-à-cloud et cloud-à-appareil) qui sont exposées par IoT Hub.
  * [Envoyer des messages appareil-à-cloud sur IoT Hub][devguide-messages-d2c].
  * [Lire des messages appareil-à-cloud à partir du point de terminaison intégré][devguide-builtin].
  * [Utiliser des points de terminaison et des règles de routage personnalisés pour les messages appareil-à-cloud][devguide-custom].
  * [Envoyer des messages cloud-à-appareil à partir d’IoT Hub][devguide-messages-c2d].
  * [Créer et lire des messages IoT Hub][devguide-format].
* L’article [Charger des fichiers à partir d’un appareil][devguide-upload] décrit la façon dont vous pouvez charger des fichiers à partir d’un appareil. L’article comprend également des informations sur des sujets tels que les notifications qui peuvent être envoyées par le processus de chargement.
* L’article [Gérer les identités des appareils dans IoT Hub][devguide-identities] décrit les informations stockées par le registre des identités de chaque hub IoT. Il explique aussi comment y accéder et le modifier.
* L’article [Contrôler l’accès à IoT Hub][devguide-security] décrit le modèle de sécurité utilisé pour autoriser l’accès à la fonctionnalité IoT Hub pour les appareils et les composants du cloud. L’article inclut des informations sur l’utilisation des jetons et des certificats X.509, ainsi que des détails sur les autorisations que vous pouvez accorder.
* [Utiliser des jumeaux d’appareil pour synchroniser l’état et la configuration][devguide-device-twins] décrit le concept de *jumeau d’appareil*. L’article décrit également les fonctionnalités exposées par les jumeaux d’appareil, telles que la synchronisation d’un appareil avec son jumeau. L’article inclut des informations sur les données stockées dans un jumeau d’appareil.
* [Appeler une méthode directe sur un appareil][devguide-directmethods] décrit le cycle de vie d’une méthode directe. L’article décrit comment appeler des méthodes sur un appareil à partir de votre application backend, et comment gérer la méthode directe sur votre appareil.
* L’article [Planifier des travaux sur plusieurs appareils][devguide-jobs] décrit la manière dont vous pouvez planifier des travaux sur plusieurs appareils. Cet article décrit comment envoyer des travaux qui effectuent des tâches telles que l’exécution d’une méthode directe ou la mise à jour d’un appareil à l’aide d’un jumeau d’appareil. Il décrit également comment interroger l’état d’un travail.
* L’article [Référence - Choisir un protocole de communication][devguide-protocol] décrit les protocoles de communication que prend en charge IoT Hub pour la communication des appareils et répertorie les ports qui doivent être ouverts.
* La rubrique [Référence - Points de terminaison IoT Hub][devguide-endpoints] décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion. Cet article explique également comment créer des points de terminaison supplémentaires dans IoT Hub et comment utiliser une passerelle de champ pour activer la connectivité avec les points de terminaison IoT Hub dans des scénarios non standard.
* La rubrique [Référence - Langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages][devguide-query] décrit le langage de requête d’IoT Hub permettant de récupérer à partir de votre hub des informations sur vos jumeaux d’appareil et vos travaux.
* L’article [Référence - Quotas et limitation][devguide-quotas] récapitule les quotas définis dans le service IoT Hub et la limitation appliquée quand vous dépassez un quota.
* [Référence - Tarification][devguide-pricing] fournit des informations générales sur les différentes références SKU et les tarifs d’IoT Hub, ainsi que des informations sur la façon dont les différentes fonctionnalités d’IoT Hub sont mesurées en tant que messages par IoT Hub.
* L’article [Référence - Kits de développement logiciel (SDK) de services et d’appareils][devguide-sdks] répertorie les SDK Azure IoT que vous pouvez utiliser lors du développement d’appareils et d’applications de service qui interagissent avec IoT Hub. L’article inclut des liens vers la documentation en ligne sur les API.
* L’article [Référence - Prise en charge de MQTT au niveau d’IoT Hub][devguide-mqtt] fournit des informations détaillées sur la prise en charge du protocole MQTT par IoT Hub. L’article décrit la prise en charge du protocole MQTT intégré dans les Kits de développement logiciel (SDK) Azure IoT et fournit des informations sur l’utilisation directe du protocole MQTT.
* [Glossaire][devguide-glossary] contient une liste des termes courants relatifs à IoT Hub.

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[devguide-messages-d2c]: iot-hub-devguide-messages-d2c.md
[devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[devguide-custom]: iot-hub-devguide-messages-read-custom.md
[devguide-messages-c2d]: iot-hub-devguide-messages-c2d.md
[devguide-format]: iot-hub-devguide-messages-construct.md
[devguide-protocol]: iot-hub-devguide-protocols.md
