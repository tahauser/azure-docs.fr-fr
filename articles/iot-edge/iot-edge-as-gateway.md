---
title: Utiliser des appareils Azure IoT Edge en tant que passerelles | Microsoft Docs
description: "Utilisez Azure IoT Edge pour créer un appareil de passerelle proxy ou opaque et transparent qui envoie des données à partir de plusieurs appareils en aval vers le cloud ou qui traite ces données localement."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/27/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fe7ad2444b9378550e9624e3d109c8be4fd29f23
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway---preview"></a>Guide pratique pour utiliser un appareil IoT Edge en tant que passerelle - préversion

L’objectif des passerelles dans les solutions IoT est spécifique à la solution et associe connectivité de l’appareil et analytique Edge. Vous pouvez utiliser Azure IoT Edge pour répondre à tous les besoins (qu’ils soient liés à la connectivité, à l’identité ou à l’analytique Edge) d’une passerelle IoT. Les modèles de passerelles dans cet article font uniquement référence aux caractéristiques de connectivité d’appareil en aval et d’identité d’appareil, et non à la façon dont les données de l’appareil sont traitées sur la passerelle.

## <a name="patterns"></a>Modèles
Il existe trois modèles où il est possible d’utiliser un appareil IoT Edge en tant que passerelle : transparent, traduction de protocole et traduction d’identité :
* **Transparent** : les appareils qui, en théorie, peuvent se connecter à IoT Hub peuvent se connecter à un appareil de passerelle à la place. Cela implique que les appareils en aval ont leurs propres identités IoT Hub et utilisent l’un des protocoles MQTT, AMQP ou HTTP. La passerelle se contente de transférer les communications entre les appareils et IoT Hub. Les appareils ne savent pas qu’ils communiquent avec le cloud via une passerelle et un utilisateur qui interagit avec les appareils dans IoT Hub ne sait pas qu’il existe un appareil de passerelle intermédiaire. Ainsi, la passerelle est transparente. Consultez le guide pratique [Créer une passerelle transparente][lnk-iot-edge-as-transparent-gateway] pour obtenir des détails sur l’utilisation d’un appareil IoT Edge en tant que passerelle transparente.
* **Traduction de protocole** : les appareils ne prenant pas en charge MQTT, AMQP ou HTTP utilisent un appareil de passerelle pour envoyer des données à IoT Hub. La passerelle est suffisamment intelligente pour comprendre ce protocole utilisé par les appareils en aval ; toutefois, il s’agit du seul appareil avec une identité dans IoT Hub. Toutes les informations semblent provenir d’un seul appareil, la passerelle. Cela implique que les appareils en aval doivent intégrer des informations d’identification supplémentaires dans leurs messages si les applications cloud veulent analyser les données pour chaque appareil. De plus, les primitives IoT Hub comme la représentation (twin) et les méthodes (methods) sont uniquement disponibles pour l’appareil de passerelle, pas pour les appareils en aval.
* **Traduction d’identité** : les appareils qui ne peuvent pas se connecter à IoT Hub se connectent à un appareil de passerelle qui fournit la traduction de protocole et d’identité IoT Hub de la part des appareils en aval. La passerelle est suffisamment intelligente pour comprendre le protocole utilisé par les appareils en aval, leur fournir une identité et traduire les primitives IoT Hub. Les appareils en aval s’affichent dans IoT Hub comme des appareils de premier niveau avec des représentations et des méthodes. Un utilisateur qui interagit avec les appareils dans IoT Hub et n’a pas connaissance de l’appareil de passerelle intermédiaire.

![Diagrammes des modèles de passerelles][1]

## <a name="use-cases"></a>Cas d'utilisation
Tous les modèles de passerelles fournissent les avantages suivants :
* **Analytique Edge** : utilisez les services IA localement pour traiter les données provenant des appareils en aval sans envoyer d’informations de télémétrie haute fidélité au cloud. Recherchez des insights localement, réagissez-y, et envoyez uniquement un sous-ensemble de données à IoT Hub. 
* **Isolation d’appareil en aval** : l’appareil de passerelle peut protéger tous les appareils en aval contre une exposition sur Internet. Il peut être placé entre un réseau OT qui n’a pas de connectivité et un réseau IT qui fournit l’accès au web. 
* **Multiplexage des connexions** : tous les appareils se connectant à IoT Hub via un appareil IoT Edge utiliseront la même connexion sous-jacente.
* **Lissage du trafic** : l’appareil IoT Edge implémentera automatiquement une interruption exponentielle en cas de limitation d’IoT Hub, tout en assurant une conservation locale des messages. Votre solution sera ainsi résiliente face aux pics de trafic.
* **Prise en charge hors connexion limitée** : l’appareil de passerelle stockera localement les messages et mises à jour de la représentation qui ne peuvent pas être remis à IoT Hub.

Une passerelle effectue la traduction de protocole et peut également effectuer l’analytique Edge, l’isolation d’appareil, le lissage du trafic et la prise en charge hors connexion pour les appareils existants et nouveaux dont les ressources sont limitées. De nombreux appareils existants produisent des données qui peuvent alimenter les insights métier : toutefois, ils n’ont pas été conçus pour la connectivité au cloud. Les passerelles opaques permettent le déverrouillage et l’utilisation de ces données dans une solution IoT de bout en bout.

Une passerelle qui effectue la traduction d’identité fournit les avantages de la traduction de protocole et permet, en plus, la gestion complète des appareils en aval à partir du cloud. Tous les appareils dans votre solution IoT s’affichent dans IoT Hub, quel que soit le protocole qu’ils utilisent.

## <a name="cheat-sheet"></a>Aide-mémoire
Voici un aide-mémoire qui compare les primitives IoT Hub lors de l’utilisation de passerelles transparentes, opaques et de proxy.

| &nbsp; | Passerelle transparente | Traduction de protocole | Traduction d’identité |
|--------|-------------|--------|--------|
| Identités stockées dans le registre des identités IoT Hub | Identités de tous les appareils connectés | Uniquement l’identité de l’appareil de passerelle | Identités de tous les appareils connectés |
| Jumeau d’appareil | Chaque appareil connecté possède son propre jumeau d’appareil | Seule la passerelle a un appareil et des jumeaux de module | Chaque appareil connecté possède son propre jumeau d’appareil |
| Méthodes directes et messages du cloud vers l’appareil | Le cloud peut porter individuellement sur chaque appareil connecté | Le cloud ne peut porter que sur l’appareil de passerelle | Le cloud peut porter individuellement sur chaque appareil connecté |
| [Limitations et quotas d’IoT Hub][lnk-iothub-throttles-quotas] | Appliquer à chaque appareil | Appliquer à l’appareil de passerelle | Appliquer à chaque appareil |

Lorsque vous utilisez un modèle de passerelle opaque (traduction de protocole), tous les appareils qui se connectent via cette passerelle partagent la même file d’attente cloud sur l’appareil, qui peut contenir au maximum 50 messages. De ce fait, le modèle de passerelle opaque ne doit être utilisé que lorsque très peu d’appareils se connectent via la passerelle de chaque champ et que le trafic entre le cloud et l’appareil est faible.

## <a name="next-steps"></a>Étapes suivantes
Utiliser un appareil IoT Edge en tant que [passerelle transparente][lnk-iot-edge-as-transparent-gateway] 

[lnk-iot-edge-as-transparent-gateway]: ./how-to-create-transparent-gateway.md
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md

[1]: ./media/iot-edge-as-gateway/edge-as-gateway.png
