---
title: Comparer Event Grid et le routage pour IoT Hub | Microsoft Docs
description: "IoT Hub offre son propre service de routage de messages, mais il s’intègre également à Event Grid pour la publication d’événement. Comparez les deux fonctionnalités."
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 5a0a97ccf033b2981ba13be455482146ba212228
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Comparer le routage des messages et Event Grid pour IoT Hub

Azure IoT Hub permet de diffuser des données à partir de vos appareils connectés et d’intégrer ces données dans vos applications métier. IoT Hub propose deux méthodes permettant d’intégrer les événements IoT dans d’autres services Azure ou applications métier. Cet article décrit les deux fonctions qui offrent cette fonctionnalité. Vous pourrez ainsi choisir l’option qui convient le mieux à votre scénario.

* **Routage des messages IoT Hub** : cette fonctionnalité d’IoT Hub permet aux utilisateurs [d’acheminer les messages appareil-à-cloud](iot-hub-devguide-messages-read-custom.md) vers des points de terminaison de service tels que des conteneurs de stockage Azure, des hubs d’événements, des files d’attente Service Bus et des rubriques Service Bus. Les règles de routage procurent la souplesse nécessaire pour établir des itinéraires reposant sur des requêtes. Elles permettent également de créer des [alertes critiques](iot-hub-devguide-messages-d2c.md) qui déclenchent des actions par le biais de requêtes, et peuvent reposer sur les en-têtes et le corps de message. 
* **Intégration d’IoT Hub avec Event Grid** : Azure Event Grid est un service de routage d’événement entièrement géré qui utilise le modèle Publication-Abonnement. IoT Hub et Event Grid collaborent afin [d’intégrer les événements IoT Hub dans les services Azure et non-Azure](iot-hub-event-grid.md), quasiment en temps réel. 

## <a name="similarities-and-differences"></a>Similitudes et différences

Le routage des messages et Event Grid permettent tous deux de configurer des alertes, mais il existe entre eux des différences clés. Pour plus dé détails, consultez le tableau suivant :

| Fonctionnalité | Routage des messages IoT Hub | Intégration d’IoT Hub avec Event Grid |
| ------- | --------------- | ---------- |
| **Messages des appareils** | Oui, le routage des messages peut être utilisé pour les données de télémétrie. | Non, Event Grid peut être utilisé uniquement pour les événements IoT Hub autres que les événements de télémétrie. |
| **Type d’événement** | Oui, le routage des messages peut signaler les modifications de jumeau et les événements de cycle de vie d’appareil. | Oui, Event Grid peut signaler quand des appareils sont inscrits auprès d’un IoT Hub et quand des appareils sont supprimés. |
| **Ordonnancement** | Oui, l’ordonnancement des événements est conservé.  | Non, l’ordonnancement des événements n’est pas garanti. | 
| **Taille de message maximale** | 256 Ko, appareil-à-cloud | 64 Ko |
| **Filtrage** | Filtrage enrichi grâce au langage de type SQL, avec prise en charge du filtrage sur les corps et les en-têtes de messages. Pour obtenir des exemples, consultez [Langage de requête IoT Hub](iot-hub-devguide-query-language.md). | Filtrage basé sur le suffixe/préfixe d’ID d’appareil, qui fonctionne bien pour les services hiérarchiques tels que le stockage. |
| **Points de terminaison** | <ul><li>Hub d’événements</li> <li>Objet blob de stockage</li> <li>File d’attente Service Bus</li> <li>Rubriques Service Bus</li></ul><br>Les références SKU IoT Hub payantes (S1, S2 et S3) sont limitées à 10 points de terminaison personnalisés. Vous pouvez créer 100 itinéraires par hub IoT. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hub</li> <li>Logic Apps</li> <li>Microsoft Flow</li> <li>Services tiers par le biais de WebHooks</li></ul><br>Pour obtenir la liste la plus récente des points de terminaison, consultez [Gestionnaires d’événements Event Grid](../event-grid/overview.md#event-handlers). |
| **Coût** | Le routage des messages n’est pas facturé séparément. Seule l’entrée de télémétrie dans IoT Hub est facturée. Par exemple, si vous avez un message acheminé vers trois points de terminaison différents, un seul message vous est facturé. | Aucun frais IoT Hub n’est facturé. Avec Event Grid, les 100 000 premières opérations par mois sont gratuites. Ensuite, le coût est de 0,60 $ par million d’opérations. |

Le routage des messages IoT Hub et Event Grid offrent aussi quelques similitudes, dont certaines sont détaillées dans le tableau suivant :

| Fonctionnalité | Routage des messages IoT Hub | Intégration d’IoT Hub avec Event Grid |
| ------- | --------------- | ---------- |
| **Fiabilité** | Élevée : remet chaque message au point de terminaison au moins une fois pour chaque itinéraire. Provoque l’expiration de tous les messages qui ne sont pas remis dans l’heure. | Élevée : remet chaque message au webhook au moins une fois pour chaque abonnement. Provoque l’expiration de tous les événements qui ne sont pas remis dans les 24 heures. | 
| **Scalabilité** | Élevée : optimisé pour prendre en charge des millions d’appareils connectés simultanément et envoyant plusieurs millions de messages. | Élevée : capable d’acheminer 10 000 000 d’événements par seconde et par région. |
| **Latence** | Faible : quasiment en temps réel. | Faible : quasiment en temps réel. |
| **Envoi à plusieurs points de terminaison** | Oui, envoi d’un message unique à plusieurs points de terminaison. | Oui, envoi d’un message unique à plusieurs points de terminaison.  | 
| **Sécurité** | Iot Hub fournit une identité par appareil et un contrôle d’accès révocable. Pour plus d’informations, consultez [Contrôle d’accès IoT Hub](iot-hub-devguide-security.md). | Event Grid fournit une validation à trois points : abonnements aux événements, publication d’événements et remise d’événements webhook. Pour en savoir plus, consultez la page [Sécurité et authentification pour Event Grid](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Choix

Le routage des messages IoT Hub et l’intégration d’IoT Hub avec Event Grid effectuent différentes actions pour parvenir à des résultats similaires. Tous deux prennent des informations à partir de votre solution IoT Hub et les transmettent afin que d’autres services puissent réagir. Comment choisir entre les deux ? En plus des données de la section précédente, répondez aux questions suivantes pour vous aider à prendre une décision : 

* **Quel type de données envoyez-vous aux points de terminaison ?**

   Utilisez le routage des messages IoT Hub quand vous devez envoyer des données de télémétrie à d’autres services. Le routage des messages permet également d’interroger les en-têtes de messages et les corps de messages. 

   L’intégration d’IoT Hub avec Event Grid fonctionne avec les événements qui se produisent dans le service IoT Hub. Ces événements IoT Hub incluent la création et la suppression d’appareil. 

* **Quels points de terminaison doivent recevoir ces informations ?**

   Le routage des messages IoT Hub prend en charge des points de terminaison limités, mais vous pouvez créer des connecteurs pour réacheminer les données et les événements vers des points de terminaison supplémentaires. Pour obtenir une liste complète des points de terminaison pris en charge, consultez le tableau dans la section précédente. 

   L’intégration d’IoT Hub avec Event Grid prend en charge plusieurs points de terminaison. Elle fonctionne également avec des webhooks pour étendre le routage en dehors de l’écosystème de services Azure et dans les applications métier tierces. 

* **Est-il important que vos données arrivent dans l’ordre ?**

   Le routage des messages IoT Hub conserve l’ordre dans lequel les messages sont envoyés. Ainsi, l’ordre d’arrivée est le même.

   Event Grid ne garantit pas que les points de terminaison recevront les événements dans l’ordre dans lequel ils se sont produits. Toutefois, le schéma d’événement comprend un horodatage que vous pouvez utiliser pour identifier l’ordre une fois les événements arrivés au point de terminaison. 

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur le [routage des messages IoT Hub](iot-hub-devguide-messages-d2c.md) et les [points de terminaison IoT Hub](iot-hub-devguide-endpoints.md).

* Apprenez-en davantage sur [Azure Event Grid](../event-grid/overview.md).

* Testez l’intégration d’Event Grid en [envoyant des notifications par e-mail à propos des événements Azure IoT Hub à l’aide de Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).