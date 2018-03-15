---
title: "Soumettre des travaux MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight - Azure | Microsoft Docs"
description: "Apprenez à envoyer des travaux MapReduce à Azure HDInsight Hadoop à l’aide du Kit de développement logiciel (SDK) .NET HDInsight."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: c85e44b0-85fd-4185-ad1c-c34a9fe5ef44
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: e967c6df89722492554998dc21e09388b7b7cdf2
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Exécuter des travaux MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Apprenez à envoyer des travaux MapReduce à l’aide du Kit de développement logiciel (SDK) .NET HDInsight. Les clusters HDInsight comportent un fichier jar qui contient des exemples MapReduce. Le fichier jar est le suivant : */example/jars/hadoop-mapreduce-examples.jar*.  Un des exemples est *wordcount*. Vous développez une application de console C# pour envoyer une tâche wordcount.  Le travail lit le fichier */example/data/gutenberg/davinci.txt* et renvoie les résultats vers */example/data/davinciwordcount*.  Si vous souhaitez réexécuter l’application, vous devez nettoyer le dossier de sortie.

> [!NOTE]
> Les étapes décrites dans cet article doivent être effectuées à partir d'un client Windows. Pour plus d’informations sur l’utilisation d’un client Linux, OS X ou Unix pour utiliser Hive, utilisez le sélecteur d’onglet en haut de l’article.
> 
> 

## <a name="prerequisites"></a>Prérequis
Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Cluster Hadoop dans HDInsight**. Consultez [Prise en main de Hadoop sous Linux dans HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Visual Studio 2013/2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Envoi de tâches MapReduce avec le Kit de développement logiciel (SDK) .NET HDInsight
Le Kit de développement logiciel (SDK) HDInsight .NET fournit des bibliothèques clientes .NET, ce qui facilite l'utilisation des clusters HDInsight à partir de .NET. 

**Pour soumettre les travaux**

1. Créez une application console C# dans Visual Studio.
2. Dans la console du gestionnaire de package NuGet, exécutez la commande suivante :

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```
3. Utilisez le code suivant :

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string existingClusterUsername = "<Cluster Username>";
            private const string existingClusterPassword = "<Cluster User Password>";

            private const string defaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";

            private const string sourceFile = "/example/data/gutenberg/davinci.txt";  
            private const string outputFolder = "/example/data/davinciwordcount";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);

                SubmitMRJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };

                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);

                System.Console.WriteLine("Waiting for the job completion ...");

                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }

                // Get job output
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + 
                        defaultStorageAccountName + 
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); 
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }
    ```

4. Appuyez sur **F5** pour exécuter l'application.

Pour réexécuter le travail, vous devez modifier le nom du dossier de sortie du travail. Dans l’exemple, il s’agit de « /example/data/davinciwordcount ».

Lorsque la tâche se termine correctement, l’application imprime le contenu du fichier de sortie « part-r-00000 ».

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté différentes méthodes pour créer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* Pour soumettre un travail Hive, consultez [Exécuter des requêtes Hive avec le Kit de développement logiciel (SDK) .NET HDInsight](apache-hadoop-use-hive-dotnet-sdk.md).
* Pour créer des clusters HDInsight, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Pour gérer des clusters HDInsight, consultez [Gestion de clusters Hadoop basés sur Linux dans HDInsight](../hdinsight-administer-use-portal-linux.md).
* Pour découvrir le Kit de développement logiciel (SDK) .NET HDInsight, consultez [Référence du Kit de développement logiciel (SDK) .NET de HDInsight](https://msdn.microsoft.com/library/mt271028.aspx).
* Pour l’authentification non interactive sur Azure, consultez [Créer des applications HDInsight .NET d’authentification non interactive](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

