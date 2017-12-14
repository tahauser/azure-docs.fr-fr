---
title: "Azure Cosmos DB : Exécuter des analyses graphiques à l’aide de Spark et d’Apache TinkerPop Gremlin | Microsoft Docs"
description: "Cet article présente les instructions de configuration et d’exécution des analyses graphiques et du calcul parallèle dans Azure Cosmos DB avec Spark et TinkerPop SparkGraphComputer."
services: cosmosdb
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: article
ms.date: 09/08/2017
ms.author: khdang
ms.openlocfilehash: d2ea692c5c353586cc2b653e57eca7ccb8c9c7ce
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB : Exécuter des analyses graphiques à l’aide de Spark et d’Apache TinkerPop Gremlin

[Azure Cosmos DB](introduction.md) est le service de base de données multi-modèle mondialement distribué de Microsoft. Vous pouvez créer et interroger des bases de données de documents, de paires clé-valeur et de graphiques, qui bénéficient toutes des fonctionnalités de distribution mondiale et de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. Azure Cosmos DB prend en charge les charges de travail graphiques OLTP (traitement des transactions en ligne) qui utilisent [Apache TinkerPop Gremlin](graph-introduction.md).

[Spark](http://spark.apache.org/) est un projet Apache Software Foundation consacré au traitement des données OLAP (traitement analytique en ligne) à usage général. Spark fournit un modèle informatique distribué hybride en mémoire/sur disque, semblable au modèle MapReduce de Hadoop. Vous pouvez déployer Apache Spark dans le cloud à l’aide d’[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

En associant Azure Cosmos DB et Spark, vous pouvez exécuter des charges de travail OLTP et OLAP lorsque vous utilisez Gremlin. Cet article de démarrage rapide montre comment exécuter des requêtes Gremlin dans Azure Cosmos DB sur un cluster Azure HDInsight Spark.

## <a name="prerequisites"></a>Composants requis

Avant de pouvoir exécuter cet exemple, vous devez posséder les composants requis suivants :
* Cluster Azure HDInsight Spark 2.0
* JDK 1.8 + (exécutez `apt-get install default-jdk` si vous ne possédez pas JDK)
* Maven (exécutez `apt-get install maven` si vous ne possédez pas Maven)
* Un abonnement Azure ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

Pour plus d’informations sur la configuration d’un cluster Azure HDInsight Spark, consultez [Approvisionnement des clusters HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Créer un compte de base de données Azure Cosmos DB

Tout d’abord, créez un compte de base de données avec l’API Graph comme suit :

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>Ajouter une collection

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>Obtenir Apache TinkerPop

Obtenez Apache TinkerPop en procédant comme suit :

1. Établissez une connexion à distance au nœud principal du cluster HDInsight `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`.

2. Clonez le code source de TinkerPop3, générez-le localement et installez-le dans le cache de Maven.

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. Installez le plug-in Spark-Gremlin. 

    a. L’installation du plug-in est gérée par Grape. Renseignez les informations des référentiels pour Grape de sorte qu’il puisse télécharger le plug-in et ses dépendances. 

      Créez le fichier de configuration Grape s’il est absent de `~/.groovy/grapeConfig.xml`. Utilisez les paramètres suivants :

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    b. Démarrez la console Gremlin `bin/gremlin.sh`.
        
    c. Installez le plug-in Spark-Gremlin avec la version 3.3.0-SNAPSHOT générée lors des étapes précédentes :

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. Vérifiez si `Hadoop-Gremlin` est activé avec `:plugin list`. Désactivez ce plug-in, car il peut interférer avec le plug-in Spark-Gremlin `:plugin unuse tinkerpop.hadoop`.

## <a name="prepare-tinkerpop3-dependencies"></a>Préparer les dépendances TinkerPop3

Lorsque vous avez généré TinkerPop3 à l’étape précédente, le processus a également extrait toutes les dépendances JAR pour Spark et Hadoop dans le répertoire cible. Utilisez les fichiers JAR qui sont préinstallés avec HDI et extrayez uniquement les dépendances supplémentaires en cas de besoin.

1. Accédez au répertoire cible de la console Gremlin à l’emplacement `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

2. Déplacez tous les fichiers JAR sous `ext/` vers `lib/` : `find ext/ -name '*.jar' -exec mv {} lib/ \;`

3. Sous `lib/`, supprimez toutes les bibliothèques JAR qui ne figurent pas dans la liste suivante :

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>Obtenir le connecteur Spark Azure Cosmos DB

1. Obtenez le connecteur Spark Azure Cosmos DB `azure-cosmosdb-spark-0.0.3-SNAPSHOT.jar` et le Kit de développement logiciel (SDK) Java Cosmos DB `azure-documentdb-1.12.0.jar` à partir du [connecteur Spark Azure Cosmos DB sur GitHub](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11).

2. Sinon, vous pouvez le créer localement. Étant donné que la dernière version de Spark-Gremlin a été créée avec Spark 1.6.1 et qu’elle n’est pas compatible avec la version Spark 2.0.2 actuellement utilisée dans le connecteur Spark Azure Cosmos DB, vous pouvez générer le code TinkerPop3 le plus récent et installer manuellement les fichiers JAR. Effectuez les actions suivantes :

    a. Clonez le connecteur Spark Azure Cosmos DB.

    b. Générer TinkerPop3 (déjà effectué lors des étapes précédentes). Installez tous les fichiers JAR TinkerPop 3.3.0-SNAPSHOT localement.

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. Mettez à jour `tinkerpop.version` `azure-documentdb-spark/pom.xml` vers `3.3.0-SNAPSHOT`.
    
    d. Générer avec Maven. Les fichiers JAR nécessaires sont placés dans `target` et `target/alternateLocation`.

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Copiez les fichiers JAR mentionnés ci-dessus vers un répertoire local à l’emplacement ~/azure-documentdb-spark :

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Distribuer les dépendances sur les nœuds de travail Spark 

1. Étant donné que la transformation des données de graphique dépend de TinkerPop3, vous devez distribuer les dépendances associées à tous les nœuds de travail Spark.

2. Copiez les dépendances Gremlin indiquées précédemment, ainsi que le fichier JAR du connecteur Spark Cosmos DB et le Kit de développement logiciel (SDK) Cosmos DB Java sur les nœuds de travail comme suit :

    a. Copiez tous les fichiers JAR dans `~/azure-documentdb-spark`.

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    b. Obtenez la liste de tous les nœuds de travail Spark, qui se trouve sur le tableau de bord Ambari, dans la liste `Spark2 Clients`, section `Spark2`.

    c. Copiez ce répertoire sur chacun des nœuds.

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>Définir les variables d’environnement

1. Recherchez la version HDP du cluster Spark. Il s’agit du nom de répertoire sous `/usr/hdp/` (par exemple, 2.5.4.2-7).

2. Définissez hdp.version pour tous les nœuds. Dans le tableau de bord Ambari, accédez à la section **YARN** > **Configurations** > **Avancé**, puis procédez comme suit : 
 
    a. Dans `Custom yarn-site`, ajoutez une nouvelle propriété `hdp.version` avec la valeur de la version HDP sur le nœud principal. 
     
    b. Enregistrez les configurations. Vous pouvez ignorer les avertissements. 
     
    c. Redémarrez les services YARN et Oozie, comme l’indiquent les icônes de notification.

3. Définissez les variables d’environnement suivantes sur le nœud principal (remplacez les valeurs appropriées) :

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Préparer la configuration du graphique

1. Créez un fichier de configuration avec les paramètres de connexion Azure Cosmos DB et les paramètres Spark, et placez-le à l’emplacement `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`.

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Mettez à jour `spark.driver.extraClassPath` et `spark.executor.extraClassPath` pour inclure le répertoire des fichiers JAR que vous avez distribués à l’étape précédente, dans le cas présent `/home/sshuser/azure-documentdb-spark/*`.

3. Renseignez les informations suivantes pour Azure Cosmos DB :

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>Charger le graphique de TinkerPop et l’enregistrer dans Azure Cosmos DB
Pour illustrer comment conserver un graphique dans Azure Cosmos DB, cet exemple utilise le graphique moderne TinkerPop prédéfini. Le graphique est stocké au format Kryo et fourni dans le référentiel TinkerPop.

1. Étant donné que vous exécutez Gremlin en mode YARN, vous devez mettre les données de graphique à disposition dans le système de fichiers Hadoop. Utilisez les commandes ci-dessous pour créer un répertoire et y copier le fichier graphique local. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Mettez à jour temporairement le fichier `gremlin-spark.properties` afin d’utiliser `GryoInputFormat` pour lire le graphique. Indiquez également `inputLocation` comme répertoire créé par vos soins, comme suit :

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. Lancez la console Gremlin et créez ces étapes de calcul pour conserver les données dans la collection Azure Cosmos DB configurée :  

    a. Créez le graphique `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`.

    b. Utilisez SparkGraphComputer pour écrire `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`.

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. Dans l’Explorateur de données, vous pouvez vérifier que les données ont été conservées dans Azure Cosmos DB.

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>Charger le graphique depuis Azure Cosmos DB et exécuter des requêtes Gremlin

1. Pour charger le graphique, modifiez `gremlin-spark.properties` pour définir `graphReader` sur `DocumentDBInputRDD` :

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. Pour charger le graphique, parcourir les données et exécuter des requêtes Gremlin avec celui-ci, procédez comme suit :

    a. Démarrez la console Gremlin `bin/gremlin.sh`.

    b. Créez le graphique avec la configuration `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`.

    c. Créez une traversée de graphique avec SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)`.

    d. Exécutez les requêtes de graphique Gremlin suivantes :

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> Pour afficher une journalisation plus détaillée, dans `conf/log4j-console.properties`, définissez un niveau de journalisation plus détaillé.
>

## <a name="next-steps"></a>Étapes suivantes

Dans cet article de démarrage rapide, vous avez appris à utiliser des graphiques en associant Azure Cosmos DB et Spark.

> [!div class="nextstepaction"]
