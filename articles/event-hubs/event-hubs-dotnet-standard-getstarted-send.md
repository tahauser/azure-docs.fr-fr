---
title: "Envoyer des événements vers Azure Event Hubs avec .NET Standard | Microsoft Docs"
description: "Prise en main de l’envoi d’événements vers Event Hubs dans .NET Standard"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: sethm
ms.openlocfilehash: f59f88d47bfcb3e761f509a3d87c6d068f44e0db
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="get-started-sending-messages-to-azure-event-hubs-in-net-standard"></a>Bien démarrer avec l’envoi de messages vers Azure Event Hubs dans .NET Standard

> [!NOTE]
> Cet exemple est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender).

Ce didacticiel montre comment écrire une application console .NET Core qui envoie un jeu de messages à un concentrateur d’événements. Vous pouvez exécuter la solution [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender), en remplaçant les chaînes `EhConnectionString` et `EhEntityPath` par vos valeurs de concentrateur d’événements. Ou vous pouvez suivre les étapes de ce didacticiel pour créer les vôtres.

## <a name="prerequisites"></a>Prérequis

* [Microsoft Visual Studio 2015 ou 2017](http://www.visualstudio.com). Les exemples de ce didacticiel utilisent Visual Studio 2017, mais Visual Studio 2015 est également pris en charge.
* [Outils Visual Studio 2015 ou 2017 .NET Core](http://www.microsoft.com/net/core).
* Un abonnement Azure.
* Un espace de noms de concentrateur d’événements.

Pour envoyer des messages à un concentrateur d’événements, ce didacticiel utilise Visual Studio pour écrire une application console C#.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Création d’un espace de noms Event Hubs et d’un concentrateur d’événements

La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type concentrateur d’événements et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un concentrateur d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md), puis passez aux étapes suivantes.

## <a name="create-a-console-application"></a>Création d’une application console

Démarrez Visual Studio. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**. Créez une application console .NET Core.

![Nouveau projet][1]

## <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

Ajoutez le package NuGet de bibliothèque standard .NET [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) à votre projet en procédant comme suit : 

1. Cliquez avec le bouton droit sur le projet créé et sélectionnez **Gérer les packages NuGet**.
2. Cliquez sur l’onglet **Parcourir**, puis recherchez « Microsoft.Azure.EventHubs » et sélectionnez le package **Microsoft.Azure.EventHubs**. Cliquez sur **Installer** pour terminer l’installation, puis fermez cette boîte de dialogue.

## <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Écriture de code pour envoyer des messages à un concentrateur d’événements

1. Ajoutez les instructions `using` ci-après en haut du fichier Program.cs :

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Ajoutez des constantes à la classe `Program` pour le chemin de l’entité et la chaîne de connexion Event Hubs (nom du concentrateur d’événements individuel). Remplacez les espaces réservés entre crochets par les valeurs appropriées obtenues lors de la création du concentrateur d’événements. Vérifiez que `{Event Hubs connection string}` est la chaîne de connexion au niveau de l’espace de noms, et pas la chaîne de concentrateur d’événements. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Ajoutez une nouvelle méthode nommée `MainAsync` à la classe `Program`, comme suit :

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Ajoutez une nouvelle méthode nommée `SendMessagesToEventHub` à la classe `Program`, comme suit :

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Ajoutez le code suivant à la méthode `Main` dans la classe `Program` :

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Voici à quoi doit ressembler votre fichier Program.cs.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but this simple scenario
                // uses the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Exécutez le programme et assurez-vous qu’il n’y a aucune erreur.

Félicitations ! Vous venez d’envoyer des messages à un concentrateur d’événements.

## <a name="next-steps"></a>étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Recevoir des événements d’Event Hubs](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Vue d'ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)
* [Créer un concentrateur d’événements](event-hubs-create.md)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcore.png
