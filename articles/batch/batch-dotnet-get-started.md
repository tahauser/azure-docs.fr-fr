---
title: 'Didacticiel : utiliser la bibliothèque cliente Azure Batch pour .NET | Microsoft Docs'
description: Découvrez les concepts de base d’Azure Batch et créez une solution simple à l’aide de .NET.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 76cb9807-cbc1-405a-8136-d1e53e66e82b
ms.service: batch
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 06/28/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9968d18f9e27d7a138831394658b40a483b66709
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="get-started-building-solutions-with-the-batch-client-library-for-net"></a>Bien démarrer avec la création de solutions avec la bibliothèque cliente Batch pour .NET

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.JS](batch-nodejs-get-started.md)
>
>

Découvrez les principes de base [d’Azure Batch][azure_batch] et la bibliothèque [Batch .NET][net_api] dans cet article où nous décrivons l’application C# étape par étape. Nous allons voir comment l’exemple d’application tire parti du service Batch pour traiter une charge de travail parallèle dans le cloud, ainsi que la façon dont cette application interagit avec [Azure Storage](../storage/common/storage-introduction.md) pour la gestion intermédiaire et la récupération des fichiers. Vous allez vous familiariser avec les flux de travail d’application Batch et découvrir une vue d’ensemble des principaux composants de Batch, tels que les travaux, les tâches, les pools et les nœuds de calcul.

![Flux de travail de la solution Batch (de base)][11]<br/>

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous avez acquis une connaissance pratique de C# et Visual Studio. Il suppose également que vous êtes en mesure de satisfaire les exigences de création de compte spécifiées ci-dessous pour Azure et les services Batch et Storage.

### <a name="accounts"></a>Comptes
* **Compte Azure** : si vous ne possédez pas encore d’abonnement Azure, [créez un compte Azure gratuit][azure_free_account].
* **Compte Batch**: une fois que vous disposez d’un abonnement Azure, [créez un compte Azure Batch](batch-account-create-portal.md).
* **Compte de stockage** : voir la section [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) de l’article [À propos des comptes de stockage Azure](../storage/common/storage-create-storage-account.md).

> [!IMPORTANT]
> Le service Batch ne prend actuellement en charge *que* le type de compte de stockage à **usage général**, comme décrit à l’étape 5, [Création d’un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) dans [À propos des comptes de stockage Azure](../storage/common/storage-create-storage-account.md).
>
>

### <a name="visual-studio"></a>Visual Studio
Vous devez disposer de **Visual Studio 2015 ou d’une version ultérieure** pour générer l’exemple de projet. Des versions gratuites et d’évaluation de Visual Studio sont accessibles à partir de la page [Vue d’ensemble des produits de Visual Studio][visual_studio].

### <a name="dotnettutorial-code-sample"></a>*DotNetTutorial*
L’exemple [DotNetTutorial][github_dotnettutorial] est l’un des nombreux exemples de code Batch disponibles dans le référentiel [azure-batch-samples][github_samples] sur GitHub. Vous pouvez télécharger tous les exemples en cliquant sur le bouton **Cloner ou télécharger > Télécharger ZIP** de la page d’accueil du référentiel ou en cliquant sur le lien de téléchargement direct [azure-batch-samples-master.zip][github_samples_zip]. Après avoir extrait le contenu de ce fichier ZIP, vous trouverez la solution dans le dossier suivant :

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="batchlabs-optional"></a>BatchLabs (facultatif)
[BatchLabs][github_batchlabs] est un outil client autonome, gratuit et doté de nombreuses fonctionnalités, aidant à créer, déboguer et surveiller les applications Azure Batch. Bien qu’il ne soit pas nécessaire pour suivre ce didacticiel, il peut être utile lors du développement et du débogage de vos solutions Batch.

## <a name="dotnettutorial-sample-project-overview"></a>Vue d’ensemble de l’exemple de projet DotNetTutorial
L’exemple de code *DotNetTutorial* est une solution Visual Studio qui se compose de deux projets : **DotNetTutorial** et **TaskApplication**.

* **DotNetTutorial** est l’application cliente qui interagit avec les services Batch et Storage pour exécuter une charge de travail parallèle sur des nœuds de calcul (machines virtuelles). DotNetTutorial s’exécute sur votre station de travail locale.
* **TaskApplication** est le programme qui s’exécute sur les nœuds de calcul dans Azure pour mener à bien l’opération proprement dite. Dans l’exemple, `TaskApplication.exe` analyse le texte d’un fichier téléchargé depuis Stockage Azure (le fichier d’entrée). Ensuite, il génère un fichier texte (fichier de sortie) qui contient une liste des trois mots principaux qui s’affichent dans le fichier d’entrée. Après avoir créé le fichier de sortie, TaskApplication télécharge le fichier dans Azure Storage. Il est ainsi mis à la disposition de l’application cliente pour le téléchargement. TaskApplication s’exécute en parallèle sur plusieurs nœuds de calcul dans le service Batch.

Le diagramme ci-après illustre les principales opérations effectuées par l’application cliente *DotNetTutorial*, ainsi que l’application exécutée par les tâches, *TaskApplication*. Ce flux de travail de base est caractéristique de nombreuses solutions de calcul créées avec Batch. Même s’il ne présente pas toutes les fonctionnalités disponibles dans le service Batch, la quasi-totalité des scénarios Batch comporte des processus de ce flux de travail.

![Exemple de flux de travail Batch][8]<br/>

[**Étape 1.**](#step-1-create-storage-containers) Créer des **conteneurs** dans le Stockage Blob Azure.<br/>
[**Étape 2.**](#step-2-upload-task-application-and-data-files) Charger les fichiers d’application de tâche et les fichiers d’entrée dans les conteneurs.<br/>
[**Étape 3.**](#step-3-create-batch-pool) Créer un **pool** Batch.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Le pool **StartTask** télécharge les fichiers binaires de tâche (TaskApplication) dans les nœuds lorsque ces derniers rejoignent le pool.<br/>
[**Étape 4.**](#step-4-create-batch-job) Créer un **travail** Batch.<br/>
[**Étape 5.**](#step-5-add-tasks-to-job) Ajoutez des **tâches** au travail.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Les tâches sont planifiées pour s’exécuter sur des nœuds.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Chaque tâche télécharge ses données d’entrée depuis Stockage Azure, puis commence l’exécution.<br/>
[**Étape 6.**](#step-6-monitor-tasks) Surveiller les tâches.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Lorsque les tâches sont terminées, les résultats générés sont chargés dans Stockage Azure.<br/>
[**Étape 7.**](#step-7-download-task-output) Télécharger la sortie des tâches à partir de Storage.

Comme indiqué précédemment, certaines solutions Batch ne suivent pas exactement cette procédure et peuvent exécuter de nombreuses autres opérations ; toutefois, l’exemple d’application *DotNetTutorial* illustre les processus fréquemment inclus dans une solution Batch.

## <a name="build-the-dotnettutorial-sample-project"></a>Générer l’exemple de projet *DotNetTutorial* .
Avant de pouvoir exécuter l’exemple, vous devez spécifier les informations d’identification du compte Batch et du compte de stockage dans le fichier `Program.cs` du projet *DotNetTutorial*. Si ce n’est pas encore fait, ouvrez la solution dans Visual Studio en double-cliquant sur le fichier solution `DotNetTutorial.sln` . Vous pouvez également l’ouvrir dans Visual Studio à l’aide du menu **Fichier > Ouvrir > Projet/Solution**.

Ouvrez `Program.cs` dans le projet *DotNetTutorial* . Ajoutez ensuite vos informations d’identification comme indiqué en haut du fichier :

```csharp
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [!IMPORTANT]
> Comme mentionné ci-dessus, vous devez renseigner les informations d’identification pour un compte de stockage **à usage général** dans Stockage Azure. Vos applications Batch utiliseront Blob Storage dans le compte de stockage **à usage général**. Ne renseignez pas les informations d’identification pour un compte de stockage ayant été créé en sélectionnant *Blob Storage* comme type de compte.
>
>

Les informations d’identification de votre compte Batch et de votre compte de stockage figurent dans le panneau du compte de chaque service dans le [portail Azure][azure_portal] :

![Informations d’identification Batch dans le portail][9]
![Informations d’identification Stockage dans le portail][10]<br/>

Une fois le projet mis à jour avec vos informations d’identification, cliquez avec le bouton droit sur la solution dans l’Explorateur de solutions, puis cliquez sur **Générer la solution**. Si vous y êtes invité, confirmez la restauration de tous les packages NuGet.

> [!TIP]
> Si les packages NuGet ne sont pas restaurés automatiquement ou si vous obtenez des erreurs à la suite de l’échec de la restauration des packages, assurez-vous que le [Gestionnaire de packages NuGet][nuget_packagemgr] est installé. Activez ensuite le téléchargement de packages manquants. Pour plus d’informations sur le téléchargement des packages, voir l’article [Enabling Package Restore During Build][nuget_restore] (Activation de la restauration des packages lors de la génération).
>
>

Dans les sections suivantes, nous examinons en détail l’exemple d’application en nous servant des opérations qu’elle effectue pour traiter une charge de travail dans le service Batch. Nous vous invitons à vous référer à la solution ouverte dans Visual Studio à mesure que vous progressez dans la lecture de cet article, car certaines lignes de code de cet exemple ne sont pas expliquées ici.

Accédez à la partie supérieure de la méthode `MainAsync` dans le fichier `Program.cs` du projet *DotNetTutorial* pour commencer par l’étape 1. Les différentes étapes ci-après suivent ensuite approximativement la progression des appels de méthode effectués dans `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Étape 1 : créer des conteneurs de stockage
![Créer des conteneurs dans le Stockage Azure][1]
<br/>

Batch prend en charge l’interaction avec Azure Storage. Les conteneurs présents dans votre compte de stockage fournissent les fichiers nécessaires aux tâches s’exécutant dans votre compte Batch. Les conteneurs fournissent également un emplacement pour stocker les données de sortie générées par les tâches. L’application cliente *DotNetTutorial* commence par créer trois conteneurs dans [Azure Blob Storage](../storage/common/storage-introduction.md):

* **application**: ce conteneur stockera l’application exécutée par les tâches, ainsi que toutes ses dépendances, telles que les DLL.
* **input**: les tâches téléchargeront les fichiers de données à traiter à partir du conteneur *input* .
* **output**: une fois que les tâches auront terminé de traiter les fichiers d’entrée, elles chargeront leurs résultats dans le conteneur *output* .

Pour interagir avec un compte de stockage et créer des conteneurs, nous utilisons la [bibliothèque cliente Stockage Azure pour .NET][net_api_storage]. Nous créons une référence au compte avec [CloudStorageAccount][net_cloudstorageaccount], et à partir de là nous créons un [CloudBlobClient][net_cloudblobclient] :

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Nous utilisons la référence `blobClient` dans l’ensemble de l’application et la transmettons sous forme d’un paramètre à un certain nombre de méthodes. Le bloc de code figurant immédiatement après le code ci-dessus constitue un exemple de cette opération dans lequel nous appelons la méthode `CreateContainerIfNotExistAsync` pour créer concrètement les conteneurs.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Une fois les conteneurs créés, l’application peut charger les fichiers destinés à être utilisés par les tâches.

> [!TIP]
> L’article [Utilisation du stockage blob à partir de .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) offre un bon aperçu de l’utilisation des conteneurs de stockage Azure et des objets blob. Il doit se trouver au début de votre liste de lecture lorsque vous commencez à travailler avec le traitement Batch.
>
>

## <a name="step-2-upload-task-application-and-data-files"></a>Étape 2 : charger les fichiers d’application de tâche et les fichiers de données
![Charger les fichiers d’application de tâche et les fichiers (de données) d’entrée dans les conteneurs][2]
<br/>

Dans le cadre de l’opération de chargement des fichiers, *DotNetTutorial* commence par définir les groupes de chemins d’accès aux fichiers **d’application** et **d’entrée** tels qu’ils existent sur la machine locale. Il télécharge ensuite ces fichiers dans les conteneurs créés à l’étape précédente.

```csharp
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Le fichier `Program.cs` comprend deux méthodes qui sont impliquées dans le processus de chargement :

* `UploadFilesToContainerAsync` : cette méthode renvoie une collection d’objets [ResourceFile][net_resourcefile] (décrits ci-après) et appelle en interne la méthode `UploadFileToContainerAsync` pour charger chaque fichier transmis dans le paramètre *filePaths*.
* `UploadFileToContainerAsync` : cette méthode procède au chargement des fichiers et crée les objets [ResourceFile][net_resourcefile]. Après le chargement du fichier, la méthode obtient une signature d’accès partagé (SAP) pour le fichier et renvoie un objet ResourceFile qui la représente. Les signatures d’accès partagé sont également décrites ci-dessous.

```csharp
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>Objets ResourceFile
Un objet [ResourceFile][net_resourcefile] fournit aux tâches de Batch l’URL d’un fichier de Stockage Azure qui est téléchargé dans un nœud de calcul avant l’exécution de ces tâches. La propriété [ResourceFile.BlobSource][net_resourcefile_blobsource] spécifie l’URL complète du fichier tel qu’il existe dans Stockage Azure. L’URL peut également inclure une signature d’accès partagé (SAS) fournissant un accès sécurisé au fichier. La propriété *ResourceFiles* est utilisée par la plupart des types de tâche de Batch .NET, notamment :

* [CloudTask][net_task]
* [StartTask][net_pool_starttask]
* [JobPreparationTask][net_jobpreptask]
* [JobReleaseTask][net_jobreltask]

L’exemple d’application DotNetTutorial n’utilise pas les types de tâche JobPreparationTask ou JobReleaseTask. Pour plus d’informations sur ces types de tâche, consultez la section [Exécution de tâches de préparation et de fin du travail sur les nœuds de calcul Azure Batch](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Signature d’accès partagé (SAP)
Les signatures d’accès partagé sont des chaînes qui, une fois intégrées à une URL, offrent un accès sécurisé aux conteneurs et aux objets blob dans Azure Storage. L’application DotNetTutorial utilise les URL de signature d’accès partagées des objets blob et des conteneurs et montre comment obtenir ces chaînes de signature d’accès partagé auprès du service Storage.

* **Signatures d’accès partagé d’objet blob**: le type de tâche StartTask du pool dans DotNetTutorial utilise les signatures d’accès partagé d’objet blob lors du téléchargement des fichiers binaires d’application et des fichiers de données d’entrée à partir de Storage (voir l’étape 3 ci-dessous). La méthode `UploadFileToContainerAsync` du `Program.cs` de DotNetTutorial contient le code qui obtient la signature d’accès partagé de chaque objet blob. Elle le fait en appelant [CloudBlob.GetSharedAccessSignature][net_sas_blob].
* **Signatures d’accès partagé de conteneur**: une fois que chaque tâche a mené à bien l’opération qui lui était affectée sur le nœud de calcul, elle charge son fichier de sortie dans le conteneur *output* de Stockage Azure. Pour ce faire, TaskApplication utilise une signature d’accès partagé de conteneur qui fournit un accès en écriture au conteneur dans le chemin d’accès lors du chargement du fichier. L’obtention de la signature d’accès partagé de conteneur obéit à la même procédure que l’obtention de signature d’accès partagé de l’objet blob. Dans DotNetTutorial, vous constaterez que la méthode d’assistance `GetContainerSasUrl` appelle [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] pour ce faire. Vous en saurez plus sur la façon dont TaskApplication utilise la signature d’accès partagé de conteneur en consultant la section l’« étape 6 : Surveiller les tâches » ci-après.

> [!TIP]
> Pour en savoir plus sur la sécurisation de l’accès aux données présentes dans votre compte Stockage Azure, consultez la série sur les signatures d’accès partagé en deux parties, [Partie 1 : Présentation du modèle de signature d’accès partagé (SAP)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) et [Partie 2 : Création et utilisation d’une signature d’accès partagé avec Stockage Blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md).
>
>

## <a name="step-3-create-batch-pool"></a>Étape 3 : créer le pool Batch
![Créer un pool Batch][3]
<br/>

Un **pool** Batch est une collection de nœuds de calcul (machines virtuelles) sur lequel Batch exécute les tâches d’un travail.

Après avoir chargé l’application et les fichiers de données dans le compte de stockage avec les API de stockage Azure, *DotNetTutorial* commence par appeler le service Batch avec les API fournies par la bibliothèque .NET Batch. Le code crée d’abord un élément [BatchClient][net_batchclient] :

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Ensuite, l’exemple crée un pool de nœuds de traitement dans le compte Batch, avec un appel à `CreatePoolIfNotExistsAsync`. `CreatePoolIfNotExistsAsync` utilise la méthode [BatchClient.PoolOperations.CreatePool][net_pool_create] pour créer un pool dans le service Batch :

```csharp
private static async Task CreatePoolIfNotExistAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    CloudPool pool = null;
    try
    {
        Console.WriteLine("Creating pool [{0}]...", poolId);

        // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
        // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
        pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicatedComputeNodes: 3,                                             // 3 compute nodes
            virtualMachineSize: "small",                                                // single-vCPU, 1.75 GB memory, 225 GB disk
            cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));   // Windows Server 2012 R2

        // Create and assign the StartTask that will be executed when compute nodes join the pool.
        // In this case, we copy the StartTask's resource files (that will be automatically downloaded
        // to the node by the StartTask) into the shared directory that all tasks will have access to.
        pool.StartTask = new StartTask
        {
            // Specify a command line for the StartTask that copies the task application files to the
            // node's shared directory. Every compute node in a Batch pool is configured with a number
            // of pre-defined environment variables that can be referenced by commands or applications
            // run by tasks.

            // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
            // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
            // StartTask execution success.
            CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
            ResourceFiles = resourceFiles,
            WaitForSuccess = true
        };

        await pool.CommitAsync();
    }
    catch (BatchException be)
    {
        // Swallow the specific error code PoolExists since that is expected if the pool already exists
        if (be.RequestInformation?.BatchError != null && be.RequestInformation.BatchError.Code == BatchErrorCodeStrings.PoolExists)
        {
            Console.WriteLine("The pool {0} already existed when we tried to create it", poolId);
        }
        else
        {
            throw; // Any other exception is unexpected
        }
    }
}
```

Lorsque vous créez un pool avec [CreatePool][net_pool_create], vous spécifiez un certain nombre de paramètres comme le nombre de nœuds de calcul, la [taille des nœuds](../cloud-services/cloud-services-sizes-specs.md) et le système d’exploitation des nœuds. Dans *DotNetTutorial*, nous utilisons [CloudServiceConfiguration][net_cloudserviceconfiguration] pour spécifier Windows Server 2012 R2 à partir de [Cloud Services](../cloud-services/cloud-services-guestos-update-matrix.md). 

Vous pouvez aussi créer des pools de nœuds de calcul qui sont des machines virtuelles Azure (VM) en spécifiant la configuration [VirtualMachineConfiguration][net_virtualmachineconfiguration] de votre pool. Vous pouvez créer un pool de nœuds de calcul de machines virtuelles à partir d’[images Windows ou Linux](batch-linux-nodes.md). La source de vos images de machine virtuelle peut être :

- Le [Marketplace de machines virtuelles Azure][vm_marketplace], qui fournit des images Windows et Linux prêtes à l’emploi. 
- Une image personnalisée que vous préparez et fournissez. Pour en savoir plus sur les images personnalisées, consultez [Develop large-scale parallel compute solutions with Batch](batch-api-basics.md#pool) (Développer des solutions de calcul parallèles à grande échelle avec Batch).

> [!IMPORTANT]
> Les ressources de calcul dans Batch vous sont facturées. Pour réduire les coûts, vous pouvez diminuer la valeur du paramètre `targetDedicatedComputeNodes` et la définir sur 1 avant d’exécuter l’exemple.
>
>

Outre ces propriétés de nœuds physiques, vous pouvez spécifier une tâche [StartTask][net_pool_starttask] pour le pool. La tâche StartTask s’exécute sur chacun des nœuds rejoignant le pool, ainsi qu’à chaque redémarrage d’un nœud. StartTask se révèle particulièrement utile pour l’installation d’applications sur les nœuds de calcul avant l’exécution de tâches. Par exemple, si vos tâches traitent des données à l’aide de scripts Python, vous pouvez utiliser une tâche StartTask pour installer Python sur les nœuds de calcul.

Dans cet exemple d’application, la tâche StartTask copie les fichiers qu’elle a téléchargés à partir de Storage (spécifiés avec la propriété [StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles]) depuis le répertoire de travail de StartTask vers le répertoire partagé accessible à *toutes* les tâches qui s’exécutent sur le nœud. Pour l’essentiel, ceci copie `TaskApplication.exe` et ses dépendances dans le répertoire partagé sur chaque nœud lorsque le nœud rejoint le pool, afin que toutes les tâches qui s’exécutent sur le nœud puissent y accéder.

> [!TIP]
> La fonctionnalité **packages d’application** d’Azure Batch offre une autre manière d’intégrer votre application aux nœuds de calcul d’un pool. Consultez [Déployer des applications sur les nœuds avec des packages d’applications Batch](batch-application-packages.md) pour plus de détails.
>
>

Notez également que l’extrait de code ci-dessus utilise deux variables d’environnement dans la propriété *CommandLine* de StartTask : `%AZ_BATCH_TASK_WORKING_DIR%` et `%AZ_BATCH_NODE_SHARED_DIR%`. Chaque nœud de calcul au sein d’un pool Batch est automatiquement configuré avec plusieurs variables d’environnement propres au Batch. Tout processus qui est exécuté par une tâche a accès à ces variables d’environnement.

> [!TIP]
> Pour plus d’informations sur les variables d’environnement disponibles sur les nœuds de calcul d’un pool Batch, ainsi que sur les répertoires de travail de tâche, voir les sections [Paramètres d’environnement des tâches](batch-api-basics.md#environment-settings-for-tasks) et [Fichiers et répertoires](batch-api-basics.md#files-and-directories) de l’article [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Étape 4 : créer un travail Batch
![Créer un travail Batch][4]<br/>

Un **travail** Batch constitue un ensemble de tâches et est associé à un pool de nœuds de calcul. Les tâches d’un travail s’exécutent sur les nœuds de calcul du pool associé.

Vous pouvez utiliser un travail non seulement dans le cadre de l’organisation et du suivi des tâches dans des charges de travail liées, mais également pour imposer certaines contraintes telles que la durée d’exécution maximale du travail (et, par extension, de ses tâches) et la priorité du travail par rapport aux autres travaux du compte Batch. Dans cet exemple, toutefois, le travail est uniquement associé au pool qui a été créé à l’étape 3. Aucune propriété supplémentaire n’est configurée.

Tous les travaux Batch sont associés à un pool spécifique. Cette association indique les nœuds sur lesquels les tâches du travail seront exécutées. Vous pouvez la spécifier avec la propriété [CloudJob.PoolInformation][net_job_poolinfo], comme indiqué dans l’extrait de code ci-dessous.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Une fois qu’un travail a été créé, des tâches lui sont ajoutées pour mener à bien l’opération requise.

## <a name="step-5-add-tasks-to-job"></a>Étape 5 : ajouter des tâches au travail
![Ajouter des tâches au travail][5]<br/>
*(1) Les tâches sont ajoutées au travail, (2) les tâches sont planifiées pour s’exécuter sur les nœuds et (3) les tâches téléchargent les fichiers de données à traiter*

Les **tâches** Batch constituent les unités de travail individuelles qui s’exécutent sur les nœuds de calcul. Une ligne de commande est associée à une tâche et cette dernière exécute les scripts ou les exécutables que vous spécifiez dans la ligne de commande.

Pour mener à bien l’opération requise, il est nécessaire d’ajouter les tâches à un travail. Chaque tâche [CloudTask][net_task] est configurée par le biais d’une propriété de ligne de commande et de [ResourceFiles][net_task_resourcefiles] (comme avec la tâche StartTask du pool) que la tâche télécharge dans le nœud avant l’exécution automatique de sa ligne de commande. Dans l’exemple de projet *DotNetTutorial* , chaque tâche traite un seul fichier. Par conséquent, sa collection ResourceFiles contient un seul élément.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [!IMPORTANT]
> Lorsqu’elles accèdent aux variables d’environnement telles que `%AZ_BATCH_NODE_SHARED_DIR%` ou exécutent une application introuvable dans le `PATH` du nœud, les lignes de commande de tâche doivent être précédées de `cmd /c`. L’interpréteur de commande est ainsi exécuté de façon explicite et reçoit une instruction de s’interrompre après avoir exécuté votre commande. Ceci est inutile si les tâches exécutent une application dans le `PATH` du nœud (comme *robocopy.exe* ou *powershell.exe*) et si aucune variable d’environnement n’est utilisée.
>
>

Dans la boucle `foreach` de l’extrait de code ci-dessus, vous pouvez remarquer que la ligne de commande de la tâche est construite de façon à transmettre trois arguments à *TaskApplication.exe*:

1. Le **premier argument** est le chemin d’accès du fichier à traiter. Il s’agit du chemin d’accès local au fichier tel qu’il existe sur le nœud. Lorsque l’objet ResourceFile dans `UploadFileToContainerAsync` a été créé ci-dessus, le nom de fichier a été utilisé pour cette propriété (en tant que paramètre dans le constructeur ResourceFile). Cela indique que le fichier se trouve dans le même répertoire que *TaskApplication.exe*.
2. Le **deuxième argument** spécifie que les *N* premiers mots doivent être écrits dans le fichier de sortie. Dans notre exemple, ceci est codé en dur afin que les trois premiers mots soient écrits dans le fichier de sortie.
3. Le **troisième argument** est la signature d’accès partagé (SAP) qui fournit un accès en écriture au conteneur **output** de Stockage Azure. *TaskApplication.exe* utilise cette URL de signature d’accès partagé lorsqu’il charge le fichier de sortie dans Stockage Azure. Vous trouverez le code correspondant dans la méthode `UploadFileToContainer` dans le fichier de projet TaskApplication `Program.cs` :

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Étape 6 : surveiller les tâches
![Surveiller les tâches][6]<br/>
*L’application cliente (1) surveille l’état d’achèvement et de réussite des tâches, et (2) les tâches chargent les données de résultat dans Stockage Azure*

Lorsque les tâches sont ajoutées à un travail, elles sont automatiquement mises en file d’attente et planifiées pour s’exécuter sur les nœuds de calcul dans le pool associé au travail. Selon les paramètres que vous spécifiez, Batch gère l’ensemble des opérations de mise en file d’attente, de planification, de ré-exécution et d’administration des tâches à votre intention.

Il existe plusieurs approches pour l’exécution de la tâche d’analyse. DotNetTutorial en présente un exemple simple signalant uniquement les états d’achèvement et d’échec ou de réussite des tâches. La méthode `MonitorTasks` du fichier `Program.cs` de DotNetTutorial intègre trois concepts Batch .NET qui sont décrits ci-après. Ils sont répertoriés ci-dessous dans leur ordre d’apparition :

1. **ODATADetailLevel** : la spécification d’un élément [ODATADetailLevel][net_odatadetaillevel] dans les opérations de liste (telles que l’obtention d’une liste des tâches d’un travail) est primordiale pour garantir les performances de l’application Batch. Ajoutez l’article [Interroger efficacement le service Azure Batch](batch-efficient-list-queries.md) à votre liste de lecture si vous prévoyez d’effectuer une quelconque surveillance d’état dans vos applications Batch.
2. **TaskStateMonitor** : l’élément [TaskStateMonitor][net_taskstatemonitor] fournit aux applications Batch .NET des utilitaires d’assistance pour la surveillance des états de tâche. Dans `MonitorTasks`, *DotNetTutorial* attend que toutes les tâches atteignent l’état [TaskState.Completed][net_taskstate] dans un délai spécifique. puis met fin au travail.
3. **TerminateJobAsync** : l’arrêt d’un travail avec [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (ou avec l’élément de blocage JobOperations.TerminateJob) marque ce travail comme terminé. Cette opération est essentielle si votre solution Batch utilise une tâche [JobReleaseTask][net_jobreltask]. Il s’agit d’un type spécial de tâche, qui est décrit dans [Tâches d’achèvement et de préparation des travaux](batch-job-prep-release.md).

La méthode `MonitorTasks` du fichier `Program.cs` de *DotNetTutorial* s’affiche ci-après :

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a failure
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.Result == TaskExecutionResult.Failure)
        {
            // A task with failure information set indicates there was a problem with the task. It is important to note that
            // the task's state can be "Completed," yet still have encountered a failure.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a failure: {1}", task.Id, task.ExecutionInformation.FailureInformation.Message);
            if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Étape 7 : télécharger la sortie des tâches
![Télécharger la sortie des tâches à partir du service Stockage][7]<br/>

Une fois le travail terminé, les données de sortie des tâches peuvent être téléchargées à partir d’Azure Storage. Cette opération s’effectue par le biais d’un appel de `DownloadBlobsFromContainerAsync` dans le fichier `Program.cs` de *DotNetTutorial* :

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [!NOTE]
> L’appel de `DownloadBlobsFromContainerAsync` dans l’application *DotNetTutorial* indique que les fichiers doivent être téléchargés dans votre dossier `%TEMP%`. Vous pouvez modifier cet emplacement de sortie.
>
>

## <a name="step-8-delete-containers"></a>Étape 8 : supprimer les conteneurs
Dans la mesure où les données qui résident dans Azure Storage vous sont facturées, il est toujours judicieux de supprimer tous les objets blob dont vous n’avez plus besoin pour vos travaux Batch. Dans le fichier `Program.cs` de DotNetTutorial, cette opération est effectuée à l’aide de trois appels de la méthode d’assistance `DeleteContainerAsync` :

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

La méthode proprement dite obtient simplement une référence au conteneur, puis appelle [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete] :

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Étape 9 : Supprimer le travail et le pool
Au cours de la dernière étape, l’utilisateur est invité à supprimer le travail et le pool créés par l’application DotNetTutorial. Bien que vous ne soyez pas facturé pour les travaux et les tâches à proprement parler, les nœuds de calcul vous *sont* facturés. Par conséquent, nous vous conseillons d’affecter les nœuds uniquement en fonction des besoins. La suppression des pools inutilisés peut être incluse dans votre processus de maintenance.

Les classes [JobOperations][net_joboperations] et [PoolOperations][net_pooloperations] de BatchClient disposent toutes deux de méthodes de suppression correspondantes, appelées si l’utilisateur confirme la suppression :

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [!IMPORTANT]
> N’oubliez pas que les ressources de calcul vous sont facturées et que la suppression des pools inutilisés vous permet de réduire les coûts. Tenez également compte du fait que la suppression d’un pool permet la suppression de tous les nœuds de calcul de ce pool, et que toutes les données sur les nœuds seront irrécupérables une fois le pool supprimé.
>
>

## <a name="run-the-dotnettutorial-sample"></a>Exécuter l’exemple *DotNetTutorial*
Lorsque vous exécutez l’exemple d’application, la sortie de la console est identique à ce qui suit. Pendant l’exécution, l’étape `Awaiting task completion, timeout in 00:30:00...` fait l’objet d’une pause correspondant au démarrage des nœuds de calcul du pool. Utilisez le [portail Azure][azure_portal] pour surveiller le pool, les nœuds de calcul, le travail et les tâches pendant et après l’exécution. Utilisez le [portail Azure][azure_portal] ou l’un des [explorateurs Stockage Azure][storage_explorers] disponibles pour visualiser les ressources de stockage (conteneurs et objets blob) créées par l’application.

Le temps d’exécution standard de l’application est **d’environ 5 minutes** lorsque l’application fonctionne dans sa configuration par défaut.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez apporter des modifications à *DotNetTutorial* et à *TaskApplication* pour tester différents scénarios de calcul. Essayez par exemple d’ajouter une suspension d’exécution à *TaskApplication*, par exemple avec [Thread.Sleep][net_thread_sleep], pour simuler des tâches de longue durée et les surveiller dans le portail. Essayez d’ajouter davantage de tâches ou d’ajuster le nombre de nœuds de calcul. Ajoutez une logique pour rechercher un pool existant et en autoriser l’utilisation afin d’accélérer le processus d’exécution (*astuce* : consultez le fichier `ArticleHelpers.cs` du projet [Microsoft.Azure.Batch.Samples.Common][github_samples_common] dans le référentiel [azure-batch-samples][github_samples]).

À présent que vous voici familiarisé avec le flux de travail de base d’une solution Batch, il est temps pour vous d’approfondir les fonctionnalités supplémentaires du service Batch.

* Consultez l’article [Présentation des fonctionnalités de présentation d’Azure Batch](batch-api-basics.md) , que nous vous recommandons si vous ne connaissez pas le service.
* Commencez par les autres articles de développement Batch sous **Développement approfondi** dans le [parcours d’apprentissage Batch][batch_learning_path].
* Consultez une autre implémentation du traitement de la charge de travail « N premiers mots » en utilisant Batch dans l’exemple [TopNWords][github_topnwords].
* Examinez les [notes de publication](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/Batch/DataPlane/changelog.md#azurebatch-release-notes) de .NET Batch pour découvrir les dernières modifications de la bibliothèque.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchlabs]: https://azure.github.io/BatchLabs/
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/vs/
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Créer des conteneurs dans le Stockage Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Charger les fichiers d’application de tâche et les fichiers (de données) d’entrée dans les conteneurs"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Créer un pool Batch"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Créer un travail Batch"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Ajouter des tâches au travail"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Surveiller les tâches"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Télécharger la sortie des tâches à partir de Storage"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Flux de travail de la solution Batch (diagramme complet)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Informations d’identification de compte Batch dans le portail"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Informations d’identification de compte de stockage dans le portail"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Flux de travail de la solution Batch (diagramme minimal)"
