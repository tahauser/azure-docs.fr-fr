---
title: MapReduce avec Hadoop sur HDInsight | Microsoft Docs
description: "Apprenez à exécuter des tâches MapReduce sur Hadoop dans des clusters HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: bfd176aaf02dd96cee8877f1754dd0ce7ebded13
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Utilisation de MapReduce sur Hadoop sur HDInsight

Apprenez à exécuter des tâches MapReduce sur des clusters HDInsight. Utilisez le tableau suivant pour découvrir les différentes façons d’utiliser MapReduce avec HDInsight :

| **Élément à utiliser...** | **...pour faire cela** | ...avec ce **système d’exploitation cluster** | ...depuis ce **système d’exploitation cluster** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Utilisation de la commande Hadoop via **SSH** |Linux |Linux, Unix, Mac OS X ou Windows |
| [REST](apache-hadoop-use-mapreduce-curl.md) |Envoyer la tâche à distance à l’aide de **REST** (dans les exemples, cURL est utilisé) |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Envoyer la tâche à distance à l'aide de **Windows PowerShell** |Linux ou Windows |Windows |

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>

## <a id="whatis"></a>Qu’est-ce que MapReduce ?

Hadoop MapReduce est une infrastructure logicielle qui permet d'écrire des tâches traitant d'importantes quantités de données. Les données d’entrée sont fractionnées en blocs indépendants. Chaque bloc est traité en parallèle sur les nœuds de votre cluster. Une tâche MapReduce se compose de deux fonctions :

* **Mappeur**: il consomme les données d'entrée, les analyse (généralement avec les opérations de tri et de filtre) et émet des tuples (paires clé-valeur)

* **Raccord de réduction**: il consomme les tuples émis par le Mappeur et effectue une opération de synthèse qui crée un résultat plus petit, combiné à partir des données du Mappeur

Un exemple de tâche MapReduce de comptage de mots de base est illustré dans le diagramme suivant :

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

Le résultat de ce travail indique le nombre d’occurrences de chaque mot dans le texte.

* Le mappeur prend chaque ligne du texte saisi en tant qu'entrée, et la divise en mots. Il émet une paire clé/valeur pour chaque occurrence de mot ou si le mot est suivi d’un 1. Le résultat est trié avant d'être envoyé au raccord de réduction.
* Ce dernier calcule la somme du compte de mots, puis émet une seule paire clé/valeur contenant le mot défini, suivi par la somme de ses occurrences.

MapReduce peut être implémenté dans plusieurs langues. Java est l'implémentation la plus courante. Ce langage est utilisé à titre d’exemple dans ce document.

## <a name="development-languages"></a>Langues de développement

Les langages ou infrastructures basés sur Java et la Machine virtuelle Java peuvent être exécutés directement comme une tâche MapReduce. L’exemple utilisé dans ce document est une application Java MapReduce. Les langages autres que Java, comme C#, Python ou des exécutables autonomes, doivent utiliser la **diffusion en continu Hadoop**.

La diffusion en continu Hadoop communique avec le mappeur et le raccord de réduction via STDIN et STDOUT. Le mappeur et le raccord de réduction lisent les données ligne par ligne depuis STDIN et écrivent la sortie dans STDOUT. Chaque ligne lue ou émise par le mappeur et le raccord de réduction doit être au format d’une paire clé / valeur, délimitée par un caractère de tabulation :

    [key]/t[value]

Pour plus d’informations, consultez [Diffusion en continu Hadoop](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Pour obtenir des exemples d’utilisation de diffusion en continu Hadoop avec HDInsight, consultez les documents suivants :

* [Développement de tâches MapReduce C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Développement de tâches MapReduce Python](apache-hadoop-streaming-python.md)

## <a id="data"></a>Exemple de données

HDInsight propose différents exemples de jeux de données, qui sont stockés dans les répertoires `/example/data` et `/HdiSamples`. Ces répertoires sont disponibles dans le stockage par défaut de votre cluster. Dans ce document, nous utilisons le fichier `/example/data/gutenberg/davinci.txt`. Ce fichier contient les carnets de Léonard De Vinci.

## <a id="job"></a>Exemple MapReduce

Un exemple d’application de comptage de mots MapReduce est inclus dans votre cluster HDInsight. Cet exemple se trouve dans `/example/jars/hadoop-mapreduce-examples.jar` sur le stockage par défaut pour votre cluster.

Le code Java suivant est la source de l’application MapReduce contenue dans le fichier `hadoop-mapreduce-examples.jar` :

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Pour savoir comment écrire vos propres applications MapReduce, consultez les documents suivants :

* [Développement d’applications MapReduce en Java pour HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Développement d’applications MapReduce en Python pour HDInsight](apache-hadoop-streaming-python.md)

## <a id="run"></a>Exécution de la tâche MapReduce

HDInsight peut exécuter des tâches HiveQL de différentes manières. Utilisez la table suivante pour choisir la méthode qui vous convient, puis cliquez sur le lien pour obtenir une présentation détaillée.

| **Élément à utiliser...** | **...pour faire cela** | ...avec ce **système d’exploitation cluster** | ...depuis ce **système d’exploitation cluster** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Utilisation de la commande Hadoop via **SSH** |Linux |Linux, Unix, Mac OS X ou Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Envoyer la tâche à distance à l'aide de **REST** |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Envoyer la tâche à distance à l'aide de **Windows PowerShell** |Linux ou Windows |Windows |

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="nextsteps"></a>Étapes suivantes

Pour savoir comment utiliser les données dans HDInsight, consultez les documents suivants :

* [Développement de programmes MapReduce en Java pour HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Développement de programmes MapReduce de diffusion en continu Python pour HDInsight](apache-hadoop-streaming-python.md)

* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]

* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
