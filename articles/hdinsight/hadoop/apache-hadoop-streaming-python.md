---
title: "Développer des travaux MapReduce de diffusion en continu Python avec HDInsight - Azure | Documents Microsoft"
description: "Découvrez comment utiliser Python dans des travaux MapReduce de diffusion en continu. Hadoop fournit une API de diffusion en continu pour MapReduce pour l’écriture dans des langages autres que Java."
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2018
ms.author: larryfr
ms.openlocfilehash: 2563927684720a0be1144fa51aea7b9dae4abe7e
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Développer des programmes MapReduce de diffusion en continu Python pour HDInsight

Découvrez comment utiliser Python dans des opérations MapReduce de diffusion en continu. Hadoop fournit une API de diffusion en continu pour MapReduce qui vous permet d'écrire des fonctions de mappage et de réduction dans d'autres langages que Java. Les étapes décrites dans ce document implémentent les composants de mappage et de réduction dans Python.

## <a name="prerequisites"></a>configuration requise

* Un cluster Hadoop Linux sur HDInsight

  > [!IMPORTANT]
  > Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un éditeur de texte

  > [!IMPORTANT]
  > L’éditeur de texte doit utiliser LF comme caractère de fin de ligne. L’utilisation d’une fin de ligne du CRLF génère des erreurs lors de l’exécution de la tâche MapReduce dans les clusters HDInsight sous Linux.

* Les commandes `ssh` et `scp` ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Nombre de mots

Cet exemple illustre un nombre de mots de base dans un mappeur et un réducteur Python. Le mappeur sépare les phrases en mots individuels, et le raccord de réduction rassemble les mots et les nombres pour produire la sortie.

L’organigramme suivant illustre ce qui se passe durant les phases de mappage et de réduction.

![illustration du processus mapreduce](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>Diffusion en continu de MapReduce

Hadoop vous permet de spécifier un fichier qui dispose de la logique de mappage et de réduction utilisée par un travail. Parmi les exigences spécifiques de mappage et de réduction, on retrouve les éléments suivants :

* **Entrée**: les composants de mappage et de réduction doivent lire les données d’entrée depuis STDIN.
* **Sortie**: les composants de mappage et de réduction doivent écrire les données de sortie vers STDOUT.
* **Format de données**: les données consommées et produites doivent représenter une paire clé/valeur, séparée par un caractère de tabulation.

Python peut facilement gérer ces exigences en utilisant le module `sys` pour lire depuis STDIN et utiliser `print` pour imprimer vers STDOUT. Le travail restant consiste à disposer un caractère de tabulation (`\t`) entre la clé et la valeur pour vous permettre d’effectuer, si vous le souhaitez, le formatage de ces données.

## <a name="create-the-mapper-and-reducer"></a>Création du mappeur et du raccord de réduction

1. Créez un fichier nommé `mapper.py` et utilisez le code suivant comme contenu :

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. Créez un fichier nommé **reducer.py** et utilisez le code suivant comme contenu :

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>Exécuter à l’aide de PowerShell

Pour vous assurer que vos fichiers ont le droit de modifier les fins de ligne, utilisez le script PowerShell suivant :

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Utilisez le script PowerShell suivant pour charger les fichiers, exécuter la tâche et afficher le résultat :

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Exécution à partir d’une session SSH

1. À partir de votre environnement de développement, dans le même répertoire que `mapper.py` et `reducer.py`, utilisez la commande suivante :

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Remplacez `username` par le nom d’utilisateur SSH de votre cluster, et remplacez `clustername` par le nom de votre cluster.

    Avec cette commande, les fichiers du système local sont copiés dans le nœud principal.

    > [!NOTE]
    > Si vous utilisez un mot de passe pour sécuriser votre compte SSH, vous êtes invité à le saisir. Si vous utilisez une clé SSH, vous devrez peut-être utiliser le paramètre `-i` et le chemin d'accès à la clé privée. Par exemple : `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Connectez-vous au cluster à l’aide de SSH :

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Pour en savoir plus, consultez [Utilisation de SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Pour garantir que les fichiers mapper.py et reducer.py ont des fins de ligne correctes, utilisez les commandes suivantes :

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Exécutez la commande suivante pour démarrer la tâche MapReduce :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Cette commande dispose des éléments suivants :

   * **hadoop-streaming.jar**: utilisé lors de l’exécution d’opérations de diffusion en contenu MapReduce. Il établit un lien entre Hadoop et le code externe MapReduce que vous fournissez

   * **-files** : ajoute les fichiers spécifiés à la tâche MapReduce.

   * **-mapper**: indique à Hadoop quel fichier doit être utilisé comme mappeur.

   * **-reducer**: indique à Hadoop quel fichier doit être utilisé comme raccord de réduction.

   * **-input**: le fichier d’entrée à partir duquel nous devrions compter les mots.

   * **-output** : le répertoire sur lequel le résultat est écrit.

    La tâche MapReduce fonctionne et le processus s’affiche sous forme de pourcentages.

        15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%


5. Pour afficher la sortie, utilisez la commande suivante :

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Cette commande affiche une liste des mots et le nombre de fois où ils apparaissent.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment utiliser des travaux de diffusion en continu MapReduce avec HDInsight, cliquez sur les liens suivants pour explorer d’autres façons d’utiliser Azure HDInsight.

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
