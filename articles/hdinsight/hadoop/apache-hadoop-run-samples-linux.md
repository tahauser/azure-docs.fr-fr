---
title: "Exécuter des exemples MapReduce Hadoop dans HDInsight - Azure | Documents Microsoft"
description: "Bien démarrer avec des exemples MapReduce dans des fichiers jar inclus dans HDInsight. Utilisez le protocole SSH pour vous connecter au cluster, puis utilisez la commande Hadoop pour exécuter des exemples de travaux."
keywords: exemple de fichier jar Hadoop, exemples de fichiers jar hadoop, exemples mapreduce hadoop, exemples mapreduce
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1d2a0b9-1659-4fab-921e-4a8990cbb30a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: 1882412236e630322d52cb1875060798fef6c017
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Exécuter les exemples MapReduce inclus dans HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Découvrez comment exécuter les exemples MapReduce inclus à Hadoop dans HDInsight.

## <a name="prerequisites"></a>configuration requise

* **Un cluster HDInsight** : consultez la rubrique [Bien démarrer avec Hadoop avec Hive dans HDInsight sur Linux](apache-hadoop-linux-tutorial-get-started.md)

    > [!IMPORTANT]
    > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Client SSH** : pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS XH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Exemples MapReduce

**Emplacement** : les exemples se trouvent sur le cluster HDInsight à l’emplacement `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Contenu**: les exemples suivants sont contenus dans cette archive :

* `aggregatewordcount` : un programme mapreduce basé sur une agrégation qui compte les mots contenus dans les fichiers d’entrée.
* `aggregatewordhist` : un programme mapreduce basé sur une agrégation qui calcule l’histogramme des mots contenus dans les fichiers d’entrée.
* `bbp`: Un programme mapreduce qui utilise Bailey-Borwein-Plouffe pour calculer les chiffres exacts de Pi.
* `dbcount` : un exemple de travail qui compte les journaux d’affichage de pages stockés dans une base de données.
* `distbbp` : un programme mapreduce qui utilise une formule de type BBP pour calculer le nombre exact de bits de Pi.
* `grep` : un programme mapreduce qui compte les correspondances regex dans l’entrée.
* `join` : un travail qui effectue une jonction sur des jeux de données triés, à partition égale.
* `multifilewc` : un travail qui compte les mots de plusieurs fichiers.
* `pentomino` : un programme mapreduce de disposition de vignettes permettant de trouver des solutions aux problèmes posés par les pentominos.
* `pi` : un programme mapreduce qui estime la valeur de Pi à l’aide de la méthode quasi Monte Carlo.
* `randomtextwriter` : un programme mapreduce qui écrit 10 Go de données textuelles aléatoires par nœud.
* `randomwriter` : un programme mapreduce qui écrit 10 Go de données aléatoires par nœud.
* `secondarysort` : un exemple définissant un tri secondaire lors de la phase de réduction.
* `sort` : un programme mapreduce qui trie les données écrites par l’enregistreur aléatoire.
* `sudoku` : un solveur de sudoku.
* `teragen` : générateur de données pour le programme terasort.
* `terasort` : exécution du terasort.
* `teravalidate` : vérification des résultats du programme terasort.
* `wordcount` : un programme mapreduce qui compte les mots contenus dans les fichiers d’entrée.
* `wordmean` : un programme mapreduce qui compte la longueur moyenne des mots contenus dans les fichiers d’entrée.
* `wordmedian` : un programme mapreduce qui compte la longueur moyenne des mots contenus dans les fichiers d’entrée.
* `wordstandarddeviation` : un programme mapreduce qui compte l’écart type de la longueur des mots contenus dans les fichiers d’entrée.

**Code source** : le code source de ces exemples est inclus dans le cluster HDInsight à l’emplacement `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

## <a name="run-the-wordcount-example"></a>Exécuter l’exemple wordcount

1. Connectez-vous à HDInsight à l’aide de SSH. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. À l’invite `username@#######:~$` , utilisez la commande suivante pour répertorier les exemples :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Cette commande génère la liste des exemples de la section précédente de ce document.

3. Utilisez la commande suivante pour obtenir de l’aide sur un exemple spécifique. Dans ce cas, l’exemple **wordcount** :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Le message suivant s’affiche :

        Usage: wordcount <in> [<in>...] <out>

    Ce message indique que vous pouvez fournir plusieurs chemins d’accès d’entrée pour les documents sources. Le chemin d’accès final correspond à l’emplacement de stockage de la sortie (nombre de mots dans les documents sources).

4. Pour compter tous les mots figurant dans les carnets de Léonard De Vinci qui sont fournis comme exemples de données avec votre cluster, utilisez les éléments suivants :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    L’entrée de ce travail est lue à partir de `/example/data/gutenberg/davinci.txt`. La sortie de cet exemple est stockée dans `/example/data/davinciwordcount`. Les deux chemins d’accès sont situés sur le stockage par défaut du cluster, et non du système de fichiers local.

   > [!NOTE]
   > Comme indiqué dans l’aide de l’exemple wordcount, vous pouvez également spécifier plusieurs fichiers d’entrée. Par exemple, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` compte les mots figurant dans les fichiers davinci.txt et ulysses.txt.

5. Une fois la tâche terminée, utilisez la commande suivante pour afficher le résultat généré :

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Cette commande concatène tous les fichiers de sortie générés par le travail. Elle affiche la sortie dans la console. Le résultat ressemble au texte suivant :

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Chaque ligne représente un mot et le nombre d’occurrences dans les données d’entrée.

## <a name="the-sudoku-example"></a>L’exemple de Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) est un puzzle logique composé de 3 x 3 grilles. Certaines cellules de la grille comportent des numéros, tandis que d’autres sont vides, l’objectif consiste à résoudre les cellules vides. Le lien précédent comporte davantage d’informations sur le casse-tête, mais l’objectif de cet exemple consiste à résoudre les cellules vides. Par conséquent, notre entrée doit être un fichier au format suivant :

* Neuf lignes de neuf colonnes
* Chaque colonne peut contenir un nombre ou `?` (qui indique une cellule vide)
* Les cellules sont séparées par un espace

La façon de construire des puzzles Sudoku est particulière dans la mesure où vous ne pouvez pas répéter un nombre dans une colonne ou une ligne. Il existe un exemple sur le cluster HDInsight qui est construit correctement. Il se trouve dans `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` et contient le texte suivant :

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Pour exécuter cet exemple de problème dans l’exemple de Sudoku, utilisez la commande suivante :

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Les résultats sont les suivants :

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Exemple de Pi (π)

L’exemple de Pi utilise une méthode statistique (quasi-Monte-Carlo) pour estimer la valeur de Pi. Les points sont placés de manière aléatoire dans un carré unitaire. Le carré contient également un cercle. La probabilité que les points tombent dans le cercle est égale à l’aire du cercle, pi/4. La valeur de pi peut être estimée à partir de la valeur de 4R. R est le rapport entre le nombre de points situés à l’intérieur du cercle et le nombre total de points situés à l’intérieur du carré. Plus l'échantillon de points est grand, plus l'estimation est précise.

Utilisez la commande suivante pour exécuter cet exemple : Cette commande utilise 16 mappages, avec pour chacun 10 000 000 exemples, pour estimer la valeur de Pi :

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

La valeur retournée par cette commande doit être semblable à **3,14159155000000000000**. Pour référence, voici les 10 chiffres de Pi après la virgule : 3,1415926535.

## <a name="10-gb-greysort-example"></a>Exemple de Greysort de 10 Go

GraySort est un tri de benchmark. La mesure est le taux de tri (To/minute) obtenu lors du tri de très grandes quantités de données, en général au minimum 100 To.

Cet exemple utilise seulement 10 Go de données afin de pouvoir être exécuté relativement rapidement. Il utilise les applications MapReduce développées par Owen O'Malley et Arun Murthy. Ces applications ont remporté en 2009 le benchmark du tri de téraoctets (« daytona ») annuel universel général avec un taux de 0,578 To/min (100 To en 173 minutes). Pour plus d'informations à ce sujet et sur d'autres benchmarks de tri, consultez le site [Sortbenchmark](http://sortbenchmark.org/) .

Cet exemple utilise trois ensembles de programmes MapReduce :

* **TeraGen**: programme MapReduce qui génère des lignes de données à trier

* **TeraSort**: échantillonne les données d'entrée et utilise MapReduce pour trier les données en une commande totale.

    TeraSort est un tri MapReduce standard, à l’exception d’un partitionneur personnalisé. Le partitionneur utilise une liste triée des clés N-1 échantillonnée qui définissent la plage de clés pour chaque réduction. Plus particulièrement, toutes les clés semblables à cet échantillon [i-1] <= key < sample[i] sont envoyées pour réduire i. Le partitionneur garantit que les sorties de réduction i sont toutes inférieures aux sorties de réduction i+1.

* **TeraValidate**: programme MapReduce qui valide le tri global de la sortie

    Il crée un mappage par fichier dans le répertoire de sortie et chaque mappage assure que chaque clé est inférieure ou égale à la précédente. La fonction de mappage génère des enregistrements de la première et de la dernière clés de chaque fichier. La fonction de réduction veille à ce que la première clé du fichier i soit supérieure à la dernière clé du fichier i-1. Un problème est signalé comme une sortie de la phase de réduction avec les clés dans le désordre.

Utilisez les étapes suivantes pour générer des données, trier, puis valider la sortie :

1. Générez 10 Go de données, qui sont stockées dans le stockage par défaut du cluster HDInsight à l’emplacement `/example/data/10GB-sort-input` :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks` indique à Hadoop le nombre de tâches de mappage à utiliser pour cette tâche. Les deux derniers paramètres demandent au travail de créer 10 Go de données et de les stocker sous `/example/data/10GB-sort-input`.

2. Exécutez la commande suivante pour trier les données :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks` indique à Hadoop le nombre de tâches de réduction à utiliser pour cette tâche. Les deux derniers paramètres sont simplement les emplacements d’entrée et de sortie des données.

3. Pour valider les données générées par le tri, utilisez les éléments suivants :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à exécuter les exemples inclus avec les clusters HDInsight sous Linux. Pour des didacticiels sur l’utilisation de Pig, Hive et MapReduce avec HDInsight, consultez les rubriques suivantes :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:apache-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

