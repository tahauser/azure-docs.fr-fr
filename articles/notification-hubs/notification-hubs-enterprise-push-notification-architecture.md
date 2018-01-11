---
title: "Notification Hubs - Architecture Push d’entreprise"
description: "Aide sur l’utilisation d’Azure Notification Hubs dans un environnement d’entreprise"
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: c626d6415a27f8495304eeaab480ab62606102ea
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="enterprise-push-architectural-guidance"></a>Guide architectural des notifications Push d’entreprise
Les entreprises se tournent aujourd’hui progressivement vers la création d’applications mobiles pour leurs clients (externes) ou leurs collaborateurs (internes). Ils disposent de systèmes principaux, qu’il s’agisse de grands systèmes ou de certaines applications métiers, qui doivent être intégrés à l’architecture de l’application mobile. Ce guide vous présente comment réussir au mieux cette intégration, en recommandant des solutions possibles pour les scénarios habituels.

Une exigence fréquente est l’envoi de notifications Push aux utilisateurs via leurs applications mobiles, lorsqu’un événement se produit dans les systèmes principaux. Par exemple, un client d’une banque disposant d’une application bancaire sur son iPhone souhaite être averti lorsque son compte est débité d’un montant supérieur à un seuil défini ou un scénario intranet dans lequel un employé du service financier disposant d’une application d’approbation du budget sur son téléphone Windows Phone souhaite être averti lorsqu’il reçoit une demande d’approbation.

Le traitement des comptes bancaires ou des approbations sont généralement effectués dans un système principal, qui doit envoyer une notification Push à l’utilisateur. Il peut y avoir plusieurs systèmes principaux qui doivent tous générer le même genre de logique afin d’implémenter une notification Push lorsqu’un événement la déclenche. Ici, la complexité réside dans l’intégration de plusieurs systèmes principaux avec un système Push unique où les utilisateurs peuvent être abonnés à différentes notifications et même peut-être à plusieurs applications mobiles, par exemple dans le cas d’applications mobiles intranet où une application mobile peut recevoir des notifications à partir de plusieurs systèmes principaux. Les systèmes principaux n’ont pas besoin de connaître une technologie ou une sémantique Push. Une solution courante a donc généralement été d’introduire un composant qui interroge les systèmes principaux pour tout événement vous intéressant et qui est chargé d’envoyer les messages Push au client.
Nous présenterons ici une solution encore plus efficace qui s’appuie sur le modèle Azure Service Bus - Rubrique/abonnement et réduit la complexité tout en rendant la solution évolutive.

Voici l’architecture générale de la solution (généralisée pour plusieurs applications mobiles mais également applicable lorsqu’il n’y a qu’une seule application mobile)

## <a name="architecture"></a>Architecture
![][1]

L'élément clé de ce diagramme architectural est Azure Service Bus, qui fournit un modèle de programmation des rubriques/abonnements (la page [programmation Service Bus Pub/Sub]apporte plus d'informations à ce sujet). Le récepteur, dans ce cas le serveur principal Mobile (généralement [Azure Mobile Service], qui initie une notification Push vers les applications mobiles), ne reçoit pas les messages directement à partir des systèmes principaux, mais, au lieu de cela, il existe une couche d’abstraction intermédiaire fournie par [Azure Service Bus] qui permet au serveur mobile principal de recevoir des messages à partir d’un ou plusieurs systèmes principaux. Une rubrique Service Bus doit être créée pour chacun des systèmes principaux, par exemple Comptabilité, RH, Finance, qui sont essentiellement des « rubriques » d’intérêt qui génèrent des messages à envoyer en tant que notification Push. Les systèmes principaux envoient les messages à ces rubriques. Un service principal Mobile peut s’abonner à une ou plusieurs de ces rubriques en créant un abonnement Service Bus. Cela autorisera le serveur principal mobile à recevoir une notification du système principal correspondant. Le serveur principal mobile continue à écouter les messages sur ses abonnements et dès qu’un message arrive, il le reprend et l’envoie sous forme de notification à son hub de notification. Pour finir, les hubs de notification remettent ensuite le message à l’application mobile. Pour résumer, les composants clés sont donc :

1. les systèmes principaux (systèmes métiers/hérités)
   * Crée une rubrique Service Bus
   * Envoie un message
2. Serveur principal mobile
   * Crée l’abonnement au service
   * Reçoit le message (du système principal)
   * Envoie une notification aux clients (via Azure Notification Hub)
3. Application mobile
   * Reçoit et affiche une notification

### <a name="benefits"></a>Avantages :
1. Le découplage entre l’expéditeur (les systèmes principaux) et le récepteur (application/service mobile via Notification Hub) permet à des systèmes principaux supplémentaires d’être intégrés, et ce avec un minimum de modifications.
2. Cela permet également à plusieurs applications mobiles de recevoir des événements à partir d’un ou plusieurs systèmes principaux.  

## <a name="sample"></a>Exemple :
### <a name="prerequisites"></a>Composants requis
Vous devez suivre les didacticiels suivants pour vous familiariser avec les concepts et les étapes habituelles de création et de configuration :

1. [programmation Service Bus Pub/Sub] : ce didacticiel explique en détail comment utiliser des rubriques/abonnements Service Bus, comment créer un espace de noms pour contenir des rubriques/abonnements et comment envoyer et recevoir des messages à partir de ces rubriques/abonnements.
2. [Notification Hubs : didacticiel Windows Universal] : cette rubrique explique comment configurer une application Windows Store et Notification Hubs pour vous inscrire et recevoir des notifications.

### <a name="sample-code"></a>Exemple de code
L'exemple de code complet est disponible dans la page [Exemples de Notification Hub]. Il est divisé en trois composants :

1. **EnterprisePushBackendSystem**
   
    a. Ce projet utilise le package NuGet *WindowsAzure.ServiceBus*. Il repose sur la [programmation Service Bus Pub/Sub].
   
    b. Il s’agit d’une console d’application C# simple pour simuler un système métier qui lance le message à remettre à l’application mobile.
   
        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the topic where we will send notifications
            CreateTopic(connectionString);
   
            // Send message
            SendMessage(connectionString);
        }
   
    c. `CreateTopic` permet de créer la rubrique Service Bus où nous enverrons les messages.
   
        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already
   
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);
   
            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }
   
    d. `SendMessage` est utilisé pour envoyer les messages à cette rubrique Service Bus. Dans le cadre de l’exemple, nous nous contentons d’envoyer périodiquement un ensemble de messages aléatoires à la rubrique. Normalement, un système principal envoie des messages lorsqu’un événement se produit.
   
        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);
   
            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };
   
            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);
   
                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);
   
                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);
   
                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }
2. **ReceiveAndSendNotification**
   
    a. Ce projet utilise les packages NuGet *WindowsAzure.ServiceBus* et *Microsoft.Web.WebJobs.Publish*. Il repose sur la [programmation Service Bus Pub/Sub].
   
    b. Il s'agit d'une autre application de console C# qui s'exécutera comme une [tâche web Azure] , étant donné qu'elle doit s'exécuter en continu pour écouter les messages des systèmes métiers/principaux. Cela fera partie de votre serveur principal Mobile.
   
        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the subscription which will receive messages
            CreateSubscription(connectionString);
   
            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }
   
    c. `CreateSubscription` permet de créer un abonnement Service Bus pour la rubrique où le système principal enverra les messages. Selon le scénario commercial, ce composant créera un ou plusieurs abonnements aux rubriques correspondantes (par exemple, certains peuvent recevoir les messages du système de RH, du système des Finances, etc.)
   
        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);
   
            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }
   
    d. ReceiveMessageAndSendNotification est utilisée pour lire le message dans la rubrique en utilisant son abonnement et, si la lecture réussit, pour créer une notification (dans notre exemple, une notification toast native Windows) à envoyer à l’application mobile à l’aide d’Azure Notification Hubs.
   
        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");
   
            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);
   
            Client.Receive();
   
            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";
   
                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");
   
                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);
   
                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }
   
    e. Pour publier ceci sous forme de **travail web**, cliquez avec le bouton droit sur la solution dans Visual Studio et sélectionnez **Publier en tant que travail web**
   
    ![][2]
   
    f. Sélectionnez votre profil de publication et créez un site web Azure s’il n’existe pas déjà : il hébergera ce travail web. Quand vous disposez du site web, utilisez **Publier**.
   
    ![][3]
   
    g. Configurez le travail sur « Exécuter en continu ». Ainsi, quand vous vous connectez au [portail Azure], vous devriez voir ce qui suit :
   
    ![][4]
3. **EnterprisePushMobileApp**
   
    a. Il s’agit d’une application Windows Store qui reçoit des notifications toast du WebJob en cours d’exécution dans le cadre de votre serveur principal Mobile et les affiche. Elle repose sur [Notification Hubs : didacticiel Windows Universal].  
   
    b. Assurez-vous que la réception des notifications toast est activée dans votre application.
   
    c. Assurez-vous que le code d’inscription suivant de Notification Hubs est appelé au démarrage de l’application (après avoir remplacé *HubName* et *DefaultListenSharedAccessSignature*) :
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>Exemple d’exécution :
1. Assurez-vous que votre WebJob est exécuté avec succès et planifié pour s’« Exécuter en continu ».
2. Exécutez **EnterprisePushMobileApp** , qui lancera l'application Windows Store.
3. Exécutez l’application console **EnterprisePushBackendSystem** qui simule le serveur principal métier et enverra des messages. Vous devez voir apparaître des notifications toast similaires à celles-ci :
   
    ![][5]
4. Les messages ont été envoyés aux rubriques Service Bus qui ont été analysées par les abonnements Service Bus dans votre tâche Web. Lors de la réception d’un message, une notification a été créée et envoyée à l’application mobile. Vous pouvez consulter les journaux WebJob pour confirmer le traitement quand vous accédez au lien Journaux dans le [portail Azure] pour votre tâche Web :
   
    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Exemples de Notification Hub]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Service]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[programmation Service Bus Pub/Sub]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[tâche web Azure]: ../app-service/web-sites-create-web-jobs.md
[Notification Hubs : didacticiel Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[portail Azure]: https://portal.azure.com/
