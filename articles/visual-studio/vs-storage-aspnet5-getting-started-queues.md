---
title: Bien démarrer avec Stockage File d’attente et les services connectés de Visual Studio (ASP.NET Core) | Microsoft Docs
description: Guide pratique pour bien démarrer avec Azure Stockage File d’attente dans un projet ASP.NET Core dans Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: e2d11102d07eeb151769f81e79abd22dfa1d8734
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Bien démarrer avec Stockage File d’attente et les services connectés de Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Cet article explique comment bien démarrer avec Stockage File d’attente d’Azure dans Visual Studio après avoir créé ou référencé un compte de stockage Azure dans un projet ASP.NET Core à l'aide de la fonctionnalité **Services connectés** de Visual Studio. L’opération **Services connectés** installe les packages NuGet appropriés pour accéder au stockage Azure de votre projet et ajoute la chaîne de connexion pour le compte de stockage aux fichiers de configuration de votre projet. (Pour des informations générales sur Azure Storage, consultez la [documentation relative au stockage](https://azure.microsoft.com/documentation/services/storage/).)

Le service de stockage de files d'attente Azure permet de stocker un grand nombre de messages accessibles partout dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un message de file d’attente peut avoir une taille maximale de 64 Ko et une file d’attente peut contenir plusieurs millions de messages, jusqu’à la limite de capacité totale d’un compte de stockage. Consultez également [Prise en main du stockage de files d’attente Azure à l’aide de .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) pour plus d’informations sur la manipulation des files d’attente par programme.

Pour commencer, créez une file d’attente Azure dans votre compte de stockage. Cet article montre ensuite comment créer une file d'attente en C# et comment effectuer des opérations de file d'attente comme ajouter, modifier, lire et supprimer les messages d'une file d'attente.  Le code utilise la Bibliothèque cliente Azure Storage pour .NET. Pour plus d’informations sur ASP.NET, voir le site [ASP.NET](http://www.asp.net)(en anglais).

Certaines des API Azure Storage sont asynchrones, et le code dans cet article suppose l'utilisation de méthodes asynchrones. Voir [Programmation asynchrone](https://docs.microsoft.com/dotnet/csharp/async) pour plus d'informations.

## <a name="access-queues-in-code"></a>Accéder à des files d’attente dans le code

Pour accéder à des files d’attente dans des projets ASP.NET Core, incluez les éléments suivants dans tout fichier source C# qui accède à Azure Stockage File d’attente. Utilisez l’ensemble de ce code devant le code des sections suivantes.

1. Ajoutez les instructions `using` requises :
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Obtenez un objet `CloudStorageAccount` qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure :

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenez un objet `CloudQueueClient` pour référencer les objets de file d'attente de votre compte de stockage :

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obtenez un objet `CloudQueue` pour référencer une file d’attente spécifique :

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Créer une file d’attente dans le code

Pour créer la file d'attente Azure dans le code, appelez CreateIfNotExistsAsync :

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Ajout d'un message à une file d'attente

Pour insérer un message dans une file d’attente existante, créez un objet `CloudQueueMessage`, puis appelez la méthode `AddMessageAsync`. Un objet `CloudQueueMessage` peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'octets.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Lire un message dans une file d’attente

Vous pouvez lire furtivement le message au début de la file d'attente sans l'enlever de la file d'attente en appelant la méthode `PeekMessageAsync` :

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Lire et supprimer un message dans une file d’attente

Votre code permet de supprimer (retirer) un message d’une file d’attente en deux étapes.

1. Lorsque vous appelez `GetMessageAsync`, vous obtenez le message suivant au sein de la file d'attente. Un message renvoyé par `GetMessageAsync` devient invisible par les autres codes lisant les messages de cette file d'attente. Par défaut, ce message reste invisible pendant 30 secondes.
1. Pour finaliser la suppression du message de la file d’attente, vous devez appeler `DeleteMessageAsync`.

Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Le code suivant appelle `DeleteMessageAsync` juste après le traitement du message :

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Options supplémentaires pour la suppression des messages dans la file d'attente

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode `GetMessages` pour obtenir 20 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle `foreach`. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message. Notez que le délai de cinq minutes démarre en même temps pour tous les messages, donc une fois les cinq minutes écoulées, tous les messages n'ayant pas été supprimés redeviennent visibles.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode `FetchAttributes` demande au service de files d'attente d'extraire les attributs de la file d'attente, y compris le nombre de messages. La propriété `ApproximateMethodCount` renvoie la dernière valeur extraite par la méthode `FetchAttributes`, sans appeler le service de File d’attente.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Utiliser le modèle Async-Await avec les API de file d’attente commune

Cet exemple décrit comment utiliser le modèle Async-Await avec les API de file d’attente commune qui se terminent par `Async`. Quand une méthode asynchrone est utilisée, le modèle async-await suspend l’exécution locale jusqu’à la fin de l’appel. Ce comportement permet au thread actuel d’effectuer d’autres tâches afin d’éviter les goulots d’étranglement au niveau des performances et d’améliorer la réactivité globale de votre application.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode `Delete` sur l'objet file d'attente :

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
