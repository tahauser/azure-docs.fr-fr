---
title: Transformer des données à l’aide de l’activité de procédure stockée dans Azure Data Factory | Microsoft Docs
description: Explique comment utiliser l’activité de procédure stockée SQL Server pour appeler une procédure stockée dans Azure SQL Database/Data Warehouse à partir d’un pipeline Data Factory.
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 84ef1e48de17db6f8b3b6fa4bdea2b5ef0f2efe6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformer des données à l’aide de l’activité de procédure stockée SQL Server dans Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-stored-proc-activity.md)
> * [Version 2 - Préversion](transform-data-using-stored-procedure.md)


Vous utilisez des activités de transformation dans un [pipeline](concepts-pipelines-activities.md) Data Factory pour transformer et traiter des données brutes en prévisions et en analyses. L’activité de procédure stockée est l’une des activités de transformation prises en charge par Data Factory. Cet article s’appuie sur l’article [Transformer des données](transform-data.md) qui présente une vue d’ensemble de la transformation des données et des activités de transformation prises en charge dans Data Factory.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, qui est actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, consultez [Activité de procédure stockée dans V1](v1/data-factory-stored-proc-activity.md).
> 
> Si vous découvrez Azure Data Factory, lisez la [présentation d’Azure Data Factory](introduction.md) et suivez le [Didacticiel : Transformer des données](tutorial-transform-data-spark-powershell.md) avant de lire cet article. 

Vous pouvez utiliser l’activité de procédure stockée pour appeler une procédure stockée dans l’une des banques de données suivantes dans votre entreprise ou sur une machine virtuelle Azure : 

- Base de données SQL Azure
- Azure SQL Data Warehouse
- Base de données SQL Server  Si vous utilisez SQL Server, installez le runtime d’intégration auto-hébergé sur l’ordinateur qui héberge la base de données ou sur un autre ordinateur ayant accès à la base de données. Le runtime d’intégration auto-hébergé est un composant qui connecte des sources de données locales ou se trouvant sur une machine virtuelle Azure à des services cloud de manière gérée et sécurisée. Pour plus d’informations, consultez l’article [Runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md).

> [!IMPORTANT]
> Lorsque vous copiez des données dans Azure SQL Database ou SQL Server, vous pouvez configurer l’élément **SqlSink** dans l’activité de copie pour appeler une procédure stockée en utilisant la propriété **sqlWriterStoredProcedureName**. Pour plus d’informations sur la propriété, consultez les articles suivants sur les connecteurs : [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). L’appel d’une procédure stockée lors de la copie de données dans Azure SQL Data Warehouse avec une activité de copie n’est pas pris en charge. Toutefois, vous pouvez utiliser l’activité de procédure stockée pour appeler une procédure stockée dans SQL Data Warehouse. 
>
> Lors de la copie de données à partir d’Azure SQL Database, SQL Server ou Azure SQL Data Warehouse, vous pouvez configurer **SqlSource** dans l’activité de copie pour appeler une procédure stockée afin de lire les données à partir de la base de données source en utilisant la propriété **sqlReaderStoredProcedureName**. Pour plus d’informations, consultez les articles suivants sur les connecteurs : [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Détails de la syntaxe
Voici le format JSON pour la définition d’une activité de procédure stockée :

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

Le tableau suivant décrit ces paramètres JSON :

| Propriété                  | Description                              | Obligatoire |
| ------------------------- | ---------------------------------------- | -------- |
| Nom                      | Nom de l’activité                     | OUI      |
| description               | Texte décrivant la raison motivant l’activité. | Non        |
| Type                      | Pour l’activité de procédure stockée, le type d’activité est **SqlServerStoredProcedure**. | OUI      |
| linkedServiceName         | Référence au service **Azure SQL Database**, **Azure SQL Data Warehouse** ou **SQL Server** enregistré en tant que service lié dans Data Factory. Pour en savoir plus sur ce service lié, consultez l’article [Services liés de calcul](compute-linked-services.md). | OUI      |
| storedProcedureName       | Spécifiez le nom de la procédure stockée à appeler. | OUI      |
| storedProcedureParameters | Spécifiez les valeurs des paramètres de procédure stockée. Utilisez `"param1": { "value": "param1Value","type":"param1Type" }` pour transmettre les valeurs des paramètres et leur type pris en charge par la source de données. Pour passer la valeur Null en paramètre, utilisez `"param1": { "value": null }` (tout en minuscules). | Non        |

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants qui expliquent comment transformer des données par d’autres moyens : 

* [Activité U-SQL](transform-data-using-data-lake-analytics.md)
* [Activité Hive](transform-data-using-hadoop-hive.md)
* [Activité pig](transform-data-using-hadoop-pig.md)
* [Activité MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Activité de diffusion en continu Hadoop](transform-data-using-hadoop-streaming.md)
* [Activité Spark](transform-data-using-spark.md)
* [Activité personnalisée .NET](transform-data-using-dotnet-custom-activity.md)
* [Activité d’exécution par lot Machine Learning](transform-data-using-machine-learning.md)
* [Activité de procédure stockée](transform-data-using-stored-procedure.md)
