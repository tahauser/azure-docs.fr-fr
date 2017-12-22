---
title: "Notifications Push sécurisées avec Azure Notification Hubs"
description: "Découvrez comment envoyer des notifications Push sécurisées dans Azure. Exemples de code écrits en C# à l’aide de l’API .NET."
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 9c626ec1534c4899588150a58c0da57b9d963f6f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="azure-notification-hubs-secure-push"></a>Notifications Push sécurisées avec Azure Notification Hubs
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Vue d'ensemble
La prise en charge des notifications Push dans Microsoft Azure vous permet d’accéder à une infrastructure Push conviviale, multiplateforme avec montée en charge qui simplifie fortement l’implémentation des notifications Push pour les applications grand public et d’entreprise destinées aux plateformes mobiles.

En raison de contraintes liées à la réglementation ou à la sécurité, une application peut avoir besoin d'inclure dans la notification des informations qui ne peuvent pas être transmises via l'infrastructure de notification Push standard. Ce didacticiel montre comment procéder en envoyant des informations sensibles par l'intermédiaire d'une connexion authentifiée sécurisée entre l'appareil client et le serveur principal de l'application.

Globalement, le processus est le suivant :

1. Le serveur principal de l'application :
   * stocke la charge utile sécurisée dans la base de données principale ;
   * envoie l'ID de cette notification à l'appareil (aucune information sécurisée n'est envoyée).
2. L'application qui se trouve sur l'appareil, lorsqu'elle reçoit la notification :
   * L'appareil contacte le serveur principal en demandant la charge utile sécurisée.
   * L'application peut afficher la charge utile sous la forme d'une notification sur l'appareil.

Veuillez noter que dans le flux précédent (et dans ce didacticiel), nous partons du principe que l’appareil stocke un jeton d’authentification dans un stockage local, une fois l’utilisateur connecté. Cela simplifie nettement l’expérience, car l’appareil peut récupérer la charge utile sécurisée en utilisant ce jeton. Si votre application ne stocke pas les jetons d'authentification sur l'appareil, ou si ces jetons sont susceptibles d'expirer, lorsque l'application sur l'appareil reçoit la notification, elle doit afficher une notification générique demandant à l'utilisateur de lancer l'application. L'application authentifie alors l'utilisateur et affiche la charge utile de la notification.

Ce didacticiel sur les notifications Push sécurisées montre comment envoyer une notification Push en toute sécurité. Il s’appuie sur le didacticiel [Envoi de notifications à des utilisateurs](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md). Vous devez donc suivre ce dernier au préalable.

> [!NOTE]
> Ce didacticiel part du principe que vous avez créé et configuré votre hub de notification comme décrit dans [Prise en main de Notification Hubs (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Notez également que Windows Phone 8.1 nécessite des informations d’identification Windows (pas Windows Phone) et que les tâches en arrière-plan ne fonctionnent pas sur Windows Phone 8.0 ou Silverlight 8.1. Pour les applications Windows Store, vous pouvez recevoir des notifications via une tâche en arrière-plan uniquement si l'écran de verrouillage activé pour l'application (activez la case à cocher dans Appmanifest).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modification du projet Windows Phone
1. Dans le projet **NotifyUserWindowsPhone** , ajoutez le code suivant à App.xaml.cs afin d'enregistrer la tâche en arrière-plan pour les notifications Push. Ajoutez la ligne de code ci-après à la fin de la méthode `OnLaunched()` :
   
        RegisterBackgroundTask();
2. Toujours dans App.xaml.cs, ajoutez le code ci-dessous juste après la méthode `OnLaunched()` :
   
        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();
   
                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }
3. Ajoutez les instructions `using` suivantes au début du fichier App.xaml.cs :
   
        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;
4. Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Enregistrer tout**.

## <a name="create-the-push-background-component"></a>Création du composant en arrière-plan pour les notifications Push
L’étape suivante consiste à créer le composant en arrière-plan pour les notifications Push.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nœud de niveau supérieur de la solution (**Solution SecurePush** dans le cas présent). Cliquez ensuite sur **Ajouter**, puis sur **Nouveau projet**.
2. Développez **Applications du Windows Store** et cliquez sur **Applications Windows Phone**, puis sur **Composant Windows Runtime (Windows Phone)**. Nommez le projet **PushBackgroundComponent**, puis cliquez sur **OK** pour créer le projet.
   
    ![][12]
3. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **PushBackgroundComponent (Windows Phone 8.1)**. Cliquez ensuite sur **Ajouter**, puis sur **Classe**. Nommez la nouvelle classe **PushBackgroundTask.cs**. Cliquez sur **Ajouter** pour générer la classe.
4. Remplacez l’ensemble du contenu de la définition de l’espace de noms **PushBackgroundComponent** par le code ci-après en remplaçant l’espace réservé `{back-end endpoint}` par le point de terminaison du serveur principal obtenu lors du déploiement de votre serveur principal :
   
        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }
   
            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";
   
                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;
   
                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);
   
                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);
   
                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);
   
                    ShowToast(notification);
   
                    deferral.Complete();
                }
   
                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }
5. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **PushBackgroundComponent (Windows Phone 8.1)**, puis cliquez sur **Gérer les packages NuGet**.
6. Dans la partie gauche, cliquez sur **En ligne**.
7. Dans la zone **Rechercher**, entrez **Client Http**.
8. Dans la liste de résultats, cliquez sur **Bibliothèques clientes HTTP Microsoft**, puis cliquez sur **Installer**. Terminez l'installation.
9. De retour dans la zone **Recherche** NuGet, tapez **Json.net**. Installez le package **Json.NET** , puis fermez la fenêtre du Gestionnaire de package NuGet.
10. Ajoutez les instructions `using` suivantes au début du fichier **PushBackgroundTask.cs** :
    
        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;
11. Dans l’Explorateur de solutions, dans le projet **NotifyUserWindowsPhone (Windows Phone 8.1)**, cliquez avec le bouton droit sur **Références**, puis cliquez sur **Ajouter une référence...**. Dans la boîte de dialogue Gestionnaire de références, cochez la case en regard de **PushBackgroundComponent**, puis cliquez sur **OK**.
12. Dans l’Explorateur de solutions, double-cliquez sur **Package.appxmanifest** dans le projet **NotifyUserWindowsPhone (Windows Phone 8.1)**. Sous **Notifications**, définissez **Compatible toast** sur **Oui**.
    
    ![][3]
13. Toujours dans **Package.appxmanifest**, cliquez en haut sur le menu **Déclarations**. Dans la liste déroulante **Déclarations disponibles**, cliquez sur **Tâches en arrière-plan**, puis cliquez sur **Ajouter**.
14. Dans **Package.appxmanifest**, sous **Propriétés**, cochez **Notification Push**.
15. Dans **Package.appxmanifest**, sous **Paramètres de l’application**, tapez **PushBackgroundComponent.PushBackgroundTask** dans le champ **Point d’entrée**.
    
    ![][13]
16. Dans le menu **Fichier**, cliquez sur **Enregistrer tout**.

## <a name="run-the-application"></a>Exécution de l'application
Pour exécuter l'application, procédez comme suit :

1. Dans Visual Studio, exécutez l'application d'API web **AppBackend** . Une page web ASP.NET s'affiche.
2. Dans Visual Studio, exécutez l'application Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)** . L'émulateur Windows Phone s'exécute et charge l'application automatiquement.
3. Dans l'interface utilisateur de l'application **NotifyUserWindowsPhone** , entrez un nom d'utilisateur et un mot de passe. La valeur peut être une chaîne quelconque, mais elle doit être identique pour les deux.
4. Dans l’interface utilisateur de l’application **NotifyUserWindowsPhone**, cliquez sur **Se connecter et s’inscrire**. Cliquez ensuite sur **Send push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
