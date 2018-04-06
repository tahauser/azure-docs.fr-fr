---
title: Recevoir des événements d’Azure Event Hubs avec Java | Microsoft Docs
description: Prise en main de la réception d’événements des Event Hubs avec Java
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 38e3be53-251c-488f-a856-9a500f41b6ca
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: sethm
ms.openlocfilehash: bf87bed80c142bce6229ad858a33a1c6ede63a23
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Recevoir des événements d’Azure Event Hubs avec Java

Les concentrateurs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectés dans des hubs d’événements, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez la section [Vue d’ensemble d’Event Hubs][Event Hubs overview].

Ce didacticiel explique comment recevoir des événements dans un concentrateur d’événements à l’aide d’une application console en Java.

## <a name="prerequisites"></a>Prérequis


Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Un environnement de développement Java. Pour ce didacticiel, nous partons du principe que la solution utilisée est [Eclipse](https://www.eclipse.org/).
* Un compte Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][] avant de commencer.

Le code de ce didacticiel est basé sur le [code EventProcessorSample sur GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), que vous pouvez analyser pour afficher la version complète de l’application en cours.

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Recevoir des messages avec EventProcessorHost en Java

**EventProcessorHost** est une classe Java qui simplifie la réception d’événements provenant d’Event Hubs grâce à la gestion des points de contrôle permanents et des réceptions en parallèle d’Event Hubs. EventProcessorHost permet de répartir des événements sur plusieurs récepteurs, même quand ils sont hébergés dans des nœuds différents. Cet exemple illustre l'utilisation de la classe EventProcessorHost pour un récepteur unique.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Pour utiliser EventProcessorHost, vous devez disposer d’un [compte Azure Storage][Azure Storage account] :

1. Connectez-vous au [Portail Azure][Azure portal], puis cliquez sur **+ Créer une ressource** à gauche de l’écran.
2. Cliquez sur **Stockage**, puis sur **Compte de stockage**. Dans la fenêtre **Créer un compte de stockage**, saisissez un nom pour votre compte de stockage. Renseignez les autres champs, sélectionnez la région souhaitée, puis cliquez sur **Créer**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Cliquez sur le compte de stockage que vous venez de créer, puis cliquez sur **Clés d'accès** :
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Copiez la valeur clé1 dans un emplacement temporaire afin de pouvoir l’utiliser ultérieurement au cours de ce didacticiel.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Créer un projet Java à l’aide de l’hôte EventProcessor

La bibliothèque cliente Java pour Event Hubs est utilisable dans les projets Maven à partir du [Référentiel central Maven][Maven Package]. Elle peut être référencée à l’aide de la déclaration de dépendance suivante dans votre fichier projet Maven. La version actuelle est 1.0.0 :    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>1.0.0</version>
</dependency>
```

Pour différents types d’environnements build, vous pouvez obtenir explicitement les fichiers JAR les plus récents à partir du [Référentiel central Maven][Maven Package].  

1. Pour l’exemple suivant, créez tout d’abord un nouveau projet Maven pour une application de console/shell dans votre environnement de développement Java favori. La classe est appelée `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Utilisez le code suivant pour créer une classe appelée `EventProcessorSample`. Remplacez les espaces réservés par les valeurs utilisées lorsque vous avez créé le concentrateur d’événements et le compte de stockage :
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Créez une autre classe appelée `EventProcessor` à l’aide du code suivant :
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. In a real implementation, this
        // is the place to do initialization so that events can be processed when they arrive, such as opening a database
        // connection.
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. The reason argument indicates whether the shut down
        // is because another host has stolen the lease for this partition or due to error or host shutdown. In a real implementation,
        // this is the place to do cleanup for resources that were opened in onOpen.
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        // It is NOT called for exceptions thrown out of onOpen/onClose/onEvents. EventProcessorHost is responsible for recovering from
        // the error, if possible, or shutting the event processor down if not, in which case there will be a call to onClose. The
        // notification provided to onError is primarily informational.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. The maximum number of events in a batch
        // can be controlled via EventProcessorOptions. Also, if the "invoke processor after receive timeout" option is set to true,
        // this method will be called with null when a receive timeout occurs.
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                // It is important to have a try-catch around the processing of each event. Throwing out of onEvents deprives
                // you of the chance to process any remaining events in the batch. 
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. Checkpointing is usually not a fast operation, so there is a tradeoff
                    // between checkpointing frequently (to minimize the number of events that will be reprocessed after a crash, or
                    // if the partition lease is stolen) and checkpointing infrequently (to reduce the impact on event processing
                    // performance). Checkpointing every five events is an arbitrary choice for this sample.
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

> [!NOTE]
> Ce didacticiel utilise une seule instance de EventProcessorHost. Pour augmenter le débit, il est recommandé d’exécuter plusieurs instances d’EventProcessorHost, de préférence sur des machines séparées.  Cela offre également davantage de redondance. Dans ces cas, les différentes instances se coordonnent automatiquement entre elles afin d'équilibrer la charge des événements reçus. Si vous souhaitez que plusieurs récepteurs traitent *tous* les événements, vous devez utiliser le concept **ConsumerGroup** . Lors de la réception des événements à partir de différents ordinateurs, il peut être utile de spécifier des noms pour les instances de EventProcessorHost basées sur les ordinateurs (ou rôles) dans lesquels ils sont déployés.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d'ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Créer un Event Hub)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Azure Storage account]: ../storage/common/storage-create-storage-account.md
[Azure portal]: https://portal.azure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png
[compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio