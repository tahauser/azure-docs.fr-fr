---
title: "Connexion d’Apache Spark à Azure Cosmos DB | Microsoft Docs"
description: "Grâce à ce didacticiel, vous en apprendrez davantage sur le connecteur Spark-Azure Cosmos DB, qui vous permet de connecter Apache Spark à Azure Cosmos DB pour réaliser des agrégations distribuées et des opérations de sciences des données sur le système de base de données mutualisé conçu pour le cloud de Microsoft, qui est distribué dans le monde entier."
keywords: apache spark
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: denlee
ms.openlocfilehash: 4ba8a53f2018727cc4fa225b2d4ce14d9f1d7467
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Accélérer l’analyse en temps réel des données volumineuses grâce au connecteur Spark-Azure Cosmos DB

Le connecteur Spark-Azure Cosmos DB permet à Azure Cosmos DB de jouer le rôle de source d’entrée ou de récepteur de sortie pour les tâches Apache Spark. Connecter [Spark](http://spark.apache.org/) à [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) accélère votre capacité à résoudre les problèmes liés aux opérations de sciences de données dynamiques, sachant que vous pouvez utiliser Azure Cosmos DB pour conserver et interroger rapidement des données. Le connecteur Spark-Azure Cosmos DB utilise efficacement les index gérés natifs d’Azure Cosmos DB. Les index activent des colonnes modifiables lorsque vous effectuez des analyses et opérez un filtrage de prédicat sur des données globalement distribuées qui changent rapidement, allant de l’Internet des objets (IoT) aux scénarios de sciences et d’analyse des données.

Découvrez-en plus dans cette vidéo avec le principal chef de programme d’Azure Cosmos DB, Denny Lee. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

Pour utiliser Spark GraphX et les API Graph Gremlin d’Azure Cosmos DB, voir [Exécuter des analyses graphiques à l’aide de Spark et d’Apache TinkerPop Gremlin](spark-connector-graph.md).

## <a name="download"></a>Download

Pour commencer, téléchargez le connecteur Spark-Azure Cosmos DB à partir du référentiel [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/) sur GitHub.

## <a name="connector-components"></a>Composants du connecteur

Le connecteur utilise les composants suivants :

* [Azure Cosmos DB](http://documentdb.com) permet à ses utilisateurs d’approvisionner et d’adapter de façon élastique leurs capacités de débit et de stockage partout dans le monde. Le service offre ce qui suit :
   * [Distribution globale](distribute-data-globally.md) et échelle horizontale clé en main
   * Latences à un chiffre dans 99 pour cent des cas
   * [Différents modèles de cohérence bien définis](consistency-levels.md)
   * Haute disponibilité garantie avec capacités d’hébergement multiple
   * Toutes les fonctionnalités sont garanties par des [contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/cosmos-db) (SLA) complets, les meilleurs du marché.

* [Apache Spark](http://spark.apache.org/) est un puissant moteur de traitement Open Source axé sur la vitesse, la facilité d’utilisation et des analyses sophistiquées.

* [Apache Spark sur Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) qui vous permet de déployer Apache Spark dans le cloud pour les déploiements stratégiques à l’aide d’[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Versions officiellement prises en charge :

| Composant | Version |
|---------|-------|
|Apache Spark|2.0.2, 2.1.0, 2.2.0|
| Scala| 2.10, 2.11|
| Kit SDK Java SQL Azure Cosmos DB | 1.14.0, 1.15.0 |

Cet article vous permet d’exécuter quelques exemples simples à l’aide de Python (via pyDocumentDB) et des interfaces Scala.

Vous pouvez choisir entre deux approches pour connecter Apache Spark et Azure Cosmos DB :
- Utiliser pyDocumentDB via le [Kit SDK Python SQL Azure Cosmos DB](https://github.com/Azure/azure-documentdb-python).
- Créer un connecteur Spark-Azure Cosmos DB basé sur Java en utilisant le [Kit SDK Java SQL Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>Implémentation de pyDocumentDB
Le [Kit SDK pyDocumentDB](https://github.com/Azure/azure-documentdb-python) actuel permet de connecter Spark à Azure Cosmos DB, comme illustré dans le diagramme suivant :

![Flux de données Spark-Azure Cosmos DB via pyDocumentDB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Flux de données de l’implémentation de pyDocumentDB

Le flux de données est le suivant :

1. Le nœud principal Spark se connecte au nœud de passerelle Azure Cosmos DB via pyDocumentDB. Un utilisateur spécifie uniquement les connexions Spark et Azure Cosmos DB. Les connexions aux nœuds principal et de passerelle sont transparentes pour l’utilisateur.
2. Le nœud de passerelle effectue la requête sur Azure Cosmos DB, sachant que la requête s’exécute par la suite sur les partitions de la collection dans les nœuds de données. La réponse à ces requêtes est renvoyée au nœud de passerelle, puis le jeu de résultats est retourné au nœud principal Spark.
3. Toutes les requêtes suivantes (par exemple, sur une tramedonnées Spark) sont envoyées aux nœuds worker Spark à des fins de traitement.

La communication entre Spark et Azure Cosmos DB est limitée au nœud principal Spark et aux nœuds de passerelle Azure Cosmos DB.  Les requêtes sont opérées aussi rapidement que la couche de transport entre ces deux nœuds le permet.

### <a name="install-pydocumentdb"></a>Installer pyDocumentDB
Vous pouvez installer pyDocumentDB sur votre nœud de pilote à l’aide de **pip**, par exemple :

```bash
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>Connecter Spark à Azure Cosmos DB via pyDocumentDB
La simplicité du transport de communication rend relativement simple l’exécution d’une requête de Spark à Azure Cosmos DB à l’aide de pyDocumentDB.

L’extrait de code suivant montre comment utiliser pyDocumentDB dans un contexte Spark.

```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Comme indiqué dans l’extrait de code :

* Le Kit SDK Python d’Azure DB Cosmos (`pyDocumentDB`) contient tous les paramètres de connexion nécessaires. Par exemple, le paramètre des emplacements par défaut choisit le réplica en lecture et l’ordre de priorité.
*  Importez les bibliothèques nécessaires et configurez votre **masterKey** et **host** pour créer le *client* Azure Cosmos DB (**pydocumentdb.document_client**).


### <a name="execute-spark-queries-via-pydocumentdb"></a>Exécuter des requêtes Spark via pyDocumentDB
Les exemples suivants utilisent l’instance Azure Cosmos DB créée dans l’extrait de code précédent en utilisant les clés en lecture seule spécifiées. L’extrait de code suivant se connecte à la collection **airports.codes** dans le compte DoctorWho comme indiqué précédemment, puis exécute une requête pour extraire les villes de l’État de Washington disposant d’un aéroport.

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

Une fois la requête exécutée via **query**, le résultat est un élément **query_iterable. QueryIterable** qui est converti en une liste Python. Une liste Python peut être aisément convertie en une tramedonnées Spark à l’aide du code suivant :

```python
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>Pourquoi utiliser pyDocumentDB pour connecter Spark à Azure Cosmos DB ?
pyDocumentDB est généralement utilisé pour connecter Spark à Azure Cosmos DB dans les scénarios où :

* Vous souhaitez utiliser Python.
* Un jeu de résultats relativement petit est retourné d’Azure Cosmos DB à Spark. Notez que le jeu de données sous-jacent dans Azure Cosmos DB peut être très volumineux. Vous appliquez des filtres, c’est-à-dire que vous exécutez des filtres de prédicat, sur votre source Azure Cosmos DB.  

## <a name="spark-to-azure-cosmos-db-connector"></a>Connecteur Spark-Azure Cosmos DB

Le connecteur Spark-Azure Cosmos DB utilise le [Kit SDK Java SQL Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java) et déplace les données entre les nœuds worker Spark et Azure Cosmos DB, comme indiqué dans le diagramme suivant :

![Flux de données dans le connecteur Spark-Azure Cosmos DB](./media/spark-connector/spark-connector.png)

Le flux de données est le suivant :

1. Le nœud principal Spark se connecte au nœud de passerelle Azure Cosmos DB pour obtenir le mappage de partitions. Un utilisateur spécifie uniquement les connexions Spark et Azure Cosmos DB. Les connexions aux nœuds principal et de passerelle sont transparentes pour l’utilisateur.
2. Ces informations sont renvoyées au nœud maître Spark.  À ce stade, vous devez être en mesure d’analyser la requête pour déterminer les partitions (et leurs emplacements) auxquelles vous devez accéder dans Azure Cosmos DB.
3. Ces informations sont transmises aux nœuds worker Spark.
4. Les nœuds worker Spark se connectent directement aux partitions Azure Cosmos DB pour extraire les données et les retourner aux partitions Spark dans les nœuds worker Spark.

La communication entre Spark et Azure Cosmos DB est beaucoup plus rapide, car les données sont déplacées entre les nœuds worker Spark et les nœuds de données Azure Cosmos DB (partitions).

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>Créer le connecteur Spark-Azure Cosmos DB
Actuellement, le projet de connecteur utilise maven. Pour créer le connecteur sans dépendances, vous pouvez exécuter :
```
mvn clean package
```
Vous pouvez également télécharger les dernières versions du fichier JAR à partir du dossier [*releases* ](https://github.com/Azure/azure-cosmosdb-spark/releases).

### <a name="include-the-azure-cosmos-db-spark-jar"></a>Inclure le JAR Azure Cosmos DB Spark
Avant d’exécuter quelque code que ce sot, vous devez inclure le JAR Azure Cosmos DB Spark.  Si vous utilisez le **spark-shell**, vous pouvez inclure le JAR à l’aide de l’option **--jars**.  

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0-uber.jar
```

Si vous souhaitez exécuter le JAR sans dépendances, utilisez le code suivant :

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar,/$location/azure-documentdb-1.14.0.jar,/$location/azure-documentdb-rx-0.9.0-rc2.jar,/$location/json-20140107.jar,/$location/rxjava-1.3.0.jar,/$location/rxnetty-0.4.20.jar 
```

Si vous utilisez un service de bloc-notes comme le bloc-notes Jupyter Azure HDInsight, vous pouvez utiliser les commandes **spark magic** :

```
%%configure
{ "name":"Spark-to-Cosmos_DB_Connector", 
  "jars": ["wasb:///example/jars/1.0.0/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-1.14.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-rx-0.9.0-rc2.jar", "wasb:///example/jars/1.0.0/json-20140107.jar", "wasb:///example/jars/1.0.0/rxjava-1.3.0.jar", "wasb:///example/jars/1.0.0/rxnetty-0.4.20.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

La commande **jars** vous permet d’inclure les deux fichiers JAR nécessaires pour **azure-cosmosdb-spark** (lui-même et le Kit SDK Java SQL Azure Cosmos DB) et d’exclure **scala-reflect** pour qu’il n’interfère pas avec les appels Livy (bloc-notes Jupyter > Livy > Spark).

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>Connecter Spark à Azure Cosmos DB à l’aide du connecteur
Si le transport de communication est un peu plus complexe, l’exécution d’une requête de Spark à Azure Cosmos DB à l’aide du connecteur est très rapide.

L’extrait de code suivant décrit comment utiliser le connecteur dans une session Spark. Consultez le `azure-cosmosdb-spark`dépôt [GitHub](https://github.com/Azure/azure-cosmosdb-spark) pour accéder à des exemples Python.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val baseConfig = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(baseConfig)
coll.createOrReplaceTempView("c")
```

Comme indiqué dans l’extrait de code :

- **Azure-cosmosdb-spark** contient tous les paramètres de connexion nécessaires, dont les emplacements par défaut. Par exemple, vous pouvez choisir le réplica en lecture et l’ordre de priorité.
- Importez simplement les bibliothèques nécessaires et configurez votre masterKey et host pour créer le client Azure Cosmos DB.

### <a name="execute-spark-queries-via-the-connector"></a>Exécuter les requêtes Spark via le connecteur

L’exemple suivant utilise l’instance Azure Cosmos DB créée dans l’extrait de code précédent à l’aide des clés en lecture seule spécifiées. L’extrait de code suivant se connecte à la collection DepartureDelays.flights_pcoll (dans le compte DoctorWho, comme indiqué précédemment), puis exécute une requête pour extraire les informations de retard des vols au départ de Seattle.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>Pourquoi utiliser l’implémentation du connecteur Spark-Azure Cosmos DB ?

La connexion de Spark à Azure Cosmos DB à l’aide du connecteur est généralement destinée aux scénarios où :

* Vous souhaitez utiliser Python et/ou Scala.
* Vous avez une grande quantité de données à transférer entre Apache Spark et Azure Cosmos DB.

Pour vous donner une idée de la différence des performances de requête, consultez la [wiki sur les exécutions de tests de requête](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Exemple d’agrégations distribuées
Cette section fournit des exemples montrant comment réaliser des agrégations distribuées et des analyses à l’aide d’Apache Spark et d’Azure Cosmos DB. Azure Cosmos DB prend déjà en charge les agrégations, comme décrit dans le blog [Planet scale aggregates with Azure Cosmos DB](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/) (agrégats de mise à l’échelle Planet avec Azure Cosmos DB). Voici comment le mener au niveau suivant avec Apache Spark.

Notez que ces agrégations font référence au [bloc-notes de connecteur Spark-Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb).

### <a name="connect-to-flights-sample-data"></a>Se connecter aux exemples de données de vols
Ces exemples d’agrégations accèdent à certaines données de performances de vol stockées dans notre base de données Azure Cosmos DB **DoctorWho**. Pour vous connecter à celle-ci, vous devez utiliser l’extrait de code suivant :

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Avec cet extrait de code, nous allons également exécuter une requête de base qui transfère l’ensemble de données filtré d’Azure Cosmos DB à Spark (où ce dernier peut réaliser des agrégats distribués). Dans ce cas, nous demandons les vols au départ de Seattle (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Les résultats suivants ont été générés par l’exécution des requêtes à partir du service de bloc-notes Jupyter.  Notez que tous les extraits de code sont génériques et non spécifiques à un service.

### <a name="running-limit-and-count-queries"></a>Exécution des requêtes LIMIT et COUNT
Comme vous y êtes habitué dans SQL/Spark SQL, commençons par une requête **LIMIT** :

![Requête LIMIT Spark](./media/spark-connector/spark-sql-query.png)

La requête suivante est une requête **COUNT** simple et rapide :

![Requête COUNT Spark](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>Requête GROUP BY
Dans le jeu suivant, nous pouvons facilement exécuter des requêtes **GROUP BY** sur notre base de données Azure Cosmos DB :

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![Graphique de la requête GROUP BY Spark](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>Requête DISTINCT, ORDER BY
Et voici une requête **DISTINCT, ORDER BY** :

![Graphique de la requête GROUP BY Spark](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>Continuer l’analyse des données de vol
Vous pouvez utiliser les exemples de requête suivants pour continuer l’analyse des données de vol :

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>Les 5 principales destinations (villes) vers lesquelles il y a du retard au départ de Seattle
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![Graphique des retards principaux Spark](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Calculer les retards médians par villes de destination au départ de Seattle
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![Graphique des retards médians Spark](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas encore fait, téléchargez le connecteur Spark-Azure Cosmos DB à partir du dépôt GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) et explorez les ressources supplémentaires dans le dépôt :

* [Distributed Aggregations Examples](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples) (Exemples d’agrégations distribuées)
* [Sample Scripts and Notebooks](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples) (Exemples de scripts et blocs-notes)

Vous pouvez également consulter [Apache Spark SQL, DataFrames, and Datasets Guide](http://spark.apache.org/docs/latest/sql-programming-guide.html) (Guide sur Apache Spark SQL, les tableaux de données et les jeux de données) et l’article [Apache Spark sur Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
