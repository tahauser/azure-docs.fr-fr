---
title: Utiliser une diffusion en continu Apache Spark avec Event Hubs dans Azure HDInsight | Documents Microsoft
description: "Créez un Exemple de diffusion en continu Apache Spark montrant comment envoyer un flux de données à Azure Event Hub, puis recevoir ces événements dans un cluster Spark HDInsight à l’aide d’une application Scala."
keywords: diffusion en continu apache spark,diffusion en continu spark, exemple spark,exemple de diffusion en continu apache spark,exemple azure event hubs
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: jgao
ms.openlocfilehash: 43ae956ca284485cc68f8120a31af1c493c0b254
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="apache-spark-streaming-process-data-from-azure-event-hubs-with-spark-cluster-on-hdinsight"></a>Diffusion en continu Apache Spark : traitement de données d’Azure Event Hubs avec un cluster Spark sur HDInsight

Dans le cadre de cet article, vous allez créer un exemple de diffusion en continu Apache Spark qui implique les étapes suivantes :

1. Vous utilisez une application autonome pour ingérer les messages dans Azure Event Hub.

2. Avec deux approches différentes, vous récupérez les messages d’Event Hub en temps réel à l’aide d’une application s’exécutant dans le cluster Spark sur Azure HDInsight.

3. Vous générez des pipelines analytiques de diffusion en continu pour conserver les données dans différents systèmes de stockage ou obtenir en un clin d’œil des insights à partir des données.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="spark-streaming-concepts"></a>Concepts de la diffusion en continu de Spark

Pour obtenir une explication détaillée de la diffusion en continu Spark, voir la [présentation de la diffusion en continu Apache Spark](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight apporte les mêmes fonctionnalités de diffusion en continu à un cluster Spark sur Azure.  

## <a name="what-does-this-solution-do"></a>Que fait cette solution ?

Dans cet article, pour créer un exemple de diffusion en continu Spark, procédez comme suit :

1. Créez un Event Hub Azure qui doit recevoir un flux d’événements.

2. Exécutez une application autonome locale qui génère des événements et les envoie à Azure Event Hub. L’exemple d’application qui effectue cette opération est publié à l’adresse [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

3. Exécutez une application de diffusion en continu à distance sur un cluster Spark qui lit les événements de diffusion en continu à partir d’Azure Event Hub et effectuez diverses tâches de traitement/d’analyse des données.

## <a name="create-an-azure-event-hub"></a>Création d'un hub d'événements Azure

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com), puis cliquez sur **Nouveau** en haut à gauche de l’écran.

2. Cliquez sur **Internet des Objets**, puis sur **Hubs d’événements**.

    ![Créer un Event Hub pour un exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Créer un Event Hub pour un exemple de diffusion en continu Spark")

3. Dans le panneau **Créer un espace de noms** , entrez un nom d’espace de noms. Choisissez le niveau tarifaire (De base ou Standard). Choisissez également un abonnement Azure, un groupe de ressources et l’emplacement où créer la ressource. Cliquez sur **Créer** pour créer l’espace de noms.

      ![Fournir un nom d’Event Hub pour un exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Fournir un nom d’Event Hub pour un exemple de diffusion en continu Spark")

    > [!NOTE]
    > Vous devez sélectionner le même **emplacement** que celui de votre cluster Apache Spark dans HDInsight pour réduire la latence et les coûts.
    >
    >

4. Dans la liste d’espaces de noms Event Hubs, cliquez sur l’espace de noms créé.      


5. Dans le panneau d’espace de noms, cliquez sur **Event Hubs**, puis sur **+ Event Hub** pour créer un concentrateur Event Hub.
   
    ![Créer un Event Hub pour un exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-open-event-hubs-blade-for-spark-streaming-example.png "Créer un Event Hub pour un exemple de diffusion en continu Spark")

6. Tapez un nom pour votre concentrateur Event Hub, puis définissez le nombre de partitions sur 10 et la rétention des messages sur 1. Étant donné que nous n’archivons pas les messages dans cette solution, vous pouvez laisser la suite telle qu’elle est définie par défaut, puis cliquer sur **Créer**.
   
    ![Fournir des détails d’Event Hub pour un exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Fournir des détails d’Event Hub pour un exemple de diffusion en continu Spark")

7. Le concentrateur Event Hub que vous venez de créer est répertorié dans le panneau Event Hub.
    
     ![Afficher un Event Hub pour l’exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-for-spark-streaming-example.png "Afficher un Event Hub pour l’exemple de diffusion en continu Spark")

8. Revenez au panneau de l’espace de noms (pas le panneau de l’Event Hub spécifique), cliquez sur **Stratégies d’accès partagé**, puis sur **RootManageSharedAccessKey**.
    
     ![Définir des stratégies d’Event Hub pour l’exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Définir des stratégies d’Event Hub pour l’exemple de diffusion en continu Spark")

9. Cliquez sur le bouton Copier pour copier la chaîne de connexion et la clé primaire **RootManageSharedAccessKey** dans le Presse-papiers. Enregistrez-les pour les utiliser ultérieurement dans ce didacticiel.
    
     ![Afficher des clés de stratégie d’Event Hub pour l’exemple de diffusion en continu Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Afficher des clés de stratégie d’Event Hub pour l’exemple de diffusion en continu Spark")

## <a name="send-messages-to-azure-event-hub-using-a-sample-scala-application"></a>Envoyer des messages à un Azure Event Hub à l’aide d’un exemple d’application Scala

Dans cette section, vous allez utiliser une application Scala locale autonome qui génère un flux d’événements et envoie celui-ci à l’Azure Event Hub que vous avez créé à l’étape précédente. Cette application est disponible sur GitHub à l’adresse [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). Les étapes décrites ici supposent que vous avez déjà dupliqué ce dépôt GitHub.

1. Veillez à installer les éléments suivants sur l’ordinateur sur lequel vous exécutez cette application.

    * Kit de développement logiciel (SDK) Oracle Java. Vous pouvez l’installer à partir [d’ici](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
    * Apache Maven. Vous pouvez le télécharger [ici](https://maven.apache.org/download.cgi). Les instructions d’installation de Maven sont disponibles [ici](https://maven.apache.org/install.html).

2. Ouvrez une invite de commandes et accédez à l’emplacement où vous avez cloné le référentiel GitHub pour l’exemple d’application Scala et exécutez la commande suivante pour générer l’application.

        mvn package

3. Le fichier jar de sortie de l’application, **com-microsoft-azure-eventhubs-client-example-0.2.0.jar**, est créé dans le répertoire **/target**. Ce fichier JAR sera utilisé plus loin dans cet article pour tester la solution.

## <a name="create-application-to-receive-messages-from-event-hub-into-a-spark-cluster"></a>Créer une application pour recevoir des messages d’Event Hub dans un cluster Spark 

Nous disposons de deux approches pour connecter Spark Streaming et Azure Event Hubs : la connexion basée sur récepteur et la connexion basée sur Direct-DStream. La connexion basée sur Direct-DStream a été introduite dans la version 2.0.3 en janvier 2017. Elle est supposée remplacer la connexion d’origine basée sur récepteur, car elle est plus performante et plus efficace en matière de ressources. Vous trouverez plus de détails à l’adresse [https://github.com/hdinsight/spark-eventhubs](https://github.com/hdinsight/spark-eventhubs). Direct DStream prend uniquement en charge Spark 2.0+.

### <a name="build-applications-with-the-dependency-to-spark-eventhubs-connector"></a>Générer des applications avec une dépendance au connecteur spark-eventhubs

Nous publierons également la version intermédiaire de Spark-EventHubs dans GitHub. Pour utiliser la version intermédiaire de Spark-EventHubs, la première étape consiste à indiquer GitHub en tant que référentiel source en ajoutant l’entrée suivante dans le fichier pom.xml :

```xml
<repository>
      <id>spark-eventhubs</id>
      <url>https://raw.github.com/hdinsight/spark-eventhubs/maven-repo/</url>
      <snapshots>
        <enabled>true</enabled>
        <updatePolicy>always</updatePolicy>
      </snapshots>
</repository>
```

Vous pouvez ensuite ajouter les dépendances suivantes à votre projet pour prendre la préversion.

Dépendance Maven

```xml
<!-- https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11 -->
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-streaming-eventhubs_2.11</artifactId>
    <version>2.0.4</version>
</dependency>
```

Dépendance SBT

```
// https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11
libraryDependencies += "com.microsoft.azure" % "spark-streaming-eventhubs_2.11" % "2.0.4"
```

### <a name="direct-dstream-connection"></a>Connexion Direct DStream

Un fichier jar prégénéré contenant des exemples utilisant Direct DStream peut être téléchargé dans [http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar](http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar).

Le fichier jar contient trois exemples dont le code source est disponible à l’adresse [https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream](https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream).

Utilisons [WindowingWordCount](https://github.com/hdinsight/spark-eventhubs/blob/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream/WindowingWordCount.scala) comme exemple :

```scala
private def createStreamingContext(
  sparkCheckpointDir: String,
  batchDuration: Int,
  namespace: String,
  eventHunName: String,
  eventhubParams: Map[String, String],
  progressDir: String) = {
val ssc = new StreamingContext(new SparkContext(), Seconds(batchDuration))
ssc.checkpoint(sparkCheckpointDir)
val inputDirectStream = EventHubsUtils.createDirectStreams(
  ssc,
  namespace,
  progressDir,
  Map(eventHunName -> eventhubParams))

inputDirectStream.map(receivedRecord => (new String(receivedRecord.getBody), 1)).
  reduceByKeyAndWindow((v1, v2) => v1 + v2, (v1, v2) => v1 - v2, Seconds(batchDuration * 3),
    Seconds(batchDuration)).print()

ssc
}

def main(args: Array[String]): Unit = {

if (args.length != 8) {
  println("Usage: program progressDir PolicyName PolicyKey EventHubNamespace EventHubName" +
    " BatchDuration(seconds) Spark_Checkpoint_Directory maxRate")
  sys.exit(1)
}

val progressDir = args(0)
val policyName = args(1)
val policykey = args(2)
val namespace = args(3)
val name = args(4)
val batchDuration = args(5).toInt
val sparkCheckpointDir = args(6)
val maxRate = args(7)

val eventhubParameters = Map[String, String] (
  "eventhubs.policyname" -> policyName,
  "eventhubs.policykey" -> policykey,
  "eventhubs.namespace" -> namespace,
  "eventhubs.name" -> name,
  "eventhubs.partition.count" -> "32",
  "eventhubs.consumergroup" -> "$Default",
  "eventhubs.maxRate" -> s"$maxRate"
)

val ssc = StreamingContext.getOrCreate(sparkCheckpointDir,
  () => createStreamingContext(sparkCheckpointDir, batchDuration, namespace, name,
    eventhubParameters, progressDir))

ssc.start()
ssc.awaitTermination()
}
```

Dans l’exemple ci-dessus, `eventhubParameters` sont les paramètres spécifiques à une seule instance EventHubs et vous devez les transférer à l’API `createDirectStreams` qui construit un mappage d’objet Direct DStream à un espace de noms Event Hubs. Sur l’objet Direct DStream, vous pouvez appeler n’importe quelle API DStream fournie par le framework d’API Spark Streaming. Dans cet exemple, nous calculons la fréquence de chaque mot dans les 3 micro-intervalles de lot.

### <a name="receiver-based-connection"></a>Connexion basée sur récepteur

Un exemple d’application de diffusion en continu Spark écrite en Scala, qui reçoit des événements et les achemine vers différentes destinations est disponible à l’adresse [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Suivez les étapes ci-dessous pour mettre à jour l’application pour la configuration de votre Event Hub et créer le fichier jar de sortie.

1. Lancez IntelliJ IDEA, sélectionnez **Check out from Version Control** (Extraire du contrôle de version) dans l’écran de démarrage, puis cliquez sur **Git**.
   
    ![Exemple de diffusion en continu Apache Spark - obtenir des sources de Git](./media/apache-spark-eventhub-streaming/spark-streaming-example-get-source-from-git.png "Exemple de diffusion en continu Apache Spark - obtenir des sources de Git")

2. Dans la boîte de dialogue **Clone Repository** (Cloner le dépôt), entrez l’URL du dépôt Git à partir duquel opérer le clonage, spécifiez le répertoire vers lequel cloner, puis cliquez sur **Clone**. (Cloner).
   
    ![Exemple de diffusion en continu Apache Spark - cloner Git](./media/apache-spark-eventhub-streaming/spark-streaming-example-clone-from-git.png "Exemple de diffusion en continu Apache Spark - cloner Git")
3. Suivez les invites jusqu’à ce que le projet soit entièrement cloné. Appuyez sur **Alt + 1** pour ouvrir la **Vue du projet**. Elle doit ressembler à ceci :
   
    ![Exemple de diffusion en continu Apache Spark - Affichage de projet](./media/apache-spark-eventhub-streaming/spark-streaming-example-project-view.png "Exemple de diffusion en continu Apache Spark - Affichage de projet")
4. Assurez-vous que le code d’application est compilé avec Java8. Pour ce faire, cliquez sur **Fichier**, sur **Structure de projet**, puis sur l’onglet **Projet**, assurez-vous que le niveau de langue du projet est défini sur **8 - Lambdas, type annotations, etc.** (8- Expressions lambda, annotations de type, etc).
   
    ![Exemple de diffusion en continu Apache Spark - Définir un compilateur](./media/apache-spark-eventhub-streaming/spark-streaming-example-java-8-compiler.png "Exemple de diffusion en continu Apache Spark - Définir un compilateur")
5. Ouvrez le fichier **pom.xml** et assurez-vous que la version de Spark est correcte. Dans le nœud `<properties>`, recherchez l’extrait de code suivant et vérifiez la version de Spark.

        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. L’application requiert un fichier jar de dépendance appelé **fichier jar du pilote JDBC**. Ce fichier est nécessaire pour écrire les messages reçus de l’Event Hub dans une base de données SQL Azure. Vous pouvez télécharger ce fichier jar (version 4.1 ou version ultérieure) à partir d’[ici](https://msdn.microsoft.com/sqlserver/aa937724.aspx). Ajoutez la référence à ce fichier jar dans la bibliothèque de projet. Procédez comme suit :
     
     1. Dans la fenêtre IntelliJ IDEA où l’application est ouverte, cliquez sur **File** (Fichier), sur **Project Structure** (Structure de projet), puis sur **Libraries** (Bibliothèques). 
     2. Cliquez sur l’icône Ajouter (![icône Ajouter](./media/apache-spark-eventhub-streaming/add-icon.png)), sur **Java**, puis accédez à l’emplacement où vous avez téléchargé le fichier jar du pilote JDBC. Suivez les invites pour ajouter le fichier jar à la bibliothèque de projet.

         ![ajouter les dépendances manquantes](./media/apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Ajouter les fichiers jars de dépendance manquants")
     3. Cliquez sur **Appliquer**.

7. Créez le fichier jar de sortie. Procédez comme suit.

   1. Dans la boîte de dialogue **Project Structure** (Structure de projet) cliquez sur **Artifacts** (Artefacts), puis sur le signe plus. Dans la boîte de dialogue contextuelle, cliquez sur **JAR**, puis sur **À partir de modules ayant des dépendances**.      
       
       ![Exemple de diffusion en continu Apache Spark - créer un fichier jar](./media/apache-spark-eventhub-streaming/spark-streaming-example-create-jar.png "Exemple de diffusion en continu Apache Spark - créer un fichier jar")
   2. Dans la boîte de dialogue **Créer un fichier JAR à partir de modules**, cliquez sur le bouton de sélection (![ellipse](./media/apache-spark-eventhub-streaming/ellipsis.png)) en regard de **Classe principale**.
   3. Dans la boîte de dialogue **Select Main Class** (Sélectionner une classe principale), sélectionnez l’une des classes disponibles, puis cliquez sur **OK** (OK).
      
       ![Exemple de diffusion en continu Apache Spark - sélectionner une classe pour un fichier jar](./media/apache-spark-eventhub-streaming/spark-streaming-example-select-class-for-jar.png "Exemple de diffusion en continu Apache Spark - sélectionner une classe pour un fichier jar")
   4. Dans la boîte de dialogue **Create JAR from Modules** (Créer un fichier JAR à partir de modules), assurez-vous que l’option **Extract to the target JAR** (Extraire vers le fichier JAR cible) est activée, puis cliquez sur **OK** (OK). Cela crée un fichier JAR contenant toutes les dépendances.
      
       ![Exemple de diffusion en continu Apache Spark - créer un fichier jar à partir de modules](./media/apache-spark-eventhub-streaming/spark-streaming-example-create-jar-from-modules.png "Exemple de diffusion en continu Apache Spark - créer un fichier jar à partir de modules")
   5. L’onglet **Output Layout** (Disposition de la sortie) répertorie tous les fichiers jar inclus dans le cadre du projet Maven. Vous pouvez sélectionner et supprimer ceux sur lesquels l’application Scala n’a aucune dépendance directe. Pour l’application que nous créons ici, vous pouvez les supprimer tous sauf le dernier (**spark-streaming-data-persistence-examples compile output**). Sélectionnez les fichiers JAR à supprimer, puis cliquez sur l’icône **Delete** (Supprimer) (![icône de suppression](./media/apache-spark-eventhub-streaming/delete-icon.png)).
      
       ![Exemple de diffusion en continu Apache Spark - supprimer un fichier jar extrait](./media/apache-spark-eventhub-streaming/spark-streaming-example-delete-output-jars.png "Exemple de diffusion en continu Apache Spark - supprimer un fichier jar extrait")
      
       Assurez-vous que la case à cocher **Générer à la création** est activée, ce qui garantit la création du fichier JAR à chaque génération ou mise à jour du projet. Cliquez sur **Appliquer**.
   6. Sous l’onglet **Output Layout** (Disposition de la sortie), en bas à droite de la zone **Available Elements (Éléments disponibles)**, vous pouvez voir le fichier jar SQL JDBC jar que vous avez ajouté précédemment à la bibliothèque de projet. Vous devez l’ajouter à l’onglet **Output Layout** (Disposition de la sortie). Cliquez avec le bouton droit sur le fichier jar, puis cliquez sur **Extract Into Output Root**(Extraire dans la racine de sortie).
      
       ![Exemple de diffusion en continu Apache Spark - extraire un fichier jar de dépendance](./media/apache-spark-eventhub-streaming/spark-streaming-example-extract-dependency-jar.png "Exemple de diffusion en continu Apache Spark - extraire un fichier jar de dépendance")  
      
       L’onglet **Output Layout** (Disposition de la sortie) doit maintenant ressembler à ceci.
      
       ![Exemple de diffusion en continu Apache Spark - onglet de sortie finale](./media/apache-spark-eventhub-streaming/spark-streaming-example-final-output-tab.png "Exemple de diffusion en continu Apache Spark - onglet de sortie finale")        
      
       Dans la boîte de dialogue **Project Structure** (Structure de projet), cliquez sur **Apply** (Appliquer), puis sur **OK** (OK).    
   7. Dans la barre de menus, cliquez sur **Build** (Générer), puis sur **Make Project** (Créer le projet). Vous pouvez également cliquer sur **Générer les artefacts** pour créer le fichier JAR. Le fichier jar de sortie est créé sous **\classes\artifacts**.
      
       ![Exemple de diffusion en continu Apache Spark - fichier jar de sortie](./media/apache-spark-eventhub-streaming/spark-streaming-example-output-jar.png "Exemple de diffusion en continu Apache Spark - fichier jar de sortie")

## <a name="run-the-application-remotely-on-a-spark-cluster-using-livy"></a>Exécuter l’application à distance sur un cluster Spark à l’aide de Livy

Dans le cadre de cet article, vous allez utiliser Livy pour exécuter l’application de diffusion en continu Apache Spark à distance sur un cluster Spark. Pour une présentation détaillée de l’utilisation de Livy avec le cluster HDInsight Spark, voir [Soumettre des travaux à distance à un cluster Apache Spark sur Azure HDInsight](apache-spark-livy-rest-interface.md). Avant de commencer à exécuter l’application de diffusion en continu Spark, vous devez effectuer plusieurs opérations :

1. Démarrez l’application autonome locale pour générer des événements et les envoyer à l’Event Hub. Pour ce faire, utilisez la commande suivante :

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Copiez le fichier jar de diffusion en continu (**spark-streaming-data-persistence-examples.jar**) vers le stockage Blob Azure associé au cluster. Le fichier jar est ainsi accessible à Livy. Pour ce faire, vous pouvez utiliser l’utilitaire de ligne de commande [**AzCopy**](../../storage/common/storage-use-azcopy.md). De nombreux autres clients permettent également de télécharger des données. Pour en savoir plus à leur sujet, consultez [Téléchargement de données pour les travaux Hadoop dans HDInsight](../hdinsight-upload-data.md).
3. Installez CURL sur l’ordinateur à partir duquel vous exécutez ces applications. Nous utilisons CURL pour appeler les points de terminaison Livy afin d’exécuter les travaux à distance.

### <a name="run-the-spark-streaming-application-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Exécuter l’application de diffusion en continu Spark pour recevoir les événements dans un Azure Storage Blob en tant que texte

Ouvrez une invite de commandes, accédez au répertoire dans lequel vous avez installé CURL, puis exécutez la commande suivante (en remplaçant le nom/mot de passe d’utilisateur et le nom du cluster) :

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Les paramètres du fichier **inputBlob.txt** sont définis comme suit :

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Nous devons comprendre quels sont les paramètres dans le fichier d’entrée :

* **file** est le chemin d’accès au fichier jar d’application sur le compte Azure Storage associé au cluster.
* **className** est le nom de la classe dans le fichier jar.
* **args** est la liste des arguments requis par la classe.
* **numExecutors** est le nombre de cœurs que Spark utilise pour exécuter l’application de diffusion en continu. Il doit toujours y avoir au moins deux fois le nombre de partitions de l’Event Hub.
* **executorMemory**, **executorCores** et **driverMemory** sont des paramètres utilisés pour affecter les ressources nécessaires à l’application de diffusion en continu.

> [!NOTE]
> Il est inutile de créer les dossiers de sortie (EventCheckpoint, EventCount/EventCount10) utilisés comme paramètres. L’application de diffusion en continu les crée pour vous.
>
>

Lorsque vous exécutez la commande, vous devez voir une sortie similaire à ce qui suit :

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Prenez note de l’ID de lot dans la dernière ligne de la sortie (dans cet exemple, « 1 »). Pour vérifier que l’application s’exécute correctement, vous pouvez consulter votre compte de stockage Azure associé au cluster, et devriez y voir le dossier **/nombre d’événements/EventCount10** créé. Ce dossier doit contenir les objets blob qui capturent le nombre d’événements traités pendant la période spécifiée pour le paramètre **batch-interval-in-seconds**.

L’application de diffusion en continu Spark continue à s’exécuter jusqu’à ce que vous l’arrêtiez. Pour ce faire, utilisez la commande suivante :

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Exécuter les applications pour recevoir les événements dans un objet blob Azure Storage en tant que fichier JSON
Ouvrez une invite de commandes, accédez au répertoire dans lequel vous avez installé CURL, puis exécutez la commande suivante (en remplaçant le nom/mot de passe d’utilisateur et le nom du cluster) :

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Les paramètres du fichier **inputJSON.txt** sont définis comme suit :

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Les paramètres sont similaires à ce que vous avez spécifié pour la sortie de texte à l’étape précédente. Une fois encore, il est inutile de créer les dossiers de sortie (EventCheckpoint, EventCount/EventCount10) utilisés comme paramètres. L’application de diffusion en continu les crée pour vous.

 Après avoir exécuté la commande, vous pouvez consulter votre compte de stockage Azure associé au cluster, et devriez y voir le dossier **/EventStore10** créé. Ouvrez n’importe quel fichier ayant le préfixe **part-**. Vous devriez y voir les événements traités au format JSON.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Exécuter les applications pour recevoir les événements dans une table Hive
Pour exécuter l’application de diffusion en continu Spark qui diffuse des événements dans une table Hive, vous avez besoin de composants supplémentaires. Ces règles sont les suivantes :

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-SGBDR-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

Les fichiers **.jar** sont disponibles sur votre cluster HDInsight Spark à l’adresse `/usr/hdp/current/spark-client/lib`. Le fichier **hive-site.xml** est disponible à l’adresse `/usr/hdp/current/spark-client/conf`.

Vous pouvez utiliser [WinScp](http://winscp.net/eng/download.php) pour copier ces fichiers à partir du cluster sur votre ordinateur local. Vous pouvez ensuite vous servir d’outils pour copier ces fichiers sur votre compte de stockage associé au cluster. Pour plus d’informations sur la façon de charger des fichiers sur le compte de stockage, voir [Téléchargement de données pour des tâches Hadoop dans HDInsight](../hdinsight-upload-data.md).

Après avoir copié les fichiers sur votre compte de stockage Azure, ouvrez une invite de commandes, accédez au répertoire dans lequel vous avez installé CURL, puis exécutez la commande suivante (en remplaçant le nom/mot de passe d’utilisateur et le nom du cluster) :

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Les paramètres du fichier **inputHive.txt** sont définis comme suit :

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasb:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasb:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasb:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasb:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Les paramètres sont similaires à ce que vous avez spécifié pour la sortie de texte aux étapes précédentes. Une fois encore, il est inutile de créer les dossiers de sortie(EventCheckpoint, EventCount/EventCount10) ou la table Hive de sortie (EventHiveTable10) utilisés comme paramètres. L’application de diffusion en continu les crée pour vous. Notez que l’option **jars** et **files** inclut des chemins d’accès aux fichiers .jar et au fichier hive-site.xml que vous avez copiés vers le compte de stockage.

Pour vérifier que la table hive a été créée, utilisez l’[affichage Ambari Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md). Vous pouvez exécuter une requête SELECT pour afficher le contenu de la table.

    SELECT * FROM eventhivetable10 LIMIT 10;

Un résultat similaire à ce qui suit s’affiche normalement :

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Exécuter les applications pour recevoir les événements dans une table de Base de données SQL Azure
Avant d’exécuter cette étape, assurez-vous que vous disposez d’une base de données SQL Azure. Pour obtenir des instructions, consultez [Créer une base de données SQL en quelques minutes](../../sql-database/sql-database-get-started-portal.md). Pour terminer cette section, vous avez besoin des valeurs de nom de base de données, de nom de serveur de base de données, ainsi que des informations d’identification de l’administrateur de base de données en tant que paramètres. Il est cependant inutile de créer la table de base de données. L’application de diffusion en continu Spark la crée pour vous.

Ouvrez une invite de commandes, accédez au répertoire dans lequel vous avez installé CURL, puis exécutez la commande suivante :

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Les paramètres du fichier **inputSQL.txt** sont définis comme suit :

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Pour vérifier que l’application s’exécute correctement, vous pouvez vous connecter à la base de données SQL Azure à l’aide de SQL Server Management Studio. Pour obtenir des instructions sur la procédure à suivre, voir [Se connecter à Base de données SQL avec SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md). Une fois connecté à la base de données, vous pouvez accéder à la table **EventContent** créée par l’application de diffusion en continu. Vous pouvez exécuter une requête rapide pour obtenir les données de la table. Exécutez la requête suivante :

    SELECT * FROM EventCount

Le résultat ressemble à ce qui suit :

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)
* [Conception de la connexion basée sur récepteur et Direct DStream](https://www.slideshare.net/NanZhu/seattle-sparkmeetup032317)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Exécution de travaux à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utilisez le plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]: ../storage-create-storage-account/
