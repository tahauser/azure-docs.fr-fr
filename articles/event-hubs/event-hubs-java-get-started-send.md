---
title: "Envoyer des événements vers Azure Event Hubs avec Java | Microsoft Docs"
description: "Prise en main de l’envoi vers Event Hubs avec Java"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 5c8c24e1f168be4b46ccfdb1d0c268866fc8ff7d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Envoyer des événements vers Azure Event Hubs avec Java

## <a name="introduction"></a>Introduction
Les concentrateurs d’événements représentent un système d’ingestion à l’extensibilité élevée en mesure d’absorber des millions d’événements par seconde, ce qui permet à une application de traiter et d’analyser les quantités énormes de données produites par vos périphériques connectés et vos applications. Une fois collectées dans un concentrateur d’événements, les données peuvent être transformées et stockées à l’aide de n’importe quel fournisseur d’analyses en temps réel ou d’un cluster de stockage.

Pour plus d’informations, consultez la section [Vue d’ensemble d’Event Hubs][Event Hubs overview].

Ce didacticiel explique comment envoyer des événements vers un concentrateur d’événements à l’aide d’une application console en Java. Pour recevoir des événements avec le processeur d’événements Java, consultez [cet article](event-hubs-java-get-started-receive-eph.md) ou cliquez sur le langage de réception approprié dans le sommaire à gauche.

Pour effectuer ce didacticiel, vous devrez disposer des éléments suivants :

* Un environnement de développement Java. Pour ce didacticiel, nous partons du principe que la solution utilisée est [Eclipse](https://www.eclipse.org/).
* Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.

## <a name="send-messages-to-event-hubs"></a>Envoi de messages vers Event Hubs
La bibliothèque cliente de Java pour les concentrateurs d’événements peut être utilisée dans les projets Maven à partir du [référentiel central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Vous pouvez référencer cette bibliothèque à l’aide de la déclaration de dépendance suivante au sein de votre fichier de projet Maven :    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```

Pour différents types d’environnements de génération, vous pouvez obtenir explicitement les fichiers JAR les plus récents à partir du [référentiel central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Pour un éditeur d’événements simple, importez le package *com.microsoft.azure.eventhubs* pour les classes clientes Event Hubs et le package *com.microsoft.azure.servicebus* pour les classes utilitaires, telles que les exceptions courantes qui sont partagées avec le client de messagerie Azure Service Bus. 

Pour l’exemple suivant, créez tout d’abord un nouveau projet Maven pour une application de console/shell dans votre environnement de développement Java favori. Nommez la classe `Send`.     

```java
import java.io.IOException;
import java.nio.charset.*;
import java.util.*;

import com.microsoft.azure.eventhubs.*;

public class Send
{
    public static void main(String[] args) 
            throws EventHubException, IOException
    {
```

Remplacez l’espace de noms et les noms du concentrateur d’événements par les valeurs utilisées lorsque vous avez créé le concentrateur d’événements.

```java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Ensuite, créez un événement unique en transformant une chaîne dans son encodage UTF-8 octets. Enfin, créez une nouvelle instance cliente Event Hubs à partir de la chaîne de connexion et envoyez le message.   

```java 

    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);

    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    
    // close the client at the end of your program
    ehClient.closeSync();
    }
}

``` 

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Recevoir des événements avec EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Vue d’ensemble des hubs d’événements][Event Hubs overview]
* [Créer un concentrateur d’événements](event-hubs-create.md)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
