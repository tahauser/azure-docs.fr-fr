---
title: Intégration d’Apache Spark avec Azure Event Hubs | Microsoft Docs
description: Intégrer Apache Spark pour activer la diffusion en continu de flux structuré avec Event Hubs
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija;sethm
ms.openlocfilehash: 3b44c7e7387eceb2d9ea0b2c214b13a82869110f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Intégration d’Apache Spark avec Azure Event Hubs

Azure Event Hubs s’intègre parfaitement avec [Apache Spark](https://spark.apache.org/) pour faciliter la génération d’applications de diffusion en continu distribuées de bout en bout. Cette intégration prend en charge [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html) et la [diffusion en continu de flux structuré](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Le connecteur Event Hubs pour Apache Spark est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs-spark). Cette bibliothèque est également disponible pour les projets Maven à partir du [Référentiel central Maven](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Cet article explique comment créer une application continue dans [Azure Databrick](https://azure.microsoft.com/services/databricks/). Cet article utilise [Azure Databricks](https://azure.microsoft.com/services/databricks/), mais les clusters Spark sont également disponibles avec [HDInsight](../hdinsight/spark/apache-spark-overview.md).

L’exemple suivant utilise deux blocs-notes Scala : un pour les événements en diffusion en continu à partir d’un concentrateur d’événements et l’autre pour le renvoi d’événements vers celui-ci.

## <a name="prerequisites"></a>Prérequis


* Un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Une instance Event Hubs. Si vous n’en avez pas, [créez-en une](event-hubs-create.md).
* Une instance [Azure Databricks](https://azure.microsoft.com/services/databricks/). Si vous n’en avez pas, [créez-en une](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Créez une bibliothèque à l’aide des coordonnées Maven](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package) : `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.0`

Utilisez le code suivant sur votre bloc-notes pour diffuser en continu des événements à partir du concentrateur d’événements.

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()

// Select the body column and cast it to a string.
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
L’exemple de code suivant envoie des événements à votre concentrateur d’événements avec les API de lot Spark. Vous pouvez également écrire une requête de diffusion en continu pour envoyer des événements au concentrateur d’événements.

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build

val eventHubsConf = EventHubsConf(connectionString)

// Create a column representing the partitionKey.
val partitionKeyColumn = (col("id") % 5).cast("string").as("partitionKey")
// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(partitionKeyColumn, bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 

```

## <a name="next-steps"></a>Étapes suivantes

Cet article explique le fonctionnement du connecteur Event Hubs pour la génération en temps réel de solutions de diffusion en continu avec tolérance de panne. Pour obtenir plus d’informations sur la diffusion en continu de flux structuré et le connecteur intégré Event Hubs, suivez ces liens :

* [Guide d’intégration pour la diffusion en continu de flux structuré + Azure Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Guide d’intégration pour Spark Streaming + Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
