---
title: "Prise en main du stockage de tables Azure à l’aide de .NET | Microsoft Docs"
description: "Stockez des données structurées dans le cloud à l’aide du stockage de tables Azure, un magasin de données NoSQL."
services: cosmos-db
documentationcenter: .net
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/30/2018
ms.author: mimig
ms.openlocfilehash: 317adaac1551cf00e25640f1d99429ad76d598a2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-azure-table-storage-using-net"></a>Prise en main du stockage de tables Azure à l’aide de .NET
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Le Stockage Table Azure est un service qui stocke des données NoSQL structurées dans le cloud, en fournissant une conception sans schéma à un magasin de clés/attributs. Comme le stockage de tables est sans schéma, il est aisé d’adapter vos données en fonction des besoins de votre application. L’accès aux données du Stockage Table est rapide et économique pour de nombreux types d’applications, et généralement moins coûteux que le SQL traditionnel pour des volumes de données similaires.

Vous pouvez utiliser le Stockage Table pour stocker des jeux de données flexibles, comme des données utilisateur pour des applications Web, des carnets d’adresses, des informations sur les périphériques ou d’autres types de métadonnées requis par votre service. Vous pouvez stocker un nombre quelconque d'entités dans une table, et un compte de stockage peut contenir un nombre quelconque de tables, jusqu'à la limite de capacité du compte de stockage.

### <a name="about-this-tutorial"></a>À propos de ce didacticiel
Ce didacticiel montre comment utiliser la [bibliothèque de tables Microsoft Azure CosmosDB pour .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) dans les scénarios de stockage de tables Azure courants. Le nom du package indique qu’il doit être utilisé avec Azure Cosmos DB, mais le package fonctionne avec Azure Cosmos DB et avec le stockage de tables Azure, chaque service possédant un point de terminaison unique. Ces scénarios sont explorés à l’aide d’exemples C# qui montrent comment :
* créer et supprimer des tables ;
* insérer, mettre à jour et supprimer des lignes.
* Tables de requête

## <a name="prerequisites"></a>configuration requise

Vous aurez besoin des éléments suivants pour suivre ce didacticiel :

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Bibliothèque commune de stockage Azure pour .NET (préversion)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). Il s’agit d’un package de préversion requis pris en charge dans les environnements de production. 
* [Bibliothèque de tables CosmosDB Microsoft Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)
* [Gestionnaire de configuration Azure pour .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Compte Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Autres exemples
Pour obtenir des exemples supplémentaires utilisant Table Storage, voir [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)(Prise en main d’Azure Table Storage dans .NET). Vous pouvez télécharger l’exemple d’application et l’exécuter ou parcourir le code sur GitHub.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>Créer un compte de service Azure

Vous pouvez travailler avec des tables à l’aide du stockage de tables Azure ou d’Azure Cosmos DB. Pour plus d’informations sur les différences entre les services, lisez [Table offres](table-introduction.md#table-offerings) (offres de tables). Vous devrez créer un compte pour le service que vous allez utiliser. 

### <a name="create-an-azure-storage-account"></a>Créer un compte de stockage Azure
Le moyen le plus simple de créer votre premier compte de stockage Azure est d’utiliser le [portail Azure](https://portal.azure.com). Pour plus d’informations, consultez la page [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account).

Vous pouvez également créer un compte de stockage Azure à l’aide [d’Azure PowerShell](../storage/common/storage-powershell-guide-full.md), de [l’interface de ligne de commande Azure](../storage/common/storage-azure-cli.md) ou de la [Bibliothèque cliente des fournisseurs de ressources de stockage pour .NET](/dotnet/api/microsoft.azure.management.storage).

Si vous préférez ne pas créer un compte de stockage à ce stade, vous pouvez également utiliser l’émulateur de stockage Azure pour exécuter et tester votre code dans un environnement local. Pour plus d’informations, consultez [Utilisation de l’émulateur de stockage Azure pour le développement et le test](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Créer un compte d’API de table Azure Cosmos DB

Pour obtenir des instructions sur la création d’un compte d’API de table Azure Cosmos DB, consultez [Create a Table API account](create-table-dotnet.md#create-a-database-account) (Créer un compte d’API de table).

## <a name="set-up-your-development-environment"></a>Configuration de l'environnement de développement
Ensuite, configurez votre environnement de développement dans Visual Studio afin d’être prêt pour essayer les exemples de code fournis dans ce guide.

### <a name="create-a-windows-console-application-project"></a>Créer un projet d’application de console Windows
Dans Visual Studio, créez une application de console Windows. Les étapes suivantes vous montrent comment créer une application de console dans Visual Studio 2017. Les étapes sont semblables pour d’autres versions de Visual Studio.

1. Sélectionnez **Fichier** > **Nouveau** > **Projet**.
2. Sélectionnez **Installé** > **Visual C#** > **Bureau classique Windows**.
3. Sélectionnez **Application console (.NET Framework)**.
4. Saisissez un nom pour votre application dans le champ **Nom**.
5. Sélectionnez **OK**.

Tous les exemples de code figurant dans ce didacticiel peuvent être ajoutés à la méthode `Main()` du fichier `Program.cs` de votre application de console.

Vous pouvez utiliser la bibliothèque de tables d’Azure CosmosDB dans n’importe quel type d’application .NET, y compris un service cloud Azure, une application web, une application de bureau ou une application mobile. Dans ce guide, nous utilisons une application console pour plus de simplicité.

### <a name="use-nuget-to-install-the-required-packages"></a>Utiliser NuGet pour installer les packages requis
Vous devez référencer trois packages dans votre projet pour terminer ce didacticiel :

* [Bibliothèque commune de stockage Azure pour .NET (8.6.0 - préversion)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/8.6.0-preview). 
* [Bibliothèque de tables CosmosDB Microsoft Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Ce package fournit un accès par programmes aux ressources de données dans votre compte de stockage Table Azure ou votre compte API Table Azure Cosmos DB.
* [Bibliothèque Microsoft Azure Configuration Manager pour .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) : ce package fournit une classe pour l’analyse d’une chaîne de connexion à partir d’un fichier de configuration, quel que soit l’emplacement d’exécution de votre application.

Vous pouvez utiliser NuGet pour obtenir ces deux packages. Procédez comme suit :

1. Cliquez avec le bouton droit sur votre projet dans **l’Explorateur de solutions**, puis sélectionnez **Gérer les packages NuGet**.
2. Recherchez en ligne « Microsoft.Azure.Storage.Common », puis sélectionnez **Installer** pour installer la bibliothèque commune de stockage Azure pour .NET (version préliminaire) et ses dépendances. Vérifiez que la case **Inclure la version préliminaire** est cochée, car il s’agit d’un version préliminaire du package de préversion.
3. Recherchez en ligne « Microsoft.Azure.CosmosDB.Table », puis sélectionnez **Installer** pour installer la bibliothèque de tables CosmosDB Microsoft Azure.
4. Recherchez en ligne « WindowsAzure.ConfigurationManager », puis sélectionnez **Installer** pour installer la bibliothèque du gestionnaire de configuration Microsoft Azure.

> [!NOTE]
> Les dépendances ODataLib de la bibliothèque de stockage commune pour .NET sont résolues via les packages ODataLib disponibles sur NuGet, et non à partir de WCF Data Services. Vous pouvez télécharger directement les bibliothèques ODataLib ou les référencer avec votre projet de code via NuGet. Les packages ODataLib utilisés par la bibliothèque cliente de stockage sont [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) et [Spatial](http://nuget.org/packages/System.Spatial/). Bien qu’elles soient utilisées par les classes de stockage de tables Azure, ces bibliothèques sont des dépendances requises pour la programmation avec la bibliothèque de stockage commune.
> 
> 

### <a name="determine-your-target-environment"></a>Déterminer votre environnement cible
Vous avez le choix entre deux environnements pour exécuter les exemples de ce guide :

* Vous pouvez exécuter votre code sur un compte Azure Storage dans le cloud. 
* Vous pouvez exécuter votre code sur un compte Azure Cosmos DB dans le cloud.
* Vous pouvez exécuter votre code sur l’émulateur de stockage Azure. L’émulateur de stockage est un environnement local qui émule un compte Azure Storage dans le cloud. L’émulateur est une option gratuite permettant de tester et déboguer votre code lors du développement de votre application. L’émulateur utilise un compte et une clé connus. Pour plus d’informations, consultez [Utilisation de l’émulateur de stockage Azure pour le développement et le test](../storage/common/storage-use-emulator.md).

Si vous ciblez un compte de stockage dans le cloud, copiez la clé d’accès primaire de votre compte de stockage à partir du portail Azure. Pour plus d’informations, voir [Affichage et copie de clés d’accès de stockage](../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [!NOTE]
> Vous pouvez cibler l’émulateur de stockage pour éviter les frais liés à l’utilisation des services de stockage Azure. Toutefois, si vous choisissez de cibler un compte de stockage Azure situé dans le cloud, les frais associés à l’utilisation de ce didacticiel seront négligeables.
> 
> 

Si vous ciblez un compte Azure Cosmos DB, copiez la clé d’accès primaire de votre compte de tables API à partir du portail Azure. Consultez la section [Mise à jour de votre chaîne de connexion](create-table-dotnet.md#update-your-connection-string) pour plus d'informations.

### <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage
La bibliothèque de stockage commune Azure pour .NET prend en charge l’utilisation d’une chaîne de connexion de stockage pour la configuration de points de terminaison et d’informations d’identification permettant d’accéder aux services de stockage. La meilleure façon de conserver votre chaîne de connexion de stockage est dans un fichier de configuration. 

Pour plus d’informations sur les chaînes de connexion, voir [Configuration d’une chaîne de connexion dans Stockage Azure](../storage/common/storage-configure-connection-string.md).

> [!NOTE]
> Votre clé de compte est similaire au mot de passe racine pour votre compte de stockage. Veillez toujours à protéger votre clé de compte de stockage. Évitez de la communiquer à d’autres utilisateurs, de la coder en dur ou de l’enregistrer dans un fichier texte brut accessible à d’autres personnes. Régénérez votre clé à l’aide du portail Azure si vous pensez que sa confidentialité est compromise.
> 
> 

Pour configurer votre chaîne de connexion, ouvrez le fichier `app.config` depuis l’Explorateur de solutions dans Visual Studio. Ajoutez le contenu de l’élément `<appSettings>` indiqué ci-dessous. Remplacez `account-name` par le nom de votre compte et `account-key` par votre clé d’accès au compte.

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Par exemple, si vous utilisez un compte de stockage Azure, vos paramètres de configuration ressemblent à :

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Si vous utilisez un compte Azure Cosmos DB, vos paramètres de configuration ressemblent à :

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

Pour cibler l’émulateur de stockage, vous pouvez utiliser un raccourci qui correspond à la clé et au nom de compte connus. Dans ce cas, le paramètre de votre chaîne de connexion est :

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Ajouter des directives d’utilisation
Ajoutez les directives **d’utilisation** suivantes au fichier `Program.cs` :

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage.Common; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Analyse de la chaîne de connexion
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Création du client du service de Table
La classe [CloudTableClient][dotnet_CloudTableClient] vous permet de récupérer des tables et entités stockées dans le Stockage Table. Voici un moyen de créer le client du service de Table :

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

Vous êtes maintenant prêt à écrire du code qui lit et écrit des données dans Table Storage.

## <a name="create-a-table"></a>Création d’une table
Cet exemple montre comment créer une table, si elle n’existe pas encore :

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Ajout d'une entité à une table
Les entités mappent vers les objets C# en utilisant une classe personnalisée dérivée d’une [TableEntity][dotnet_TableEntity]. Pour ajouter une entité à une table, créez une classe définissant les propriétés de votre entité. Le code suivant définit une classe d’entité utilisant le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition. Ensemble, les clés de partition et de ligne d’une entité l’identifient de façon unique dans la table. Les requêtes d’entités dont les clés de partition sont identiques sont plus rapides que les entités dont les clés de partition sont différentes, mais le fait d’utiliser différentes clés de partition améliore l’extensibilité des opérations parallèles. Les entités à stocker dans des tables doivent être d’un type pris en charge, par exemple dérivées de la classe [TableEntity][dotnet_TableEntity]. Les propriétés de l’entité à stocker dans une table doivent être des propriétés publiques du type et prendre en charge la récupération et la définition de valeurs. De plus, votre type d’entité *doit* exposer un constructeur sans paramètre.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Les opérations de table qui impliquent des entités sont effectuées par le biais de l’objet [CloudTable][dotnet_CloudTable] que vous avez créé précédemment dans la section « Création d’une table ». L’opération à effectuer est représentée par un objet [TableOperation][dotnet_TableOperation]. L’exemple de code suivant illustre la création des objets [CloudTable][dotnet_CloudTable] et **CustomerEntity**. Pour préparer l’opération, un objet [TableOperation][dotnet_TableOperation] est créé afin d’insérer l’entité du client dans la table. Finalement, l’opération est exécutée en appelant [CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute].

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Insertion d'un lot d'entités
Vous pouvez insérer un lot d'entités dans une table en une seule opération d'écriture. Autres remarques sur les opérations par lot :

* Vous pouvez effectuer des mises à jour, des suppressions et des insertions dans la même opération par lot.
* Une seule opération par lot peut inclure jusqu’à 100 entités.
* Toutes les entités d'une opération par lot doivent avoir la même clé de partition.
* Même s'il est possible d'exécuter une requête en tant qu'opération par lot, il doit s'agir de la seule opération du lot.

L’exemple de code suivant crée deux objets d’entité et ajoute chacun d’eux à [TableBatchOperation][dotnet_TableBatchOperation] en utilisant la méthode [Insert][dotnet_TableBatchOperation_Insert]. La méthode [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch] est ensuite appelée pour exécuter l’opération.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```

## <a name="retrieve-all-entities-in-a-partition"></a>Extraction de toutes les entités d'une partition
Pour exécuter une requête de table pour toutes les entités d’une partition, utilisez un objet [TableQuery][dotnet_TableQuery]. L’exemple de code suivant indique un filtre pour les entités où ’Smith’ est la clé de partition. Il imprime les champs de chaque entité dans les résultats de requête vers la console.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Extraction d’un ensemble d’entités dans une partition
Si vous ne voulez pas exécuter une requête pour toutes les entités d’une partition, vous pouvez spécifier un ensemble en combinant le filtre de clé de partition avec un filtre de clé de ligne. L’exemple de code suivant utilise deux filtres pour obtenir toutes les entités dans la partition « Smith » où la clé de ligne (prénom) commence par une lettre située avant la lettre « E » dans l’ordre alphabétique, puis imprime les résultats de la requête.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>Extraction d'une seule entité
Vous pouvez écrire une requête pour extraire une seule entité. Le code suivant utilise [TableOperation][dotnet_TableOperation] pour spécifier le client « Ben Smith ». Cette méthode renvoie une seule entité (et non une collection). De plus, la valeur renvoyée dans [TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result] est un objet **CustomerEntity**. La méthode la plus rapide pour extraire une seule entité dans le service de Table consiste à spécifier une clé de partition et une clé de ligne.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>Remplacement d’une entité
Pour mettre à jour une entité, récupérez-la du service de Table, modifiez l’objet d’entité, puis enregistrez les modifications dans le service de Table. Le code suivant modifie le numéro de téléphone d'un client existant. Au lieu d’appeler la méthode [Insert][dotnet_TableOperation_Insert], ce code utilise la méthode [Replace][dotnet_TableOperation_Replace]. [Replace][dotnet_TableOperation_Replace] entraîne le remplacement complet de l’entité sur le serveur, sauf si cette dernière a été modifiée depuis sa récupération, auquel cas l’opération échoue. Cet échec survient pour empêcher votre application de remplacer par erreur une modification apportée entre la récupération et la mise à jour par un autre composant de votre application. Pour gérer correctement cet échec, vous devez récupérer de nouveau l’entité, apporter vos modifications (si elles sont toujours valides), puis effectuer une autre opération [Replace][dotnet_TableOperation_Replace]. La prochaine section vous apprendra à remplacer ce comportement.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Insertion ou remplacement d’une entité
Les opérations [Replace][dotnet_TableOperation_Replace] échouent si l’entité a été modifiée depuis sa récupération à partir du serveur. De plus, vous devez d’abord récupérer l’entité du serveur pour que l’opération [Replace][dotnet_TableOperation_Replace] réussisse. Cependant, il se peut parfois que vous ne sachiez pas si l’entité existe sur le serveur et si les valeurs stockées sont inadaptées. Votre mise à jour doit donc toutes les remplacer. Pour cela, il vous faut utiliser une opération [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]. Cette opération insère l’entité (s’il n’y en a pas déjà une) ou la remplace (s’il y en a une), indépendamment du moment de la dernière mise à jour.

Dans l’exemple de code suivant, une entité client de « Fred Jones » est créée et insérée dans la table « people ». Ensuite, nous utilisons l’opération [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] pour enregistrer une entité avec la même clé de partition (Jones) et la même clé de ligne (Fred) sur le serveur, cette fois avec une valeur différente pour la propriété PhoneNumber. Comme nous utilisons [InsertOrReplace][dotnet_TableOperation_InsertOrReplace], toutes ses valeurs de propriété sont remplacées. Toutefois, s’il n’existait pas d’entité « Fred Jones » dans la table, elle aurait été insérée.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>Interrogation d'un sous-ensemble de propriétés d'entité
Vous pouvez utiliser une requête de table pour récupérer uniquement quelques propriétés au lieu de l’intégralité des propriétés de l’entité. Cette technique, nommée « projection », réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. La requête contenue dans le code suivant renvoie uniquement les adresses de messagerie électronique des entités dans la table. Pour ce faire, nous utilisons une requête [DynamicTableEntity][dotnet_DynamicTableEntity] et [EntityResolver][dotnet_EntityResolver]. Pour plus d’informations sur la projection, consultez le billet de blog [Présentation d’Upsert et de la projection de requête][blog_post_upsert]. La projection n’est pas prise en charge par l’émulateur de stockage : ce code ne s’exécute donc que si vous utilisez un compte dans le service de Table.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Suppression d’une entité
Il est facile de supprimer une entité après l’avoir récupérée. Il suffit pour cela d’utiliser la procédure suivie pour la mise à jour d’une entité. Le code suivant extrait et supprime une entité de client.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Suppression d’une table
Pour finir, l’exemple de code suivant supprime une table d’un compte de stockage. Une table supprimée ne peut plus être recréée pendant un certain temps.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Récupérer des entités dans les pages de manière asynchrone
Si vous lisez un grand nombre d’entités et souhaitez traiter ou afficher les entités dès qu’elles sont récupérées au lieu d’attendre qu’elles aient toutes été renvoyées, vous pouvez les récupérer à l’aide d’une requête segmentée. Cet exemple illustre comment renvoyer les résultats dans des pages au moyen du modèle Async-Await afin que l’exécution ne soit pas bloquée pendant que vous attendez le renvoi d’un ensemble de résultats volumineux. Pour plus d’informations sur l’utilisation du modèle Async-Await dans .NET, consultez l’article [Programmation asynchrone avec Async et Await (C# et Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>Étapes suivantes
Comme vous connaissez maintenant les bases du stockage des tables, vous pouvez consulter les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.
* Pour obtenir des exemples supplémentaires utilisant Table Storage, voir [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* Pour plus d'informations sur les API disponibles, consultez la documentation de référence du service de Table :
* [Référence de la bibliothèque cliente de stockage pour .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [Référence d’API REST](http://msdn.microsoft.com/library/azure/dd179355)
* Découvrez comment simplifier le code que vous écrivez avec Azure Storage, à l’aide du [Kit de développement logiciel (SDK) Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki)
* Pour plus d’informations sur les autres options de stockage de données dans Azure, consultez d’autres guides de fonctionnalités.
* [Prise en main d’Azure Blob Storage à l’aide de .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) pour le stockage de données non structurées.
* [Connexion à SQL Database à l’aide de .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) pour stocker des données relationnelles.

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[blog_post_upsert]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
