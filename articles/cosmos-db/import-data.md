---
title: "Outil de migration de base de données Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment utiliser l’outil de migration de données open source Azure Cosmos DB pour importer des données depuis différentes sources, y compris des fichiers MongoDB, SQL Server, Stockage Table, Amazon DynamoDB, CSV et JSON. Conversion CSV vers JSON."
keywords: "csv vers json, outils de migration de base de données, conversion csv vers json"
services: cosmos-db
author: andrewhoh
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: d173581d-782a-445c-98d9-5e3c49b00e25
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 036683698c49b8acb8a83117ac823c90fef0b2b3
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB : outil de migration de données

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Ce didacticiel explique comment utiliser l’outil de migration de données Azure Cosmos DB pour importer des données dans les collections et les tables Azure Cosmos DB à partir de différentes sources. Vous pouvez effectuer l’importation à partir de fichiers JSON, fichiers CSV, SQL, MongoDB, Stockage Table Azure, Amazon DynamoDB et même à partir de collections d’API SQL Azure Cosmos DB. Ensuite, vous migrez ces données vers des collections et des tables pour les utiliser avec Azure Cosmos DB. L’outil de migration de données peut également être utilisé pour migrer des données à partir d’une collection à partition unique vers une collection à plusieurs partitions pour l’API SQL.

Quelle API allez-vous utiliser avec Azure Cosmos DB ? 
* **[API SQL](documentdb-introduction.md)** - Vous pouvez utiliser toutes les options de source fournies dans l’outil de migration de données pour importer des données.
* **[API Table](table-introduction.md)** - Vous pouvez utiliser l’outil de migration de données ou AzCopy pour importer les données. Consultez [Importer des données à utiliser avec l’API Table Azure Cosmos DB](table-import.md) pour plus d’informations.
* **[API MongoDB](mongodb-introduction.md)**  - L’outil de migration de données ne prend pas en charge l’API MongoDB Azure Cosmos DB en tant que source ou cible. Si vous souhaitez migrer les données dans ou hors de collections d’API MongoDB dans Azure Cosmos DB, consultez [Azure Cosmos DB : Guide pratique pour migrer des données pour l’API MongoDB](mongodb-migrate.md) pour obtenir des instructions. Vous pouvez toujours utiliser l’outil de migration de données pour exporter des données de MongoDB vers des collections d’API SQL Azure Cosmos DB en vue d’une utilisation avec l’API SQL. 
* **[API Graph](graph-introduction.md)** - L’outil de migration de données n’est pas un outil d’importation pris en charge pour les comptes d’API Graph pour l’instant. 

Ce didacticiel décrit les tâches suivantes :

> [!div class="checklist"]
> * Installation de l’outil de migration de données
> * Importation de données à partir de différentes sources de données
> * Exportation de données à partir d’Azure Cosmos DB vers JSON

## <a id="Prerequisites"></a>Configuration requise
Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

* [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) ou une version ultérieure.

## <a id="Overviewl"></a>Vue d’ensemble
L’outil de migration de données est une solution open source permettant d’importer des données dans Azure Cosmos DB à partir de différentes sources, notamment :

* Fichiers JSON
* MongoDB
* SQL Server
* Fichiers CSV
* Stockage de tables Azure
* Amazon DynamoDB
* hbase
* Collections Azure Cosmos DB

L'outil d'importation inclut une interface utilisateur graphique (dtui.exe) et peut aussi être piloté à partir de la ligne de commande (dt.exe). En fait, il existe une option pour générer la commande associée après avoir configuré une importation via l'interface utilisateur. Des données sources tabulaires (par exemple, des fichiers SQL Server ou CSV) peuvent être transformées de manière à ce que des relations hiérarchiques (sous-documents) puissent être créées pendant l'importation. Poursuivez votre lecture pour en savoir plus sur les options sources, les exemples de lignes de commande pour l’importation depuis chaque source, les options cibles et l'affichage des résultats d’importation.

## <a id="Install"></a>Installation
Le code source de l’outil de migration est disponible sur GitHub dans [ce dépôt](https://github.com/azure/azure-documentdb-datamigrationtool). Vous pouvez télécharger et compiler la solution localement, puis exécuter l’un des deux fichiers suivants :

* **Dtui.exe**: version de l’interface graphique de l’outil
* **Dt.exe**: version en ligne de commande de l’outil

## <a name="select-data-source"></a>Sélectionnez la source de données

Une fois que vous avez installé l’outil, il est temps d’importer vos données. Quel type de données voulez-vous importer ?

* [Fichiers JSON](#JSON)
* [MongoDB](#MongoDB)
* [Fichiers d’exportation MongoDB](#MongoDBExport)
* [SQL Server](#SQL)
* [Fichiers CSV](#CSV)
* [Stockage Table Azure](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Objet blob](#BlobImport)
* [Collections Azure Cosmos DB](#SQLSource)
* [HBase](#HBaseSource)
* [Importation en bloc Azure Cosmos DB](#SQLBulkImport)
* [Importation d’enregistrement séquentiel Azure Cosmos DB](#DocumentDSeqTarget)


## <a id="JSON"></a>Importation de fichiers JSON
L'option d'importateur source du fichier JSON vous permet d'importer un ou plusieurs fichiers JSON ou des fichiers JSON qui contiennent chacun un tableau de documents JSON. Quand vous ajoutez des dossiers qui contiennent des fichiers JSON à importer, vous avez la possibilité de rechercher des fichiers de manière récursive dans les sous-dossiers.

![Capture d’écran des options sources du fichier JSON - Outils de migration de base de données](./media/import-data/jsonsource.png)

Voici quelques exemples de lignes de commande pour importer des fichiers JSON :

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a id="MongoDB"></a>Importation à partir de MongoDB

> [!IMPORTANT]
> Si vous importez des données vers un compte Azure Cosmos DB avec la prise en charge de MongoDB, suivez ces [instructions](mongodb-migrate.md).
> 
> 

L'option d'importateur source MongoDB vous permet d’importer à partir d'une collection MongoDB individuelle et de filtrer éventuellement les documents à l'aide d'une requête et/ou de modifier la structure du document à l'aide d'une projection.  

![Capture d’écran des options sources MongoDB](./media/import-data/mongodbsource.png)

La chaîne de connexion est au format MongoDB standard :

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> Utilisez la commande Verify pour vous assurer que l'instance MongoDB spécifiée dans le champ de la chaîne de connexion est accessible.
> 
> 

Saisissez le nom de la collection depuis laquelle les données seront importées. Vous pouvez éventuellement spécifier ou fournir un fichier pour une requête (par exemple, {pop: {$gt: 5000}} ) et/ou une projection (par exemple, {loc:0} ) pour filtrer et mettre en forme les données à importer.

Voici quelques exemples de ligne de commande pour l’importation depuis MongoDB :

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>Importation de fichiers d'exportation MongoDB

> [!IMPORTANT]
> Si vous importez des données vers un compte Azure Cosmos DB prenant en charge MongoDB, suivez ces [instructions](mongodb-migrate.md).
> 
> 

L’option d’importateur source du fichier JSON d’exportation MongoDB vous permet d’importer un ou plusieurs fichiers JSON générés depuis l’utilitaire mongoexport.  

![Capture d’écran des options sources d’exportation MongoDB](./media/import-data/mongodbexportsource.png)

Lorsque vous ajoutez des dossiers qui contiennent des fichiers JSON d’exportation à importer, vous avez la possibilité de rechercher des fichiers de manière récursive dans les sous-dossiers.

Voici un exemple de ligne de commande pour importer à partir de fichiers JSON d'exportation MongoDB :

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>Importation depuis SQL Server
L’option d’importateur source SQL vous permet d'importer à partir d'une base de données SQL Server individuelle et de filtrer éventuellement les enregistrements à importer à l'aide d'une requête. De plus, vous pouvez modifier la structure du document en spécifiant un séparateur d'imbrication (plus d’informations dans un instant).  

![Capture d’écran des options sources SQL - Outils de migration de base de données](./media/import-data/sqlexportsource.png)

Le format de la chaîne de connexion est le format de chaîne de connexion SQL standard.

> [!NOTE]
> Utilisez la commande Verify pour vous assurer que l'instance SQL Server spécifiée dans le champ de la chaîne de connexion est accessible.
> 
> 

La propriété du séparateur d'imbrication est utilisée pour créer des relations hiérarchiques (sous-documents) lors de l'importation. Examinez la requête SQL suivante :

*select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

Cette requête retourne les résultats (partiels) suivants :

![Capture d’écran des résultats de requête SQL](./media/import-data/sqlqueryresults.png)

Notez les alias tels que Address.AddressType et Address.Location.StateProvinceName. En spécifiant un séparateur d'imbrication de « . », l'outil d'importation crée les sous-documents Address et Address.Location lors de l'importation. Voici un exemple de document qui en résulte dans Azure Cosmos DB :

*{« ID » : « 956 », « Nom » : « Service et vente au détail », « Adresse »: {« AddressType »: « Siège », « AddressLine1 »: « #500-75 o ’ Connor Street », « Lieu »: {« Ville »: « Ottawa », « StateProvinceName »: « Ontario »}, « Code postal »: « K4B 1S2 », « CountryRegionName »: « Canada »}}*

Voici quelques exemples de lignes de commande pour l’importation depuis SQL Server :

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>Importer des fichiers CSV et convertir le format CSV en JSON
L'option d'importateur source du fichier CSV vous permet d'importer un ou plusieurs fichiers CSV. Quand vous ajoutez des dossiers qui contiennent des fichiers CSV à importer, vous avez la possibilité de rechercher des fichiers de manière récursive dans les sous-dossiers.

![Capture d’écran des options sources CSV - CSV vers JSON](media/import-data/csvsource.png)

De même que pour la source SQL, la propriété du séparateur d'imbrication peut être utilisée pour créer des relations hiérarchiques (sous-documents) lors de l'importation. Prenez en compte la ligne d'en-tête et les lignes de données du CSV suivant :

![Capture d’écran des exemples d’enregistrement CSV - CSV vers JSON](./media/import-data/csvsample.png)

Notez les alias tels que DomainInfo.Domain_Name et RedirectInfo.Redirecting. En spécifiant un séparateur d'imbrication de « . », l'outil d'importation crée les sous-documents DomainInfo et RedirectInfo lors de l'importation. Voici un exemple de document qui en résulte dans Azure Cosmos DB :

*{« DomainInfo » : {« Domain_name » : « ACUS.GOV », « Domain_Name_Address » : « http://www.ACUS.GOV »}, « Agence fédérale » : « Conférence administrative des États-Unis », « RedirectInfo » : {« Redirection » : « 0 », « Redirect_Destination » : « »}, « ID » : « 9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d »}*

L'outil d'importation tente de déduire les informations de type pour les valeurs sans guillemets dans les fichiers CSV (les valeurs entre guillemets sont toujours traitées comme des chaînes).  Les types sont identifiés dans l'ordre suivant : nombre, date et heure, valeur booléenne.  

Deux autres points sont à prendre en considération concernant l'importation CSV :

1. Par défaut, les valeurs sans guillemets sont toujours privées de leurs tabulations et espaces, tandis que les valeurs entre guillemets sont conservées telles quelles. Ce comportement peut être remplacé en cochant la case Rogner les valeurs entre guillemets ou à l'aide de l'option de ligne de commande /s.TrimQuoted.
2. Par défaut, une valeur null sans guillemets est considérée comme une valeur null. Ce comportement peut être remplacé (c'est-à-dire, traiter une valeur null sans guillemets comme une chaîne « null ») en cochant la case Traiter les valeurs null sans guillemets comme des chaînes ou à l'aide de l'option de ligne de commande /s.NoUnquotedNulls.

Voici un exemple de ligne de commande pour une importation CSV :

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>Importation depuis le stockage de tables Azure
L'option d’importateur source de Stockage Table Azure vous permet d’effectuer l’importation à partir d'une table individuelle de Stockage Table Azure. Vous pouvez aussi filtrer les entités de table à importer. 

Les données importées à partir du Stockage Table Azure peuvent être générées dans des tables et entités Azure Cosmos DB pour être utilisées avec l’API Table, ou dans des collections et des documents pour être utilisées avec l’API SQL. Toutefois, l’API Table est uniquement disponible en tant que cible dans l’utilitaire de ligne de commande, vous ne pouvez pas exporter vers l’API Table à l’aide de l’interface utilisateur de l’outil Data Migration. Pour plus d’informations, consultez la section [Importer des données à utiliser avec l’API Table Azure Cosmos DB](table-import.md). 

![Capture d’écran des options sources de stockage de tables Azure](./media/import-data/azuretablesource.png)

Le format de la chaîne de connexion de stockage de tables Azure est :

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> Utilisez la commande Verify pour vous assurer que l'instance de stockage de tables Azure spécifiée dans le champ de la chaîne de connexion est accessible.
> 
> 

Entrez le nom de la table Azure à partir de laquelle effectuer l’importation. Vous pouvez éventuellement spécifier un [filtre](https://msdn.microsoft.com/library/azure/ff683669.aspx).

L'option d’importateur source de stockage de tables Azure dispose des options supplémentaires suivantes :

1. Inclusion des champs internes
   1. Tous : inclure tous les champs internes (PartitionKey, RowKey et Timestamp)
   2. Aucun : exclure tous les champs internes
   3. RowKey : inclure uniquement le champ RowKey
2. Sélection des colonnes
   1. Les filtres de stockage de tables Azure ne prennent pas en charge les projections. Si vous souhaitez importer uniquement des propriétés d'entité de table Azure spécifiques, ajoutez-les à la liste Sélection des colonnes. Toutes les autres propriétés d'entité sont ignorées.

Voici un exemple de ligne de commande pour effectuer l’importation à partir du Stockage Table Azure :

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>Importation à partir d’Amazon DynamoDB
L’option d’importateur source d’Amazon DynamoDB vous permet d'importer à partir d'une table d’Amazon DynamoDB et de filtrer éventuellement les entités à importer. Plusieurs modèles sont fournis pour faciliter au maximum la configuration d'une importation.

![Capture d’écran des options sources Amazon DynamoDB - Outils de migration de base de données](./media/import-data/dynamodbsource1.png)

![Capture d’écran des options sources Amazon DynamoDB - Outils de migration de base de données](./media/import-data/dynamodbsource2.png)

Le format de la chaîne de connexion Amazon DynamoDB est :

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> Utilisez la commande Verify pour vous assurer que l'instance Amazon DynamoBD spécifiée dans le champ de la chaîne de connexion est accessible.
> 
> 

Voici un exemple de ligne de commande pour effectuer l’importation à partir d'Amazon DynamoDB :

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>Importer à partir du Stockage Blob Azure
Les options d’importateur source du fichier JSON, du fichier d'exportation MongoDB et du fichier CSV vous permettent d'importer un ou plusieurs fichiers à partir du stockage d’objets blob Azure. Après avoir spécifié l’URL d’un conteneur d'objets blob et une clé de compte, fournissez une expression régulière pour sélectionner le ou les fichier à importer.

![Capture d’écran des options sources du fichier blob](./media/import-data/blobsource.png)

Voici un exemple de ligne de commande pour importer des fichiers JSON à partir du Stockage Blob Azure :

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="SQLSource"></a>Importer à partir d’une collection d’API SQL
L’option de l’importateur source Azure Cosmos DB vous permet d’importer des données à partir d’une ou de plusieurs collections Azure Cosmos DB et de filtrer éventuellement des documents à l’aide d’une requête.  

![Capture d’écran des options sources d’Azure Cosmos DB](./media/import-data/documentdbsource.png)

Le format de la chaîne de connexion Azure Cosmos DB est :

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

La chaîne de connexion de compte Azure Cosmos DB peut être récupérée à partir de la page Clés du portail Azure, comme décrit dans [Guide pratique pour gérer un compte Azure Cosmos DB](manage-account.md), mais le nom de la base de données doit être ajouté à la chaîne de connexion au format suivant :

    Database=<CosmosDB Database>;

> [!NOTE]
> Utilisez la commande Verify pour vous assurer que l’instance Azure Cosmos DB spécifiée dans le champ de la chaîne de connexion est accessible.
> 
> 

Pour effectuer l’importation à partir d’une seule collection Azure Cosmos DB, entrez le nom de la collection à partir de laquelle importer les données. Pour effectuer l’importation à partir de plusieurs collections Azure Cosmos DB, fournissez une expression régulière correspondant à un ou plusieurs noms de collection (par exemple, collection01 | collection02 | collection03). Vous pouvez éventuellement spécifier ou fournir un fichier pour une requête pour filtrer et mettre en forme les données à importer.

> [!NOTE]
> Étant donné que le champ de collection accepte les expressions régulières, si vous importez à partir d'une collection unique dont le nom contient des caractères d'expression régulière, ces caractères doivent être placés en conséquence dans une séquence d'échappement.
> 
> 

L’option d’importateur source Azure Cosmos DB dispose des options avancées suivantes :

1. Inclure des champs internes : Spécifie s’il faut inclure ou non dans l’exportation les propriétés système du document Azure Cosmos DB (par exemple, _rid, _ts).
2. Nombre de nouvelles tentatives en cas d’échec : Spécifie le nombre de nouvelles tentatives de connexion à Azure Cosmos DB en cas d’échecs temporaires (par exemple, une interruption de connectivité réseau).
3. Intervalle avant nouvelle tentative : Spécifie le délai d’attente entre les nouvelles tentatives de connexion à Azure Cosmos DB en cas d’échecs temporaires (par exemple, une interruption de la connectivité réseau).
4. Mode de connexion : cette option indique le mode de connexion à utiliser avec Azure Cosmos DB. Les choix disponibles sont DirectTcp, DirectHttps et la passerelle. Les modes de connexion directs sont plus rapides, tandis que le mode passerelle est mieux adapté au pare-feu car il utilise uniquement le port 443.

![Capture d’écran des options sources avancées d’Azure Cosmos DB](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> L’outil d’importation utilise le mode de connexion DirectTcp par défaut. Si vous rencontrez des problèmes liés au pare-feu, passer au mode de connexion passerelle qui ne nécessite que le port 443.
> 
> 

Voici quelques exemples de lignes de commande pour l’importation à partir d’Azure Cosmos DB :

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> L’outil d’importation de données Azure Cosmos DB prend également en charge l’importation de données à partir de [l’émulateur Azure Cosmos DB](local-emulator.md). Quand vous importez des données à partir d’un émulateur local, affectez `https://localhost:<port>` comme point de terminaison. 
> 
> 

## <a id="HBaseSource"></a>Importation à partir de HBase
L’option d’importateur source HBase vous permet d’importer des données à partir d'une table HBase et de filtrer éventuellement les données. Plusieurs modèles sont fournis pour faciliter au maximum la configuration d'une importation.

![Capture d’écran des options sources HBase](./media/import-data/hbasesource1.png)

![Capture d’écran des options sources HBase](./media/import-data/hbasesource2.png)

Le format de la chaîne de connexion HBase Stargate est :

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> Utilisez la commande Verify pour vous assurer que l'instance HBase spécifiée dans le champ de la chaîne de connexion est accessible.
> 
> 

Voici un exemple de ligne de commande pour l’importation à partir de HBase :

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="SQLBulkTarget"></a>Importer vers l’API SQL (importation en bloc)
L’importateur en bloc Azure Cosmos DB vous permet d’importer à partir des options sources disponibles, à l’aide d’une procédure Azure Cosmos DB stockée pour plus d’efficacité. L’outil prend en charge l’importation dans une seule collection Azure Cosmos DB à partition unique, ainsi que l’importation partitionnée pour laquelle les données sont partitionnées sur plusieurs collections Azure Cosmos DB à partition unique. Pour plus d’informations sur le partitionnement de données, consultez [Partitionnement et mise à l’échelle dans Azure Cosmos DB](partition-data.md). L'outil crée, exécute, puis supprime la procédure stockée de la ou les collections cibles.  

![Capture d’écran des options de bloc d’Azure Cosmos DB](./media/import-data/documentdbbulk.png)

Le format de la chaîne de connexion Azure Cosmos DB est :

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

La chaîne de connexion de compte Azure Cosmos DB peut être récupérée à partir de la page Clés du portail Azure, comme décrit dans [Guide pratique pour gérer un compte Azure Cosmos DB](manage-account.md), mais le nom de la base de données doit être ajouté à la chaîne de connexion au format suivant :

    Database=<CosmosDB Database>;

> [!NOTE]
> Utilisez la commande Verify pour vous assurer que l’instance Azure Cosmos DB spécifiée dans le champ de la chaîne de connexion est accessible.
> 
> 

Pour importer des données dans seule collection, entrez le nom de la collection cible et cliquez sur le bouton Ajouter. Pour importer dans plusieurs collections, entrez le nom de chaque collection individuellement ou utilisez la syntaxe suivante pour spécifier plusieurs collections : *préfixe_collection*[index de début - index de fin]. Quand vous spécifiez plusieurs collections via la syntaxe ci-dessus, tenez compte des points suivants :

1. Seuls les modèles de nom de plage de nombres entiers sont pris en charge. Par exemple, la spécification de collection[0-3] crée les collections suivantes : collection0, collection1, collection2, collection3.
2. Vous pouvez utiliser une syntaxe abrégée : collection[3], qui crée le même jeu de collections que celui mentionné à l'étape 1.
3. Plusieurs substitutions peuvent être fournies. Par exemple, collection[0-1] [0-9] génère 20 noms de collection avec des zéros non significatifs (collection01, ..02, ..03).

Une fois que les noms de la collection ont été spécifiés, choisissez le débit souhaité des collections (entre 400 RU et 10 000 RU). Pour de meilleures performances d’importation, choisissez un débit plus élevé. Pour plus d’informations sur les niveaux de performances, consultez les [niveaux de performances d’Azure Cosmos DB](performance-levels.md).

> [!NOTE]
> Le paramètre de débit de performance s’applique uniquement à la création de collections. Si la collection spécifiée existe déjà, son débit n’est pas modifié.
> 
> 

Quand vous importez des données dans plusieurs collections, l'outil d'importation prend en charge le partitionnement basé sur le hachage. Dans ce scénario, spécifiez la propriété de document que vous voulez utiliser comme clé de partition (si la clé de partition est vide, les documents sont partitionnés de manière aléatoire entre les collections cibles).

Vous pouvez éventuellement spécifier le champ de la source d’importation à utiliser comme propriété d’ID du document Azure Cosmos DB pendant l’importation (notez que si les documents ne contiennent pas cette propriété, l’outil d’importation génère un GUID comme valeur de propriété d’ID).

De nombreuses options avancées sont disponibles lors de l'importation. Tout d'abord, tandis que l'outil inclut une procédure stockée d’importation en bloc par défaut (BulkInsert.js), vous pouvez choisir d’indiquer votre propre procédure stockée d'importation :

 ![Capture d’écran de l’option sproc d’insertion de bloc Azure Cosmos DB](./media/import-data/bulkinsertsp.png)

Par ailleurs, quand vous importez des types de date (par exemple, à partir de SQL Server ou de MongoDB), vous avoir le choix entre trois options d'importation :

 ![Capture d’écran des options d’importation de date et d’heure Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* Chaîne : conserver en tant que valeur de chaîne
* Epoch : conserver en tant que valeur numérique Epoch
* Les deux : conserver la chaîne et les valeurs numériques Epoch Cette option crée un sous-document, par exemple : "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

L’importateur en bloc Azure Cosmos DB dispose des options avancées supplémentaires suivantes :

1. Taille du lot : l'outil par défaut avec une taille de lot de 50.  Si les documents qui doivent être importés sont volumineux, pensez à réduire la taille du lot. À l’inverse, si les documents qui doivent être importés sont peu volumineux, pensez à augmenter la taille du lot.
2. Taille de script maximale (octets) : L'outil définit une taille de script maximale par défaut de 512 Ko.
3. Désactivation de la génération automatique d’ID : si tous les documents à importer contiennent un champ d'ID, la sélection de cette option permettra d’en augmenter les performances. Les documents avec un champ d’ID unique manquant ne sont pas importés.
4. Mise à jour des documents existants : par défaut, l’outil ne replace pas les documents existants présentant des conflits d'ID. Cette option permet de remplacer les documents existants par les ID correspondants. Cette fonctionnalité est utile pour les migrations de données planifiées qui mettent à jour des documents existants.
5. Nombre de nouvelles tentatives en cas d’échec : Spécifie le nombre de nouvelles tentatives de connexion à Azure Cosmos DB en cas d’échecs temporaires (par exemple, une interruption de connectivité réseau).
6. Intervalle avant nouvelle tentative : Spécifie le délai d’attente entre les nouvelles tentatives de connexion à Azure Cosmos DB en cas d’échecs temporaires (par exemple, une interruption de la connectivité réseau).
7. Mode de connexion : cette option indique le mode de connexion à utiliser avec Azure Cosmos DB. Les choix disponibles sont DirectTcp, DirectHttps et la passerelle. Les modes de connexion directs sont plus rapides, tandis que le mode passerelle est mieux adapté au pare-feu car il utilise uniquement le port 443.

![Capture d’écran des options d’importation en bloc avancées d’Azure Cosmos DB](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> L’outil d’importation utilise le mode de connexion DirectTcp par défaut. Si vous rencontrez des problèmes liés au pare-feu, passer au mode de connexion passerelle qui ne nécessite que le port 443.
> 
> 

## <a id="SQLSeqTarget"></a>Importer vers l’API SQL (importation d’enregistrement séquentiel)
L’importateur d’enregistrement séquentiel Azure Cosmos DB vous permet d’importer à partir de n’importe quelle option source disponible sur un enregistrement en fonction des enregistrements. Vous pouvez choisir cette option si vous importez vers une collection existante ayant atteint son quota de procédures stockées. L’outil prend en charge l’importation dans une seule collection Azure Cosmos DB (à partition unique et à plusieurs partitions), ainsi que l’importation partitionnée pour laquelle les données sont partitionnées sur plusieurs collections Azure Cosmos DB à partition unique et/ou à plusieurs partitions. Pour plus d’informations sur le partitionnement de données, consultez [Partitionnement et mise à l’échelle dans Azure Cosmos DB](partition-data.md).

![Capture d’écran des options d’importation d’enregistrement séquentiel d’Azure Cosmos DB](./media/import-data/documentdbsequential.png)

Le format de la chaîne de connexion Azure Cosmos DB est :

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

La chaîne de connexion de compte Azure Cosmos DB peut être récupérée à partir de la page Clés du portail Azure, comme décrit dans [Guide pratique pour gérer un compte Azure Cosmos DB](manage-account.md), mais le nom de la base de données doit être ajouté à la chaîne de connexion au format suivant :

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> Utilisez la commande Verify pour vous assurer que l’instance Azure Cosmos DB spécifiée dans le champ de la chaîne de connexion est accessible.
> 
> 

Pour importer dans seule collection, entrez le nom de la collection dans laquelle les données seront importées et cliquez sur le bouton Ajouter. Pour importer dans plusieurs collections, entrez le nom de chaque collection individuellement ou utilisez la syntaxe suivante pour spécifier plusieurs collections : *préfixe_collection*[index de début - index de fin]. Quand vous spécifiez plusieurs collections via la syntaxe ci-dessus, tenez compte des points suivants :

1. Seuls les modèles de nom de plage de nombres entiers sont pris en charge. Par exemple, la spécification de collection[0-3] crée les collections suivantes : collection0, collection1, collection2, collection3.
2. Vous pouvez utiliser une syntaxe abrégée : collection[3], qui crée le même jeu de collections que celui mentionné à l'étape 1.
3. Plusieurs substitutions peuvent être fournies. Par exemple, collection[0-1] [0-9] crée 20 noms de collection avec des zéros non significatifs (collection01, ..02, ..03).

Une fois que les noms de la collection ont été spécifiés, choisissez le débit souhaité des collections (entre 400 RU et 250 000 RU). Pour de meilleures performances d’importation, choisissez un débit plus élevé. Pour plus d’informations sur les niveaux de performances, consultez les [niveaux de performances d’Azure Cosmos DB](performance-levels.md). Les importations dans des collections avec un débit > 10 000 RU nécessitent une clé de partition. Si vous choisissez d’avoir plus de 250 000 RU, vous devez envoyer une demande d’augmentation de votre compte dans le portail.

> [!NOTE]
> Le paramètre de débit s’applique uniquement à la création de collections. Si la collection spécifiée existe déjà, son débit ne sera pas modifié.
> 
> 

Quand vous importez des données dans plusieurs collections, l'outil d'importation prend en charge le partitionnement basé sur le hachage. Dans ce scénario, spécifiez la propriété de document que vous voulez utiliser comme clé de partition (si la clé de partition est vide, les documents sont partitionnés de manière aléatoire entre les collections cibles).

Vous pouvez éventuellement spécifier le champ de la source d’importation à utiliser comme propriété d’ID du document Azure Cosmos DB pendant l’importation (notez que si les documents ne contiennent pas cette propriété, l’outil d’importation génère un GUID comme valeur de propriété d’ID).

De nombreuses options avancées sont disponibles lors de l'importation. Tout d’abord, quand vous importez des types de date (par exemple, à partir de SQL Server ou de MongoDB), vous avez le choix entre trois options d'importation :

 ![Capture d’écran des options d’importation de date et d’heure Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* Chaîne : conserver en tant que valeur de chaîne
* Epoch : conserver en tant que valeur numérique Epoch
* Les deux : conserver la chaîne et les valeurs numériques Epoch Cette option crée un sous-document, par exemple : "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

L’importateur d’enregistrement séquentiel Azure Cosmos DB dispose des options avancées supplémentaires suivantes :

1. Nombre de demandes parallèles : L'outil définit 2 demandes parallèles par défaut. Si les documents qui doivent être importés sont peu volumineux, pensez à augmenter le nombre de demandes parallèles. Notez que si ce nombre est trop élevé, l'importation peut rencontrer une limitation.
2. Désactivation de la génération automatique d’ID : si tous les documents à importer contiennent un champ d'ID, la sélection de cette option permettra d’en augmenter les performances. Les documents avec un champ d’ID unique manquant ne sont pas importés.
3. Mise à jour des documents existants : par défaut, l’outil ne replace pas les documents existants présentant des conflits d'ID. Cette option permet de remplacer les documents existants par les ID correspondants. Cette fonctionnalité est utile pour les migrations de données planifiées qui mettent à jour des documents existants.
4. Nombre de nouvelles tentatives en cas d’échec : Spécifie le nombre de nouvelles tentatives de connexion à Azure Cosmos DB en cas d’échecs temporaires (par exemple, une interruption de connectivité réseau).
5. Intervalle avant nouvelle tentative : Spécifie le délai d’attente entre les nouvelles tentatives de connexion à Azure Cosmos DB en cas d’échecs temporaires (par exemple, une interruption de la connectivité réseau).
6. Mode de connexion : cette option indique le mode de connexion à utiliser avec Azure Cosmos DB. Les choix disponibles sont DirectTcp, DirectHttps et la passerelle. Les modes de connexion directs sont plus rapides, tandis que le mode passerelle est mieux adapté au pare-feu car il utilise uniquement le port 443.

![Capture d’écran des options d’importation d’enregistrement séquentiel avancées d’Azure Cosmos DB](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> L’outil d’importation utilise le mode de connexion DirectTcp par défaut. Si vous rencontrez des problèmes liés au pare-feu, passer au mode de connexion passerelle qui ne nécessite que le port 443.
> 
> 

## <a id="IndexingPolicy"></a>Spécifier une stratégie d’indexation
Quand vous autorisez l’outil de migration à créer des collections d’API SQL Azure Cosmos DB pendant l’importation, vous pouvez spécifier la stratégie d’indexation des collections. Dans la section des options d’importation en bloc avancées Azure Cosmos DB et des options d’enregistrement séquentiel Azure Cosmos DB, accédez à la section de la stratégie de l’indexation.

![Capture d’écran des options de stratégie d’indexation avancées d’Azure Cosmos DB](./media/import-data/indexingpolicy1.png)

À l'aide de l’option de stratégie d'indexation avancée, vous pouvez sélectionner un fichier de stratégie d'indexation, entrer manuellement une stratégie d'indexation ou en sélectionner une parmi les différents modèles proposés par défaut (en cliquant avec le bouton droit dans la zone de texte de stratégie d'indexation).

L'outil fournit les modèles de stratégie suivants :

* Par défaut. Cette stratégie est préférable si vous exécutez des requêtes d’efficacité sur des chaînes et des requêtes ORDER BY, de plage et d’efficacité sur des nombres. Cette stratégie dispose d’une surcharge de stockage d'index inférieure à Plage.
* Plage. Cette stratégie est préférable si vous exécutez des requêtes ORDER BY, de plage et d'efficacité sur des nombres et des chaînes. Cette stratégie dispose d’une surcharge de stockage d'index supérieure à Par défaut ou Hachage.

![Capture d’écran des options de stratégie d’indexation avancées d’Azure Cosmos DB](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Si vous ne spécifiez pas de stratégie d'indexation, la stratégie par défaut est appliquée. Pour plus d’informations sur les stratégies d’indexation, consultez [Stratégies d’indexation d’Azure Cosmos DB](indexing-policies.md).
> 
> 

## <a name="export-to-json-file"></a>Exportation vers un fichier JSON
L’exportateur JSON Azure Cosmos DB vous permet d’exporter des options sources disponibles vers un fichier JSON qui contient un tableau des documents JSON. L'outil gère l'exportation pour vous. Vous pouvez également choisir d'afficher la commande de migration qui en résulte et d’exécuter la commande vous-même. Le fichier JSON résultant peut être stocké localement ou dans le stockage d’objets blob Azure.

![Capture d’écran des options d’exportation de fichier local JSON Azure Cosmos DB](./media/import-data/jsontarget.png)

![Capture d’écran des options d’exportation du stockage blob Azure JSON Azure Cosmos DB](./media/import-data/jsontarget2.png)

Vous pouvez éventuellement choisir d’agrémenter le JSON qui en résulte, ce qui augmente la taille du document obtenu tout en rendant le contenu plus lisible.

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]

## <a name="advanced-configuration"></a>Configuration avancée
Dans l'écran Configuration avancée, spécifiez l'emplacement du fichier journal dans lequel écrire toutes les erreurs. Les règles suivantes s'appliquent à cette page :

1. Si un nom de fichier n'est pas fourni, toutes les erreurs sont retournées dans la page Résultats.
2. Si un nom de fichier est fourni sans répertoire, le fichier est créé (ou remplacé) dans le répertoire de l'environnement actuel.
3. Si vous sélectionnez un fichier existant, le fichier est remplacé, il n'existe aucune option d'ajout.

Choisissez ensuite si vous souhaitez consigner tous les messages d’erreur, uniquement les messages critiques, ou aucun message d'erreur. Enfin, indiquez la fréquence de mise à jour du message de progression du transfert qui s’affiche à l’écran.

    ![Screenshot of Advanced configuration screen](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Confirmation des paramètres d'importation et affichage de la ligne de commande
1. Après avoir spécifié les informations sources et cibles ainsi que la configuration avancée, vérifiez le résumé de la migration et affichez ou copiez éventuellement la commande de migration qui en résulte (la copie de la commande est utile pour automatiser les opérations d'importation) :
   
    ![Capture d'écran de l'écran de résumé](./media/import-data/summary.png)
   
    ![Capture d'écran de l'écran de résumé](./media/import-data/summarycommand.png)
2. Une fois que vous êtes satisfait de vos options sources et cibles, cliquez sur **Importer**. Les informations concernant le temps écoulé, la quantité transférée et les échecs (si vous n'avez pas fourni de nom de fichier dans la configuration avancée) sont mises à jour pendant le processus d'importation. Une fois l’importation terminée, vous pouvez exporter les résultats (par exemple, pour gérer les échecs d’importation éventuels).
   
    ![Capture d’écran des options d’exportation JSON Azure Cosmos DB](./media/import-data/viewresults.png)
3. Vous pouvez également démarrer une nouvelle importation en conservant les paramètres existants (par exemple, les informations de chaîne de connexion, le choix de source et cible, etc.) ou en réinitialisant toutes les valeurs.
   
    ![Capture d’écran des options d’exportation JSON Azure Cosmos DB](./media/import-data/newimport.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez effectué les tâches suivantes :

> [!div class="checklist"]
> * Installation de l’outil de migration de données
> * Importation de données à partir de différentes sources de données
> * Exportation de données à partir d’Azure Cosmos DB vers JSON

Vous pouvez maintenant passer à l’étape suivante du didacticiel et découvrir comment interroger les données à l’aide d’Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Comment interroger les données ?](../cosmos-db/tutorial-query-sql-api.md)
