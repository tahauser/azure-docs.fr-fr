---
title: "Ajout de notifications Push à votre application Xamarin.Android | Microsoft Docs"
description: "Découvrez comment utiliser Azure App Service et Azure Notification Hubs pour envoyer des notifications Push à votre application Xamarin Android."
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: 
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: c7d2b68d2b523a04c38881d53747adfbb8b295cc
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Ajouter des notifications push à votre application Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Vue d'ensemble
Dans ce didacticiel, vous ajoutez des notifications Push au projet [Démarrage rapide Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md) afin qu'une notification Push soit envoyée chaque fois qu'un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devrez ajouter le package d’extension de notification Push. Consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) pour plus d’informations.

## <a name="prerequisites"></a>Conditions préalables
Ce didacticiel requiert les éléments suivants :

* Un compte Google actif. Vous pouvez vous connecter à un compte Google sur [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
* Le composant client [Google Cloud Messaging](http://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Configurer un nouveau hub de notification
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Activation de Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Configuration d’Azure pour l’envoi de demandes push
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Mettre à jour le projet de serveur pour l’envoi de notifications Push
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Configurer le projet client pour les notifications push
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Ajouter le code des notifications push à votre application
[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Tester les notifications push dans votre application
Vous pouvez tester l’application à l’aide d’un appareil virtuel dans l’émulateur. Des étapes de configuration supplémentaires sont requises lors de l’exécution sur un émulateur.

1. Assurez-vous de procéder au déploiement ou au débogage sur un périphérique virtuel sur lequel les API Google sont définis comme cible, comme indiqué dans le Gestionnaire d’appareil virtuel Android (AVD).
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)
2. Ajouter un compte Google à l’appareil Android en cliquant sur **Applications** > **Paramètres** > **Ajouter un compte**, puis suivez les invites.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)
3. Exécutez normalement l’application todolist et insérez un nouvel élément todo. Cette fois, une icône de notification s’affiche dans la zone de notification. Vous pouvez ouvrir le tiroir de notification pour afficher le texte intégral de la notification.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
