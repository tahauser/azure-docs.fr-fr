---
title: 'Didacticiel : Diffuser en continu des données dans Azure Databricks à l’aide d’Event Hubs | Microsoft Docs'
description: Apprenez à utiliser Azure Databricks avec Event Hubs pour ingérer des données de diffusion en continu de Twitter et la lecture des données en temps quasi-réel.
services: azure-databricks
documentationcenter: ''
author: lenadroid
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.custom: mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 03/23/2018
ms.author: alehall
ms.openlocfilehash: 94b09b824becc8a67adf4edfd2d4b44496a6169c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-stream-data-into-azure-databricks-using-event-hubs"></a>Didacticiel : Diffuser en continu des données dans Azure Databricks à l’aide d’Event Hubs

Dans ce didacticiel, vous connectez un système d’ingestion des données à Azure Databricks pour diffuser en continu des données en temps quasi-réel dans un cluster Apache Spark. Vous allez configurer le système d’ingestion des données à l’aide d’Azure Event Hubs et le connecter à Azure Databricks pour traiter les messages entrants. Pour accéder à un flux de données, vous allez utiliser des API Twitter pour faire ingérer des tweets à Event Hubs. Une fois les données reçues dans Azure Databricks, vous pourrez exécuter des tâches d’analyse pour aller plus loin. 

À la fin de ce didacticiel, vous disposerez de tweets diffusés en continu de Twitter (comprenant le terme « Azure ») et lirez ces tweets dans Azure Databricks.

L’illustration suivante montre le flux d’application :

![Azure Databricks avec Event Hubs](./media/databricks-stream-from-eventhubs/databricks-eventhubs-tutorial.png "Azure Databricks avec Event Hubs")

Ce didacticiel décrit les tâches suivantes :

> [!div class="checklist"]
> * Créer un espace de travail Azure Databricks
> * Créer un cluster Spark dans Azure Databricks
> * Créer une application Twitter pour accéder aux données de diffusion en continu
> * Créer des notebooks dans Azure Databricks
> * Joindre des bibliothèques pour Event Hubs et les API Twitter
> * Envoyer des tweets vers Event Hubs
> * Lire des tweets à partir d’Event Hubs

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis


Avant de commencer le didacticiel, veillez à disposer des éléments suivants :
- Un espace de noms Azure Event Hubs.
- Un concentrateur d’événements au sein de l’espace de noms.
- Une chaîne de connexion pour accéder à l’espace de noms Event Hubs. La chaîne de connexion suit un format similaire à `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.
- Un nom de stratégie d’accès partagé et une clé de stratégie pour Event Hubs.

Vous pouvez obtenir tous ces éléments en terminant les étapes de l’article [Créer un espace de noms et un concentrateur d’événements Azure Event Hubs](../event-hubs/event-hubs-create.md).

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Créer un espace de travail Azure Databricks

Dans cette section, vous créez un espace de travail Azure Databricks en utilisant le portail Azure.

1. Dans le portail Azure, sélectionnez **Créer une ressource** > **Données + Analytique** > **Azure Databricks**.

    ![Databricks sur le portail Azure](./media/databricks-stream-from-eventhubs/azure-databricks-on-portal.png "Databricks sur le portail Azure")

3. Sous **Service Azure Databricks**, renseignez les valeurs pour créer un espace de travail Databricks.

    ![Créer un espace de travail Azure Databricks](./media/databricks-stream-from-eventhubs/create-databricks-workspace.png "Créer un espace de travail Azure Databricks")

    Renseignez les valeurs suivantes :

    |Propriété  |Description  |
    |---------|---------|
    |**Nom de l’espace de travail**     | Renseignez un nom pour votre espace de travail Databricks.        |
    |**Abonnement**     | Sélectionnez votre abonnement Azure dans la liste déroulante.        |
    |**Groupe de ressources**     | Indiquez si vous souhaitez créer un groupe de ressources Azure ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez [Présentation des groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Lieu**     | Sélectionnez **Est des États-Unis 2**. Pour les autres régions disponibles, consultez [Disponibilité des services Azure par région](https://azure.microsoft.com/regions/services/).        |
    |**Niveau tarifaire**     |  Choisissez entre **Standard** ou **Premium**. Pour plus d’informations sur ces niveaux, consultez la [page de tarification Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.

4. La création du compte prend quelques minutes. Lors de la création d’un compte, le portail affiche la vignette **Envoi du déploiement pour Azure Databricks** sur le côté droit. Vous devrez peut-être faire défiler votre tableau de bord vers la droite pour voir la vignette. Il existe également une barre de progression en haut de l’écran. Vous pouvez surveiller la progression de la zone souhaitée.

    ![Vignette de déploiement Databricks](./media/databricks-stream-from-eventhubs/databricks-deployment-tile.png "Vignette de déploiement Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Créer un cluster Spark dans Databricks

1. Dans le portail Azure, accédez à l’espace de travail Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

2. Vous êtes redirigé vers le portail Azure Databricks. Dans le portail, sélectionnez **Cluster**.

    ![Databricks sur Azure](./media/databricks-stream-from-eventhubs/databricks-on-azure.png "Databricks sur Azure")

3. Dans la page **Nouveau cluster**, renseignez les valeurs pour créer un cluster.

    ![Créer un cluster Databricks Spark sur Azure](./media/databricks-stream-from-eventhubs/create-databricks-spark-cluster.png "Créer un cluster Databricks Spark sur Azure")

    Acceptez toutes les valeurs par défaut autres que les suivantes :

    * Entrez un nom pour le cluster.
    * Pour cet article, créez un cluster avec le runtime **4.0**.
    * Veillez à cocher la case **Arrêter après ___ minutes d’inactivité**. Spécifiez une durée (en minutes) pour arrêter le cluster, si le cluster n’est pas utilisé.

    Sélectionnez **Créer un cluster**. Une fois que le cluster est en cours d’exécution, vous pouvez y attacher des notebooks et exécuter des travaux Spark.

## <a name="create-a-twitter-application"></a>Création d'une application Twitter

Pour recevoir un flux de tweets, vous créez une application dans Twitter. Suivez les instructions pour créer une application Twitter et enregistrez les valeurs dont vous avez besoin pour terminer ce didacticiel.

1. À partir d’un navigateur web, accédez à [Gestion des applications Twitter](http://twitter.com/app) et sélectionnez **Créer une application**.

    ![Créer une application Twitter](./media/databricks-stream-from-eventhubs/databricks-create-twitter-app.png "Créer une application Twitter")

2. Sur la page **Créer une application**, renseignez les informations de la nouvelle application, puis sélectionnez **Créer votre application Twitter**.

    ![Détails de l’application de Twitter](./media/databricks-stream-from-eventhubs/databricks-provide-twitter-app-details.png "Détails de l’application de Twitter")

3. Sur la page de l’application, sélectionnez l’onglet **Clés et jetons d’accès** et copiez les valeurs de **Clé de consommation** et **Secret de consommation**. Sélectionnez aussi **Créer mon jeton d’accès** pour générer des jetons d’accès. Copiez les valeurs de **Jeton d’accès** et **Secret du jeton d’accès**.

    ![Détails de l’application de Twitter](./media/databricks-stream-from-eventhubs/twitter-app-key-secret.png "Détails de l’application de Twitter")

Enregistrez les valeurs que vous avez récupérées pour l’application Twitter. Vous aurez besoin de ces valeurs plus loin dans le didacticiel.

## <a name="attach-libraries-to-spark-cluster"></a>Joindre des bibliothèques au cluster Spark

Dans ce didacticiel, vous allez utiliser les API Twitter pour envoyer des tweets à Event Hubs. Vous allez aussi utiliser le [connecteur Apache Spark Event Hubs](https://github.com/Azure/azure-event-hubs-spark) pour lire et écrire des données dans Azure Event Hubs. Pour utiliser ces API au sein de votre cluster, ajoutez-les en tant que bibliothèques à Azure Databricks puis associez-les à votre cluster Spark. Les instructions suivantes expliquent comment ajouter la bibliothèque au dossier **Partagé** dans votre espace de travail.

1.  Dans l’espace de travail Azure Databricks, sélectionnez **Espace de travail**, puis cliquez avec le bouton droit sur **Partagé**. Dans le menu contextuel, sélectionnez **Créer** > **Bibliothèque**.

    ![Boîte de dialogue Ajouter une bibliothèque](./media/databricks-stream-from-eventhubs/databricks-add-library-option.png "Boîte de dialogue Ajouter une bibliothèque")

2. Sur la page Nouvelle bibliothèque, sélectionnez **Coordonnées Maven** comme **Source**. Pour **Coordonnées**, saisissez les coordonnées du package que vous voulez ajouter. Voici les coordonnées Maven des bibliothèques utilisées dans ce didacticiel :

    * Connecteur Spark Event Hubs : `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.1`
    * API Twitter : `org.twitter4j:twitter4j-core:4.0.6`

    ![Renseigner des coordonnées Maven](./media/databricks-stream-from-eventhubs/databricks-eventhub-specify-maven-coordinate.png "Renseigner des coordonnées Maven")

3. Sélectionnez **Créer une bibliothèque**.

4. Sélectionnez le dossier dans lequel vous avez ajouté la bibliothèque, puis sélectionnez le nom de la bibliothèque.

    ![Sélectionner la bibliothèque à ajouter](./media/databricks-stream-from-eventhubs/select-library.png "Sélectionner la bibliothèque à ajouter")

5. Sur la page de la bibliothèque, sélectionnez le cluster dans lequel vous voulez utiliser la bibliothèque. Une fois que vous avez associé la bibliothèque au cluster, le statut change immédiatement en **Jointe**.

    ![Joindre une bibliothèque à un cluster](./media/databricks-stream-from-eventhubs/databricks-library-attached.png "Joindre une bibliothèque à un cluster")

6. Répétez ces étapes pour le package Twitter, `twitter4j-core:4.0.6`.

## <a name="create-notebooks-in-databricks"></a>Créer des notebooks dans Databricks

Dans cette section, vous allez créer deux notebooks dans l’espace de travail Databricks avec les noms suivants :

- **SendTweetsToEventHub** : un notebook producteur à utiliser pour obtenir des tweets de Twitter et les diffuser dans Event Hubs.
- **ReadTweetsFromEventHub** : un notebook consommateur à utiliser pour lire les tweets à partir d’Event Hubs.

1. Dans le volet gauche, sélectionnez **Espace de travail**. Dans la liste déroulante **Espace de travail**, sélectionnez **Créer** > **Notebook**.

    ![Créer un notebook dans Databricks](./media/databricks-stream-from-eventhubs/databricks-create-notebook.png "Créer un notebook dans Databricks")

2. Dans la boîte de dialogue **Créer un notebook**, entrez **SendTweetsToEventHub**, sélectionnez **Scala** comme langage, puis sélectionnez le cluster Spark que vous avez créé précédemment.

    ![Créer un notebook dans Databricks](./media/databricks-stream-from-eventhubs/databricks-notebook-details.png "Créer un notebook dans Databricks")

    Sélectionnez **Créer**.

3. Répétez les étapes pour créer le notebook **ReadTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Envoyer des tweets vers Event Hubs

Dans le notebook **SendTweetsToEventHub**, collez le code suivant, et remplacez les espaces réservés par les valeurs de votre espace de noms Event Hubs et de l’application Twitter que vous avez créés précédemment. Ce notebook diffuse les tweets avec le mot-clé « Azure » dans Event Hubs en temps réel.

    import java.util._
    import scala.collection.JavaConverters._
    import com.microsoft.azure.eventhubs._
    import java.util.concurrent._

    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sendEvent(message: String) = {
      val messageData = EventData.create(message.getBytes("UTF-8"))
      eventHubClient.get().send(messageData)
      System.out.println("Sent event: " + message + "\n")
    }

    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter configuration!
    // Replace values below with yours

    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"

    val cb = new ConfigurationBuilder()
      cb.setDebugEnabled(true)
      .setOAuthConsumerKey(twitterConsumerKey)
      .setOAuthConsumerSecret(twitterConsumerSecret)
      .setOAuthAccessToken(twitterOauthAccessToken)
      .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()

Appuyez sur **Maj+Entrée** pour exécuter le notebook. Vous verrez une sortie identique à l’extrait de code ci-dessous. Chaque événement de la sortie représente un tweet ingéré par Event Hubs contenant le terme « Azure ».

    Sent event: @Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence

    Sent event: Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure

    Sent event: 4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie

    Sent event: Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk

    Sent event: Top 10 Tricks to #Save Money with #Azure Virtual Machines https://t.co/F2wshBXdoz #Cloud

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Lire des tweets à partir d’Event Hubs

Dans le notebook **ReadTweetsFromEventHub**, collez le code suivant, et remplacez l’espace réservé par les valeurs du concentrateur d’événements Azure Event Hubs que vous avez créé précédemment. Ce notebook lit les tweets que vous avez diffusés précédemment dans Event Hubs à l’aide du notebook **SendTweetsToEventHub**.

    import org.apache.spark.eventhubs._

    // Build connection string with the above information
    val connectionString = ConnectionStringBuilder("<EVENT HUBS CONNECTION STRING>")
      .setEventHubName("<EVENT HUB NAME>")
      .build

    val customEventhubParameters =
      EventHubsConf(connectionString)
      .setMaxEventsPerTrigger(5)

    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

    incomingStream.printSchema

    // Sending the incoming stream into the console.
    // Data comes in batches!
    incomingStream.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()

Vous obtenez la sortie suivante :


    root
     |-- body: binary (nullable = true)
     |-- offset: long (nullable = true)
     |-- seqNumber: long (nullable = true)
     |-- enqueuedTime: long (nullable = true)
     |-- publisher: string (nullable = true)
     |-- partitionKey: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+------+--------------+---------------+---------+------------+
    |body  |offset|sequenceNumber|enqueuedTime   |publisher|partitionKey|
    +------+------+--------------+---------------+---------+------------+
    |[50 75 62 6C 69 63 20 70 72 65 76 69 65 77 20 6F 66 20 4A 61 76 61 20 6F 6E 20 41 70 70 20 53 65 72 76 69 63 65 2C 20 62 75 69 6C 74 2D 69 6E 20 73 75 70 70 6F 72 74 20 66 6F 72 20 54 6F 6D 63 61 74 20 61 6E 64 20 4F 70 65 6E 4A 44 4B 0A 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 37 76 73 37 63 4B 74 76 61 68 20 0A 23 63 6C 6F 75 64 63 6F 6D 70 75 74 69 6E 67 20 23 41 7A 75 72 65]                              |0     |0             |2018-03-09 05:49:08.86 |null     |null        |
    |[4D 69 67 72 61 74 65 20 79 6F 75 72 20 64 61 74 61 62 61 73 65 73 20 74 6F 20 61 20 66 75 6C 6C 79 20 6D 61 6E 61 67 65 64 20 73 65 72 76 69 63 65 20 77 69 74 68 20 41 7A 75 72 65 20 53 51 4C 20 44 61 74 61 62 61 73 65 20 4D 61 6E 61 67 65 64 20 49 6E 73 74 61 6E 63 65 20 7C 20 23 41 7A 75 72 65 20 7C 20 23 43 6C 6F 75 64 20 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 73 4A 48 58 4E 34 74 72 44 6B]            |168   |1             |2018-03-09 05:49:24.752|null     |null        |
    +------+------+--------------+---------------+---------+------------+

    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Étant donné que la sortie est en mode binaire, utilisez l’extrait de code suivant pour la convertir en chaîne.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so we cast it to string to see the actual content of the message
    val messages =
      incomingStream
      .withColumn("Offset", $"offset".cast(LongType))
      .withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType))
      .withColumn("Timestamp", $"enqueuedTime".cast(LongType))
      .withColumn("Body", $"body".cast(StringType))
      .select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()

La sortie ressemble maintenant à l’extrait de code suivant :

    root
     |-- Offset: long (nullable = true)
     |-- Time (readable): timestamp (nullable = true)
     |-- Timestamp: long (nullable = true)
     |-- Body: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+-----------------+----------+-------+
    |Offset|Time (readable)  |Timestamp |Body
    +------+-----------------+----------+-------+
    |0     |2018-03-09 05:49:08.86 |1520574548|Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure          |
    |168   |2018-03-09 05:49:24.752|1520574564|Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |
    |0     |2018-03-09 05:49:02.936|1520574542|@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|
    |176   |2018-03-09 05:49:20.801|1520574560|4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |
    +------+-----------------+----------+-------+
    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Et voilà ! Avec Azure Databricks, vous avez réussi à diffuser en continu des données dans Azure Event Hubs en temps quasi-réel. Vous avez ensuite utilisé les données de flux avec le connecteur Event Hubs pour Apache Spark.

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois le didacticiel terminé, vous pouvez arrêter le cluster. Pour cela, dans l’espace de travail Azure Databricks, dans le volet gauche, sélectionnez **Clusters**. Pour le cluster que vous voulez arrêter, déplacez le curseur sur les points de suspension dans la colonne **Actions**, puis sélectionnez l’icône **Arrêter**.

![Arrêter un cluster Databricks](./media/databricks-stream-from-eventhubs/terminate-databricks-cluster.png "Arrêter un cluster Databricks")

Si vous n’arrêtez pas le cluster manuellement, il s’arrêtera automatiquement, à condition d’avoir coché **Arrêter après __ minutes d’inactivité** lors de la création du cluster. Dans ce cas, le cluster s’arrête automatiquement s’il a été inactif pendant la période renseignée.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un espace de travail Azure Databricks
> * Créer un cluster Spark dans Azure Databricks
> * Créer une application Twitter pour générer des données de diffusion en continu
> * Créer des notebooks dans Azure Databricks
> * Ajouter des bibliothèques pour Event Hubs et les API Twitter
> * Envoyer des tweets vers Event Hubs
> * Lire des tweets à partir d’Event Hubs

Passez au didacticiel suivant pour en savoir plus sur l’exécution d’une analyse d’opinions sur les données diffusées en continu à l’aide d’Azure Databricks et de l’[API Microsoft Cognitive Services](../cognitive-services/text-analytics/overview.md).

> [!div class="nextstepaction"]
>[Analyse d’opinions sur les données de streaming à l’aide d’Azure Databricks ](databricks-sentiment-analysis-cognitive-services.md)
