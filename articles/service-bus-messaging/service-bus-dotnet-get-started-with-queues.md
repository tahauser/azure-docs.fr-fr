---
title: "Prise en main des files d’attente Azure Service Bus | Microsoft Docs"
description: "Écrire une application console C# qui utilise les files d’attente de messagerie Service Bus."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/7/2017
ms.author: sethm
ms.openlocfilehash: 6af7e4d238c10c0fed3443db58644e3557525993
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-service-bus-queues"></a>Prise en main des files d’attente Service Bus

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ce didacticiel couvre les étapes suivantes :

1. Créer un espace de noms Service Bus à l’aide du Portail Azure.
2. Créer une file d’attente Service Bus à l’aide du portail Azure.
3. Écrivez une application de console .NET Core pour envoyer un ensemble de messages à la file d’attente.
4. Écrivez une application de console .NET Core pour recevoir ces messages de la file d’attente.

## <a name="prerequisites"></a>Composants requis

1. [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](http://www.visualstudio.com/vs) ou version ultérieure.
2. [Kit de développement logiciel (SDK) NET Core](https://www.microsoft.com/net/download/windows), version 2.0 ou ultérieure.
2. Un abonnement Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Créer un espace de noms à l’aide du Portail Azure

> [!NOTE] 
> Vous pouvez également créer des entités de messagerie et un espace de noms Service Bus à l’aide de [PowerShell](/powershell/azure/get-started-azureps). Pour plus d’informations, consultez [Utiliser PowerShell pour gérer les ressources Service Bus](service-bus-manage-with-ps.md).

Si vous avez déjà créé un espace de noms de messagerie Service Bus, passez directement à la section [Créer une file d’attente à l’aide du portail Azure](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Créer une file d’attente à l’aide du Portail Azure

Si vous avez déjà créé une file d’attente Service Bus, passez directement à la section [Envoyer des messages à la file d’attente](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Envoyer des messages à la file d’attente

Pour envoyer des messages à la file d’attente, écrivez une application de console C# à l’aide de Visual Studio.

### <a name="create-a-console-application"></a>Création d’une application console

Ouvrez Visual Studio et créez un projet **Application de console (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Ajout du package NuGet Service Bus

1. Cliquez avec le bouton droit sur le projet créé et sélectionnez **Gérer les packages NuGet**.
2. Cliquez sur l’onglet **Parcourir**, recherchez **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**, puis sélectionnez l’élément **Microsoft.Azure.ServiceBus**. Cliquez sur **Installer** pour terminer l’installation, puis fermez cette boîte de dialogue.
   
    ![Sélectionner un package NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Écrire du code pour envoyer des messages à la file d’attente

1. Dans Program.cs, ajoutez les instructions `using` suivantes en haut de la définition de l’espace de noms, avant la déclaration de classe :
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Dans la classe `Program`, déclarez les variables suivantes. Définissez la variable `ServiceBusConnectionString` sur la chaîne de connexion obtenue lors de la création de l’espace de noms, puis définissez `QueueName` sur le nom utilisé lors de la création de la file d’attente :
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
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
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
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
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
   
6. Voici à quoi doit ressembler votre fichier Program.cs.
   
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
            // Connection String for the namespace can be obtained from the Azure portal under the 
            // 'Shared Access policies' section.
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string QueueName = "<your_queue_name>";
            static IQueueClient queueClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Send Messages
                await SendMessagesAsync(numberOfMessages);
                        
                Console.ReadKey();

                await queueClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the queue
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the queue
                        await queueClient.SendAsync(message);
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

7. Exécutez le programme et consultez le portail Azure : cliquez sur le nom de votre file d’attente dans la fenêtre **Vue d’ensemble** de l’espace de noms. L’écran de file d’attente **Fondamentaux** est affiché. Notez que la valeur **Nombre de messages actifs** pour la file d’attente doit à présent être de **10**. Chaque fois que vous exécutez l’application de l’expéditeur sans récupérer les messages (tel que décrit dans la section suivante), cette valeur augmente de 10. Notez également que la taille actuelle de la file d’attente incrémente la valeur **Actuel** dans la fenêtre **Fondamentaux** chaque fois que l’application ajoute des messages à la file d’attente.
   
      ![Taille des messages][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Recevoir les messages d'une file d'attente

Pour recevoir les messages que vous venez d’envoyer, créez une autre application de console .NET Core et installez le package NuGet **Microsoft.Azure.ServiceBus**, identique à l’application d’expéditeur précédente.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Écrire du code pour recevoir des messages de la file d’attente

1. Dans Program.cs, ajoutez les instructions `using` suivantes en haut de la définition de l’espace de noms, avant la déclaration de classe :
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Dans la classe `Program`, déclarez les variables suivantes. Définissez la variable `ServiceBusConnectionString` sur la chaîne de connexion obtenue lors de la création de l’espace de noms, puis définissez `QueueName` sur le nom utilisé lors de la création de la file d’attente :
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

3. Remplacez le contenu par défaut de `Main()` par la ligne de code suivante :

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Juste après `Main()`, ajoutez la méthode `MainAsync()` asynchrone suivante qui appelle la méthode `RegisterOnMessageHandlerAndReceiveMessages()` :

    ```csharp
    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register the queue message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
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
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

6. Juste après la méthode précédente, ajoutez la méthode `ProcessMessagesAsync()` suivante pour traiter les messages reçus :
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
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
   
8. Voici à quoi doit ressembler votre fichier Program.cs :
   
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
            // Connection String for the namespace can be obtained from the Azure portal under the 
            // 'Shared Access policies' section.
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string QueueName = "<your_queue_name>";
            static IQueueClient queueClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register QueueClient's MessageHandler and receive messages in a loop
                RegisterOnMessageHandlerAndReceiveMessages();
                    
                Console.ReadKey();

                await queueClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that will process messages
                queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
                await queueClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
                // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
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
4. Réexécutez le programme et vérifiez le portail. Notez que les valeurs **Nombre de messages actifs** et **Actuel** sont à présent de **0**.
   
    ![Longueur de la file d’attente][queue-message-receive]

Félicitations ! Vous avez maintenant créé une file d’attente, envoyé un ensemble de messages à cette dernière et reçu ces messages de la même file d’attente.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [référentiels GitHub accompagnés d’exemples](https://github.com/Azure/azure-service-bus/tree/master/samples) qui illustrent certaines des fonctionnalités les plus avancées de la messagerie Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png

