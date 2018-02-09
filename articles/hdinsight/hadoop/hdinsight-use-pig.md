---
title: Utilisation de Hadoop Pig dans HDInsight | Microsoft Docs
description: Utilisation de Pig avec Hadoop sur HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: acfeb52b-4b81-4a7d-af77-3e9908407404
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: larryfr
ms.openlocfilehash: ca68391db99d882887e41005928a30610c7cb930
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>Utilisation de Pig avec Hadoop sur HDInsight

Découvrez comment utiliser [Apache Pig](http://pig.apache.org/) avec HDInsight.

Pig est une plateforme qui permet de créer des programmes pour Hadoop dans un langage procédural appelé *Pig Latin*. Pig est une alternative à Java, dédiée à la création de solutions *MapReduce* , qui est incluse avec Azure HDInsight. Utilisez le tableau suivant pour découvrir les différentes façons dont Pig peut être utilisé avec HDInsight :

| **Utilisez-le** si vous souhaitez... | ... un interpréteur de commandes **interactif** | ... un traitement par **lots** | ...avec ce **système d’exploitation cluster** | ...depuis ce **système d’exploitation cluster** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [API REST](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Kit de développement logiciel (SDK) .NET pour Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux ou Windows |Windows (pour l’instant) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux ou Windows |Windows |

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="why"></a>Pourquoi utiliser Pig ?

Lun des défis de traitement de données à l'aide de MapReduce dans Hadoop consiste à implémenter votre logique de traitement en utilisant uniquement un mappage et une fonction de réduction. Pour un traitement complexe, vous devez souvent interrompre le traitement lors de plusieurs opérations MapReduce qui sont chaînées ensemble pour produire le résultat souhaité.

Pig permet de définir un traitement comme une série de transformations via lesquelles les données circulent pour produire le résultat souhaité.

Le langage Pig Latin vous permet de décrire le flux de données provenant d’une entrée brute, via une ou plusieurs transformations, pour produire le résultat souhaité. Les programmes Pig Latin suivent le modèle général suivant :

* **Chargement**: lecture des données à manipuler dans le système de fichiers

* **Transformation**: manipulation des données

* **Sortie ou stockage**: affichage du résultat à l'écran ou stockage pour traitement

### <a name="user-defined-functions"></a>Fonctions définies par l’utilisateur

Pig Latin prend également en charge les fonctions définies par l'utilisateur (UDF), ce qui vous permet d'appeler des composants externes qui implémentent la logique qui est difficile à modéliser dans Pig Latin.

Pour plus d’informations sur Pig Latin, consultez les pages [Manuel de référence Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) et [Manuel de référence Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Pour obtenir un exemple d'utilisation des fonctions définies par l’utilisateur (UDF) avec Pig, consultez les documents suivants :

* [Utilisation de DataFu avec Pig dans HDInsight](apache-hadoop-use-pig-datafu-udf.md) : DataFu est une collection de fonctions définies par l’utilisateur utiles gérées par Apache
* [Utilisation de Python avec Hive et Pig dans HDInsight](python-udf-hdinsight.md)
* [Utilisation de C# avec Hive et Pig dans HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Exemple de données

HDInsight propose différents exemples de jeux de données, qui sont stockés dans les répertoires `/example/data` et `/HdiSamples`. Ces répertoires sont disponibles dans le stockage par défaut de votre cluster. L’exemple Pig présenté dans ce document utilise le fichier *log4j* à partir de `/example/data/sample.log`.

Chaque journal à l'intérieur du fichier est constitué d'une ligne de champs qui contient un champ `[LOG LEVEL]` pour indiquer le type et la gravité, par exemple :

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Dans l'exemple précédent, le niveau de journal est ERROR.

> [!NOTE]
> Vous pouvez également générer un fichier log4j à l'aide de l’outil de journalisation [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) , puis télécharger ce fichier vers votre objet blob. Pour des instructions, consultez la page [Téléchargement de données vers HDInsight](../hdinsight-upload-data.md) . Pour plus d'informations sur l'utilisation du stockage d'objets blob Azure avec HDInsight, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Exemple de travail

Le travail Pig Latin suivant charge le fichier `sample.log` depuis le stockage par défaut de votre cluster HDInsight. Elle effectue ensuite une série de transformations qui créent un décompte du nombre de fois où chaque niveau du journal s'est produit dans les données d'entrée. Les résultats sont écrits en STDOUT.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

L’image suivante montre un résumé de ce qu’effectue chaque transformation sur les données.

![Représentation graphique des transformations][image-hdi-pig-data-transformation]

## <a id="run"></a>Exécution de la tâche Pig Latin

HDInsight peut exécuter des tâches Pig Latin de différentes façons. Utilisez la table suivante pour choisir la méthode qui vous convient, puis cliquez sur le lien pour obtenir une présentation détaillée.

| **Utilisez-le** si vous souhaitez... | ... un interpréteur de commandes **interactif** | ... un traitement par **lots** | ...avec ce **système d’exploitation cluster** | ... depuis ce **client** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Kit de développement logiciel (SDK) .NET pour Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux ou Windows |Windows (pour l’instant) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux ou Windows |Windows |

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="pig-and-sql-server-integration-services"></a>Pig et SQL Server Integration Services

Vous pouvez utiliser les services SQL Server Integration Services (SSIS) pour exécuter une travail Pig. Le pack de fonctionnalités Azure pour SSIS fournit les composants suivants, compatibles avec les tâches Pig sur HDInsight.

* [Tâche d’Azure HDInsight Pig][pigtask]

* [Gestionnaire de connexions d’abonnement Azure][connectionmanager]

Pour en savoir plus sur le pack de fonctionnalités Azure pour SSIS, cliquez [ici][ssispack].

## <a id="nextsteps"></a>Étapes suivantes
Maintenant que vous avez vu comment utiliser Pig avec HDInsight, utilisez les liens suivants pour découvrir d'autres façons d'utiliser Azure HDInsight.

* [Téléchargement de données vers HDInsight](../hdinsight-upload-data.md)
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Sqoop avec HDInsight](hdinsight-use-sqoop.md)
* [Utilisation d’Oozie avec HDInsight](../hdinsight-use-oozie.md)
* [Utilisation des tâches MapReduce avec HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
