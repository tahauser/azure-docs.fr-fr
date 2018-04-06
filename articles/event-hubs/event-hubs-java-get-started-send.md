---
title: Envoyer des événements vers Azure Event Hubs avec Java | Microsoft Docs
description: Prise en main de l’envoi vers Event Hubs avec Java
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: sethm
ms.openlocfilehash: 5dd0c88dab9ff4b7073a9acf6872b4c3ff085586
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Envoyer des événements vers Azure Event Hubs avec Java

Les concentrateurs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectées dans un concentrateur d’événements, les données peuvent être transformées et stockées à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez la section [Vue d’ensemble d’Event Hubs][Event Hubs overview].

Ce didacticiel explique comment envoyer des événements vers un concentrateur d’événements à l’aide d’une application console en Java. Pour recevoir des événements avec le processeur d’événements Java, consultez [cet article](event-hubs-java-get-started-receive-eph.md) ou cliquez sur le langage de réception approprié dans le sommaire à gauche.

Pour effectuer ce didacticiel, vous devrez disposer des éléments suivants :

* Un environnement de développement Java. Pour ce didacticiel, nous partons du principe que la solution utilisée est [Eclipse](https://www.eclipse.org/).
* Un compte Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][] avant de commencer.

Le code de ce didacticiel est basé sur [l’exemple Envoyer GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/Send), que vous pouvez analyser pour afficher la version complète de l’application en cours.

## <a name="send-events-to-event-hubs"></a>Envoyer des événements vers Event Hubs

La bibliothèque cliente de Java pour les concentrateurs d’événements peut être utilisée dans les projets Maven à partir du [référentiel central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Vous pouvez référencer cette bibliothèque à l’aide de la déclaration de dépendance suivante au sein de votre fichier projet Maven. La version actuelle est 1.0.0 :    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
```

Pour différents types d’environnements de génération, vous pouvez obtenir explicitement les fichiers JAR les plus récents à partir du [référentiel central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Pour un éditeur d’événements simple, importez le package *com.microsoft.azure.eventhubs* pour les classes clientes Event Hubs et le package *com.microsoft.azure.servicebus* pour les classes utilitaires, telles que les exceptions courantes qui sont partagées avec le client de messagerie Azure Service Bus. 

### <a name="declare-the-send-class"></a>Déclarer la classe Envoyer

Pour l’exemple suivant, créez tout d’abord un nouveau projet Maven pour une application de console/shell dans votre environnement de développement Java favori. Nommez la classe `Send` :     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.PartitionSender;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.Random;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class Send {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>Construire la chaîne de connexion

Utilisez la classe ConnectionStringBuilder pour construire une valeur de chaîne de connexion et passer à l’instance client Event Hubs. Remplacez les espaces réservés par les valeurs obtenues lors de la création de l’espace de noms et du concentrateur d’événements :

```java
   final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
      .setNamespaceName("----NamespaceName-----")
      .setEventHubName("----EventHubName-----")
      .setSasKeyName("-----SharedAccessSignatureKeyName-----")
      .setSasKey("---SharedAccessSignatureKey----");
```

### <a name="send-events"></a>Envoyer des événements

Ensuite, créez un événement unique en transformant une chaîne dans son encodage UTF-8 octets. Enfin, créez une nouvelle instance cliente Event Hubs à partir de la chaîne de connexion et envoyez le message.   

```java 
byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
EventData sendEvent = new EventData(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Recevoir des événements avec EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Vue d’ensemble des hubs d’événements][Event Hubs overview]
* [Créer un concentrateur d’événements](event-hubs-create.md)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[compte gratuit]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

