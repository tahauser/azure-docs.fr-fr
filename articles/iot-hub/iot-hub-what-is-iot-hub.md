---
title: "Présentation d’Azure IoT Hub | Microsoft Docs"
description: "Présentation du service Azure IoT Hub : signification du IoT Hub, connectivité des appareils, modèles de communication Internet des Objets, passerelles et modèle de communication de service assistée"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b00c89183ff0d4e7df49d29834508643e68246bb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="overview-of-the-azure-iot-hub-service"></a>Présentation du service Azure IoT Hub

Bienvenue dans Azure IoT Hub. Cet article fournit une vue d’ensemble d’Azure IoT Hub et décrit la raison pour laquelle il est recommandé d’utiliser ce service afin de mettre en œuvre une solution IoT. Azure IoT Hub est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur principal de solution. Azure IoT Hub :

* propose plusieurs options de communication appareil-à-cloud et cloud-à-appareil. Ces options comprennent la messagerie unidirectionnelle, le transfert de fichiers et les méthodes de demande-réponse.
* Intègre une fonctionnalité de routage des messages déclaratifs vers d’autres services Azure.
* Fournit un stockage utilisable dans une requête pour les métadonnées d’appareil et les informations d’état synchronisées.
* Assure la sécurité des communications et le contrôle d’accès grâce aux clés de sécurité par appareil ou aux certificats X.509.
* Fournit une surveillance complète de la connectivité des appareils et des événements de gestion de l’identité des appareils.
* Inclut des bibliothèques d’appareils pour les langages et les plateformes les plus courants.

L’article [Comparaison entre IoT Hub et Event Hubs][lnk-compare] décrit les différences principales entre ces deux services et présente les avantages liés à l’utilisation d’IoT Hub dans vos solutions IoT.

Pour plus d’informations sur la façon dont Azure et IoT Hub permettent de sécuriser votre solution IoT, reportez-vous à [Sécurisation de l’Internet des objets de bout en bout][lnk-security-ground-up].

![Azure IoT Hub comme passerelle cloud dans une solution Internet des objets][img-architecture]

> [!NOTE]
> Pour une étude approfondie de l’architecture IoT, consultez le document [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Microsoft Azure IoT : Architecture de référence).

## <a name="iot-device-connectivity-challenges"></a>Défis liés à la connectivité des appareils IoT

IoT Hub et les bibliothèques d’appareils vous aident à relever les défis en matière de fiabilité et de sécurisation de la connexion d’appareil au serveur principal de la solution. Les appareils IoT :

* sont souvent des systèmes intégrés, qui ne font appel à aucun opérateur humain ;
* peuvent être situés sur des sites distants avec un accès physique coûteux ;
* sont accessibles uniquement via le serveur principal de la solution ;
* peuvent avoir des performances et/ou des ressources de traitement limitées ;
* peuvent avoir une connectivité réseau intermittente, lente ou coûteuse ;
* peuvent nécessiter l’utilisation des protocoles d’application personnalisés, propriétaires ou spécifiques à un secteur.
* peuvent être créés à l’aide d’un large éventail de plateformes matérielles et logicielles populaires.

Outre les exigences ci-dessus, n’importe quelle solution IoT doit également offrir des possibilités d’évolution, la sécurité et la fiabilité. Il en résulte un ensemble d’exigences de connectivité complexe, long à mettre en place lorsque vous utilisez des technologies traditionnelles telles que des conteneurs web et des courtiers de messagerie.

## <a name="why-use-azure-iot-hub"></a>Pourquoi utiliser Azure IoT Hub ?

Azure IoT Hub propose un ensemble enrichi d’options de communication [d’appareil-à-cloud][lnk-d2c-guidance] et [de cloud-à-appareil][lnk-c2d-guidance]. En outre, Azure IoT Hub résout les défis créés par une connexion fiable et sécurisée à des appareils, comme suit :

* **Représentations d’appareil physique**. À l’aide de [représentations d’appareil physique][lnk-twins], vous pouvez stocker, synchroniser et interroger les informations de métadonnées et d’état de l’appareil. Les représentations d’appareil sont des documents JSON qui stockent des informations sur l’état des appareils (métadonnées, configurations et conditions). IoT Hub conserve un jumeau d’appareil pour chaque appareil que vous y connectez.

* **Authentification par appareil et connectivité sécurisée**. Vous pouvez configurer chaque appareil avec sa propre [clé de sécurité][lnk-devguide-security] pour lui permettre de se connecter à IoT Hub. Le [Registre d’identité IoT Hub][lnk-devguide-identityregistry] stocke les clés et les identités des appareils dans une solution. Un serveur principal de la solution peut ajouter des appareils individuels à une liste d’autorisation ou de refus, permettant ainsi un contrôle complet de l’accès à l’appareil.

* **Routage des messages appareil-à-cloud vers les services Azure en fonction de règles déclaratives**. IoT Hub vous permet de définir des itinéraires de messages reposant sur des règles de routage pour contrôler la destination à laquelle votre Hub envoie les messages appareil-à-cloud. Les règles de routage ne nécessitent aucune écriture de code et peuvent remplacer des répartiteurs de messages post-ingestion personnalisés.

* **Intégrer des événements IoT Hub à votre application métier**. IoT Hub s’intègre à Azure Event Grid. Utilisez cette intégration pour configurer d’autres services Azure ou applications tierces pour guetter des événements IoT Hub. Azure Event Grid vous permet de réagir rapidement à des événements critiques de façon sûre, évolutive et sécurisée.

* **Surveillance des opérations de connectivité des appareils**. Vous pouvez recevoir des journaux d’opérations détaillés sur les opérations de gestion de l’identité des appareils et sur les événements de connectivité des appareils. Cette fonctionnalité de surveillance permet à votre solution IoT pour identifier les problèmes de connectivité. Utilisez ces journaux pour identifier les appareils qui fournissent des informations d’identification incorrectes, envoient des messages trop fréquemment ou rejettent tous les messages cloud-à-appareil.

* **Un ensemble complet de bibliothèques d’appareils**. Les [Kits de développement logiciel (SDK) d’appareils Azure IoT][lnk-device-sdks] sont disponibles et pris en charge pour différents langages et plateformes : C pour de nombreuses distributions Linux, Windows et les systèmes d’exploitation en temps réel. Les Kits de développement logiciel (SDK) d’appareil Azure IoT prennent également en charge les langages gérés tels que C#, Java et JavaScript.

* **Protocoles et possibilités d’extension IoT**. Si votre solution ne peut pas utiliser les bibliothèques d’appareils, IoT Hub propose un protocole public qui permet aux appareils d’utiliser nativement les protocoles MQTT 3.1.1, HTTPS 1.1 ou AMQP 1.0. Vous pouvez également étendre IoT Hub afin de prendre en charge des protocoles personnalisés en :

  * Créant une passerelle de champ avec [Azure IoT Edge][lnk-iot-edge] qui convertit votre protocole personnalisé en un protocole pris en charge par IoT Hub.
  * Personnalisant la [passerelle de protocole Azure IoT][protocol-gateway], composant open source qui s’exécute dans le cloud.

* **Mettant à l’échelle**. Azure IoT Hub peut supporter des millions d’appareils connectés simultanément et des millions d’événements par seconde.

* **Approvisionnement des appareils**. Le service [IoT Hub Device Provisioning](https://docs.microsoft.com/azure/iot-dps/) est un service d’assistance pour IoT Hub qui permet d’effectuer un approvisionnement sans contact juste-à-temps sur le hub IoT approprié, sans intervention humaine. Vous pouvez ainsi approvisionner des millions d’appareils de façon sécurisée et évolutive.

## <a name="gateways"></a>Passerelles

Une passerelle dans une solution IoT est généralement une [passerelle de protocole][lnk-iotedge] déployée dans le cloud ou une [passerelle de champ][lnk-field-gateway] déployée en local avec vos appareils.

Une _passerelle de protocole_ effectue la traduction de protocole, par exemple MQTT à AMQP.

Une _passerelle de champ_ peut :

* Effectuer des analyses précises.
* Prendre des décisions en peu de temps pour réduire la latence.
* Fournir des services de gestion d’appareil.
* Appliquer des contraintes relatives à la confidentialité et à la sécurité.
* Traduire des protocoles.

Les deux types de passerelle agissent comme intermédiaire entre vos appareils et IoT Hub.

Une passerelle de champ est différente d’un appareil de routage de trafic simple, par exemple un pare-feu ou un appareil de traduction d’adresses réseau, car elle a généralement un rôle actif dans la gestion de l’accès et du flux des informations dans votre solution.

Une solution peut inclure des passerelles de protocole et de champ.

## <a name="how-does-iot-hub-work"></a>Comment IoT Hub fonctionne-t-il ?

Azure IoT Hub met en œuvre le modèle de [communications assistées par service][lnk-service-assisted-pattern] pour gérer les interactions entre vos appareils et votre serveur principal de service. L’objectif de ce modèle consiste à établir des itinéraires de communication bidirectionnels fiables entre un système de contrôle (comme IoT Hub) et les appareils à usage spécifique dans des espaces physiques non approuvés. Le modèle établit les principes suivants :

* La sécurité est prioritaire sur toutes les autres fonctionnalités.

* Les appareils n’acceptent pas les informations réseau non sollicitées. Un appareil établit tous les itinéraires et connexions pour le trafic sortant uniquement. Pour qu’un appareil reçoive une commande de la part du serveur principal de la solution, il doit régulièrement établir une connexion pour vérifier toutes les commandes en attente de traitement.

* Les appareils doivent uniquement connecter ou établir des itinéraires vers des services bien connus auxquels ils sont couplés, par exemple un IoT Hub.

* L’itinéraire de communication entre l’appareil et le service ou entre l’appareil et la passerelle est sécurisé au niveau du protocole d’application.

* L’authentification et l’autorisation au niveau du système sont basées sur les identités par appareil. Les autorisations et informations d’identification sont ainsi révocables presque instantanément.

* Pour les appareils connectés de façon sporadique en raison de problèmes d’alimentation ou de connectivité, la communication bidirectionnelle est effectuée via la mise en attente de commandes et de notifications, jusqu’à ce qu’un appareil soit connecté pour les recevoir. IoT Hub gère des files d’attente spécifiques à un appareil pour les commandes qu’il envoie.

* Les données de charge utile d’application sont sécurisées séparément dans le cadre d’un transit protégé via des passerelles vers un service particulier.

L’industrie mobile utilise le schéma de communication assistée par service afin d’implémenter des services de notification Push tels que les [Services de notification Push Windows][lnk-wns], [Google Cloud Messaging][lnk-google-messaging] et [Apple Push Notification Service][lnk-apple-push].

IoT Hub est pris en charge sur le chemin d’accès d’homologation publique ExpressRoute.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à écrire du code et à exécuter certains exemples, consultez le didacticiel [Prise en main d’IoT Hub][lnk-get-started].

Pour plus d’informations sur l’envoi de messages à partir d’un appareil et sur la réception de ces messages à partir d’IoT Hub, ainsi que sur la configuration des itinéraires de messages, consultez l’article [Envoyer et recevoir des messages avec IoT Hub][lnk-send-messages].

Pour découvrir comment IoT Hub permet une gestion des appareils basée sur des normes pour que vous puissiez gérer, configurer et mettre à jour à distance vos appareils, consultez l’article [Vue d’ensemble de la gestion des appareils avec IoT Hub][lnk-device-management].

Pour implémenter des applications clientes sur un large éventail de plateformes matérielles et de systèmes d’exploitation d’appareils, vous pouvez utiliser les Kits de développement logiciel (SDK) d’appareils Azure IoT. Le kit de développement logiciel (SDK) contient des bibliothèques qui facilitent l’envoi de télémétrie à un IoT Hub et la réception de messages cloud-vers-appareil. Avec les Kits de développement logiciel (SDK) d’appareils, vous avez le choix parmi divers protocoles réseau pour communiquer avec IoT Hub. Pour plus d’informations, consultez la rubrique [plus d’informations sur les kits de développement logiciel (SDK) d’appareils][lnk-device-sdks].

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Communication de service assistée, billet de blog de Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-iotedge]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-iot-edge]: https://docs.microsoft.com/azure/iot-edge/
[lnk-send-messages]: iot-hub-devguide-messaging.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-security-ground-up]: iot-hub-security-ground-up.md
