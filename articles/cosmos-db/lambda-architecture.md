---
title: Architecture lambda avec Azure Cosmos DB et HDInsight (Apache Spark) | Microsoft Docs
description: "Cet article explique comment implémenter une architecture lambda à l’aide d’Azure Cosmos DB, HDInsight et Spark"
keywords: lambda-architecture
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: 273aeae9-e31c-4a43-b216-5751c46f212e
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 01/19/2018
ms.author: denlee
ms.openlocfilehash: f88f3fb05495b0f3330d5a4cde7718fe89b2f694
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB : implémenter une architecture lambda sur la plateforme Azure 

Les architectures lambda permettent de traiter efficacement de gros volumes de jeux de données. Les architectures lambda utilisent le traitement par lots, le traitement de flux et une couche de service afin de réduire la latence inhérente à l’interrogation de Big Data. 

Pour implémenter une architecture lambda sur Azure, vous pouvez combiner les technologies suivantes afin d’accélérer l’analytique de Big Data en temps réel :
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), premier service de base de données multi-modèle du secteur distribué à l’échelle mondiale. 
* [Apache Spark pour Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), framework de traitement qui exécute des applications d’analyse de données à grande échelle
* Le [flux de modification](change-feed.md) Azure Cosmos DB, qui achemine les nouvelles données vers la couche de traitement par lots en vue de leur traitement par HDInsight
* [Connecteur Spark-Azure Cosmos DB](spark-connector.md)

Cet article décrit les notions de base d’une architecture lambda reposant sur la conception multicouche d’origine et les avantages d’une architecture lambda « remaniée » qui simplifie les opérations.  

Pour obtenir une vue d’ensemble de l’architecture lambda et des ressources disponibles dans l’exemple d’architecture lambda, regardez la vidéo suivante :

> [!VIDEO https:///channel9.msdn.com/Events/Connect/2017/T135/player]
>

## <a name="what-is-a-lambda-architecture"></a>Qu’est-ce qu’une architecture lambda ?
Une architecture lambda est une architecture de traitement générique, scalable et à tolérance de pannes conçue pour les scénarios impliquant traitements par lot, vitesse et latence, comme décrit par [Nathan Marz](https://twitter.com/nathanmarz).

![Diagramme montrant une architecture lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Source : http://lambda-architecture.net/

Les principes de base d’une architecture lambda sont décrits dans le diagramme précédent et sont tirés de la page [https://lambda-architecture.net](http://lambda-architecture.net/).

 1. Toutes les **données** sont transmises aux *deux* couches (*traitement par lots* et *vitesse*).
 2. La **couche traitement par lots** a un jeu de données master (jeu de données brutes immuable en ajout seul) et précalcule les vues de traitement par lots.
 3. La **couche service** a des vues de traitement par lots pour accélérer le traitement des requêtes. 
 4. La **couche vitesse** compense le temps de traitement (au niveau de la couche service) en ne traitant que les données récentes.
 5. Vous pouvez traiter les requêtes en fusionnant les résultats à partir des vues de traitement par lots et des vues en temps réel ou en récupérant les résultats individuellement via la commande ping.

Plus avant, nous pourrons implémenter cette architecture à l’aide uniquement des éléments suivants :

* Collection(s) Azure Cosmos DB
* Cluster HDInsight (Apache Spark 2.1)
* Spark Connector [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Couche vitesse

Du point de vue des opérations, la conservation de deux flux de données tout en garantissant l’état correct des données peut s’avérer compliqué. Pour simplifier les opérations, utilisez la [prise en charge du flux de modification Azure Cosmos DB](change-feed.md) afin de conserver l’état de la *couche traitement par lots* tout en révélant le journal des modifications Azure Cosmos DB via *l’API de flux de modification* pour votre *couche vitesse*.  
![Diagramme mettant en évidence la partie constituée des nouvelles données, de la couche vitesse et du jeu de données master dans l’architecture lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

Ce qui est important dans ces couches :

 1. Toutes les **données** étant transmises *uniquement* à Azure Cosmos DB, vous pouvez éviter les problèmes liés aux casts multiples.
 2. La **couche traitement par lots** a un jeu de données master (jeu de données brutes immuable en ajout seul) et précalcule les vues de traitement par lots.
 3. La **couche service** est décrite dans la section suivante.
 4. La **couche vitesse** utilise HDInsight (Apache Spark) pour lire le flux de modification Azure Cosmos DB. Vous pouvez ainsi conserver vos données, ainsi qu’interroger et traiter celles-ci simultanément.
 5. Vous pouvez traiter les requêtes en fusionnant les résultats à partir des vues de traitement par lots et des vues en temps réel ou en récupérant les résultats individuellement via la commande ping.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Exemple de code : streaming structuré Spark vers un flux de modification Azure Cosmos DB
Pour exécuter un prototype rapide du flux de modification Azure Cosmos DB dans le cadre de la **couche vitesse**, vous pouvez le tester à l’aide de données Twitter dans le cadre de l’exemple [Stream Processing Changes using Azure Cosmos DB Change Feed and Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) (Diffuser des modifications de traitement à l’aide du flux de modification Azure Cosmos DB et d’Apache Spark). Pour obtenir votre sortie Twitter, consultez l’exemple de code dans [Stream feed from Twitter to Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed) (Diffuser un flux de Twitter vers Cosmos DB). Avec l’exemple précédent, vous chargez des données Twitter dans Azure Cosmos DB ; vous pouvez ensuite configurer votre cluster HDInsight (Apache Spark) pour qu’il se connecte au flux de modification. Pour plus d’informations sur la façon de définir cette configuration, consultez [Apache Spark to Azure Cosmos DB Connector Setup](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (Configuration du connecteur Apache Spark-Azure Cosmos DB).  

L’extrait de code suivant montre comment configurer `spark-shell` pour exécuter une tâche de streaming structuré afin de se connecter à un flux de modification Azure Cosmos DB, qui examine le flux de données Twitter en temps réel, pour faire le compte des intervalles au fur et à mesure.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Pour obtenir des exemples de code complets, consultez [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), notamment :
* [Streaming Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Streaming Tags Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Vous obtenez une console `spark-shell`, qui exécute en permanence une tâche de streaming structuré qui fait le compte des intervalles par rapport aux données Twitter du flux de modification Azure Cosmos DB. L’illustration suivante montre la sortie de la tâche de flux de données et les comptes d’intervalles.

![Sortie de streaming montrant le compte d’intervalles par rapport aux données Twitter du flux de modification Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Pour plus d’informations sur le flux de modification Azure Cosmos DB, consultez :

* [Utilisation du support de flux de modification dans Azure Cosmos DB](change-feed.md)
* [Présentation de la bibliothèque du processus de flux de modification Azure Cosmos DB](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Stream Processing Changes: Azure CosmosDB change feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/) (Diffuser des modifications de traitement : flux de modification Azure CosmosDB + Apache Spark)

## <a name="batch-and-serving-layers"></a>Couches traitement par lots et service
Les nouvelles données étant chargées dans Azure Cosmos DB (où le flux de modification est utilisé pour la couche vitesse), c’est là que réside le **jeu de données master** (ensemble de données brutes immuable en ajout seul). Dorénavant, utilisez HDInsight (Apache Spark) pour effectuer les fonctions de calcul préalable entre la **couche traitement par lots** et la **couche service**, comme illustré dans l’image suivante :

![Diagramme mettant en évidence la couche traitement par lots et la couche service de l’architecture lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Ce qui est important dans ces couches :

 1. Toutes les **données** sont transmises uniquement à Azure Cosmos DB (pour éviter les problèmes liés aux casts multiples).
 2. La **couche traitement par lots** a un jeu de données master (jeu de données brutes immuable en ajout seul) stocké dans Azure Cosmos DB. À l’aide d’HDI Spark, vous pouvez précalculer vos agrégations à stocker dans vos vues de traitement par lots calculées.
 3. La **couche service** est une base de données Azure Cosmos DB comportant des collections pour le jeu de données master et la vue de traitement par lots calculée.
 4. La **couche vitesse** est décrite plus loin dans cet article.
 5. Vous pouvez traiter les requêtes en fusionnant les résultats à partir des vues de traitement par lots et des vues en temps réel ou en récupérant les résultats individuellement via la commande ping.

### <a name="code-example-pre-computing-batch-views"></a>Exemple de code : calcul préalable de vues de traitements par lot
Pour savoir comment exécuter des vues précalculées dans votre **jeu de données master** entre Apache Spark et Azure Cosmos DB, utilisez les extraits de code suivants tirés des Notebooks [Lambda Architecture Rearchitected - Batch Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) et [Lambda Architecture Rearchitected - Batch to Serving Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). Dans ce scénario, utilisez les données Twitter stockées dans Azure Cosmos DB.

Commençons par créer la connexion de configuration aux données Twitter dans Azure Cosmos DB en utilisant le code PySpark ci-dessous.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Ensuite, exécutons l’instruction Spark SQL suivante pour déterminer les 10 principaux hashtags de l’ensemble de tweets. Nous exécutons cette requête Spark SQL dans un Notebook Jupyter (sans le graphique à barres de sortie, présenté juste après cet extrait de code).

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Graphique montrant le nombre de tweets par hashtag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Votre requête étant prête, nous allons l’enregistrer dans une collection à l’aide du connecteur Spark pour enregistrer les données de sortie dans une autre collection.  Dans cet exemple, utilisez Scala pour illustrer la connexion. Comme dans l’exemple précédent, créez la connexion de configuration pour enregistrer le DataFrame Apache Spark dans une autre collection Azure Cosmos DB.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Après avoir spécifié `SaveMode` (qui indique l’action à appliquer aux documents, à savoir `Overwrite` ou `Append`), créez un DataFrame `tweets_bytags` similaire à la requête Spark SQL dans l’exemple précédent.  Une fois le DataFrame `tweets_bytags` créé, vous pouvez l’enregistrer à l’aide de la méthode `write` et du `writeConfig` spécifié.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Cette dernière instruction a enregistré votre DataFrame Spark dans une nouvelle collection Azure Cosmos DB ; du point de vue de l’architecture lambda, il s’agit de votre **vue de traitement par lots** au sein de la **couche service**.
 
#### <a name="resources"></a>Ressources

Pour obtenir des exemples de code complets, consultez [azure-cosmosdb-spark/lambda/samples](vhttps://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), y compris :
* Lambda Architecture Rearchitected - Batch Layer [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda Architecture Rearchitected - Batch to Serving Layer [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Couche vitesse
Comme indiqué précédemment, l’utilisation de la bibliothèque de flux de modification Azure Cosmos DB vous permet de simplifier les opérations entre les couches traitement par lots et vitesse. Dans cette architecture, utilisez Apache Spark (via HDInsight) pour exécuter les requêtes de *streaming structuré* sur les données. Vous pouvez également conserver temporairement les résultats de vos requêtes de streaming structuré afin que d’autres systèmes puissent accéder à ces données.

![Diagramme mettant en évidence la couche vitesse de l’architecture lambda](./media/lambda-architecture/lambda-architecture-speed.png)

Pour ce faire, créez une collection Azure Cosmos DB distincte pour enregistrer les résultats de vos requêtes de streaming structuré.  Ainsi, d’autres systèmes peuvent accéder à ces informations, pas seulement Apache Spark. De plus, avec la fonctionnalité de durée de vie (TTL) de Cosmos DB, vous pouvez configurer vos documents afin qu’ils soient automatiquement supprimés au terme d’une certaine durée.  Pour plus d’informations sur la fonctionnalité de durée de vie d’Azure Cosmos DB, consultez [Faire expirer des données dans des collections Cosmos DB automatiquement avec la durée de vie](time-to-live.md).

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Architecture lambda remaniée
Comme indiqué dans les sections précédentes, vous pouvez simplifier l’architecture lambda d’origine à l’aide des composants suivants :
* Azure Cosmos DB
* La bibliothèque de flux de modification Azure Cosmos DB, pour ne pas avoir à effectuer plusieurs casts de vos données entre les couches vitesse et traitement par lots
* Apache Spark sur HDInsight
* Connecteur Spark pour Azure Cosmos DB

![Diagramme montrant l’architecture lambda remaniée à l’aide d’Azure Cosmos DB, de Spark et de l’API de flux de modification Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-re-architected.png)

Grâce à cette conception, seuls deux services managés sont nécessaires : Azure Cosmos DB et HDInsight. Ensemble, ils gèrent les couches traitement par lots, service et vitesse de l’architecture lambda. Cela simplifie non seulement les opérations, mais également le flux de données. 
 1. Toutes les données sont transmises à Azure Cosmos DB en vue de leur traitement.
 2. La couche traitement par lots a un jeu de données master (jeu de données brutes immuable en ajout seul) et précalcule les vues de traitement par lots.
 3. La couche service a des vues de traitement par lots de données pour accélérer le traitement des requêtes.
 4. La couche vitesse compense le temps de traitement (au niveau de la couche service) en ne traitant que les données récentes.
 5. Toutes les requêtes peuvent être traitées en fusionnant les résultats à partir des vues de traitement par lots et des vues en temps réel.

### <a name="resources"></a>Ressources

 * **Nouvelles données** : le [flux de Twitter à CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), mécanisme utilisé pour transmettre les nouvelles données à Azure Cosmos DB.
 * **Couche traitement par lots :** la couche traitement par lots est composée du *jeu de données master* (ensemble de données brutes immuable en mode ajout seul) ; c’est à ce niveau que peuvent être précalculées les vues de traitement par lots des données qui sont envoyées à la **couche service**.
    * Le Notebook **Lambda Architecture Rearchitected - Batch Layer** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) interroge le *jeu de données master* des vues de traitement par lots.
 * **Couche service :** la **couche service** est composée des données précalculées qui aboutissent à des vues de traitement par lots (par exemple, agrégations, segments spécifiques, etc.) pour accélérer le traitement des requêtes.
    * Le Notebook **Lambda Architecture Rearchitected - Batch to Serving Layer** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) transmet les données de lot à la couche service ; autrement dit, Spark interroge une collection de lots de tweets, la traite et la stocke dans une autre collection (lot calculé).
* **Couche vitesse :** la **couche vitesse** est composé de Spark, qui utilise le flux de modification Azure Cosmos DB pour lire et agir immédiatement. Les données peuvent également être enregistrées comme *données en temps réel calculées (Computed RT)* afin que d’autres systèmes puissent interroger les données traitées en temps réel, au lieu d’exécuter eux-mêmes une requête en temps réel.
    * Le script scala [Streaming Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) exécute une requête de streaming à partir du flux de modification Azure Cosmos DB pour calculer un compte d’intervalles à partir de l’interpréteur de commandes de Spark.
    * Le script scala [Streaming Tags Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) exécute une requête de streaming à partir du flux de modification Azure Cosmos DB pour calculer un compte d’intervalles de mots-clés à partir de l’interpréteur de commandes de Spark.
  
## <a name="next-steps"></a>Étapes suivantes
Si vous ne l’avez pas encore fait, téléchargez le connecteur Spark-Azure Cosmos DB à partir du dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) et explorez les ressources supplémentaires dans le dépôt :
* [Architecture lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Distributed Aggregations Examples](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples) (Exemples d’agrégations distribuées)
* [Sample Scripts and Notebooks](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples) (Exemples de scripts et Notebooks)
* [Structured streaming demos](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos) (Démonstrations de streaming structuré)
* [Change feed demos](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos) (Démonstrations de flux de modification)
* [Stream processing changes using Azure Cosmos DB Change Feed and Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) (Diffuser des modifications de traitement avec le flux de modification Azure CosmosDB et Apache Spark)

Vous pouvez également consulter [Apache Spark SQL, DataFrames, and Datasets Guide](http://spark.apache.org/docs/latest/sql-programming-guide.html) (Guide sur Apache Spark SQL, les tableaux de données et les jeux de données) et l’article [Apache Spark sur Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
