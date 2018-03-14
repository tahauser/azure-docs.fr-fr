---
title: "Azure Cosmos DB : développer avec l’API Table dans .NET | Microsoft Docs"
description: "Découvrez comment développer avec l’API Table d’Azure Cosmos DB en utilisant .NET"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4b22cb49-8ea2-483d-bc95-1172cd009498
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: arramac
ms.custom: mvc
ms.openlocfilehash: bb08a60a9ec2db0fa145f75e00be96bc05664e32
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB : développer avec l’API Table dans .NET

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous pouvez rapidement créer et interroger des bases de données de documents, de paires clé-valeur et de graphiques, qui bénéficient toutes des fonctionnalités de distribution mondiale et de mise à l’échelle horizontale au cœur d’Azure Cosmos DB.

Ce didacticiel décrit les tâches suivantes : 

> [!div class="checklist"] 
> * Création d’un compte Azure Cosmos DB 
> * Activation des fonctionnalités dans le fichier app.config 
> * Création d’une table à l’aide de l’[API Table](table-introduction.md)
> * Ajout d'une entité à une table 
> * Insertion d'un lot d'entités 
> * Extraction d'une seule entité 
> * Interrogation d’entités à l’aide d’index secondaires automatiques 
> * Remplacement d’une entité 
> * Suppression d’une entité 
> * Suppression d’une table
 
## <a name="tables-in-azure-cosmos-db"></a>Tables dans Azure Cosmos DB 

Azure Cosmos DB fournit l’[API Table](table-introduction.md) pour les applications nécessitant un magasin de paires clé/valeur avec une conception sans schéma. L’API Table Azure Cosmos DB et [Stockage Table Azure](../storage/common/storage-introduction.md) prennent désormais en charge les mêmes Kits de développement logiciel (SDK) et API REST. Vous pouvez faire appel à Azure Cosmos DB pour créer des tables avec des exigences de débit élevées.

Ce didacticiel est destiné aux développeurs familiarisés avec le Kit de développement logiciel (SDK) Stockage Table Azure qui souhaitent utiliser les fonctionnalités Premium offertes par Azure Cosmos DB. Basé sur le didacticiel [Prise en main du stockage de tables Azure à l’aide de .NET](table-storage-how-to-use-dotnet.md), il montre comment tirer parti des fonctionnalités supplémentaires telles que les index secondaires, le débit approvisionné et le multihébergement. Ce didacticiel décrit comment utiliser le portail Azure pour créer un compte Azure Cosmos DB, puis créer et déployer une application d’API Table. Nous passons également en revue des exemples .NET pour la création et la suppression d’une table, et l’insertion, la mise à jour, la suppression et l’interrogation de données de table. 

Si vous utilisez actuellement le stockage de table Azure, vous bénéficiez des avantages suivants grâce à l’API Table d’Azure Cosmos DB :

- [Distribution mondiale](distribute-data-globally.md) clé en main avec multihébergement et [basculements automatiques et manuels](regional-failover.md)
- Prise en charge de l’indexation automatique indépendante du schéma par rapport à toutes les propriétés (« index secondaires »), et requêtes rapides 
- Prise en charge de la [mise à l’échelle indépendante du stockage et du débit](partition-data.md) pour autant de régions que nécessaire
- Prise en charge du [débit dédié par table](request-units.md), qui peut être mis à l’échelle de quelques centaines à plusieurs millions de requêtes par seconde
- Prise en charge de [cinq niveaux de cohérence ajustables](consistency-levels.md) pour trouver le bon compromis entre disponibilité, latence et cohérence en fonction des besoins de votre application
- Disponibilité de 99,99 % dans une région unique, possibilité d’ajouter d’autres régions pour augmenter la disponibilité et [contrats SLA complets à la pointe du secteur](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) sur la disponibilité générale
- Possibilité de travailler avec le Kit de développement logiciel (SDK) .NET Stockage Azure existant et aucune modification du code de votre application requise

Ce didacticiel décrit l’API Table d’Azure Cosmos DB utilisant le Kit de développement logiciel (SDK) .NET. Vous pouvez télécharger le [SDK de l’API .NET Table Azure Cosmos DB](https://aka.ms/tableapinuget) à partir de NuGet.

Pour en savoir plus sur les tâches de stockage Table Azure complexes, consultez :

* [Présentation de l’API Table d’Azure Cosmos DB](table-introduction.md)
* Pour plus d’informations sur les API disponibles, consultez la documentation de référence du service de Table [Kit de développement logiciel (SDK) .NET de l’API Table d’Azure Cosmos DB](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)

### <a name="about-this-tutorial"></a>À propos de ce didacticiel
Ce didacticiel est destiné aux développeurs familiarisés avec le Kit de développement logiciel (SDK) Stockage Table Azure qui souhaitent utiliser les fonctionnalités premium offertes par Azure Cosmos DB. Basé sur le didacticiel [Prise en main du stockage de tables Azure à l’aide de .NET](table-storage-how-to-use-dotnet.md), il montre comment tirer parti des fonctionnalités supplémentaires telles que les index secondaires, le débit approvisionné et le multihébergement. Nous expliquons comment utiliser le portail Azure pour créer un compte Azure Cosmos DB, puis créer et déployer une application Table. Nous passons également en revue des exemples .NET pour la création et la suppression d’une table, et l’insertion, la mise à jour, la suppression et l’interrogation de données de table. 

Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

Commençons par créer un compte Azure Cosmos DB dans le portail Azure.  
 
> [!IMPORTANT]  
> Vous devez créer un compte d’API Table pour utiliser les kits SDK d’API Table mis à la disposition générale. Les comptes d’API Table créés pendant la durée de la préversion ne sont pas pris en charge par les kits SDK mis à la disposition générale. 
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application Table à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder à un dossier d’installation pour l’exemple d’application. 

    ```bash
    cd "C:\git-samples"
    ```

2. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Ouvrez le fichier de solution dans Visual Studio. 

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application. Cette opération permet à votre application de communiquer avec votre base de données hébergée. 

1. Dans le [portail Azure](http://portal.azure.com/), cliquez sur **Chaîne de connexion**. 

    Utilisez les boutons de copie sur le côté droit de l’écran pour copier la CHAÎNE DE CONNEXION PRINCIPALE.

    ![Afficher et copier la CHAÎNE DE CONNEXION dans le volet Chaîne de connexion](./media/create-table-dotnet/connection-string.png)

2. Dans Visual Studio, ouvrez le fichier app.config. 

3. Supprimez les marques de commentaire de la chaîne StorageConnectionString à la ligne 8 et commentez la chaîne StorageConnectionString à la ligne 7, étant donné que ce didacticiel n’utilise pas l’émulateur de stockage. Les lignes 7 et 8 doivent maintenant ressembler à ceci :

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Collez la CHAÎNE DE CONNEXION PRINCIPALE du portail dans la valeur StorageConnectionString à la ligne 8. Collez la chaîne entre les guillemets.
   
    > [!IMPORTANT]
    > Si votre point de terminaison utilise documents.azure.com, cela signifie que vous disposez d’un compte de version préliminaire et que vous devez créer un [nouveau compte d’API Table](#create-a-database-account) à utiliser avec le Kit de développement logiciel (SDK) d’API Table généralement disponible. 
    >

    La ligne 8 doit maintenant ressembler à :

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. Enregistrez le fichier app.config.

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

## <a name="azure-cosmos-db-capabilities"></a>Fonctionnalités d’Azure Cosmos DB
Azure Cosmos DB prend en charge un certain nombre de fonctionnalités qui ne sont pas disponibles dans l’API de stockage Table Azure. 

Certaines fonctionnalités sont accessibles via les nouvelles surcharges vers CreateCloudTableClient qui permettent de préciser la stratégie de connexion et le niveau de cohérence.

| Paramètres de connexion de table | Description |
| --- | --- |
| Mode de connexion  | Azure Cosmos DB prend en charge deux modes de connectivité. Dans le mode `Gateway`, les requêtes sont toujours effectuées auprès de la passerelle Azure Cosmos DB, qui les transmet aux partitions de données correspondantes. Dans le mode de connectivité `Direct`, le client lit le mappage des tables aux partitions, et les requêtes sont effectuées directement dans les partitions de données. Nous recommandons d’utiliser le mode `Direct` (il s’agit du mode par défaut).  |
| Protocole de connexion | Azure Cosmos DB prend en charge deux protocoles de connexion : `Https` et `Tcp`. `Tcp` est la valeur par défaut. C’est le protocole recommandé, car il est plus léger. |
| Emplacements préférés | Liste séparée par des virgules des emplacements (de multihébergement) préférés pour les lectures. Chaque compte Azure Cosmos DB peut être associé à un nombre de régions compris entre un et plus de 30. Chaque instance de client peut spécifier un sous-ensemble de ces régions dans l’ordre de préférence pour des lectures à faible latence. Les régions doivent être nommées à l’aide de leurs [noms d’affichage](https://msdn.microsoft.com/library/azure/gg441293.aspx), par exemple, `West US`. Voir également [API multihébergement](tutorial-global-distribution-table.md). |
| Niveau de cohérence | Vous pouvez trouver un compromis entre latence, cohérence et disponibilité en choisissant l’un des cinq niveaux de cohérence bien définis disponibles : `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix` et `Eventual`. La valeur par défaut est `Session`. Le choix du niveau de cohérence a une influence considérable sur les performances dans les configurations multirégions. Pour plus d’informations, consultez [Niveaux de cohérence](consistency-levels.md). |

D’autres fonctionnalités peuvent être activées à l’aide des valeurs de configuration `appSettings` suivantes.

| Clé | Description |
| --- | --- |
| TableQueryMaxItemCount | Configurez le nombre maximal d’éléments renvoyés par requête de table en un seul aller-retour. La valeur par défaut, `-1`, laisse Azure Cosmos DB déterminer dynamiquement la valeur lors de l’exécution. |
| TableQueryEnableScan | Si la requête ne peut pas utiliser l’index pour des filtres, vous pouvez l’exécuter malgré tout via une analyse. La valeur par défaut est `false`.|
| TableQueryMaxDegreeOfParallelism | Degré de parallélisme pour l’exécution d’une requête sur plusieurs partitions. `0` correspond à une exécution en série sans pré-extraction, `1` à une exécution en série avec pré-extraction et les valeurs plus élevées augmentent le taux de parallélisme. La valeur par défaut, `-1`, laisse Azure Cosmos DB déterminer dynamiquement la valeur lors de l’exécution. |

Pour modifier la valeur par défaut, ouvrez le fichier `app.config` à partir de l’Explorateur de solutions dans Visual Studio. Ajoutez le contenu de l’élément `<appSettings>` indiqué ci-dessous. Remplacez `account-name` par le nom de votre compte de stockage et `account-key` par votre clé d’accès au compte. 

```xml
<configuration>
    ...
    <appSettings>
      <!-- Client options -->
      <add key="CosmosDBStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://account-name.table.cosmosdb.azure.com" />
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

Passons rapidement en revue ce qu’il se passe dans l’application. Ouvrez le fichier `Program.cs` ; vous constaterez que ces lignes de code créent les ressources de table. 

## <a name="create-the-table-client"></a>Création du client de tables
Vous initialisez un `CloudTableClient` pour vous connecter au compte de tables.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
Ce client est initialisé à l’aide des valeurs de configuration `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel` et `TablePreferredLocations` si elles sont spécifiées dans les paramètres d’application.

## <a name="create-a-table"></a>Création d’une table
Ensuite, vous créez une table à l’aide de `CloudTable`. Dans Azure Cosmos DB, le stockage et le débit peuvent être mis à l’échelle indépendamment pour chaque table, et le partitionnement est géré automatiquement par le service. Azure Cosmos DB prend à la fois en charge une taille fixe et un nombre illimité de tables. Pour plus d’informations, consultez [Partitionnement dans Azure Cosmos DB](partition-data.md). 

```csharp
CloudTable table = tableClient.GetTableReference("people");
400
table.CreateIfNotExists(throughput: 800);
```

Il existe une différence importante dans la façon dont les tables sont créées. Azure Cosmos DB réserve le débit, contrairement au modèle du stockage Azure pour les transactions, basé sur la consommation. Le débit est dédié/réservé, ce qui fait que vous n’êtes jamais limité si le taux de requêtes est inférieur ou égal à votre débit approvisionné.

Vous pouvez configurer le débit par défaut en l’incluant en tant que paramètre de CreateIfNotExists.

Une lecture d’une entité de 1 Ko est normalisée à 1 RU, et les autres opérations sont normalisées à une valeur de RU fixe en fonction de leur consommation de ressources processeur, de mémoire et d’E/S par seconde. En savoir plus sur les [Unités de requête dans Azure Cosmos DB](request-units.md) et plus particulièrement pour les [magasins de valeurs de clés](key-value-store-cost.md).

Maintenant, nous allons passer en revue les opérations de lecture et d’écriture (CRUD) simples avec le Kit de développement logiciel (SDK) Stockage Table Azure. Ce didacticiel démontre les latences prévisibles faibles, de l’ordre de quelques millisecondes, et les requêtes rapides assurées par Azure Cosmos DB.

## <a name="add-an-entity-to-a-table"></a>Ajout d'une entité à une table
Les entités du stockage Table Azure partent de la classe `TableEntity` et doivent présenter des propriétés `PartitionKey` et `RowKey`. Voici un exemple de définition d’une entité de client.

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

L’extrait de code suivant montre comment insérer une entité avec le Kit de développement logiciel (SDK) Stockage Azure. Azure Cosmos DB est conçu pour garantir une latence faible à n’importe quelle échelle, partout dans le monde.

Pour les applications exécutées dans la même région que le compte Azure Cosmos DB, 99 % des écritures prennent moins de 15 ms, 50 % d’entre elles prenant moins de 6 ms environ. Cette durée tient compte du fait que les écritures sont confirmées au client seulement une fois qu’elles ont été répliquées de manière synchrone, validées durablement, et que tout le contenu a été indexé.


```csharp
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
Le stockage Table Azure prend en charge une API d’opération par lot qui permet de combiner des mises à jour, des suppressions et des insertions dans la même opération par lot.

```csharp
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
## <a name="retrieve-a-single-entity"></a>Extraction d'une seule entité
Dans la même région Azure, 99 % des extractions dans Azure Cosmos DB prennent moins de 10 ms, 50 % d’entre elles prenant moins de 1 ms environ. Vous pouvez ajouter autant de régions à votre compte pour bénéficier de lectures à faible latence, et déployer les applications de sorte qu’elles effectuent les lectures à partir de leur région locale (applications dites « multihébergées ») en définissant `TablePreferredLocations`. 

Vous pouvez récupérer une entité unique à l’aide de l’extrait de code suivant :

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> Pour plus d’informations sur les API multihébergement, consultez [Développement avec plusieurs régions](tutorial-global-distribution-table.md).
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>Interrogation d’entités à l’aide d’index secondaires automatiques
Les tables peuvent être interrogées à l’aide de la classe `TableQuery`. Azure Cosmos DB intègre un moteur de base de données optimisé pour l’écriture qui indexe automatiquement toutes les colonnes de votre table. L’indexation dans Azure Cosmos DB est indépendante du schéma. Par conséquent, même si votre schéma est différent entre les lignes, ou s’il évolue au fil du temps, il est automatiquement indexé. Comme Azure Cosmos DB prend en charge les index secondaires automatiques, les requêtes sur n’importe quelle propriété peuvent utiliser l’index et être traitées efficacement.

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

Azure Cosmos DB prend en charge les mêmes fonctionnalités de requête que le stockage Table Azure pour l’API Table. Azure Cosmos DB prend également en charge le tri, les agrégats, les requêtes géospatiales, les hiérarchies et un large éventail de fonctions intégrées. Les fonctionnalités supplémentaires seront ajoutées à l’API Table dans une prochaine mise à jour de service. Pour une vue d’ensemble de ces fonctionnalités, consultez [Azure Cosmos DB query](sql-api-sql-query.md) (Requête dans Azure Cosmos DB). 

## <a name="replace-an-entity"></a>Remplacement d’une entité
Pour mettre à jour une entité, récupérez-la du service de Table, modifiez l’objet d’entité, puis enregistrez les modifications dans le service de Table. Le code suivant modifie le numéro de téléphone d'un client existant. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
De même, vous pouvez effectuer des opérations `InsertOrMerge` ou `Merge`.  

## <a name="delete-an-entity"></a>Suppression d’une entité
Il est facile de supprimer une entité après l’avoir récupérée. Il suffit pour cela d’utiliser la procédure suivie pour la mise à jour d’une entité. Le code suivant extrait et supprime une entité de client.

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>Suppression d’une table
Pour finir, l’exemple de code suivant supprime une table d’un compte de stockage. Vous pouvez supprimer et recréer une table immédiatement avec Azure Cosmos DB.

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons vu comment prendre en main Azure Cosmos DB avec l’API Table, et vous avez effectué les tâches suivantes : 

> [!div class="checklist"] 
> * Création d’un compte Azure Cosmos DB 
> * Activation des fonctionnalités dans le fichier app.config 
> * Création d’une table 
> * Ajout d’une entité à une table 
> * Insertion d’un lot d’entités 
> * Extraction d’une seule entité 
> * Interrogation d’entités à l’aide d’index secondaires automatiques 
> * Remplacement d’une entité 
> * Suppression d’une entité 
> * Suppression d’une table  

Vous pouvez maintenant passer au didacticiel suivant pour en savoir plus sur l’interrogation de données de table. 

> [!div class="nextstepaction"]
> [Exécution de requêtes avec l’API Table](tutorial-query-table.md)
