---
title: "Analyser des données Twitter avec Apache Hive - Azure HDInsight | Documents Microsoft"
description: "Découvrez comment utiliser Hive et Hadoop sur HDInsight pour transformer des données TWitter brutes en une table Hive utilisable pour effectuer des recherches."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1e249ed-5f57-40d6-b3bc-a1b4d9a871d3
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: b6e540576bc4a5876bc8546262a181bd82ad9727
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="analyze-twitter-data-using-hive-and-hadoop-on-hdinsight"></a>Analyser des données Twitter avec Hive et Hadoop sur HDInsight

Découvrez comment utiliser Apache Hive pour traiter des données Twitter. Une liste des utilisateurs de Twitter ayant envoyé le plus de tweets contenant un mot donné vous est ensuite retournée.

> [!IMPORTANT]
> Les étapes décrites dans ce document ont été testées sur HDInsight 3.6.
>
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="get-the-data"></a>Obtenir les données

Twitter vous permet de récupérer les [données de chaque tweet](https://dev.twitter.com/docs/platform-objects/tweets) sous la forme d’un document JavaScript Object Notation (JSON) via une API REST. [OAuth](http://oauth.net) est requis pour l'authentification de l'API.

### <a name="create-a-twitter-application"></a>Création d'une application Twitter

1. Depuis un navigateur Web, connectez-vous à [https://apps.twitter.com/](https://apps.twitter.com/). Cliquez sur le lien **Sign up now** si vous ne possédez pas de compte Twitter.

2. Cliquez sur **Create New App**.

3. Renseignez les champs **Name**, **Description** et **Website**. Vous pouvez créer une URL pour le champ **Website** . Le tableau suivant affiche quelques exemples de valeurs à utiliser :

   | Champ | Valeur |
   |:--- |:--- |
   | NOM |MyHDInsightApp |
   | Description |MyHDInsightApp |
   | Website |http://www.myhdinsightapp.com |

4. Cochez la case **Yes, I agree**, puis cliquez **Create your Twitter application**.

5. Cliquez sur l'onglet **Permissions** . L'autorisation par défaut est **Read only**.

6. Cliquez sur l’onglet **Keys and Access Tokens** .

7. Cliquez sur **Create my access token**.

8. Cliquez sur **Test OAuth** dans le coin supérieur droit de la page.

9. Renseignez les valeurs **consumer key**, **Consumer secret**, **Access token** et **Access token secret**.

### <a name="download-tweets"></a>Téléchargement de tweets

Le code Python suivant télécharge 10 000 tweets à partir de Twitter et les enregistrera dans un fichier nommé **tweets.txt**.

> [!NOTE]
> Les étapes suivantes sont effectuées sur le cluster HDInsight, puisque Python a déjà été installé.

1. Connectez-vous au cluster HDInsight à l’aide de SSH :

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Utilisez les commandes suivantes pour installer [Tweepy](http://www.tweepy.org/), [Progressbar](https://pypi.python.org/pypi/progressbar/2.2) et d’autres packages requis :

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

4. Utilisez la commande suivante pour créer un fichier nommé **gettweets.py** :

   ```bash
   nano gettweets.py
   ```

5. Utilisez le texte suivant comme contenu du fichier **gettweets.py** :

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=10000

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!IMPORTANT]
    > Remplacez les espaces réservés des éléments suivants par les informations de votre application twitter :
    >
    > * `consumer_secret`
    > * `consumer_key`
    > * `access_token`
    > * `access_token_secret`

    > [!TIP]
    > Ajustez le filtre de rubriques sur la dernière ligne afin de suivre les mots clés courants. L’utilisation des mots clés courants au moment de l’exécution du script permet une capture plus rapide des données.

6. Appuyez sur **Ctrl + X**, puis sur **Y** pour enregistrer le fichier.

7. Utilisez la commande suivante pour exécuter le fichier et télécharger des tweets :

    ```bash
    python gettweets.py
    ```

    Un indicateur de progression s’affiche. Il progresse jusqu’à 100 % au fur et à mesure que les tweets sont téléchargés.

   > [!NOTE]
   > Si la barre de progression n’avance pas, vous devez modifier le filtre pour effectuer le suivi des tendances. Lorsqu’il existe un grand nombre de tweets concernant la rubrique que vous filtrez, vous pouvez obtenir rapidement les 10 000 tweets nécessaires.

### <a name="upload-the-data"></a>Téléchargement des données

Pour télécharger les données vers le stockage HDInsight, utilisez les commandes suivantes :

   ```bash
   hdfs dfs -mkdir -p /tutorials/twitter/data
   hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Ces commandes stockent les données dans un emplacement accessible à tous les nœuds du cluster.

## <a name="run-the-hiveql-job"></a>Exécutez la tâche HiveQL

1. Utilisez la commande suivante pour créer un fichier contenant des instructions HiveQL :

   ```bash
   nano twitter.hql
   ```

    Utilisez les données suivantes comme contenu du fichier :

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

2. Appuyez sur **Ctrl + X**, puis sur **Y** pour enregistrer le fichier.
3. Utilisez la commande suivante pour exécuter le HiveQL contenu dans le fichier :

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Cette commande exécute le fichier **twitter.hql**. Une fois la requête terminée, vous voyez une invite `jdbc:hive2//localhost:10001/>`.

4. À l’invite Beeline, utilisez la requête suivante pour vérifier que les données ont été importées :

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Cette requête renvoie un maximum de 10 tweets contenant le mot **Azure** dans le corps du message.

    > [!NOTE]
    > Si vous avez modifié le filtre dans le script `gettweets.py`, remplacez **Azure** avec l’un des filtres utilisés.

## <a name="next-steps"></a>étapes suivantes

Vous avez appris à transformer un jeu de données JSON non structuré en une table Hive structurée. Pour en savoir plus sur Hive avec HDInsight, consultez les documents suivants :

* [Prise en main de HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analyse des données sur les retards de vol avec HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
