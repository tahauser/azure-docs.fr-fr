---
title: "Intégration de Data Lake Store à d’autres services Azure | Microsoft Docs"
description: "Présentation de l'intégration de Data Lake Store à d'autres services Azure"
documentationcenter: 
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: d43459b900232612d83506438e6a70daa893eb80
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>Intégration de Data Lake Store à d'autres services Azure
Azure Data Lake Store peut être utilisé conjointement avec d'autres services Azure afin d'augmenter le nombre de scénarios. L'article suivant liste les services auxquels Data Lake Store peut être intégré.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Utiliser Data Lake Store avec Azure HDInsight
Vous pouvez approvisionner un cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) qui utilise Data Lake Store comme stockage compatible HDFS. Pour cette version, pour les clusters Hadoop et Storm sous Windows et Linux, vous ne pouvez utiliser Data Lake Store que comme stockage supplémentaire. Ces clusters utilisent toujours Azure Storage (WASB) comme stockage par défaut. Toutefois, pour les clusters HBase sous Windows et Linux, vous pouvez utiliser Data Lake Store comme stockage par défaut, comme stockage supplémentaire ou les deux.

Pour savoir comment approvisionner un cluster HDInsight avec Data Lake Store, consultez :

* [Approvisionner un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Approvisionner un cluster HDInsight avec Data Lake Store comme stockage par défaut à l’aide d’Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Approvisionner un cluster HDInsight avec Data Lake Store comme stockage supplémentaire à l’aide d’Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Utiliser Data Lake Store avec Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) vous permet de travailler avec le Big Data à l'échelle du cloud. Il approvisionne dynamiquement des ressources et vous permet d'effectuer des analyses sur des téraoctets, voire des exaoctets, de données stockées dans plusieurs sources de données prises en charge, parmi lesquelles Data Lake Store. Data Lake Analytics est spécialement optimisé pour fonctionner avec Azure Data Lake Store, fournissant ainsi le plus haut niveau de performances, de débit et de parallélisation pour vos charges de travail de Big Data.

Pour obtenir des instructions sur l'utilisation de Data Lake Analytics avec Data Lake Store, consultez [Prise en main de Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-store-with-azure-data-factory"></a>Utiliser Data Lake Store avec Azure Data Factory
Vous pouvez utiliser [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pour recevoir des données à partir de tables Azure, de bases de données SQL Azure, d'Azure SQL DataWarehouse, d'objets Blob Azure Storage et de bases de données locales. Jouissant d'un statut de premier ordre dans l'écosystème Azure, Azure Data Factory peut être utilisé pour orchestrer l'ingestion de données de ces sources vers Azure Data Lake Store.

Pour obtenir des instructions sur l'utilisation d'Azure Data Factory avec Data Lake Store, consultez [Déplacer des données vers et depuis Data Lake Store avec Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Copier des données d’objets blob Azure Storage dans Data Lake Store
Azure Data Lake Store fournit un outil en ligne de commande, AdlCopy, qui vous permet de copier les données d’objets blob Azure Storage vers un compte Data Lake Store. Pour plus d’informations, consultez [Copier des données d’objets blob Azure Storage vers Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Copier des données entre Azure SQL Database et Data Lake Store
Vous pouvez utiliser Apache Sqoop pour importer et exporter des données entre Azure SQL Database et Data Lake Store. Pour plus d’informations, consultez [Copier des données entre Data Lake Store et une base de données SQL Azure à l’aide de Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-store-with-stream-analytics"></a>Utiliser Data Lake Store avec Stream Analytics
Vous pouvez utiliser Data Lake Store en tant que sortie pour stocker les données diffusées en continu à l’aide d’Azure Stream Analytics. Pour plus d’informations, consultez [Diffuser des données à partir d’un objet blob Azure Storage dans Data Lake Store à l’aide d’Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Utiliser Data Lake Store avec Power BI
Vous pouvez utiliser Power BI pour importer des données à partir d’un compte Data Lake Store en vue de les analyser et de les visualiser. Pour plus d’informations, consultez [Analyse des données dans Data Lake Store à l’aide de Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Utiliser Data Lake Store avec Data Catalog
Vous pouvez inscrire des données issues de Data Lake Store dans Azure Data Catalog pour qu’elles puissent être découvertes à l’échelle de l’organisation. Pour plus d’informations, consultez [Register data from Data Lake Store in Azure Data Catalog](data-lake-store-with-data-catalog.md)(Inscrire des données issues de Data Lake Store dans Azure Data Catalog).

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>Utiliser Data Lake Store avec SQL Server Integration Services (SSIS)
Vous pouvez utiliser le Gestionnaire de connexions Azure Data Lake Store dans SSIS pour connecter un paquet SSIS à Azure Data Lake Store. Pour obtenir plus d’informations, consultez [Utiliser Data Lake Store avec SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>Utiliser Data Lake Store avec SQL Data Warehouse
Vous pouvez utiliser Polybase pour charger des données à partir d’Azure Data Lake Store vers SQL Data Warehouse. Pour obtenir plus d’informations, consultez [Utiliser Data Lake Store avec SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-store-with-azure-event-hubs"></a>Utiliser Data Lake Store avec Azure Event Hubs
Vous pouvez utiliser Azure Data Lake Store pour archiver et capturer les données reçues par Azure Event Hubs. Pour plus d’informations, consultez [Utiliser Data Lake Store avec Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Voir aussi
* [Présentation d'Azure Data Lake Store](data-lake-store-overview.md)
* [Prise en main de Data Lake Store avec le portail](data-lake-store-get-started-portal.md)
* [Prise en main de Data Lake Store avec PowerShell](data-lake-store-get-started-powershell.md)  

