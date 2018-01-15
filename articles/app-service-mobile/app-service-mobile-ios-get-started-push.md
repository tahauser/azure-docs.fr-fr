---
title: "Ajouter des notifications Push à votre application iOS à l'aide d'Azure Mobile Apps"
description: "Découvrez comment utiliser Azure Mobile Apps pour envoyer des notifications Push à votre application iOS."
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: 
author: conceptdev
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/10/2016
ms.author: crdun
ms.openlocfilehash: 063926a5a98aeab62e191d372831a00c29b9f6d7
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="add-push-notifications-to-your-ios-app"></a>Ajout de notifications Push à votre application iOS
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Vue d'ensemble
Dans ce didacticiel, vous ajoutez des notifications Push au projet [Démarrage rapide iOS] afin qu'une notification Push soit envoyée chaque fois qu'un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devrez ajouter le package d’extension de notification Push. Consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) pour plus d’informations.

Les [notifications Push ne sont pas prises en charge par le simulateur iOS](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Vous avez besoin d’un appareil iOS physique et d’une [appartenance au programme pour développeurs Apple](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Configurer un hub de notification
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Inscrire une application pour les notifications Push
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurer Azure pour l’envoi de notifications Push
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Mettre à jour le serveur principal pour l'envoi de notifications push
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Ajouter des notifications Push à l’application
[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Tester les notifications Push
[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>En savoir plus
* Les modèles vous apportent la souplesse nécessaire pour envoyer des notifications push multiplateformes et localisées. [Utilisation de la bibliothèque cliente iOS pour Azure Mobile Apps](app-service-mobile-ios-how-to-use-client-library.md#templates) vous montre comment enregistrer des modèles.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Démarrage rapide iOS]: app-service-mobile-ios-get-started.md
