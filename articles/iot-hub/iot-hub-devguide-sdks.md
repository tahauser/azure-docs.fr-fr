---
title: Comprendre les kits IoT Azure SDK | Microsoft Docs
description: Guide du développeur - informations et liens vers divers kits Azure IoT device et service SDK que vous pouvez utiliser pour créer des applications d’appareil et des applications principales.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aec2126369f45a89050dbd8b2d3cae7e00ccb8ed
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="understand-and-use-azure-iot-sdks"></a>Comprendre et utiliser les kits Azure IoT SDK

Il existe trois catégories de kits de développement logiciel (SDK) permettant de travailler avec IoT Hub :

* Les kits **device SDK** vous permettent de créer des applications qui s’exécuteront sur vos appareils IoT. Ces applications envoient des données de télémétrie à votre hub IoT et reçoivent éventuellement des messages provenant de votre hub IoT.

* Les kits **service SDK** vous permettent de gérer votre hub IoT et éventuellement d’envoyer des messages à vos appareils IoT.

* **Azure IoT Edge** permet de construire des passerelles pour des appareils qui n’utilisent aucun des protocoles pris en charge. Les passerelles peuvent également traiter les messages en périphérie.

Les kits SDK prennent en charge plusieurs langages de programmation.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Kits Azure IoT device SDK

Les kits Microsoft Azure IoT device SDK contiennent du code qui facilite la création d’appareils et d’applications qui se connectent aux services Azure IoT Hub et sont gérés par eux.

Vous pouvez télécharger les kits Azure IoT device SDK suivants à partir de GitHub :

* [Azure IoT device SDK pour .NET][lnk-dotnet-device-sdk]
* [Azure IoT device SDK pour Java][lnk-java-device-sdk]
* [Azure IoT device SDK pour Node.js][lnk-node-device-sdk]
* [Azure IoT device SDK pour Python][lnk-python-device-sdk]
* [Azure IoT device SDK pour C][lnk-c-device-sdk] : écrit en C ANSI (C99) pour la portabilité et la compatibilité de nombreuses plateformes. Il existe deux bibliothèques clientes d’appareil pour C, **iothub_client** de bas niveau et **serializer**.

> [!NOTE]
> Consultez les fichiers lisez-moi dans les dépôts GitHub pour plus d’informations sur l’utilisation du langage et des gestionnaires de packages spécifiques à la plateforme pour installer les fichiers binaires et dépendances sur votre ordinateur de développement.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilité des plateformes de système d’exploitation et du matériel

Pour plus d’informations sur la compatibilité des kits SDK avec des appareils physiques spécifiques, consultez le [catalogue d’appareils Azure Certified pour IoT][lnk-certified].

## <a name="azure-iot-service-sdks"></a>Kits Azure IoT service SDK

Les kits Azure IoT service SDK contiennent du code pour faciliter la création d’applications qui interagissent directement avec IoT Hub pour gérer les appareils et la sécurité.

Vous pouvez télécharger les kits Azure IoT service SDK suivants à partir de GitHub :

* [Azure IoT service SDK pour .NET][lnk-dotnet-service-sdk]
* [Azure IoT service SDK pour Java][lnk-java-service-sdk]
* [Azure IoT service SDK pour Node.js][lnk-node-service-sdk]
* [Azure IoT service SDK pour Java][lnk-python-service-sdk]
* [Azure IoT service SDK pour C][lnk-c-service-sdk]

> [!NOTE]
> Consultez les fichiers lisez-moi dans les dépôts GitHub pour plus d’informations sur l’utilisation du langage et des gestionnaires de packages spécifiques à la plateforme pour installer les fichiers binaires et dépendances sur votre ordinateur de développement.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Azure IoT Edge contient l’infrastructure et les modules nécessaires pour créer des solutions de passerelle IoT. Vous pouvez étendre IoT Edge pour créer des passerelles adaptées à n’importe quel scénario de bout en bout.

[Azure IoT Edge][lnk-iot-edge] est téléchargeable à partir de GitHub.

## <a name="online-api-reference-documentation"></a>Documentation de référence sur les API en ligne

La liste suivant contient des liens vers la documentation de référence sur les API en ligne pour les bibliothèques d’appareils, de services et de passerelles Azure IoT :

* [Internet des objets (IoT) .NET][lnk-dotnet-ref]
* [Azure IoT device SDK pour Java][lnk-java-ref]
* [Azure IoT service SDK pour Java][lnk-java-service-ref]
* [Azure IoT device SDK pour Node.js][lnk-node-ref]
* [Azure IoT service SDK pour Node.js][lnk-node-service-ref]
* [Azure IoT device SDK pour C][lnk-c-ref]
* [IoT Hub REST][lnk-rest-ref]
* [Azure IoT Edge][lnk-gateway-ref]

## <a name="next-steps"></a>Étapes suivantes

Les autres rubriques de référence de ce Guide du développeur IoT Hub comprennent :

* [Points de terminaison IoT Hub][lnk-devguide-endpoints]
* [Langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages][lnk-devguide-query]
* [Quotas et limitation][lnk-devguide-quotas]
* [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
