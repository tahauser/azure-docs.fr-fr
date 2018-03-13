---
title: Utiliser C# avec Hive et Pig sur Hadoop dans HDInsight - Azure | Microsoft Docs
description: "Découvrez comment utiliser des fonctions définies par l’utilisateur C# avec la diffusion en continu Hive et Pig dans Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 8455a45f1c39c5cb04f742d57a16a81776f850e0
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Utilisation des fonctions définies par l’utilisateur C# avec la diffusion en continu Hive et Pig dans HDInsight

Découvrez comment utiliser des fonctions définies par l’utilisateur C# avec Apache Hive et Pig sur HDInsight.

> [!IMPORTANT]
> Les étapes décrites dans ce document fonctionnent uniquement avec les clusters HDInsight Windows et Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Contrôle de version des composants HDInsight](../hdinsight-component-versioning.md).

Hive et Pig permettent de transmettre des données vers des applications externes pour le traitement. Ce processus est appelé _diffusion en continu (streaming)_. Lorsque vous utilisez une application .NET, les données sont transmises à l’application sur STDIN, et l’application renvoie les résultats sur STDOUT. Pour lire et écrire à partir de STDIN et STDOUT, vous pouvez utiliser `Console.ReadLine()` et `Console.WriteLine()` à partir d’une application console.

## <a name="prerequisites"></a>configuration requise

* Des connaissances en écriture et en génération de code C# qui cible .NET Framework 4.5.

    * Utilisez n’importe quel IDE souhaité. Nous vous conseillons d’utiliser [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017 ou [Visual Studio Code](https://code.visualstudio.com/). Dans le cadre de ce document, Visual Studio 2017 a été utilisé.

* Permet de télécharger les fichiers .exe dans le cluster et d’exécuter des tâches Pig et Hive. Nous vous recommandons d’utiliser Data Lake Tools pour Visual Studio, Azure PowerShell et Azure CLI. La procédure décrite dans ce document utilise les outils Data Lake pour Visual Studio pour charger les fichiers et exécuter l’exemple de requête Hive.

    Pour plus d’informations sur les autres méthodes d’exécution des requêtes Hive et des tâches Pig, consultez les documents suivants :

    * [Utilisation d’Apache Hive avec HDInsight](hdinsight-use-hive.md)

    * [Utilisation d’Apache Pig avec HDInsight](hdinsight-use-pig.md)

* Un cluster Hadoop sur HDInsight. Pour plus d’informations sur la création d’un cluster, consultez [Créer un cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET sur HDInsight

* Clusters __HDInsight sous Linux__ avec [Mono (https://mono-project.com)](https://mono-project.com) pour exécuter des applications .NET. La version 4.2.1 de Mono est incluse dans la version 3.6 de HDInsight.

    Pour plus d’informations sur la compatibilité Mono avec les versions de .NET Framework, consultez [Compatibilité Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

    Pour utiliser une version particulière de Mono, consultez le document [Installation ou mise à jour de Mono](../hdinsight-hadoop-install-mono.md).

* Les clusters __HDInsight sous Windows__ utilisent le CLR Microsoft .NET pour exécuter des applications .NET.

Pour plus d’informations sur la version de .NET Framework et Mono fournie avec les versions de HDInsight, consultez [Versions des composants HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Création des projets C\#

### <a name="hive-udf"></a>UDF Hive

1. Ouvrez Visual Studio et créez une solution. Pour le type de projet, sélectionnez **Application console (.NET Framework)**, puis nommez le nouveau projet **HiveCSharp**.

    > [!IMPORTANT]
    > Si vous utilisez un cluster HDInsight sous Linux, sélectionnez __.NET Framework 4.5__. Pour plus d’informations sur la compatibilité Mono avec les versions de .NET Framework, consultez [Compatibilité Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

2. Remplacez le contenu de **Program.cs** par le code suivant :

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. Créez le projet.

### <a name="pig-udf"></a>UDF Pig

1. Ouvrez Visual Studio et créez une solution. Pour le type de projet, sélectionnez **Application console**, puis nommez le nouveau projet **PigUDF**.

2. Remplacez le contenu du fichier **Program.cs** par le code suivant :

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Ce code analyse les lignes envoyées à partir de Pig et reformate les lignes qui commencent par `java.lang.Exception`.

3. Enregistrez **Program.cs**, puis générez le projet.

## <a name="upload-to-storage"></a>Téléchargement vers le stockage

1. Dans Visual Studio, ouvrez l' **Explorateur de serveurs**.

2. Développez **Azure**, puis **HDInsight**.

3. Si vous y êtes invité, entrez les informations d’identification de votre abonnement Azure, puis cliquez sur **Se connecter**.

4. Développez le cluster HDInsight sur lequel vous souhaitez déployer cette application. Une entrée avec le texte __(compte de stockage par défaut)__ est répertoriée.

    ![Explorateur de serveurs affichant le compte de stockage pour le cluster](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Si cette entrée peut être développée, vous utilisez un __compte de stockage Azure__ en tant que stockage par défaut pour le cluster. Pour afficher les fichiers sur le stockage par défaut pour le cluster, développez l’entrée et double-cliquez sur le __(conteneur par défaut)__.

    * Si cette entrée ne peut pas être développée, vous utilisez __Azure Data Lake Store__ en tant que stockage par défaut pour le cluster. Pour afficher les fichiers sur le stockage par défaut pour le cluster, double-cliquez sur l’entrée __(compte de stockage par défaut)__.

6. Pour charger les fichiers .exe, appliquez l’une des méthodes suivantes :

    * Si vous utilisez un __compte de stockage Azure__, cliquez sur l’icône de téléchargement, puis accédez au dossier **bin\debug** pour le projet **HiveCSharp**. Enfin, sélectionnez le fichier **HiveCSharp.exe** et cliquez sur **Ok**.

        ![icône télécharger](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Si vous utilisez __Azure Data Lake Store__, faites un clic droit sur une zone vide de la liste des fichiers, puis sélectionnez __Charger__. Enfin, sélectionnez le fichier **HiveCSharp.exe** et cliquez sur **Ouvrir**.

    Une fois le chargement de __HiveCSharp.exe__ terminé, répétez le processus de chargement pour le fichier __PigUDF.exe__.

## <a name="run-a-hive-query"></a>Exécution d'une tâche Hive

1. Dans Visual Studio, ouvrez l' **Explorateur de serveurs**.

2. Développez **Azure**, puis **HDInsight**.

3. Cliquez avec le bouton droit sur le cluster dans lequel vous avez déployé l’application **HiveCSharp**, puis sélectionnez **Écrire une requête Hive**.

4. Pour la requête Hive, utilisez le texte suivant :

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Store
    -- add file adl:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Supprimez les commentaires de l’instruction `add file` qui correspond au type de stockage par défaut utilisé pour votre cluster.

    Cette requête permet de sélectionner les champs `clientid`, `devicemake` et `devicemodel` dans `hivesampletable`, puis de les transmettre à l’application HiveCSharp.exe. La requête s’attend à ce que l’application renvoie les trois champs, qui sont stockés en tant que `clientid`, `phoneLabel` et `phoneHash`. Elle s’attend également à trouver HiveCSharp.exe à la racine du conteneur de stockage par défaut.

5. Cliquez sur **Envoyer** pour envoyer la tâche au cluster HDInsight. La fenêtre **Résumé de la tâche Hive** s’ouvre.

6. Cliquez sur **Actualiser** pour actualiser le résumé jusqu’à ce que **État du travail** soit défini sur **Terminé**. Pour afficher le résultat de la tâche, cliquez sur **Sortie de la tâche**.

## <a name="run-a-pig-job"></a>Exécution d’une tâche Pig

1. Utilisez l’une des méthodes suivantes pour vous connecter à votre cluster HDInsight :

    * Si vous utilisez un cluster HDInsight __sous Linux__, utilisez SSH. Par exemple : `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Pour plus d’informations, consultez la rubrique [Utilisation de SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * Si vous utilisez un cluster HDInsight __sous Windows__, [utilisez le Bureau à distance pour vous connecter au cluster.](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)

2. Utilisez la commande suivante pour lancer la ligne de commande Pig :

        pig

    > [!IMPORTANT]
    > Si vous utilisez un cluster sous Windows, utilisez plutôt les commandes suivantes :
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    Une invite `grunt>` s’affiche.

3. Entrez la commande suivante pour exécuter une tâche Pig qui utilise l’application .NET Framework :

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    L’instruction `DEFINE` crée l’alias `streamer` pour les applications pigudf.exe et `CACHE` le charge à partir du stockage par défaut pour le cluster. Plus tard, `streamer` est utilisé avec l’opérateur `STREAM` pour traiter les lignes uniques contenues dans le journal et renvoyer les données sous forme de colonnes.

    > [!NOTE]
    > Le nom d’application utilisé pour la diffusion en continu doit être entouré du caractère \` (accent grave) s’il s’agit d’un alias ou du caractère ’ (apostrophe) en cas d’utilisation avec `SHIP`.

4. Après avoir entré la dernière ligne, la tâche doit démarrer. Elle retourne un résultat semblable au texte suivant :

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à utiliser une application .NET Framework à partir de Hive et Pig sur HDInsight. Si vous souhaitez apprendre à utiliser Python avec Hive et Pig, consultez la page [Utilisation de Python avec Hive et Pig dans HDInsight](python-udf-hdinsight.md).

Pour connaître d’autres façons d’utiliser Pig et Hive et pour en savoir plus sur l’utilisation de MapReduce, consultez les documents suivants :

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
