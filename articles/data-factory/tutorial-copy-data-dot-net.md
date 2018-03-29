---
title: Copier des données à partir du Stockage Blob Azure vers SQL Database | Microsoft Docs
description: Ce didacticiel fournit les instructions pas à pas permettant de copier des données à partir du Stockage Blob Azure vers Azure SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: bfbafa2edb1d9195760a99f63113d28d3a978a78
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Copier des données à partir d’un objet blob Azure vers Azure SQL Database à l’aide d’Azure Data Factory
Dans ce didacticiel, vous créez un pipeline Azure Data Factory qui copie des données depuis le Stockage Blob Azure vers Azure SQL Database. Le modèle de configuration de ce didacticiel s’applique à la copie depuis un magasin de données de fichiers vers un magasin de données relationnelles. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer les services liés Stockage Azure et Azure SQL Database.
> * Créer des jeux de données d’objet blob Azure et Azure SQL Database.
> * Créer un pipeline contenant une activité de copie.
> * Démarrer une exécution de pipeline.
> * Surveiller les exécutions de pipeline et d’activité.

Ce didacticiel utilise le SDK .NET. Vous pouvez utiliser d’autres mécanismes pour interagir avec Azure Data Factory. Consultez les exemples dans « Démarrages rapides ».

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis


* **Compte Stockage Azure**. Vous utilisez le stockage blob comme magasins de données **source**. Si vous n’avez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour découvrir comment en créer un.
* **Base de données SQL Azure**. Vous utilisez la base de données en tant que magasin de données **récepteur**. Si vous n’avez pas de base de données Azure SQL Database, consultez l’article [Création d’une base de données Azure SQL](../sql-database/sql-database-get-started-portal.md) pour savoir comme en créer une.
* **Visual Studio** 2015 ou 2017. La procédure pas à pas dans cet article utilise Visual Studio 2017.
* **Téléchargez et installez le [SDK Azure .NET](http://azure.microsoft.com/downloads/)**.
* **Créez une application dans Azure Active Directory** en suivant [cette instruction](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Notez les valeurs suivantes que vous utiliserez lors d’étapes ultérieures : **ID d’application**, **clé d’authentification** et **ID de locataire**. Affectez l’application au rôle « **Contributeur**  » en suivant les instructions dans le même article.

### <a name="create-a-blob-and-a-sql-table"></a>Créer un objet blob et une table SQL

À présent, préparez votre objet blob Azure et Azure SQL Database pour ce didacticiel, en effectuant les étapes suivantes :

#### <a name="create-a-source-blob"></a>Créer un objet blob source

1. Lancez le Bloc-notes. Copiez le texte suivant et enregistrez-le comme fichier **inputEmp.txt** sur votre disque.

    ```
    John|Doe
    Jane|Doe
    ```

2. Utilisez des outils comme l’[explorateur Stockage Azure](http://storageexplorer.com/) pour créer le conteneur **adfv2tutorial** et charger le fichier **inputEmp.txt** sur ce dernier.

#### <a name="create-a-sink-sql-table"></a>Créer une table SQL de récepteur

1. Utilisez le script SQL suivant pour créer la table **dbo.emp** dans Azure SQL Database.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Autorisez les services Azure à accéder au serveur SQL. Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est **ACTIVÉ** pour votre serveur SQL Azure pour que le service Data Factory puisse écrire des données sur votre serveur SQL Azure. Pour vérifier et activer ce paramètre, procédez comme suit :

    1. Cliquez sur le hub **Plus de services** situé à gauche, puis sur **Serveurs SQL**.
    2. Sélectionnez votre serveur, puis cliquez sur **Pare-feu** sous **PARAMÈTRES**.
    3. Dans la page **Paramètres de pare-feu**, cliquez sur **ACTIVER** pour **Autoriser l’accès aux services Azure**.


## <a name="create-a-visual-studio-project"></a>Créer un projet Visual Studio

À l’aide de Visual Studio 2015/2017, créez une application console C# .NET.

1. Lancez **Visual Studio**.
2. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**.
3. Sélectionnez **Visual C#** -> **Application console (.NET Framework)** dans la liste des types de projets située sur la droite. .NET version 4.5.2 ou ultérieure est nécessaire.
4. Entrez **ADFv2Tutorial** pour le nom.
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
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
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
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
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

## <a name="create-linked-services"></a>Créez des services liés

Dans ce didacticiel, vous créez deux services liés pour la source et le récepteur respectivement :

### <a name="create-an-azure-storage-linked-service"></a>Créer un service lié Stockage Azure

Ajoutez le code suivant à la méthode **Main** qui crée un **service lié Stockage Azure**. Découvrez-en plus à partir des [propriétés du service lié Stockage Azure](connector-azure-blob-storage.md#linked-service-properties) sur les propriétés et détails pris en charge.

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

### <a name="create-an-azure-sql-database-linked-service"></a>Créer un service lié Azure SQL Database

Ajoutez le code suivant à la méthode **Main** qui crée un **service lié Azure SQL Database**. Découvrez-en plus à partir des [propriétés du service lié Azure SQL Database](connector-azure-sql-database.md#linked-service-properties) sur les propriétés et détails pris en charge.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>Créez les jeux de données

Dans cette section, vous créez deux jeux de données : un pour la source et l’autre pour le récepteur. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Créer un jeu de données pour l’objet blob Azure source

Ajoutez le code suivant à la méthode **Main** qui crée un **jeu de données d’objet blob Azure**. Découvrez-en plus à partir des [propriétés du jeu de données d’objet blob Azure](connector-azure-blob-storage.md#dataset-properties) sur les propriétés et détails pris en charge.

Vous définissez un jeu de données qui représente les données sources dans l’objet blob Azure. Ce jeu de données d’objet blob fait référence au service lié Stockage Azure que vous avez créé à l’étape précédente et décrit :

- l’emplacement de l’objet blob à copier à partir de : **FolderPath** et **NomFichier** ;
- le format d’objet blob qui indique comment analyser le contenu : **TextFormat** et ses paramètres (par exemple, délimiteur de colonne) ;
- la structure de données, y compris les noms de colonne et les types de données qui dans ce cas sont mappés à la table SQL du récepteur.

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
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Créer un jeu de données pour Azure SQL Database récepteur

Ajoutez le code suivant à la méthode **Main** qui crée un **jeu de données Azure SQL Database**. Découvrez-en plus à partir des [propriétés du jeu de données Azure SQL Database](connector-azure-sql-database.md#dataset-properties) sur les propriétés et détails pris en charge.

Vous définissez un jeu de données qui représente les données du récepteur dans Azure SQL Database. Ce jeu de données fait référence au service lié Azure SQL Database que vous avez créé à l’étape précédente. Il spécifie également la table SQL qui contient les données copiées. 

```csharp
// Create a Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Créer un pipeline

Ajoutez le code suivant à la méthode **Main** qui crée un **pipeline avec une activité de copie**. Dans ce didacticiel, ce pipeline contient une seule activité : une activité de copie, qui accepte le jeu de données d’objet blob en tant que source et le jeu de données SQL en tant que récepteur. Découvrez-en plus sur les détails de l’activité de copie dans la [vue d’ensemble de l’activité de copie](copy-activity-overview.md).

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Créer une exécution du pipeline

Ajoutez le code suivant à la méthode **Main** qui **déclenche une exécution du pipeline**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Surveiller une exécution du pipeline

1. Ajoutez le code suivant à la méthode **Main** afin de vérifier en permanence l’état de l’exécution du pipeline jusqu’à la fin de la copie des données.

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
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);
    
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Exécuter le code

Créez et démarrez l’application, puis vérifiez l’exécution du pipeline.

La console affiche la progression de la création d’une fabrique de données, d’un service lié, de jeux de données, du pipeline et de l’exécution du pipeline. Elle vérifie ensuite l’état de l’exécution du pipeline. Patientez jusqu’à l’affichage des détails de l’exécution de l’activité de copie avec la taille des données lues/écrites. Ensuite, utilisez des outils tels que SSMS (SQL Server Management Studio) ou Visual Studio pour vous connecter à votre Azure SQL Database de destination et vérifier si les données sont copiées dans la table que vous avez spécifiée.

### <a name="sample-output"></a>Exemple de sortie

```json
Creating a data factory AdfV2Tutorial...
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
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
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
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedCloudDataMovementUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, le pipeline copie les données d’un emplacement vers un autre dans un stockage Blob Azure. Vous avez appris à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer les services liés Stockage Azure et Azure SQL Database.
> * Créer des jeux de données d’objet blob Azure et Azure SQL Database.
> * Créer un pipeline contenant une activité de copie.
> * Démarrer une exécution de pipeline.
> * Surveiller les exécutions de pipeline et d’activité.


Passez au didacticiel suivant pour en savoir plus sur la copie des données locales vers le cloud : 

> [!div class="nextstepaction"]
>[Copier des données locales vers le cloud](tutorial-hybrid-copy-powershell.md)
