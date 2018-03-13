---
title: Utilisation de C# avec MapReduce sur Hadoop dans HDInsight, Azure | Microsoft Docs
description: "Découvrez comment utiliser C# pour créer des solutions MapReduce avec Hadoop dans Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.custom: hdinsightactive
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 0db3df1666a56ca96af208a4e43f814105de40d5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="use-c-with-mapreduce-streaming-on-hadoop-in-hdinsight"></a>Utilisation de C# avec Diffusion en continu MapReduce sur Hadoop dans HDInsight

Découvrez comment utiliser C# pour créer une solution MapReduce dans HDInsight.

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Contrôle de version des composants HDInsight](../hdinsight-component-versioning.md).

Diffusion en continu Hadoop est un utilitaire qui vous permet d’exécuter des tâches MapReduce à l’aide d’un script ou d’un exécutable. Dans cet exemple, .NET est utilisé pour implémenter le mappeur et le raccord de réduction pour une solution de comptage de mots.

## <a name="net-on-hdinsight"></a>.NET sur HDInsight

Les clusters __HDInsight sous Linux__ utilisent [Mono (https://mono-project.com)](https://mono-project.com) pour exécuter des applications .NET. La version 4.2.1 de Mono est incluse dans la version 3.6 de HDInsight. Pour plus d’informations sur la version de Mono fournie avec HDInsight, consultez [Versions des composants HDInsight](../hdinsight-component-versioning.md). Pour utiliser une version particulière de Mono, consultez le document [Installation ou mise à jour de Mono](../hdinsight-hadoop-install-mono.md).

Pour plus d’informations sur la compatibilité Mono avec les versions de .NET Framework, consultez [Compatibilité Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Fonctionnement de la diffusion en continu Hadoop

Le processus de base utilisé pour la diffusion en continu dans ce document est la suivante :

1. Hadoop transmet des données vers le mappeur (mapper.exe dans cet exemple) sur STDIN.
2. Le mappeur traite les données et émet des paires clé/valeur séparées par des tabulations sur STDOUT.
3. La sortie est lue par Hadoop, puis transmise au raccord de réduction (reducer.exe dans cet exemple) sur STDIN.
4. Le raccord de réduction lit les paires clé/valeur séparées par des tabulations, traite les données, puis émet le résultat sous forme de paires clé/valeur séparées par des tabulations sur STDOUT.
5. La sortie est lue par Hadoop et écrite dans le répertoire de sortie.

Pour plus d’informations sur la diffusion en continu, consultez [Diffusion en continu Hadoop (https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>configuration requise

* Des connaissances en écriture et en génération de code C# qui cible .NET Framework 4.5. Dans le cadre de ce document, Visual Studio 2017 a été utilisé.

* Permet de charger des fichiers .exe dans le cluster. Les étapes de ce document utilisent Data Lake Tools pour Visual Studio pour télécharger les fichiers vers le stockage principal pour le cluster.

* Azure PowerShell ou un client SSH.

* Un cluster Hadoop sur HDInsight. Pour plus d’informations sur la création d’un cluster, consultez [Créer un cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Créer le mappeur

Dans Visual Studio, créez une nouvelle __Application console__ nommée __mappeur__. Utilisez le code suivant pour l’application :

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Après avoir créé l’application, générez-la pour produire le fichier `/bin/Debug/mapper.exe` dans le répertoire du projet.

## <a name="create-the-reducer"></a>Créer le raccord de réduction

Dans Visual Studio, créez une nouvelle __Application console__ nommée __raccord de réduction__. Utilisez le code suivant pour l’application :

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Après avoir créé l’application, générez-la pour produire le fichier `/bin/Debug/reducer.exe` dans le répertoire du projet.

## <a name="upload-to-storage"></a>Téléchargement vers le stockage

1. Dans Visual Studio, ouvrez l' **Explorateur de serveurs**.

2. Développez **Azure**, puis **HDInsight**.

3. Si vous y êtes invité, entrez les informations d’identification de votre abonnement Azure, puis cliquez sur **Se connecter**.

4. Développez le cluster HDInsight sur lequel vous souhaitez déployer cette application. Une entrée avec le texte __(compte de stockage par défaut)__ est répertoriée.

    ![Explorateur de serveurs affichant le compte de stockage pour le cluster](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Si cette entrée peut être développée, vous utilisez un __compte de stockage Azure__ en tant que stockage par défaut pour le cluster. Pour afficher les fichiers sur le stockage par défaut pour le cluster, développez l’entrée et double-cliquez sur le __(conteneur par défaut)__.

    * Si cette entrée ne peut pas être développée, vous utilisez __Azure Data Lake Store__ en tant que stockage par défaut pour le cluster. Pour afficher les fichiers sur le stockage par défaut pour le cluster, double-cliquez sur l’entrée __(compte de stockage par défaut)__.

5. Pour charger les fichiers .exe, appliquez l’une des méthodes suivantes :

    * Si vous utilisez un __compte de stockage Azure__, cliquez sur l’icône de chargement, puis accédez au dossier **bin\debug** pour le projet **Mappeur**. Enfin, sélectionnez le fichier **mapper.exe** et cliquez sur **OK**.

        ![icône télécharger](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
    * Si vous utilisez __Azure Data Lake Store__, faites un clic droit sur une zone vide de la liste des fichiers, puis sélectionnez __Charger__. Enfin, sélectionnez le fichier **mapper.exe** et cliquez sur **Ouvrir**.

    Une fois que le chargement de __mapper.exe__ est terminé, répétez le processus de chargement pour le fichier __reducer.exe__.

## <a name="run-a-job-using-an-ssh-session"></a>Exécution d’une tâche : avec une session SSH

1. Utilisez SSH pour vous connecter au cluster HDInsight. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Exécutez une des commandes suivantes pour démarrer la tâche MapReduce :

    * Si vous utilisez __Data Lake Store__ en tant que stockage par défaut :

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```
    
    * Si vous utilisez __Stockage Azure__ en tant que stockage par défaut :

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```

    La liste suivante décrit la signification de chaque paramètre :

    * `hadoop-streaming.jar` : le fichier jar contenant la fonctionnalité MapReduce de diffusion en continu.
    * `-files` : ajoute les fichiers `mapper.exe` et `reducer.exe` à cette tâche. `adl:///` ou `wasb:///` devant chaque fichier est le chemin d’accès à la racine du stockage par défaut pour le cluster.
    * `-mapper` : indique le fichier qui implémente le mappeur.
    * `-reducer` : indique le fichier qui implémente le raccord de réduction.
    * `-input` : les données d’entrée.
    * `-output` : le répertoire de sortie.

3. Une fois la tâche MapReduce terminée, utilisez la commande suivante pour afficher les résultats :

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Le texte suivant est un exemple de données renvoyées par cette commande :

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Exécution d’une tâche : avec PowerShell

Utilisez le script PowerShell suivant pour exécuter une tâche MapReduce et télécharger les résultats.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Ce script vous invite à entrer le nom et le mot de passe du compte de connexion du cluster, ainsi que le nom du cluster HDInsight. Une fois que la tâche est terminée, la sortie est téléchargée dans un fichier nommé `output.txt`. Le texte suivant constitue un exemple des données contenues dans le fichier `output.txt` :

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de MapReduce avec HDInsight, consultez [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md).

Pour plus d’informations sur l’utilisation de C# avec Hive et Pig, consultez [Utilisation d’une fonction définie par l’utilisateur C# avec Hive et Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Pour plus d’informations sur l’utilisation de C# avec Storm sur HDInsight, consultez [Développement de topologies C# pour Storm sur HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).