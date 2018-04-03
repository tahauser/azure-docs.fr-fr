---
title: Prise en main d’Azure Notification Hubs pour les applications de plateforme Windows universelle | Microsoft Docs
description: Dans ce didacticiel, vous découvrirez comment utiliser Azure Notification Hubs pour envoyer des notifications Push à une application de plateforme Windows universelle.
services: notification-hubs
documentationcenter: windows
author: jwhitedev
manager: kpiteira
editor: ''
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 8464a7e48a02be39fb624322fac6c26f4c6c6806
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-notification-hubs-for-universal-windows-platform-apps"></a>Prise en main de Notification Hubs pour les applications de plateforme Windows universelle

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Vue d'ensemble
Cet article vous indique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à une application de plateforme Windows universelle (UWP).

L’article vous apprend à créer une application Windows Store vide qui reçoit des notifications Push au moyen du Service de notifications Windows Push (WNS). Une fois l’opération terminée, vous pouvez utiliser votre hub de notification pour diffuser des notifications Push sur tous les appareils exécutant votre application.

## <a name="before-you-begin"></a>Avant de commencer
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Vous pouvez trouver le code complet de ce didacticiel sur [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal).

## <a name="prerequisites"></a>Prérequis

Ce didacticiel requiert les éléments suivants :

* [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) ou version ultérieure
* [Outils de développement d’applications UWP installés](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
* Un compte Azure actif   
    Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page [Version d’évaluation gratuite d’Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F).
* Un compte Windows Store actif

Vous devez terminer ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications UWP.

## <a name="register-your-app-for-the-windows-store"></a>Inscription de votre application pour le Windows Store
Pour envoyer des notifications Push à des applications UWP, associez votre application au Windows Store. Ensuite, configurez votre Notification Hub pour l’intégrer à WNS.

1. Si vous n’avez pas déjà inscrit votre application, accédez à la page [Centre de développement Windows](https://dev.windows.com/overview), connectez-vous avec votre compte Microsoft, puis sélectionnez **Créer une application**.

2. Saisissez un nom pour votre application et sélectionnez **Réserver le nom d’application**. La nouvelle inscription au Windows Store pour votre application est alors créée.

3. Dans Visual Studio, créez un projet d’application du Windows Store Visual C# en utilisant le modèle UWP **Application vide**, puis sélectionnez **OK**.

4. Acceptez les valeurs par défaut pour les versions de plateforme cible et minimale.

5. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet d’application Windows Store, sélectionnez **Store**, puis **Associer l’application au Windows Store**.  
    L'Assistant **Associer votre application au Windows Store** s'affiche.

6. Dans l’Assistant, connectez-vous avec votre compte Microsoft.

7. Sélectionnez l’application inscrite à l’étape 2, puis sélectionnez **Suivant** et **Associer**. Cela ajoute les informations d’inscription Windows Store requises au manifeste de l’application.

8. De nouveau sur la page [Centre de développement Windows](http://dev.windows.com/overview) pour votre nouvelle application, sélectionnez **Services**, puis **Notifications Push** et enfin **WNS/MPNS**.

9. Sélectionnez **Nouvelle notification**.

10. Sélectionnez le modèle **Vide (Toast)**, puis **OK**.

11. Saisissez un **nom** et un message de **contexte** Visual pour la notification, puis sélectionnez **Enregistrer en tant que brouillon**.

12. Accédez au [portail d’inscription des applications](http://apps.dev.microsoft.com) et connectez-vous.

13. Sélectionnez le nom de votre application. Dans les paramètres de plateforme **Windows Store**, notez le mot de passe **Clé secrète d’application** et la valeur **Identificateur de sécurité (SID) du package**.

    >[!WARNING]
    >Le secret d’application et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces valeurs avec quiconque et ne les distribuez pas avec votre application.

## <a name="configure-your-notification-hub"></a>Configuration de votre hub de notification
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Sous <b>Notification Services</b>, sélectionnez <b>Windows (WNS)</b>, puis entrez le mot de passe secret de l’application dans le champ <b>Clé de sécurité</b>. Dans le champ <b>SID du package</b>, saisissez la valeur que vous avez obtenue auprès de WNS à la section précédente, puis sélectionnez <b>Enregistrer</b>.</p>
</li>
</ol>

![Les zones SID du Package et Clé de sécurité](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Votre Notification Hub est désormais configuré pour fonctionner avec WNS. Vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications.

## <a name="connect-your-app-to-the-notification-hub"></a>Connexion de votre application au hub de notification
1. Dans Visual Studio, cliquez avec le bouton droit sur la solution, puis sélectionnez **Gérer les packages NuGet**.  
    La fenêtre **Gérer les packages NuGet** s’ouvre.

2. Dans la zone de recherche, saisissez **Microsoft.Azure.NotificationHubs**, sélectionnez **Installer**et acceptez les conditions d’utilisation.
   
    ![La fenêtre Gérer les packages NuGet][20]
   
    Cette action télécharge, installe et ajoute une référence à la bibliothèque Azure Notification Hubs pour Windows à l’aide du [package NuGet Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).

3. Ouvrez le fichier projet App.xaml.cs et ajoutez les instructions `using` qui suivent : 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. Dans le fichier App.xaml.cs, ajoutez également la définition de méthode **InitNotificationsAsync** suivante à la classe **App** :
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
   
        }
   
    Ce code récupère l’URI de canal pour l’application dans WNS et l’inscrit avec votre Notification Hub.
   
    >[!NOTE]
    >* Remplacez l’espace réservé **nom de hub** par le nom du hub de notification affiché sur le Portail Azure. 
    >* Remplacez également l’espace réservé de la chaîne de connexion par la chaîne de connexion **DefaultListenSharedAccessSignature** que vous avez obtenue sur la page **Stratégies d’accès** de votre hub de notification dans une section précédente.
   > 
   > 
5. En haut du gestionnaire d'événements **OnLaunched** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **InitNotificationsAsync** :
   
        InitNotificationsAsync();
   
    Cette action garantit l’inscription de l’URI de canal dans votre Notification Hub chaque fois que l’application est lancée.

6. Pour lancer l’application, appuyez sur **F5**. Une boîte de dialogue s’affiche avec la clé d’inscription.

Votre application est maintenant prête à recevoir des notifications toast.

## <a name="send-notifications"></a>Envoi de notifications
Vous pouvez tester rapidement la réception de notifications dans votre application en envoyant des notifications dans le [Portail Azure](https://portal.azure.com/). Utilisez le bouton **Test d’envoi** sur le hub de notification, comme illustré ci-dessous :

![Le volet du test d’envoi](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Les notifications Push sont normalement envoyées dans un service principal tel que Mobile Services ou ASP.NET à l’aide d’une bibliothèque compatible. Si aucune bibliothèque n’est disponible pour votre serveur principal, vous pouvez également utiliser l’API REST directement pour envoyer des messages de notification. 

Ce didacticiel vous montre comment tester votre application cliente simplement en envoyant des notifications qui utilisent le Kit de développement logiciel (SDK) .NET pour Notification Hubs dans une application console au lieu d’un service principal. Nous vous recommandons de consulter le didacticiel [Utiliser Notification Hubs pour envoyer des notifications Push aux utilisateurs] comme prochaine étape pour envoyer des notifications à partir d’un serveur principal ASP.NET. Toutefois, vous pouvez envoyer des notifications à l’aide de l’une des approches suivantes :

* **Interface REST** : vous pouvez prendre en charge les notifications sur n’importe quel serveur principal à l’aide de [l’interface REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **SDK .NET Microsoft Azure Notification Hubs** : dans le Gestionnaire de package NuGet pour Visual Studio, exécutez [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js** : voir [Utilisation de Notification Hubs à partir de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* **Applications mobiles Azure** : pour découvrir un exemple de la procédure d’envoi de notifications à partir d’une application mobile Azure intégrée à Notification Hubs, consultez l’article [Ajouter des notifications Push pour Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

* **Java ou PHP** : pour voir des exemples d’envoi de notification au moyen des API REST, consultez :
    * [Java](notification-hubs-java-push-notification-tutorial.md)
    * [PHP](notification-hubs-php-push-notification-tutorial.md)

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils Windows en utilisant le portail ou une application de console. Pour votre prochaine étape, nous vous recommandons de consulter le didacticiel [Utiliser Notification Hubs pour envoyer des notifications Push aux utilisateurs]. Il vous montre comment envoyer des notifications à partir d’un serveur principal ASP.NET en utilisant des balises pour cibler des utilisateurs spécifiques.

Pour segmenter vos utilisateurs par groupes d'intérêt, consultez la page [Utilisation des Notification Hubs pour diffuser les dernières nouvelles]. 

Pour plus d’informations générales sur Notification Hubs, consultez la section [Recommandations relatives à Notification Hubs](notification-hubs-push-notification-overview.md).

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Utiliser Notification Hubs pour envoyer des notifications Push aux utilisateurs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
