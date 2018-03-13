---
title: "Exécuter une charge de travail parallèle - Azure Batch .NET"
description: "Didacticiel - Transcoder des fichiers multimédias en parallèle avec ffmpeg dans Azure Batch à l’aide de la bibliothèque cliente Batch .NET"
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: 
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 1100f8fddcd2f802b5f38e0b9789bc9ec359e03a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>Didacticiel : exécuter une charge de travail parallèle avec Azure Batch à l’aide de l’API .NET

Utilisez Azure Batch pour exécuter des programmes de traitement par lots de calcul haute performance (HPC) en parallèle dans Azure, efficacement et à grande échelle. Ce didacticiel vous permet de découvrir un exemple d’exécution C# d’une charge de travail parallèle utilisant Batch. Vous découvrez un workflow d’application Batch courant et comment interagir par programme avec les ressources de stockage et Batch. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Ajouter un package d’application à votre compte Batch
> * S’authentifier avec des comptes de stockage et Batch
> * Charger des fichiers d’entrée sur le stockage
> * Créer un pool de nœuds de calcul pour exécuter une application
> * Créer un travail et des tâches pour traiter les fichiers d’entrée
> * Surveiller l’exécution d’une tâche
> * Récupérer les fichiers de sortie

Dans ce didacticiel, vous convertissez des fichiers de multimédia MP4 en parallèle au format MP3 à l’aide de l’outil open-source [ffmpeg](http://ffmpeg.org/). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>configuration requise

* [Visual Studio IDE](https://www.visualstudio.com/vs) (Visual Studio 2015 ou une version plus récente). 

* Un compte Batch et un compte Stockage lié à usage général. Pour créer ces comptes, consultez les démarrages rapides Azure Batch à l’aide du [portail Azure](quick-create-portal.md) ou de l’[interface de ligne de commande Azure](quick-create-cli.md).

* [Windows version 64 bits de ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip) (.zip). Téléchargez le fichier zip sur votre ordinateur local. Pour ce didacticiel, seul le fichier zip est nécessaire. Vous n’avez pas besoin de décompresser le fichier ou de l’installer localement. 

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure depuis l’adresse [https://portal.azure.com](https://portal.azure.com).


## <a name="add-an-application-package"></a>Ajouter un package d’application

Utiliser le portail Azure pour ajouter ffmpeg à votre compte Batch en tant que [package d’application](batch-application-packages.md). Les packages d’application permettent de gérer les applications de tâche et leur déploiement sur les nœuds de calcul dans votre pool. 

1. Dans le portail Azure, cliquez sur **Plus de services** > **Comptes Batch**, puis sur le nom de votre compte Batch.
3. Cliquez sur **Applications** > **Ajouter**.
4. Pour **id d’Application** entrez *ffmpeg*et *3.4* pour la version de package. Sélectionnez le fichier zip ffmpeg que vous avez téléchargé précédemment, puis cliquez sur **OK**. Le package d’application ffmpeg est ajouté à votre compte Batch.

![Ajouter un package d’application](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>Télécharger et exécuter l’exemple

### <a name="download-the-sample"></a>Téléchargez l’exemple

[Téléchargez ou clonez l’exemple d’application](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) à partir de GitHub. Pour cloner le référentiel d’exemple d’application avec un client Git, utilisez la commande suivante :

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

Naviguez vers le répertoire qui contient le fichier de la solution Visual Studio `BatchDotNetFfmpegTutorial.sln`.

Ouvrez le fichier de la solution dans Visual Studio et mettez à jour les chaînes d’informations d’identification dans `program.cs` avec les valeurs obtenues pour vos comptes. Par exemple : 

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

Vérifiez également que la référence du package d’application ffmpeg de la solution correspond à l’Id et à la version du package ffmpeg chargée sur votre compte Batch.

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "3.4";
```
### <a name="build-and-run-the-sample-project"></a>Créer et exécuter l’exemple de projet

* Cliquez avec le bouton droit dans l’Explorateur de solutions, puis sur **Générer la solution**. 

* Si vous y êtes invité, confirmez la restauration de tous les packages NuGet. Si vous devez télécharger des packages manquants, vérifiez que le [Gestionnaire de Package NuGet](https://docs.nuget.org/consume/installing-nuget) est installé.

Puis exécutez-le. Lorsque vous exécutez l’exemple d’application, la sortie de la console est identique à ce qui suit. Pendant l’exécution, l’étape `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` fait l’objet d’une pause correspondant au démarrage des nœuds de calcul du pool. 

```
Sample start: 12/12/2017 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 12/12/2017 3:29:36 PM
Elapsed time: 00:09:14.3418742
```


Accédez à votre compte Batch dans le portail Azure pour surveiller le pool, les nœuds de calcul, les travaux et les tâches. Par exemple, pour voir une carte thermique des nœuds de calcul de votre pool, cliquez sur **Pools** > *WinFFmpegPool*.

Lorsque les tâches sont en cours d’exécution, la carte thermique est similaire à ce qui suit :

![Carte thermique des pools](./media/tutorial-parallel-dotnet/pool.png)


Le temps d’exécution standard est d’environ **10 minutes** lorsque l’application fonctionne dans sa configuration par défaut. La création d’un pool est l’opération la plus longue.

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Vérifier le code

Dans les sections suivantes, nous examinons l’exemple d’application en nous servant des opérations qu’elle effectue pour traiter une charge de travail dans le service Batch. Référez-vous à la solution ouverte dans Visual Studio lorsque vous lisez le reste de cet article, car certaines lignes de code de cet exemple ne sont pas expliquées ici.

### <a name="authenticate-blob-and-batch-clients"></a>Authentifier les clients Blob et Batch

Pour interagir avec le compte de stockage lié, l’application utilise la bibliothèque cliente de stockage Azure pour .NET. Elle crée une référence au compte avec [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) avec authentification de la clé partagée. Ensuite, elle crée un [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient).

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

L’application crée un objet [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) pour créer et gérer des pools, des travaux et des tâches dans le service Batch. Le client Batch dans l’exemple utilise l’authentification de la clé partagée. Batch prend également en charge l’authentification via [Azure Active Directory](batch-aad-auth.md) pour authentifier des utilisateurs individuels ou une application sans assistance.

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>Charger des fichiers d’entrée

L’application transmet l’objet `blobClient` à la méthode `CreateContainerIfNotExist` pour créer un conteneur de stockage pour les fichiers d’entrée (format MP4) et un conteneur pour la sortie de la tâche.

```csharp
  CreateContainerIfNotExist(blobClient, inputContainerName;
  CreateContainerIfNotExist(blobClient, outputContainerName);
```

Ensuite, les fichiers sont chargés dans le conteneur d’entrée à partir du dossier `InputFiles` local. Les fichiers de stockage sont définis en tant qu’objets Batch [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile) que Batch peut télécharger ultérieurement sur les nœuds de calcul. 

Deux méthodes de `Program.cs` sont impliquées dans le chargement des fichiers :

* `UploadResourceFilesToContainer` : renvoie une collection d’objets ResourceFile et appelle `UploadResourceFileToContainer` en interne pour charger chaque fichier transmis dans le paramètre `filePaths`.
* `UploadResourceFileToContainer`: charge chaque fichier en tant qu’un objet blob dans le conteneur d’entrée. Après le chargement du fichier, la méthode obtient une signature d’accès partagé (SAP) pour l’objet Blob et renvoie un objet ResourceFile pour la représenter. 

```csharp
  List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(@"..\..\InputFiles", "*.mp4",
      SearchOption.TopDirectoryOnly));

  List<ResourceFile> inputFiles = UploadResourceFilesToContainer(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Pour en savoir plus sur le chargement de fichiers en tant qu’objets Blob sur un compte de stockage avec .NET, consultez [Prise en main du stockage d’objets blob Azure à l’aide de .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

### <a name="create-a-pool-of-compute-nodes"></a>Créer un pool de nœuds de calcul

Ensuite, l’exemple crée un pool de nœuds de traitement dans le compte Batch, avec un appel à `CreatePoolIfNotExist`. Cette méthode définie utilise la méthode [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) pour définir le nombre de nœuds, la taille de la machine virtuelle et une configuration de pool. Ici, un objet [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) spécifie une référence [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) à une image Windows Server publiée dans la Place de marché Microsoft Azure. Azure Batch prend en charge une large plage de machine virtuelle dans la Place de marché Microsoft Azure, ainsi que des images de machines virtuelles personnalisées.

Le nombre de nœuds et la taille de machine virtuelle sont définis à l’aide de constantes définies. Azure Batch prend en charge les nœuds dédiés et les [nœuds de faible priorité](batch-low-pri-vms.md) que vous pouvez utiliser dans vos pools. Les nœuds dédiés sont réservés à votre pool. Les nœuds de faible priorité sont proposés à prix réduit à partir de la capacité de machine virtuelle excédentaire dans Azure. Les nœuds de faible priorité deviennent indisponibles si la capacité d’Azure est insuffisante. L’exemple par défaut crée un pool contenant seulement 5 nœuds de faible priorité taille *Standard_A1_v2*. 

L’application ffmpeg est déployée sur les nœuds de calcul en ajoutant un [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) à la configuration du pool. 

La méthode [Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) soumet le pool au service Batch.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2012-R2-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,                                                
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

pool.Commit();  
```

### <a name="create-a-job"></a>Création d’un travail

Un programme de traitement par lots spécifie un pool pour exécuter des tâches et des paramètres facultatifs tels qu’une priorité et un calendrier pour le travail. L’exemple crée un travail avec un appel à `CreateJobIfNotExist`. Cette méthode définie utilise la méthode [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob) pour créer un travail sur le pool. 

La méthode [Commit](/dotnet/api/microsoft.azure.batch.cloudjob.commit) soumet le travail au service Batch. Dans un premier temps, le travail n’a aucune tâche.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = JobId;
    job.PoolInformation = new PoolInformation { PoolId = PoolId };

job.Commit();        
```

### <a name="create-tasks"></a>Créer des tâches

L’exemple crée des tâches dans le travail avec un appel à la méthode `AddTasks`, ce qui crée une liste d’objets [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask). Chaque `CloudTask` exécute ffmpeg pour traiter un objet `ResourceFile` d’entrée à l’aide de la propriété [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline). ffmpeg a été précédemment installé sur chaque nœud lors de la création du pool. Ici, la ligne de commande exécute ffmpeg pour convertir chaque fichier (vidéo) MP4 en fichier MP3 (audio).

L’exemple crée un objet [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile) pour le fichier MP3 après l’exécution de la ligne de commande. Les fichiers de sortie de chaque tâche (un, dans ce cas) sont chargés sur un conteneur dans le compte de stockage lié, à l’aide de la propriété [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles).

Ensuite, l’exemple ajoute des tâches au travail avec la méthode [AddTask](/dotnet/api/microsoft.azure.batch.joboperations.addtask), qui les met en file d’attente afin de les exécuter sur les nœuds de calcul. 

```csharp
for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-3.4-win64-static\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line) 
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };
   

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
batchClient.JobOperations.AddTask(jobId, tasks);
```

### <a name="monitor-tasks"></a>Surveiller les tâches

Lorsque Batch ajoute des tâches à un travail, le service les met automatiquement en file d’attente et planifie leur exécution sur les nœuds de calcul dans le pool associé. Selon les paramètres que vous spécifiez, Batch gère l’ensemble des opérations de mise en file d’attente, de planification, de ré-exécution et d’administration des tâches. 

Il existe plusieurs approches pour l’exécution de la tâche d’analyse. Cet exemple définit une méthode `MonitorTasks` pour signaler uniquement l’achèvement d’une tâche ou son échec ou les états réussis. Le code `MonitorTasks` spécifie un [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) pour sélectionner efficacement uniquement un minimum d’informations sur les tâches. Ensuite, il crée un [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor), qui fournit des utilitaires d’assistance pour l’analyse des états de la tâche. Dans `MonitorTasks`, l’exemple attend que toutes les tâches atteignent `TaskState.Completed` dans un délai imparti. Puis, il met fin au travail et signale toutes les tâches terminées, mais pouvant avoir subi une défaillance, telle qu’un code de sortie différent de zéro.

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    batchClient.Utilities.CreateTaskStateMonitor().WaitAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId, failureMessage);
    Console.WriteLine(failureMessage);
}
batchClient.JobOperations.TerminateJob(jobId, successMessage);
...

```

## <a name="clean-up-resources"></a>Supprimer des ressources

Après avoir exécuté les tâches, l’application supprime automatiquement le conteneur de stockage d’entrée créé et vous donne la possibilité de supprimer le travail et le pool Azure Batch. Les classes [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) et [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) de BatchServiceClient disposent toutes deux de méthodes de suppression correspondantes, appelées si l’utilisateur confirme la suppression. Bien que vous ne soyez pas facturé pour les travaux et les tâches à proprement parler, les nœuds de calcul vous sont facturés. Par conséquent, nous vous conseillons d’affecter les pools uniquement en fonction des besoins. Lorsque vous supprimez le pool, toutes les sorties de tâche sur les nœuds sont supprimées. Toutefois, les fichiers d’entrée et de sortie restent dans le compte de stockage.

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, le compte Batch et le compte de stockage. Pour ce faire, dans le portail Azure, sélectionnez le groupe de ressources pour le compte Batch, puis cliquez sur **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter un package d’application à votre compte Batch
> * S’authentifier avec des comptes de stockage et Batch
> * Charger des fichiers d’entrée sur le stockage
> * Créer un pool de nœuds de calcul pour exécuter une application
> * Créer un travail et des tâches pour traiter les fichiers d’entrée
> * Surveiller l’exécution d’une tâche
> * Récupérer les fichiers de sortie

Pour plus d’exemples d’utilisation de l’API .NET pour planifier et traiter les charges de travail Batch, consultez les exemples sur GitHub.

> [!div class="nextstepaction"]
> [Exemples C# Batch](https://github.com/Azure/azure-batch-samples/tree/master/CSharp)
