---
title: Prise en main des rubriques et abonnements Azure Service Bus | Microsoft Docs
description: "Écrivez une application de console .Net Core C# qui utilise les rubriques et les abonnements de messagerie Service Bus."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/6/2017
ms.author: sethm
ms.openlocfilehash: aa75ac48d650f28d4aaeb612f2900d705cf71b5b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-service-bus-topics"></a>Prise en main des rubriques Service Bus

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ce didacticiel couvre les étapes suivantes :

1. Créer un espace de noms Service Bus à l’aide du Portail Azure.
2. Créer une rubrique Service Bus à l’aide du Portail Azure.
3. Créer un abonnement Service Bus vers cette rubrique à l’aide du Portail Azure.
4. Écrivez une application de console .NET Core pour envoyer un ensemble de messages à la rubrique.
5. Écrivez une application de console .NET Core pour recevoir ces messages de l’abonnement.

## <a name="prerequisites"></a>configuration requise

1. [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](http://www.visualstudio.com/vs) ou version ultérieure.
2. [Kit de développement logiciel (SDK) NET Core](https://www.microsoft.com/net/download/windows), version 2.0 ou ultérieure.
2. Un abonnement Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Créer un espace de noms à l’aide du Portail Azure

> [!NOTE] 
> Vous pouvez également créer des entités de messagerie et un espace de noms Service Bus à l’aide de [PowerShell](/powershell/azure/get-started-azureps). Pour plus d’informations, consultez [Utiliser PowerShell pour gérer les ressources Service Bus](service-bus-manage-with-ps.md).

Si vous avez déjà créé un espace de noms Service Bus Messaging, passez directement à la section [Créer une rubrique à l’aide du portail Azure](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Créer une rubrique à l’aide du Portail Azure

1. Connectez-vous au [portail Azure][azure-portal].
2. Dans le volet de navigation gauche du portail, cliquez sur **Service Bus** (si vous ne voyez pas **Service Bus**, cliquez sur **Tous les services** ou sur **Toutes les ressources**). Cliquez sur l’espace de noms dans lequel vous souhaitez créer la rubrique. 
3. La fenêtre de vue d’ensemble de l’espace de noms s’affiche. Cliquez sur **Rubriques** :
   
    ![Création d'une rubrique][createtopic1]
4. Cliquez sur **+ Rubrique**.
   
    ![Sélectionnez les rubriques][createtopic2]
5. Entrez le nom de la rubrique. Conservez les valeurs par défaut des autres options.
   
    ![Sélectionner Nouveau][createtopic3]
6. En bas de la boîte de dialogue, cliquez sur **Créer**.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Créer un abonnement à la rubrique

1. Dans le volet des ressources du portail, cliquez sur l’espace de noms que vous avez créé à l’étape 1, puis cliquez sur **Rubriques** et sur le nom de la rubrique que vous avez créée à l’étape 2.
2. En haut du volet de vue d’ensemble, cliquez sur **+ Abonnement** pour ajouter un abonnement à cette rubrique.

    ![Créer un abonnement][createtopic4]

3. Entrez un nom pour l’abonnement. Conservez les valeurs par défaut des autres options.

## <a name="4-send-messages-to-the-topic"></a>4. Envoyez des messages à la rubrique

Pour envoyer des messages à la rubrique, écrivez une application de console C# à l’aide de Visual Studio.

### <a name="create-a-console-application"></a>Création d’une application console

Ouvrez Visual Studio et créez un projet **Application de console (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Ajout du package NuGet Service Bus

1. Cliquez avec le bouton droit sur le projet créé et sélectionnez **Gérer les packages NuGet**.
2. Cliquez sur l’onglet **Parcourir**, recherchez **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**, puis sélectionnez l’élément **Microsoft.Azure.ServiceBus**. Cliquez sur **Installer** pour terminer l’installation, puis fermez cette boîte de dialogue.
   
    ![Sélectionner un package NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Écrire du code pour envoyer des messages à la rubrique

1. Dans Program.cs, ajoutez les instructions `using` suivantes en haut de la définition de l’espace de noms, avant la déclaration de classe :
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Dans la classe `Program`, déclarez les variables suivantes. Définissez la variable `ServiceBusConnectionString` sur la chaîne de connexion obtenue lors de la création de l’espace de noms, puis définissez `TopicName` sur le nom utilisé lors de la création de la rubrique :
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. Remplacez le contenu par défaut de `Main()` par la ligne de code suivante :

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```
   
4. Juste après `Main()`, ajoutez la méthode `MainAsync()` asynchrone suivante qui appelle la méthode d’envoi de messages :

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```

5. Juste après la méthode `MainAsync()`, ajoutez la méthode `SendMessagesAsync()` suivante qui effectue l’opération d’envoi du nombre de message spécifié par `numberOfMessagesToSend` (la valeur est actuellement définie sur 10) :

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. Voici à quoi doit ressembler votre fichier Program.cs d’expéditeur.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send messages.
                await SendMessagesAsync(numberOfMessages);

                Console.ReadKey();

                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. Exécutez le programme et consultez le portail Azure : cliquez sur le nom de votre rubrique dans la fenêtre de l’espace de noms **Vue d’ensemble**. L’écran de rubrique **Fondamentaux** est affiché. Dans l’abonnement répertorié en bas de la fenêtre, notez que la valeur **Nombre de messages** de l’abonnement est désormais de **10**. Chaque fois que vous exécutez l’application de l’expéditeur sans récupérer les messages (tel que décrit dans la section suivante), cette valeur augmente de 10. Notez également que la taille actuelle de la rubrique incrémente la valeur **Actuel** dans la fenêtre **Fondamentaux** chaque fois que l’application ajoute des messages à la rubrique.
   
      ![Taille des messages][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Réception des messages de l’abonnement

Pour recevoir les messages que vous venez d’envoyer, créez une autre application de console .NET Core et installez le package NuGet **Microsoft.Azure.ServiceBus**, identique à l’application d’expéditeur précédente.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Écrire du code pour recevoir des messages de l’abonnement

1. Dans Program.cs, ajoutez les instructions `using` suivantes en haut de la définition de l’espace de noms, avant la déclaration de classe :
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Dans la classe `Program`, déclarez les variables suivantes. Définissez la variable `ServiceBusConnectionString` sur la chaîne de connexion obtenue lors de la création de l’espace de noms, définissez `TopicName` sur le nom utilisé lors de la création de la rubrique et définissez `SubscriptionName` sur le nom utilisé lors de la création de l’abonnement à la rubrique :
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. Remplacez le contenu par défaut de `Main()` par la ligne de code suivante :

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Juste après `Main()`, ajoutez la méthode `MainAsync()` asynchrone suivante qui appelle la méthode `RegisterOnMessageHandlerAndReceiveMessages()` :

    ```csharp
    static async Task MainAsync()
    {
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();
    }
    ```

5. Juste après la méthode `MainAsync()`, ajoutez la méthode suivante qui enregistre le gestionnaire de messages et reçoit les messages envoyés par l’application d’expéditeur :

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Juste après la méthode précédente, ajoutez la méthode `ProcessMessagesAsync()` suivante pour traiter les messages reçus :
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Enfin, ajoutez la méthode suivante pour gérer les exceptions qui peuvent se produire :
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. Voici à quoi doit ressembler votre fichier de destinataire Program.cs :
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register subscription message handler and receive messages in a loop.
                RegisterOnMessageHandlerAndReceiveMessages();

                Console.ReadKey();

                await subscriptionClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. Réexécutez le programme et vérifiez le portail. Notez que les valeurs **Nombre de messages** et **Actuel** sont à présent de **0**.
   
    ![Longueur de la rubrique][topic-message-receive]

Félicitations ! Grâce à la bibliothèque .NET Standard, vous avez créé une rubrique et un abonnement, envoyé 10 messages et les avez reçus.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [référentiels GitHub accompagnés d’exemples](https://github.com/Azure/azure-service-bus/tree/master/samples) qui illustrent certaines des fonctionnalités les plus avancées de la messagerie Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
