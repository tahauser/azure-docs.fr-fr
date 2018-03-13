---
title: "Création de branches dans un pipeline Azure Data Factory | Microsoft Docs"
description: "Découvrez comment contrôler le flux de données dans Azure Data Factory à l’aide d’activités de création de branches et de chaînage."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 8259c1bd52cfd0641148dc09404debaf59640b45
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Activités de création de branches et chaînage dans un pipeline Azure Data Factory
Dans ce didacticiel, vous créez un pipeline Data Factory qui présente certaines des fonctionnalités de flux de contrôle. Ce pipeline est une simple copie depuis un conteneur Stockage Blob Azure vers un autre conteneur dans le même compte de stockage. Si l’activité de copie réussit, vous envoyez les détails de l’opération de copie réussie (par exemple, la quantité de données écrites) dans un e-mail d’avis de réussite. Si l’activité de copie échoue, vous envoyez les détails de l’échec de la copie (par exemple, le message d’erreur) dans un e-mail d’avis d’échec. Tout au long de ce didacticiel, vous allez apprendre à passer des paramètres.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Vue d’ensemble du scénario : ![vue d’ensemble](media/tutorial-control-flow/overview.png)

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un service lié Stockage Azure
> * Créer un jeu de données d’objet blob Azure
> * Créer un pipeline qui contient une activité de copie et une activité web
> * Envoyer les sorties des activités aux activités ultérieures
> * Utiliser des variables système et de passage de paramètres
> * Démarrer une exécution de pipeline
> * Surveiller les exécutions de pipeline et d’activité

Ce didacticiel utilise le kit .NET SDK. Vous pouvez utiliser d’autres mécanismes pour interagir avec Azure Data Factory. Consultez « Guides de démarrage rapide » dans la table des matières.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Composants requis

* **Compte Stockage Azure**. Vous utilisez le stockage blob comme magasins de données **source**. Si vous n’avez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour découvrir comment en créer un.
* **Base de données SQL Azure**. Vous utilisez la base de données en tant que magasin de données **récepteur**. Si vous n’avez pas de base de données Azure SQL Database, consultez l’article [Création d’une base de données Azure SQL](../sql-database/sql-database-get-started-portal.md) pour savoir comme en créer une.
* **Visual Studio** 2013, 2015 ou 2017. La procédure pas à pas de cet article utilise Visual Studio 2017.
* **Téléchargez et installez le kit [Azure .NET SDK](http://azure.microsoft.com/downloads/)**.
* **Créez une application dans Azure Active Directory** en suivant [ces instructions](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Notez les valeurs suivantes que vous utiliserez lors d’étapes ultérieures : **ID d’application**, **clé d’authentification** et **ID de locataire**. Affectez l’application au rôle « **Contributeur**  » en suivant les instructions dans le même article.

### <a name="create-blob-table"></a>Créer la table d’objets blob

1. Lancez le Bloc-notes. Copiez le texte suivant et enregistrez-le comme fichier **input.txt** sur votre disque.

    ```
    John|Doe
    Jane|Doe
    ```
2. Utilisez des outils comme l’[explorateur Stockage Azure](http://storageexplorer.com/) pour créer le conteneur **adfv2branch** et charger le fichier **input.txt** sur ce dernier.

## <a name="create-visual-studio-project"></a>Création d’un projet Visual Studio

À l’aide de Visual Studio 2015/2017, créez une application console C# .NET.

1. Lancez **Visual Studio**.
2. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**. .NET version 4.5.2 ou ultérieure est nécessaire.
3. Sélectionnez **Visual C#** -> **Application console (.NET Framework)** dans la liste des types de projets située sur la droite.
4. Entrez **ADFv2BranchTutorial** pour le nom.
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

2. Add these static variables to the **Program class**. Replace place-holders with your own values. Currently, Data Factory V2 allows you to create data factories only in the East US, East US2, and West Europe regions. The data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions.

    ```csharp
        // Set variables
        static string tenantID = "<tenant ID>";
        static string applicationId = "<application ID>";
        static string authenticationKey = "<Authentication key for your application>";
        static string subscriptionId = "<Azure subscription ID>";
        static string resourceGroup = "<Azure resource group name>";

        static string region = "East US";
        static string dataFactoryName = "<Data factory name>";

        // Specify the source Azure Blob information
        static string storageAccount = "<Azure Storage account name>";
        static string storageKey = "<Azure Storage account key>";
        // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
        static string inputBlobPath = "adfv2branch/input";
        static string inputBlobName = "input.txt";
        static string outputBlobPath = "adfv2branch/output";
        static string emailReceiver = "<specify email address of the receiver>";

        static string storageLinkedServiceName = "AzureStorageLinkedService";
        static string blobSourceDatasetName = "SourceStorageDataset";
        static string blobSinkDatasetName = "SinkStorageDataset";
        static string pipelineName = "Adfv2TutorialBranchCopy";

        static string copyBlobActivity = "CopyBlobtoBlob";
        static string sendFailEmailActivity = "SendFailEmailActivity";
        static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
    
    ```

3. Ajoutez le code suivant à la méthode **Main** qui crée une instance de la classe **DataFactoryManagementClient**. Cet objet vous permet de créer la fabrique de données, un service lié, des jeux de données ainsi qu’un pipeline. Cet objet vous permet également de surveiller les détails de l’exécution du pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Créer une fabrique de données
Créez une fonction « CreateOrUpdateDataFactory » dans votre fichier Program.cs :

```csharp
static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating data factory " + dataFactoryName + "...");
    Factory resource = new Factory
    {
        Location = region
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

    Factory response;
    {
        response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
    }

    while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
    {
        System.Threading.Thread.Sleep(1000);
    }
    return response;
}
```



Ajoutez le code suivant à la méthode **Main** qui crée une **fabrique de données**. 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Créer un service lié Stockage Azure
Créez une fonction « StorageLinkedServiceDefinition » dans votre fichier Program.cs :

```csharp
static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
    AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
    LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
    return linkedService;
}
```
Ajoutez le code suivant à la méthode **Main** qui crée un **service lié Stockage Azure**. Découvrez-en plus à partir des [propriétés du service lié Stockage Azure](connector-azure-blob-storage.md#linked-service-properties) sur les propriétés et détails pris en charge.

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>Créez les jeux de données

Dans cette section, vous créez deux jeux de données : un pour la source et l’autre pour le récepteur. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Créer un jeu de données pour l’objet blob Azure source
Ajoutez le code suivant à la méthode **Main** qui crée un **jeu de données d’objet blob Azure**. Découvrez-en plus à partir des [propriétés du jeu de données d’objet blob Azure](connector-azure-blob-storage.md#dataset-properties) sur les propriétés et détails pris en charge.

Vous définissez un jeu de données qui représente les données sources dans l’objet blob Azure. Ce jeu de données d’objet blob fait référence au service lié Stockage Azure que vous avez créé à l’étape précédente et décrit :

- l’emplacement de l’objet blob à copier à partir de : **FolderPath** et **NomFichier** ;
- Notez l’utilisation de paramètres pour FolderPath. « sourceBlobContainer » est le nom du paramètre et l’expression est remplacée par les valeurs passées dans l’exécution du pipeline. La syntaxe pour définir des paramètres est `@pipeline().parameters.<parameterName>`

Créer une fonction « SourceBlobDatasetDefinition » dans votre fichier Program.cs

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Créer un jeu de données pour l’objet blob Azure récepteur

Créer une fonction « SourceBlobDatasetDefinition » dans votre fichier Program.cs

```csharp
static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
    return dataset;
}
```

Ajoutez le code suivant à la méthode **Main** qui crée les deux jeux de données source et récepteur de l’objet blob Azure. 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>Créer une classe C# : EmailRequest
Dans votre projet C#, créez une classe nommée **EmailRequest**. Elle définit les propriétés que le pipeline envoie dans le corps de la requête lors de l’envoi d’un e-mail. Dans ce didacticiel, le pipeline envoie quatre propriétés à l’adresse e-mail :

- **Message** : corps de l’e-mail. Dans le cas d’une copie réussie, cette propriété contient les détails de l’exécution (quantité de données écrites). Dans le cas d’une copie ayant échoué, cette propriété contient les détails de l’erreur.
- **Nom de la fabrique de données** : nom de la fabrique de données.
- **Nom du pipeline** : nom du pipeline.
- **Destinataire** : paramètre passé. Cette propriété spécifie le destinataire de l’e-mail.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```
## <a name="create-email-workflow-endpoints"></a>Créer des points de terminaison de flux de travail d’e-mail
Pour déclencher l’envoi d’un e-mail, vous utilisez [Logic Apps](../logic-apps/logic-apps-overview.md) pour définir le flux de travail. Pour plus d’informations sur la création d’un flux de travail Logic Apps, consultez [Guide pratique pour créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

### <a name="success-email-workflow"></a>Flux de travail d’un e-mail d’avis de réussite 
Créez un flux de travail Logic Apps nommé `CopySuccessEmail`. Définissez le déclencheur du flux de travail comme `When an HTTP request is received` et ajoutez une action `Office 365 Outlook – Send an email`.

![Flux de travail d’un e-mail d’avis de réussite](media/tutorial-control-flow/success-email-workflow.png)

Pour le déclencheur de votre requête, renseignez `Request Body JSON Schema` avec le texte JSON suivant :

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```
Il correspond à la classe **EmailRequest** que vous avez créée dans la section précédente. 

Dans le concepteur d’application logique, votre requête doit ressembler à ceci :

![Concepteur d’application logique - requête](media/tutorial-control-flow/logic-app-designer-request.png)

Pour l’action **Envoyer un e-mail**, personnalisez le mode de mise en forme de l’e-mail, en utilisant les propriétés passées dans le schéma JSON du corps de la requête. Voici un exemple : 

![Concepteur d’application logique - action Envoyer un e-mail](media/tutorial-control-flow/send-email-action.png)

Prenez note de l’URL de votre requête HTTP Post pour votre flux de travail d’e-mail d’avis de réussite :

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>Flux de travail d’un e-mail d’avis d’échec 
Clonez votre **CopySuccessEmail** et créez un autre flux de travail Logic Apps **CopyFailEmail**. Dans le déclencheur de la requête, `Request Body JSON schema` est identique. Modifiez simplement la mise en forme de votre e-mail, notamment `Subject`, pour l’adapter à un avis d’échec. Voici un exemple : 

![Concepteur d’application logique - flux de travail d’un e-mail d’avis d’échec](media/tutorial-control-flow/fail-email-workflow.png)

Prenez note de l’URL de votre requête HTTP Post pour votre flux de travail d’e-mail d’avis d’échec :

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Vous devez maintenant avoir deux URL de flux de travail :

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```
## <a name="create-a-pipeline"></a>Créer un pipeline
Ajoutez le code suivant à la méthode Main qui crée un pipeline avec une activité de copie et une propriété dependsOn. Dans ce didacticiel, le pipeline contient une seule activité : une activité de copie, qui accepte le jeu de données d’objet blob en tant que source et un autre jeu de données d’objet blob en tant que récepteur. Selon que l’activité de copie réussit ou échoue, différentes tâches d’e-mail sont appelées.

Dans ce pipeline, vous utilisez les fonctionnalités suivantes :

- parameters
- Activité web
- Dépendance de l’activité
- Utilisation de la sortie d’une activité en tant qu’entrée de l’activité suivante

Décomposons le pipeline suivant section par section :

```csharp

static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
        {
            Console.WriteLine("Creating pipeline " + pipelineName + "...");
            PipelineResource resource = new PipelineResource
            {
                Parameters = new Dictionary<string, ParameterSpecification>
                {
                    { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                },
                Activities = new List<Activity>
                {
                    new CopyActivity
                    {
                        Name = copyBlobActivity,
                        Inputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSourceDatasetName
                            }
                        },
                        Outputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSinkDatasetName
                            }
                        },
                        Source = new BlobSource { },
                        Sink = new BlobSink { }
                    },
                    new WebActivity
                    {
                        Name = sendSuccessEmailActivity,
                        Method = WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Succeeded" }
                            }
                        }
                    },
                    new WebActivity
                    {
                        Name = sendFailEmailActivity,
                        Method =WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Failed" }
                            }
                        }
                    }
                }
            };
            Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
            return resource;
        }
```
Ajoutez le code suivant à la méthode **Main** qui crée le pipeline :

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```
### <a name="parameters"></a>parameters
La première section de notre pipeline définit les paramètres. 

- sourceBlobContainer - paramètre dans le pipeline consommé par le jeu de données d’objet blob source.
- sinkBlobContainer - paramètre dans le pipeline consommé par le jeu de données d’objet blob récepteur.
- receiver - ce paramètre est utilisé par les deux activités web dans le pipeline qui envoient des messages électroniques de réussite ou d’échec au récepteur dont l’adresse e-mail est spécifiée par ce paramètre.


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```
### <a name="web-activity"></a>Activité web
L’activité web permet d’appeler n’importe quel point de terminaison REST. Pour plus d’informations sur l’activité, consultez [Activité web](control-flow-web-activity.md). Ce pipeline utilise une activité web pour appeler le flux de travail d’un e-mail Logic Apps. Vous créez deux activités web : une qui appelle le flux de travail **CopySuccessEmail** et une autre qui appelle **CopyFailWorkFlow**.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```
Dans la propriété « Url », collez les points de terminaison d’URL de requête de votre flux de travail Logic Apps en conséquence. Dans la propriété « Body », passez une instance de la classe « EmailRequest ». La requête d’e-mail contient les propriétés suivantes :

- Message - Passage de la valeur `@{activity('CopyBlobtoBlob').output.dataWritten`. Accède à une propriété de l’activité de copie précédente et passe la valeur de dataWritten. Pour un échec, passez la sortie de l’erreur au lieu de `@{activity('CopyBlobtoBlob').error.message`.
- Nom de la fabrique de données - Passage de la valeur `@{pipeline().DataFactory}`. Il s’agit d’une variable système, qui vous permet d’accéder au nom de la fabrique de données correspondante. Consultez l’article [Variables système](control-flow-system-variables.md) pour obtenir la liste des variables système.
- Nom du pipeline : Passage de la valeur `@{pipeline().Pipeline}`. Il s’agit également d’une variable système, qui vous permet d’accéder au nom du pipeline correspondant. 
- Récepteur - Passage de la valeur "@pipeline().parameters.receiver"). Accès aux paramètres de pipeline.
 
Ce code crée une dépendance de l’activité, en fonction de l’activité de copie précédente réussie.

## <a name="create-a-pipeline-run"></a>Créer une exécution du pipeline
Ajoutez le code suivant à la méthode **Main** qui **déclenche une exécution du pipeline**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Classe Main 
Votre méthode Main finale doit ressembler à ceci. Générez et exécutez votre programme pour déclencher une exécution du pipeline.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
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
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Exécuter le code
Créez et démarrez l’application, puis vérifiez l’exécution du pipeline.
La console affiche la progression de la création de la fabrique de données, du service lié, des jeux de données, du pipeline et de l’exécution du pipeline. Elle vérifie ensuite l’état de l’exécution du pipeline. Patientez jusqu’à l’affichage des détails de l’exécution de l’activité de copie avec la taille des données lues/écrites. Utilisez ensuite des outils comme l’explorateur Stockage Azure pour vérifier que les objets blob sont copiés dans « outputBlobPath » depuis « inputBlobPath » comme vous l’avez spécifié dans les variables.

**Exemple de sortie :**

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez effectué les étapes suivantes : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Créer un service lié Stockage Azure
> * Créer un jeu de données d’objet blob Azure
> * Créer un pipeline qui contient une activité de copie et une activité web
> * Envoyer les sorties des activités aux activités ultérieures
> * Utiliser des variables système et de passage de paramètres
> * Démarrer une exécution de pipeline
> * Surveiller les exécutions de pipeline et d’activité

Vous pouvez maintenant passer à la section Concepts pour plus d’informations sur Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipelines et activités](concepts-pipelines-activities.md)
