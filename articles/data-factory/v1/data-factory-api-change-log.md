---
title: Data Factory - Journal des modifications .NET API | Microsoft Docs
description: Décrit les dernières modifications, les ajouts de fonctionnalités, les correctifs de bogues, etc., d’une version spécifique de l'API .NET de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 110297fd60b4a25f959b451f2952f32d66cb8c3e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory : Journal des modifications de l’API .NET
> [!NOTE]
> Cet article s’applique à la version 1 de Data factory, qui est généralement disponible (GA). 

Cet article fournit des informations sur les modifications apportées au SDK Azure Data Factory dans une version spécifique. Vous trouverez le dernier package NuGet pour Azure Data Factory [ici](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Version 4.11.0
Ajouts de fonctionnalités :

* Les types de services liés suivants ont été ajoutés :
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Les types de jeux de données suivants ont été ajoutés :
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Les types de sources de copie suivants ont été ajoutés :
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Version 4.10.0
* Les propriétés facultatives suivantes ont été ajoutées à TextFormat :
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Les types de services liés suivants ont été ajoutés :
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Les types de jeux de données suivants ont été ajoutés :
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Les types de sources de copie suivants ont été ajoutés :
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Ajout de la propriété [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) à AzureMLBatchExecutionActivity
  * Autorisation du transfert de plusieurs entrées de service web dans une expérience Azure Machine Learning

## <a name="version-491"></a>Version 4.9.1
### <a name="bug-fix"></a>Résolution de bogue
* L’authentification basée sur des API Web pour [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx)est déconseillée.

## <a name="version-490"></a>Version 4.9.0
### <a name="feature-additions"></a>Ajouts de fonctionnalités
* Ajout des propriétés [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) et [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) à CopyActivity. Consultez la page [Copie intermédiaire](data-factory-copy-activity-performance.md#staged-copy) pour plus d’informations sur la fonctionnalité.

### <a name="bug-fix"></a>Résolution de bogue
* Introduction d’une surcharge de méthode [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx), qui nécessite une instance de [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx).
* Marquage de [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) et [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) comme facultatifs dans CopySink.

## <a name="version-480"></a>Version 4.8.0
### <a name="feature-additions"></a>Ajouts de fonctionnalités
* Les propriétés facultatives suivantes ont été ajoutées au type d’activité Copie pour une optimisation des performances de copie :
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Version 4.7.0
### <a name="feature-additions"></a>Ajouts de fonctionnalités
* Ajout du nouveau type StorageFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) pour copier des fichiers au format ORC (Optimized Row Columnar).
* Ajout des propriétés [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) et PolyBaseSettings à SqlDWSink.
  * Permet l’utilisation de PolyBase pour copier des données dans SQL Data Warehouse.

## <a name="version-461"></a>Version 4.6.1
### <a name="bug-fixes"></a>Résolution des bogues
* Résout les requêtes HTTP pour les listes de fenêtres d’activité.
  * Supprime le nom du groupe de ressources et le nom de la fabrique de données de la charge utile des requêtes.

## <a name="version-460"></a>Version 4.6.0
### <a name="feature-additions"></a>Ajouts de fonctionnalités
* Les propriétés suivantes ont été ajoutées à [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Groupes de données](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Les propriétés suivantes ont été ajoutées à [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Ajout des types [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) et [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) pour définir les jeux de données dont les données sont au format JSON.

## <a name="version-450"></a>Version 4.5.0
### <a name="feature-additions"></a>Ajouts de fonctionnalités
* Ajout d’une [liste des opérations pour la fenêtre d’activité](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Ajout de méthodes pour récupérer les fenêtres d’activité avec des filtres en fonction des type d’entités (fabriques de données, jeux de données, pipelines et activités).
* Les types de services liés suivants ont été ajoutés :
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Les types de jeux de données suivants ont été ajoutés :
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Les types de sources de copie suivants ont été ajoutés :     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Version 4.4.0
### <a name="feature-additions"></a>Ajouts de fonctionnalités
* Le type de service lié suivant a été ajouté en tant que source de données et récepteur pour les activités de copie :
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Consultez la section [Service lié SAP Azure Storage](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) pour obtenir des informations conceptuelles et des exemples.

## <a name="version-430"></a>Version 4.3.0
### <a name="feature-additions"></a>Ajouts de fonctionnalités
* Les types de service lié suivants ont été ajoutés en tant que sources de données pour les activités de copie :
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Consultez la section [Déplacer des données de HDFS à l’aide de Data Factory](data-factory-hdfs-connector.md) pour obtenir des informations conceptuelles et des exemples.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Consultez la section [Transfert de données à partir de magasins de données ODBC à l’aide d’Azure Data Factory](data-factory-odbc-connector.md) pour obtenir des informations conceptuelles et des exemples.

## <a name="version-420"></a>Version 4.2.0
### <a name="feature-additions"></a>Ajouts de fonctionnalités
* Le type d’activité suivant a été ajouté : [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Pour plus d’informations sur l’activité, consultez [Mettre à jour les modèles Azure ML à l’aide de l’activité de mise à jour des ressources](data-factory-azure-ml-batch-execution-activity.md).
* Une nouvelle propriété facultative [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) a été ajoutée à la [classe AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* Les propriétés [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) et [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) ont été ajoutées à la classe [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx).
* Elles permettent de configurer les délais d’attente pour les appels du client vers le service Data Factory.

## <a name="version-410"></a>Version 4.1.0
### <a name="feature-additions"></a>Ajouts de fonctionnalités
* Les types de services liés suivants ont été ajoutés :
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Les types d’activités suivants ont été ajoutés :
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Les types de jeux de données suivants ont été ajoutés :
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Les types de source et de récepteur suivants pour l’activité de copie ont été ajoutés :
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Version 4.0.1
### <a name="breaking-changes"></a>Dernières modifications
Les classes suivantes ont été renommées. Les nouveaux noms des classes sont les noms antérieurs à la version 4.0.0.

| Nom dans 4.0.0 | Nom dans 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Version 4.0.0
### <a name="breaking-changes"></a>Dernières modifications
* Les classes/interfaces suivantes ont été renommées.

| Ancien nom | Nouveau nom |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Table |[Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* Les méthodes **Liste** renvoient désormais des résultats paginés. Si la réponse contient une propriété **NextLink** non vide, l’application cliente doit continuer à récupérer la page suivante jusqu’à ce que toutes les pages soient renvoyées.  Voici un exemple : 

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **Listee** renvoie uniquement le résumé d’un pipeline au lieu des détails complets. Par exemple, les activités d’un résumé de pipeline ne contiennent que le nom et le type.

### <a name="feature-additions"></a>Ajouts de fonctionnalités
* La classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) prend en charge deux nouvelles propriétés, **SliceIdentifierColumnName** et **SqlWriterCleanupScript**, pour prendre en charge la copie idempotent vers Azure SQL Data Warehouse. Voir l’article [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) pour plus d’informations sur ces propriétés.
* Nous prenons désormais en charge l'exécution d’une procédure stockée avec des sources Azure SQL Database et Azure SQL Data Warehouse dans le cadre de l'activité de copie. Les classes [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) et [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) comportent les propriétés suivantes : **SqlReaderStoredProcedureName** et **StoredProcedureParameters**. Pour plus d’informations sur ces propriétés, consultez les articles [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) et [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) sur Azure.com.  
