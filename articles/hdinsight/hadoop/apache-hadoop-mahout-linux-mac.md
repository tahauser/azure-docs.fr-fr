---
title: "Générer des recommandations à l’aide de Mahout et HDInsight (SSH) - Azure | Documents Microsoft"
description: "Apprenez à utiliser la bibliothèque à apprentissage automatique Apache Mahout pour générer des recommandations de films avec HDInsight (Hadoop)."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c78ec37c-9a8c-4bb6-9e38-0bdb9e89fbd7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: larryfr
ms.openlocfilehash: 4674008523704b9b1fb86de8606641f6140e2910
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight-ssh"></a>Génération de recommandations de films à l’aide d’Apache Mahout avec Hadoop sous Linux dans HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Apprenez à utiliser la bibliothèque à apprentissage automatique [Apache Mahout](http://mahout.apache.org) avec Azure HDInsight pour générer des recommandations de films

Mahout est une bibliothèque [Machine Learning][ml] pour Apache Hadoop. Mahout contient des algorithmes pour le traitement des données, tel que le filtrage, la classification et le clustering. Dans cet article, vous utilisez un moteur de recommandation pour générer des recommandations en fonction des films vus par vos amis.

## <a name="prerequisites"></a>Prérequis

* Un cluster HDInsight sous Linux Pour plus d’informations sur la création de ce dernier, consultez [Prise en main de Hadoop sous Linux dans HDInsight][getstarted].

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un client SSH. Pour plus d’informations, consultez le document [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="mahout-versioning"></a>Contrôle de version Mahout

Pour plus d'informations sur la version de Mahout dans HDInsight, consultez [Versions de HDInsight et composants Hadoop](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Présentation des recommandations

L’une des fonctions fournies par Mahout est un moteur de recommandation. Ce moteur accepte les données au format `userID`, `itemId` et `prefValue` (les préférences pour l’élément). Mahout peut alors effectuer une analyse des co-occurrences afin de déterminer que *les utilisateurs ayant une préférence pour un élément ont également une préférence pour ces autres éléments*. Mahout détermine ensuite des utilisateurs avec des préférences similaires, qui peuvent alors être utilisées pour faire des recommandations.

Voici un exemple simplifié de cette méthode pour les données de films :

* **Co-occurrence** : Jean, Alice et Robert ont tous aimé *La Guerre des étoiles*, *L’Empire contre-attaque* et *Le Retour du Jedi*. Mahout détermine que les utilisateurs qui aiment l’un de ces films aiment également les deux autres.

* **Co-occurrence** : Robert et Alice ont aussi aimé *La Menace fantôme*, *L’attaque des clones* et *La revanche des Sith*. Mahout détermine que les utilisateurs qui ont aimé les trois films précédents aiment également ces trois films.

* **Recommandation par similarité**: Puisque Jean a aimé les trois premiers films, Mahout regarde les films que d’autres personnes ayant les mêmes goûts que lui ont aimés aussi, mais que Jean n’a pas encore vus (aimés/notés). Dans ce cas, Mahout recommande *La Menace fantôme*, *L’attaque des clones* et *La revanche des Sith*.

### <a name="understanding-the-data"></a>Vue d’ensemble des données

De façon pratique, [GroupLens Research][movielens] fournit des données d’évaluation de films dans un format compatible avec Mahout. Ces données sont disponibles sur le stockage par défaut de votre cluster à `/HdiSamples/HdiSamples/MahoutMovieData`.

Il existe deux fichiers, `moviedb.txt` et `user-ratings.txt`. Le fichier `user-ratings.txt` est utilisé pendant l’analyse. Le fichier `moviedb.txt` est utilisé pour fournir des informations de texte conviviales au moment de l’affichage des résultats.

Les données contenues dans le fichier user-ratings.txt respectent la structure suivante : `userID`, `movieID`, `userRating` et `timestamp`, ce qui indique la note attribuée par chaque utilisateur à un film. Voici un exemple de ces données :

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Exécution de l'analyse

À partir d’une connexion SSH avec le cluster, utilisez la commande suivante pour exécuter la tâche de recommandation :

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]
> L’exécution de la tâche peut durer quelques minutes et entraîner l’exécution de plusieurs tâches MapReduce.

## <a name="view-the-output"></a>Affichage du résultat

1. Une fois la tâche terminée, utilisez la commande suivante pour afficher le résultat généré :

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    La sortie se présente comme suit :

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    La première colonne est `userID`. Les valeurs contenues entre  « [ » et « ] » sont `movieId`:`recommendationScore`.

2. Vous pouvez utiliser la sortie ainsi que le fichier moviedb.txt pour fournir plus d’informations sur les recommandations. Tout d’abord, copiez les fichiers localement en utilisant les commandes suivantes :

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Cette commande copie les données de sortie dans un fichier nommé **recommendations.txt** dans le répertoire actif, avec les fichiers de données de film.

3. Utilisez la commande suivante pour créer un script Python afin de rechercher les noms de film présents dans les données de sortie des recommandations :

    ```bash
    nano show_recommendations.py
    ```

    Lorsque l’éditeur s’ouvre, utilisez le texte suivant comme contenu du fichier :

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Appuyez sur **Ctrl-X**, **O**, et enfin **Entrée** pour enregistrer les données.

4. Exécutez le script Python. La commande suivante suppose que vous avez ouvert le répertoire dans lequel tous les fichiers ont été téléchargés :

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    La commande examinera les recommandations générées pour l’utilisateur ID 4.

    * Le fichier **user-ratings.txt** est utilisé pour récupérer les films évalués.

    * Le fichier **moviedb.txt** est utilisé pour récupérer les noms des films.

    * Le fichier **recommendations.txt** est utilisé pour récupérer les recommandations de films pour cet utilisateur.

     Le résultat de cette commande doit ressembler au texte suivant :

        Seven Years in Tibet (1997), score=5.0   Indiana Jones and the Last Crusade (1989), score=5.0   Jaws (1975), score=5.0   Sense and Sensibility (1995), score=5.0   Independence Day (ID4) (1996), score=5.0   My Best Friend’s Wedding (1997), score=5.0   Jerry Maguire (1996), score=5.0   Scream 2 (1997), score=5.0   Time to Kill, A (1996), score=5.0

## <a name="delete-temporary-data"></a>Suppression des données temporaires

Les tâches Mahout ne suppriment pas les données temporaires créées lors du traitement de la tâche. C’est la raison pour laquelle le paramètre `--tempDir` est spécifié dans la tâche donnée en exemple : il isole les fichiers temporaires dans un chemin spécifique afin de pouvoir les effacer facilement. Pour supprimer les fichiers temporaires, utilisez la commande suivante :

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]
> Si vous souhaitez réexécuter la commande, vous devez également supprimer le répertoire de sortie. Utilisez la commande suivante pour supprimer ce répertoire :
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser Mahout, découvrez d’autres façons d’utiliser les données dans HDInsight :

* [Hive avec HDInsight](hdinsight-use-hive.md)
* [Pig avec HDInsight](hdinsight-use-pig.md)
* [MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
