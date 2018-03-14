---
title: "Génération de recommandations à l’aide de Mahout HDInsight à partir de PowerShell - Azure | Microsoft Docs"
description: "Apprenez à utiliser la bibliothèque à apprentissage automatique Apache Mahout pour générer des recommandations de films avec HDInsight (Hadoop) à partir d’un script PowerShell exécuté sur votre client."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: larryfr
ms.openlocfilehash: a2477b0a7c9c76f8ce4a183f4d699ddf4c291023
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>Génération de recommandations de films à l’aide d’Apache Mahout avec Hadoop dans HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Apprenez à utiliser la bibliothèque à apprentissage automatique [Apache Mahout](http://mahout.apache.org) avec Azure HDInsight pour générer des recommandations de films L’exemple de ce document utilise Azure PowerShell pour exécuter des tâches Mahout.

## <a name="prerequisites"></a>Prérequis

* Un cluster HDInsight sous Linux Pour plus d’informations sur la création de ce dernier, consultez [Prise en main de Hadoop sous Linux dans HDInsight][getstarted].

    > [!IMPORTANT]
    > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Génération de recommandations avec Azure PowerShell

> [!WARNING]
> La tâche de cette section fonctionne avec Azure PowerShell. Nombre des classes fournies avec Mahout ne fonctionnent pas actuellement avec Azure PowerShell. Pour obtenir une liste des classes qui ne fonctionnent pas avec Azure PowerShell, consultez la section [Résolution des problèmes](#troubleshooting).
>
> Pour obtenir un exemple d’utilisation de SSH pour se connecter à HDInsight et exécuter des exemples de Mahout directement sur le cluster, consultez [Génération de recommandations de films en utilisant Mahout et HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

L’une des fonctions fournies par Mahout est un moteur de recommandation. Ce moteur accepte les données au format `userID`, `itemId` et `prefValue` (les préférences utilisateur pour l’élément). Mahout utilise les données pour déterminer les utilisateurs avec des préférences similaires, qui peuvent alors être utilisées pour faire des recommandations.

L’exemple suivant est une présentation simplifiée du fonctionnement du processus de recommandation :

* **Co-occurrence** : Jean, Alice et Robert ont tous aimé *La Guerre des étoiles*, *L’Empire contre-attaque* et *Le Retour du Jedi*. Mahout détermine que les utilisateurs qui aiment l’un de ces films aiment également les deux autres.

* **Co-occurrence** : Robert et Alice ont aussi aimé *La Menace fantôme*, *L’attaque des clones* et *La revanche des Sith*. Mahout détermine que les utilisateurs qui ont aimé les trois films précédents aiment également ces films.

* **Recommandation par similarité**: Puisque Jean a aimé les trois premiers films, Mahout regarde les films que d’autres personnes ayant les mêmes goûts que lui ont aimés aussi, mais que Jean n’a pas encore vus (aimés/notés). Dans ce cas, Mahout recommande *La Menace fantôme*, *L’attaque des clones* et *La revanche des Sith*.

### <a name="understanding-the-data"></a>Vue d’ensemble des données

[GroupLens Research][movielens] fournit des données d’évaluation de films dans un format compatible avec Mahout. Ces données sont disponibles sur le stockage par défaut de votre cluster sur `/HdiSamples/HdiSamples/MahoutMovieData`.

Il existe deux fichiers, `moviedb.txt` (informations sur les films,) et `user-ratings.txt`. Le fichier `user-ratings.txt` est utilisé pendant l’analyse. Le fichier `moviedb.txt` est utilisé pour fournir des informations texte conviviales lors de l'affichage des résultats de l'analyse.

Les données contenues dans le fichier user-ratings.txt respectent la structure suivante : `userID`, `movieID`, `userRating` et `timestamp`, ce qui indique la note attribuée par chaque utilisateur à un film. Voici un exemple de ces données :

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Exécution de la tâche

Utilisez le script Windows PowerShell suivant pour exécuter une tâche à l’aide du moteur de recommandation Mahout sur les données de film :

> [!NOTE]
> Ce fichier vous invite à saisir les informations utilisées pour vous connecter à votre cluster HDInsight et exécuter des tâches. L’exécution des tâches et le téléchargement du fichier output.txt peut prendre plusieurs minutes.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]
> Les tâches Mahout ne suppriment pas les données temporaires créées lors du traitement de la tâche. C’est la raison pour laquelle le paramètre `--tempDir` est spécifié dans la tâche donnée en exemple : il isole les fichiers temporaires dans un répertoire spécifique.

La tâche Mahout ne retourne pas la sortie dans STDOUT. Au lieu de cela, elle est stockée dans le répertoire de sortie spécifié en tant que **part-r-00000**. Le script télécharge ce fichier dans **output.txt** , dans le répertoire actif sur votre station de travail.

Voici un exemple de contenu de ce fichier :

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La première colonne est `userID`. Les valeurs contenues entre  « [ » et « ] » sont `movieId`:`recommendationScore`.

Le script télécharge aussi les fichiers `moviedb.txt` et `user-ratings.txt`, qui sont nécessaires pour mettre en forme le résultat et le rendre plus lisible.

### <a name="view-the-output"></a>Affichage du résultat

Bien que le résultat généré puisse être utilisé dans une application, il n’est pas convivial. Le fichier `moviedb.txt` provenant du serveur peut être utilisé pour convertir le `movieId` en nom de film. Utilisez le script PowerShell suivant pour afficher les recommandations avec les noms de films :

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Utilisez la commande suivante pour afficher les recommandations dans un format convivial : 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

Le résultat ressemble au texte suivant :

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="cannot-overwrite-files"></a>Remplacement de fichiers impossible

Les tâches Mahout ne suppriment pas les fichiers temporaires créés lors du traitement. En outre, les tâches ne remplaceront pas les fichiers de sortie existants.

Pour éviter les erreurs lors de l’exécution des tâches Mahout, supprimez les fichiers temporaires et de sortie entre les exécutions. Utilisez le script PowerShell suivant pour supprimer les fichiers créés par les scripts précédents dans ce document :

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Classes ne fonctionnant pas avec Azure PowerShell

Les tâches Mahout qui utilisent les classes suivantes renvoient divers messages d’erreur lorsqu’elles sont utilisées à partir de Windows PowerShell :

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Pour exécuter des tâches utilisant ces classes, connectez-vous au cluster HDInsight en utilisant le SSH et exécutez les tâches à partir de la ligne de commande. Pour obtenir un exemple d’utilisation de SSH pour exécuter des tâches Mahout, consultez [Génération de recommandations de films en utilisant Mahout et HDInsight (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser Mahout, découvrez d’autres façons d’utiliser les données dans HDInsight :

* [Hive avec HDInsight](hadoop/hdinsight-use-hive.md)
* [Pig avec HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce avec HDInsight](hadoop/hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
