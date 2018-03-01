---
title: Routage et expressions de balise
description: Cette rubrique explique les expressions de balise et de routage pour Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: 
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: c3266698a6077e85806286fadf1f48b7194a4d88
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="routing-and-tag-expressions"></a>Routage et expressions de balise
## <a name="overview"></a>Vue d'ensemble
Les expressions de balise vous permettent de cibler des ensembles spécifiques d'appareils, ou plus précisément d'inscriptions, lors de l'envoi d'une notification push via Notification Hubs.

## <a name="targeting-specific-registrations"></a>Ciblage d'inscriptions spécifiques
La seule façon de cibler des inscriptions de notification spécifiques consiste à les associer à des balises, puis à cibler ces balises. Comme indiqué dans la rubrique [Gestion des inscriptions](notification-hubs-push-notification-registration-management.md), pour recevoir des notifications push, une application doit inscrire un appareil sur un concentrateur de notification. Lorsqu’une inscription est créée sur un concentrateur de notification, le serveur principal d'application peut envoyer des notifications push.
Le serveur principal d'application peut choisir les inscriptions à cibler avec une notification spécifique en procédant ainsi :

1. **Diffusion**: toutes les inscriptions dans le concentrateur de notification reçoivent la notification.
2. **Balise**: toutes les inscriptions qui contiennent la balise spécifiée reçoivent la notification.
3. **Expression de balise**: toutes les inscriptions dont le jeu de balises correspond à l'expression spécifiée reçoivent la notification.

## <a name="tags"></a>Balises
Une balise est une chaîne de 120 caractères maximum, contenant des caractères alphanumériques ainsi que les caractères non alphanumériques suivants : « _ », « @ », « # », « . », « : » et « - ». L'exemple suivant montre une application à partir de laquelle vous pouvez recevoir des notifications toast concernant des groupes musicaux spécifiques. Dans ce scénario, une méthode simple pour acheminer des notifications consiste à étiqueter les inscriptions avec des balises représentant les différents groupes de musique, comme dans l'image suivante.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Dans cette illustration, le message étiqueté **Beatles** est uniquement envoyé à la tablette inscrite avec la balise **Beatles**.

Pour plus d'informations sur la création d’inscriptions pour des balises, consultez [Gestion des inscriptions](notification-hubs-push-notification-registration-management.md).

Vous pouvez envoyer des notifications à des balises à l'aide des méthodes d’envoi de notifications de la classe `Microsoft.Azure.NotificationHubs.NotificationHubClient` dans le Kit de développement logiciel (SDK) [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) . Vous pouvez également utiliser Node.js ou les API REST Notifications Push.  Voici un exemple utilisant le Kit de développement logiciel (SDK).

    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Les balises n'ont pas besoin d'être provisionnées et peuvent référencer plusieurs concepts propres à l'application. Par exemple, les utilisateurs de cet exemple d'application peuvent publier des commentaires sur les groupes et recevoir des notifications toast non seulement concernant les commentaires sur leurs groupes favoris, mais également pour tous les commentaires de leurs amis, quel que soit le groupe qu’ils commentent. L'illustration suivante montre un exemple de ce scénario :

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Dans cette image, Alice s'intéresse à l’actualité des Beatles, et Bob à celle des Wailers. Bob est également intéressée par les commentaires de Charlie, et Charlie s'intéresse aux Wailers. Lorsqu'une notification est envoyée concernant un commentaire de Charlie sur les Beatles, Alice et Bob la reçoivent.

Même s’il est possible d’encoder plusieurs paramètres (par exemple, « band_Beatles » ou « follows_Charlie »), les balises sont des chaînes simples et non des propriétés avec des valeurs. Un enregistrement est mis en correspondance uniquement en présence ou en absence d'une balise spécifique.

Pour obtenir un didacticiel complet et détaillé sur la façon d'utiliser des balises pour l'envoi à des groupes d'intérêt, consultez la rubrique [Dernières nouvelles](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

## <a name="using-tags-to-target-users"></a>Utilisation de balises pour cibler des utilisateurs
Une autre façon d'utiliser des balises consiste à identifier tous les appareils d'un utilisateur particulier. Les inscriptions peuvent être étiquetées avec une balise contenant un ID utilisateur, comme dans l'illustration suivante :

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Dans cette illustration, le message étiqueté uid:Alice atteint toutes les inscriptions marquées uid:Alice et, par conséquent, tous les appareils d'Alice.

## <a name="tag-expressions"></a>Expressions de balise
Dans certains cas, une notification doit cibler un jeu d'inscriptions identifié non pas par une balise unique, mais par une expression booléenne sur des balises.

Examinons une application de sports qui envoie un rappel à tous les abonnés habitant Boston qu’un match opposera les Red Sox aux Cardinals. Si l'application cliente inscrit des balises spécifiques à ces équipes et à ce lieu, la notification doit être ciblée pour tous les abonnés de Boston qui s’intéressent aux Red Sox ou aux Cardinals. Cette condition peut être exprimée avec l'expression booléenne suivante :

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Les expressions de balise peuvent contenir tous les opérateurs booléens, notamment AND (&&), OR (||) et NOT (!). Elles peuvent également contenir des parenthèses. Les expressions de balise sont limitées à 20 balises si elles contiennent uniquement des opérateurs OR ; sinon, elles sont limitées à 6 balises.

Voici un exemple d’envoi de notifications effectué avec des expressions de balise et le Kit de développement logiciel (SDK).

    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    String userTag = "(location_Boston && !follows_Cardinals)";    

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You want info on the Red Sox</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You want info on the Red Sox</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
