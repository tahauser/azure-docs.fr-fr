---
title: "Importer des données pour les utiliser avec l’API Table Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment importer des données à utiliser avec l’API Table Azure Cosmos DB."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: mimig
ms.openlocfilehash: 1c53be736ad65a53767626033be27f0891de06ba
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Importer des données à utiliser avec l’API Table Azure Cosmos DB

Ce didacticiel fournit des instructions sur l’importation de données à utiliser avec [l’API Table](table-introduction.md) Azure Cosmos DB. Si vous avez stocké des données dans le stockage Table Azure, vous pouvez utiliser l’outil de migration de données ou AzCopy pour les importer. Si vous avez des données stockées dans un compte d’API Table Azure Cosmos DB (préversion), vous devez utiliser l’outil de migration de données pour la migration. Une fois les données importées, vous pouvez tirer parti des fonctionnalités Premium d’Azure Cosmos DB, comme la distribution mondiale clé en main, le débit dédié, les latences de quelques millisecondes au 99e centile, la garantie de haute disponibilité et l’indexation automatique secondaire.

Ce didacticiel décrit les tâches suivantes :

> [!div class="checklist"]
> * Importation de données avec l’outil de migration de données
> * Importation de données avec AzCopy
> * Migration depuis une API Table (préversion) vers une autre 

## <a name="data-migration-tool"></a>Outil de migration de données

L’outil de migration des données de données Azure Cosmos DB en ligne de commande (dt.exe) peut être utilisé pour importer vos données de stockage de table Azure existante vers un compte GA d’API Table, ou migrer les données depuis un compte d’API Table (préversion) vers un compte GA d’API Table. Actuellement, les autres sources ne sont pas prises en charge. L’outil de migration des données basé sur une interface utilisateur (dtui.exe) n’est pas pris en charge pour les comptes API Table. 

Pour migrer des données de table, effectuez les tâches suivantes :

1. Téléchargez l’outil de migration à partir de [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Exécutez `dt.exe` en utilisant les arguments de ligne de commande de votre scénario.

L’outil dt.exe prend une commande au format suivant :

    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 

Les options de la commande sont les suivantes :

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All

### <a name="command-line-source-settings"></a>Paramètres de la source de ligne de commande

Utilisez les options de source suivantes lorsque vous définissez le Stockage Table Azure ou la préversion de l’API Table en tant que source de la migration.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Pour récupérer la chaîne de connexion source lors de l’importation à partir du Stockage Table Azure, ouvrez le portail Azure, puis cliquez sur **Comptes de stockage** > **Compte** > **Clés d’accès**, puis utilisez le bouton Copier pour copier la **chaîne de connexion**.

![Capture d’écran des options sources HBase](./media/table-import/storage-table-access-key.png)

Pour récupérer la chaîne de connexion source lors de l’importation à partir du compte d’API Table Azure Cosmos DB (préversion), ouvrez le portail Azure, puis cliquez sur **Azure Cosmos DB** > **Chaîne de connexion** > **Clés d’accès**, puis utilisez le bouton Copier pour copier la **chaîne de connexion**.

![Capture d’écran des options sources HBase](./media/table-import/cosmos-connection-string.png)

[Exemple de commande du Stockage Table Azure](#azure-table-storage)

[Exemple de commande d’API Table Azure Cosmos DB (préversion)](#table-api-preview)

### <a name="command-line-target-settings"></a>Paramètres de la cible de la ligne de commande

Utilisez les options de cible suivantes lorsque vous définissez l’API Table Azure Cosmos DB en tant que cible de la migration.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Exemple de commande (la source correspond au Stockage Table Azure)

Voici un exemple de ligne de commande indiquant comment effectuer l’importation entre le Stockage Table Azure et l’API Table :

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Exemple de commande (la source correspond à l’API Table Azure Cosmos DB [préversion])

Voici un exemple de ligne de commande permettant d’effectuer l’importation entre l’API Table (préversion) et la version GA de l’API Table :

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="azcopy-command"></a>Commande AzCopy

L’utilitaire de ligne de commande AzCopy est l’autre option qui permet de migrer les données à partir du Stockage Table Azure vers l’API Table Azure Cosmos DB. Pour utiliser AzCopy, vous devez d’abord exporter vos données comme décrit dans [Exporter des données à partir du stockage Table](../storage/common/storage-use-azcopy.md#export-data-from-table-storage), puis importer les données vers Azure Cosmos DB comme indiqué dans [l’API Table Azure Cosmos DB](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

Lorsque vous importez les données dans Azure Cosmos DB, consultez l’exemple suivant. Notez que la valeur /Dest utilise « cosmosdb », et non « core ».

Exemple de commande d’importation :

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migration depuis une API de table (préversion) vers une autre

> [!WARNING]
> Si vous voulez immédiatement profiter des avantages des tables généralement disponibles, migrez vos tables en préversion existantes comme indiqué dans cette section. Sinon, nous effectuerons des migrations automatiques pour les clients en préversion existants au cours des semaines à venir. Toutefois, notez que les tables en préversion migrées automatiquement sont associées à des restrictions, alors que les nouvelles tables créées ne le sont pas.
> 

L’API Table est généralement disponible (GA). Il existe des différences entre les préversions et les versions GA des tables, que ce soit dans le code exécuté dans le cloud ou dans le code exécuté sur le client. Pour cette raison, nous vous invitons à éviter de mélanger un client SDK en préversion avec un compte d’API Table GA, et vice versa. L’API Table permet de créer une préversion pour les clients qui veulent continuer à utiliser les tables existantes, mais qui, dans un environnement de production, doivent migrer des données depuis la préversion vers l’environnement GA, ou attendre une migration automatique. Si vous attendez la migration automatique, vous serez informé des restrictions associées aux tables migrées. Après la migration, vous ne pourrez pas créer sans restriction des tables sur votre compte existant (seules les tables migrées seront associées à des restrictions).

Pour effectuer la migration à partir de l’API Table (préversion) vers l’API Table généralement disponible, procédez comme suit :

1. Créez un compte Azure Cosmos DB et définissez son type d’API vers la table Azure, comme indiqué dans la section [Créer un compte de base de données](create-table-dotnet.md#create-a-database-account).

2. Modifiez les clients pour utiliser une version GA des [Kits de développement logiciel (SDK) d’API Table](table-sdk-dotnet.md).

3. Migrez les données client à partir des tables en préversion vers les tables GA à l’aide de l’outil de migration de données. Les instructions sur l’utilisation de l’outil de migration de données à cet effet sont décrites dans la section [Outil de migration de données](#data-migration-tool). 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Importer des données avec l’outil de migration de données
> * Importer des données avec AzCopy
> * Migration depuis une API Table (préversion) vers une autre

Vous pouvez maintenant passer au didacticiel suivant et découvrir comment interroger les données à l’aide de l’API Table Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Comment interroger les données ?](../cosmos-db/tutorial-query-table.md)
