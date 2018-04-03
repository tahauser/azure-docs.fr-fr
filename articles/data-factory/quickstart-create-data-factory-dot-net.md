---
title: Créer une fabrique de données Azure avec .NET | Microsoft Docs
description: Créez une fabrique de données Azure pour copier les données d’un emplacement dans le stockage Blob Azure vers un autre emplacement.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/28/2018
ms.author: jingwang
ms.openlocfilehash: c5b7af290a5e5c45d3f64ccb50586db0811dd592
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>Créer une fabrique de données et un pipeline avec le kit .NET SDK
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 - Préversion](quickstart-create-data-factory-dot-net.md)

Ce guide de démarrage rapide explique comment utiliser le kit SDK .NET pour créer une fabrique de données Azure. Le pipeline que vous créez dans cette fabrique de données **copie** les données d’un dossier vers un autre dossier dans un stockage Blob Azure. Pour suivre un didacticiel sur la **transformation** des données à l’aide d’Azure Data Factory, consultez [Didacticiel : transformation des données à l’aide de Spark](transform-data-using-spark.md). 

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Prise en main de Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Cet article ne fournit pas de présentation détaillée du service Data Factory. Pour une présentation du service Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis


### <a name="azure-subscription"></a>Abonnement Azure
Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

### <a name="azure-roles"></a>Rôles Azure
Pour créer des instances de fabrique de données, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être un membre des rôles **contributeur** ou **propriétaire**, ou un **administrateur** de l’abonnement Azure. Dans le portail Azure, cliquez sur votre **nom d’utilisateur** dans le coin supérieur droit, puis sélectionnez **Autorisations** pour afficher les autorisations dont vous disposez dans l’abonnement. Si vous avez accès à plusieurs abonnements, sélectionnez l’abonnement approprié. Pour des exemples d’instructions concernant l’ajout d’un utilisateur à un rôle, consultez l’article [Ajout de rôles](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Compte Stockage Azure
Dans ce guide de démarrage rapide, vous allez utiliser un compte Stockage Azure (un compte Stockage Blob, plus précisément) à usage général à la fois comme banque de données **source** et de **destination**. Si vous ne possédez pas de compte Stockage Azure à usage général, consultez [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour en créer un. 

#### <a name="get-storage-account-name-and-account-key"></a>Obtenir le nom de compte de stockage et la clé de compte
Dans ce guide de démarrage rapide, vous spécifiez le nom et la clé de votre compte Stockage Azure. La procédure suivante détaille les étapes à suivre pour obtenir le nom et la clé de votre compte de stockage. 

1. Lancez un navigateur web et accédez au [portail Azure](https://portal.azure.com). Connectez-vous en utilisant un nom d’utilisateur et un mot de passe Azure. 
2. Cliquez sur **Plus de services >** dans le menu de gauche, filtrez en utilisant le mot clé **Stockage**, puis sélectionnez **Comptes de stockage**.

    ![Rechercher le compte de stockage](media/quickstart-create-data-factory-dot-net/search-storage-account.png)
3. Dans la liste des comptes de stockage, appliquez un filtre pour votre compte de stockage (si nécessaire), puis sélectionnez **votre compte de stockage**. 
4. Dans la page **Compte de stockage**, sélectionnez **Clés d’accès** dans le menu.

    ![Obtenir le nom et la clé du compte de stockage](media/quickstart-create-data-factory-dot-net/storage-account-name-key.png)
5. Copiez les valeurs des champs **Nom du compte de stockage** et **key1** dans le presse-papiers. Collez-les dans un bloc-notes ou tout autre éditeur et enregistrez-le.  

#### <a name="create-input-folder-and-files"></a>Créer les dossiers et les fichiers d’entrée
Dans cette section, vous allez créer un conteneur d’objets blob nommé **adftutorial** dans votre stockage Blob Azure. Ensuite, vous créerez un dossier nommé **input** (entrée) dans le conteneur et chargerez un exemple de fichier dans ce dossier. 

1. Dans la page **Compte de stockage**, basculez vers la **vue d’ensemble**, puis cliquez sur **Objets blob**. 

    ![Sélection de l’option Objets blob](media/quickstart-create-data-factory-dot-net/select-blobs.png)
2. Dans la page **Service BLOB**, cliquez sur **+ Conteneur** dans la barre d’outils. 

    ![Bouton d’ajout de conteneur](media/quickstart-create-data-factory-dot-net/add-container-button.png)    
3. Dans la boîte de dialogue **Nouveau conteneur**, saisissez le nom **adftutorial**, puis cliquez sur **OK**. 

    ![Saisie du nom du conteneur](media/quickstart-create-data-factory-dot-net/new-container-dialog.png)
4. Cliquez sur **adftutorial** dans la liste des conteneurs. 

    ![Sélection du conteneur](media/quickstart-create-data-factory-dot-net/select-adftutorial-container.png)
1. Dans la page **Conteneur**, cliquez sur **Charger** dans la barre d’outils.  

    ![Bouton Télécharger](media/quickstart-create-data-factory-dot-net/upload-toolbar-button.png)
6. Dans la page **Charger l’objet blob**, cliquez sur **Avancé**.

    ![Clic sur le lien Avancé](media/quickstart-create-data-factory-dot-net/upload-blob-advanced.png)
7. Lancez le **Bloc-notes** et créez un fichier JSON nommé **emp.txt** avec le contenu ci-dessous. Enregistrez-le dans le dossier **c:\ADFv2QuickStartPSH** (créez le dossier **ADFv2QuickStartPSH** s’il n’existe pas déjà).
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. Dans le portail Azure, dans la page **Charger l’objet blob**, recherchez et sélectionnez le fichier **emp.txt** pour le champ **Fichiers**. 
9. Entrez **input** dans le champ **Charger dans le dossier**. 

    ![Paramètres de chargement de l’objet blob](media/quickstart-create-data-factory-dot-net/upload-blob-settings.png)    
10. Vérifiez que le dossier est **input** et que le fichier est **emp.txt**, puis cliquez sur **Charger**.
11. Vous devriez voir le fichier **emp.txt** et l’état du chargement dans la liste. 
12. Fermez la page **Charger l’objet blob** en cliquant sur **X** en haut à droite. 

    ![Fermeture de la page Charger l’objet blob](media/quickstart-create-data-factory-dot-net/close-upload-blob.png)
1. Laissez la page **Conteneur** ouverte. Vous l’utiliserez pour vérifier la sortie à la fin de ce guide de démarrage rapide.

### <a name="visual-studio"></a>Visual Studio
La procédure pas à pas de cet article utilise Visual Studio 2017. Vous pouvez également utiliser Visual Studio 2013 ou 2015.

### <a name="azure-net-sdk"></a>Kit de développement logiciel (SDK) .NET Azure
Téléchargez et installez le [Kit de développement logiciel (SDK) .NET Azure](http://azure.microsoft.com/downloads/) sur votre machine.

### <a name="create-an-application-in-azure-active-directory"></a>Créer une application dans Azure Active Directory
Suivez les instructions fournies dans les sections de [cet article](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) pour accomplir les tâches suivantes : 

1. **Créez une application Azure Active Directory**. Créez une application dans Azure Active Directory représentant l’application .NET que vous créez dans ce didacticiel. Pour l’URL de connexion, vous pouvez fournir une URL factice, comme indiqué dans l’article (`https://contoso.org/exampleapp`).
2. Obtenez l’**ID de l’application** et la **clé d’authentification**, puis notez ces valeurs. Elles vous serviront plus loin dans ce didacticiel. 
3. Obtenez l’**ID d’abonné** et notez cette valeur. Elle vous servira plus loin dans ce didacticiel.
4. Affectez l’application au rôle **Contributeur** au niveau de l’abonnement afin que l’application puisse créer des fabriques de données dans l’abonnement.

## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

À l’aide de Visual Studio 2013/2015/2017, créez une application console C# .NET.

1. Lancez **Visual Studio**.
2. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**.
3. Sélectionnez **Visual C#** -> **Application console (.NET Framework)** dans la liste des types de projets située sur la droite. .NET version 4.5.2 ou ultérieure est nécessaire.
4. Entrez **ADFv2QuickStart** pour le nom.
5. Cliquez sur **OK** pour créer le projet.

## <a name="install-nuget-packages"></a>Installer les packages NuGet

1. Cliquez sur **Outils** -> **Gestionnaire de package NuGet** -> **Console du gestionnaire de package**.
2. Dans la **console du Gestionnaire de package**, exécutez les commandes suivantes pour installer les packages :

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

    ```

## <a name="create-a-data-factory-client"></a>Créer un client de fabrique de données

1. Ouvrez **Program.cs**, insérez les instructions suivantes pour ajouter des références aux espaces de noms.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Ajoutez le code suivant à la méthode **Main** qui définit les variables. Remplacez les espaces réservés par vos propres valeurs. À l’heure actuelle, Data Factory version 2 vous permet de créer des fabriques de données uniquement dans les régions Est des États-Unis, Est des États-Unis 2 et Europe de l’Ouest. Les magasins de données (Stockage Azure, Azure SQL Database, etc.) et les services de calcul (HDInsight, etc.) utilisés par la fabrique de données peuvent se trouver dans d’autres régions.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
    string region = "East US 2";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    // specify the container and input folder from which all files need to be copied to the output folder. 
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/foldername>";
    //specify the contains and output folder where the files are copied
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/foldername>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
    ```

3. Ajoutez le code suivant à la méthode **Main** qui crée une instance de la classe **DataFactoryManagementClient**. Cet objet vous permet de créer une fabrique de données, un service lié, des jeux de données ainsi qu’un pipeline. Cet objet vous permet également de surveiller les détails de l’exécution du pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Ajoutez le code suivant à la méthode **Main** qui crée une **fabrique de données**. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Créer un service lié

Ajoutez le code suivant à la méthode **Main** qui crée un **service lié Stockage Azure**.

Vous allez créer des services liés dans une fabrique de données pour lier vos magasins de données et vos services de calcul à la fabrique de données. Dans ce guide de démarrage rapide, vous devez uniquement créer un service lié Stockage Azure à la fois pour la source de copie et le magasin récepteur, nommé « AzureStorageLinkedService » dans l’exemple.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Créer un jeu de données

Ajoutez le code suivant à la méthode **Main** qui crée un **jeu de données d’objet blob Azure**.

Vous définissez un jeu de données qui représente les données à copier d’une source vers un récepteur. Dans cet exemple, ce jeu de données d’objet blob fait référence au service lié Stockage Azure que vous avez créé à l’étape précédente. Le jeu de données prend un paramètre dont la valeur est définie dans une activité qui consomme le jeu de données. Le paramètre est utilisé pour construire le « FolderPath » pointant vers l’emplacement où les données résident/sont stockées.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Créer un pipeline

Ajoutez le code suivant à la méthode **Main** qui crée un **pipeline avec une activité de copie**.

Dans cet exemple, ce pipeline contient une activité et accepte deux paramètres : chemin de l’objet blob d’entrée et chemin de l’objet blob de sortie. Les valeurs de ces paramètres sont définies quand le pipeline est déclenché/exécuté. L’activité de copie fait référence au même jeu de données d’objet blob créé à l’étape précédente comme entrée et sortie. Quand le jeu de données est utilisé comme jeu de données d’entrée, le chemin d’entrée est spécifié. De même, quand le jeu de données est utilisé comme jeu de données de sortie, le chemin de sortie est spécifié. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Créer une exécution du pipeline

Ajoutez le code suivant à la méthode **Main** qui **déclenche une exécution du pipeline**.

Ce code définit également les valeurs des paramètres **inputPath** et **outputPath** spécifiés dans le pipeline avec les valeurs réelles des chemins des objets blob source et récepteur.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Surveiller une exécution du pipeline

1. Ajoutez le code suivant à la méthode **Main** afin de vérifier continuellement l’état jusqu’à la fin de la copie des données.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Ajoutez le code suivant à la méthode **Main** qui récupère les détails de l’exécution de l’activité de copie, par exemple la taille des données lues/écrites.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Exécuter le code

Créez et démarrez l’application, puis vérifiez l’exécution du pipeline.

La console affiche la progression de la création de la fabrique de données, du service lié, des jeux de données, du pipeline et de l’exécution du pipeline. Elle vérifie ensuite l’état de l’exécution du pipeline. Patientez jusqu’à l’affichage des détails de l’exécution de l’activité de copie avec la taille des données lues/écrites. Utilisez ensuite des outils comme l’[explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour vérifier que les objets blob sont copiés dans « outputBlobPath » depuis « inputBlobPath » comme vous l’avez spécifié dans les variables.

### <a name="sample-output"></a>Exemple de sortie : 
```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Vérifier la sortie
Le pipeline crée automatiquement le dossier de sortie dans le conteneur d’objets blob adftutorial. Ensuite, il copie le fichier emp.txt à partir du dossier d’entrée dans le dossier de sortie. 

1. Dans le portail Azure, dans la page du conteneur **adftutorial**, cliquez sur **Actualiser** pour afficher le dossier de sortie (nommé output). 
    
    ![Actualiser](media/quickstart-create-data-factory-dot-net/output-refresh.png)
2. Cliquez sur **output** dans la liste des dossiers. 
2. Vérifiez que le fichier **emp.txt** a été copié dans le dossier de sortie. 

    ![Actualiser](media/quickstart-create-data-factory-dot-net/output-file.png)

## <a name="clean-up-resources"></a>Supprimer des ressources
Pour supprimer par programmation la fabrique de données, ajoutez les lignes de code suivantes au programme : 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Consultez les [didacticiels](tutorial-copy-data-dot-net.md) pour en savoir plus sur l’utilisation de Data Factory dans d’autres scénarios. 
