---
title: "Développer des modules pour Azure IoT Edge | Microsoft Docs"
description: "Découvrez comment créer des modules personnalisés pour Azure IoT Edge."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 95b1d5d4e5e11f96b6abb17f0aeba935cc65512d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules---preview"></a>Présentation des exigences et des outils de développement de modules IoT Edge - préversion

Cet article explique quelles fonctionnalités sont disponibles lors de l’écriture d’applications qui s’exécutent en tant que module IoT Edge, et comment tirer parti de ces fonctionnalités.

## <a name="iot-edge-runtime-environment"></a>Environnement d’exécution IoT Edge
Le runtime IoT Edge fournit l’infrastructure nécessaire pour intégrer les fonctionnalités de plusieurs modules IoT Edge et pour les déployer sur des appareils IoT Edge. À un niveau global, tout programme peut être empaqueté en tant que module IoT Edge. Toutefois, pour tirer pleinement parti des fonctionnalités de communication et de gestion d’IoT Edge, un programme qui s’exécute dans un module peut se connecter au hub IoT Edge local, intégré dans le runtime IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Utilisation du hub IoT Edge
Le hub IoT Edge fournit deux fonctionnalités principales : proxy d’IoT Hub et communications locales.

### <a name="iot-hub-primitives"></a>Primitives IoT Hub
IoT Hub considère une instance de module comme un appareil, dans le sens où :

* Elle a un jumeau de module, qui est distinct et isolé du [jumeau d’appareil][lnk-devicetwin] et des autres jumeaux de module de cet appareil.
* Elle peut envoyer des [messages appareil-à-cloud][lnk-iothub-messaging].
* Elle peut recevoir des [méthodes directes][lnk-methods] ciblant spécifiquement son identité.

Actuellement, un module ne peut pas recevoir de messages cloud-à-appareil, ni utiliser la fonctionnalité de chargement de fichier.

Quand vous écrivez un module, il vous suffit d’utiliser [Azure IoT device SDK][lnk-devicesdk] pour vous connecter au hub IoT Edge et utiliser la fonctionnalité ci-dessus comme vous le feriez lors de l’utilisation d’IoT Hub avec une application d’appareil (la seule différence étant que, à partir du backend de votre application, vous devez faire référence à l’identité du module plutôt qu’à l’identité de l’appareil).

Pour obtenir un exemple d’application de module qui envoie des messages appareil-à-cloud et utilise le jumeau de module, consultez [Développer et déployer un module IoT Edge sur un appareil simulé][lnk-tutorial2].

### <a name="device-to-cloud-messages"></a>Messages appareil-à-cloud
Pour permettre le traitement complexe des messages appareil-à-cloud, le hub IoT Edge fournit un routage déclaratif des messages entre les modules, et entre les modules et IoT Hub.
Cela permet aux modules d’intercepter et de traiter les messages envoyés par d’autres modules, et de les propager dans des pipelines complexes.
L’article [Composition des modules][lnk-module-comp] explique comment composer des modules dans des pipelines complexes à l’aide d’itinéraires.

Un module IoT Edge, contrairement à une application d’appareil IoT Hub normale, peut recevoir des messages appareil-à-cloud qui sont traités en proxy par son hub IoT Edge local, afin de les traiter.

Le hub IoT Edge propage les messages vers votre module en fonction des itinéraires déclaratifs décrits dans l’article [Composition des modules][lnk-module-comp]. Quand vous développez un module IoT Edge, vous pouvez recevoir ces messages en définissant des gestionnaires de messages, comme indiqué dans le didacticiel [Développer et déployer un module IoT Edge sur un appareil simulé][lnk-tutorial2].

Afin de simplifier la création d’itinéraires, IoT Edge ajoute le concept de points de terminaison *d’entrée* et *de sortie* de module. Un module peut recevoir tous les messages appareil-à-cloud qui lui sont envoyés sans spécifier d’entrée, et peut envoyer des messages appareil-à-cloud sans spécifier de sortie.
L’utilisation d’entrées et de sorties explicites rend toutefois les règles de routage plus simples à comprendre. Pour plus d’informations sur les règles de routage et les points de terminaison d’entrée et de sortie pour les modules, consultez [Composition des modules][lnk-module-comp].

Pour finir, les messages appareil-à-cloud gérés par le hub Edge sont marqués avec les propriétés système suivantes :

| Propriété | Description |
| -------- | ----------- |
| $connectionDeviceId | ID d’appareil du client ayant envoyé le message. |
| $connectionModuleId | ID de module du module ayant envoyé le message. |
| $inputName | Entrée ayant reçu ce message. Peut être vide. |
| $outputName | Sortie utilisée pour envoyer le message. Peut être vide. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Connexion au hub IoT Edge à partir d’un module
La connexion au hub IoT Edge local à partir d’un module implique deux étapes : utiliser la chaîne de connexion fournie par le runtime IoT Edge au démarrage de votre module, et vérifier que votre application accepte le certificat présenté par le hub IoT Edge sur cet appareil.

La chaîne de connexion à utiliser est injectée par le runtime IoT Edge dans la variable d’environnement `EdgeHubConnectionString`. Du coup, n’importe quel programme souhaitant l’utiliser peut y accéder.

De la même façon, le certificat à utiliser pour valider la connexion au hub IoT Edge est injecté par le runtime IoT Edge dans un fichier dont le chemin est disponible dans la variable d’environnement `EdgeModuleCACertificateFile`.

Le didacticiel [Développer et déployer un module IoT Edge sur un appareil simulé][lnk-tutorial2] montre comment s’assurer que le certificat se trouve dans le magasin de l’ordinateur dans votre application de module. Il est clair que toute autre méthode d’approbation des connexions utilisant ce certificat fonctionne.

## <a name="packaging-as-an-image"></a>Empaquetage en tant qu’image
Les modules IoT Edge sont empaquetés en tant qu’images Docker.
Vous pouvez utiliser la chaîne d’outils Docker directement, ou Visual Studio Code comme illustré dans le didacticiel [Développer et déployer un module IoT Edge sur un appareil simulé][lnk-tutorial2].

## <a name="next-steps"></a>Étapes suivantes

Après avoir développé un module, découvrez comment [Déployer et surveiller des modules IoT Edge à l’échelle][lnk-howto-deploy].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-devicetwin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-messaging]: ../iot-hub/iot-hub-devguide-messaging.md
[lnk-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-tutorial2]: tutorial-csharp-module.md
[lnk-module-comp]: module-composition.md
[lnk-howto-deploy]: how-to-deploy-monitor.md
