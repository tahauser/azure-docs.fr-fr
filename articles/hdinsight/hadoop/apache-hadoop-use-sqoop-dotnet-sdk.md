---
title: "Exécuter des tâches Sqoop avec .NET et HDInsight - Azure | Microsoft Docs"
description: "Découvrez comment utiliser le Kit de développement logiciel (SDK) .NET HDInsight pour exécuter l’exportation et l’importation Sqoop entre un cluster Hadoop et une base de données SQL Azure."
keywords: travail sqoop
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 87bacd13-7775-4b71-91da-161cb6224a96
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: 41a1c7c0b7c0ad00746508bbf341eff448690725
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="run-sqoop-jobs-by-using-net-sdk-for-hadoop-in-hdinsight"></a>Exécuter des tâches Sqoop à l’aide du Kit de développement logiciel (SDK) .NET pour Hadoop dans HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser le Kit de développement logiciel (SDK) .NET Azure HDInsight pour exécuter des tâches Sqoop dans HDInsight afin d’effectuer des opérations d’importation et d’exportation entre un cluster HDInsight et une base de données SQL Azure ou SQL Server.

> [!NOTE]
> Même si vous pouvez utiliser les procédures décrites dans cet article avec un cluster HDInsight Windows ou Linux, ces dernières fonctionnent uniquement à partir d’un client Windows. Pour choisir d’autres méthodes, utilisez le sélecteur d’onglet en haut de cet article.
> 

## <a name="prerequisites"></a>configuration requise
Avant de commencer ce didacticiel, vous devez disposer de l’élément suivant :

* Un cluster Hadoop dans HDInsight. Pour plus d’informations, consultez [Création du cluster et de la base de données SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Utiliser Sqoop sur des clusters HDInsight avec le Kit de développement logiciel (SDK) .NET
Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation des clusters HDInsight à partir de .NET. Dans cette section, vous allez créer une application console C# pour exporter la table hivesampletable vers la table de base de données SQL Azure créée précédemment dans ce didacticiel.

## <a name="submit-a-sqoop-job"></a>Envoyer un travail Sqoop

1. Créez une application console C# dans Visual Studio.

2. Dans la console du gestionnaire de package Visual Studio, importez le package en exécutant la commande NuGet suivante :
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Utilisez le code suivant dans le fichier Program.cs :
   
        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. Pour lancer le programme, appuyez sur la touche **F5**. 

## <a name="limitations"></a>Limites
HDInsight basé sur Linux présente les limitations suivantes :

* Exportation en bloc : le connecteur Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou la base de données SQL Azure ne prend actuellement pas en charge les insertions en bloc.

* Traitement par lots : lorsque vous utilisez le commutateur `-batch` pour effectuer des insertions, Sqoop effectue plusieurs insertions plutôt qu’un traitement par lots des opérations d’insertion.

## <a name="next-steps"></a>Étapes suivantes
Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

* [Utilisation d'Oozie avec HDInsight](../hdinsight-use-oozie.md): utilisez l’action Sqoop dans un flux de travail Oozie.
* [Analyse des données sur les retards de vol avec HDInsight](../hdinsight-analyze-flight-delay-data.md): utilisez Hive pour analyser des données sur les retards de vol, puis utilisez Sqoop pour exporter ces données vers une base de données SQL Azure.
* [Téléchargement de données vers HDInsight](../hdinsight-upload-data.md) : découvrez d’autres méthodes pour télécharger des données vers HDInsight ou le stockage Blob Azure.

